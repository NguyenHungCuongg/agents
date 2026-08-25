# Lab 3 – Week 1, Day 4: Xây dựng Agent Loop từ đầu (Không dùng Agent Framework)

> **Khóa học:** Master AI Agents in 30 days – Ed Donner  
> **Mục tiêu:** Tự tay xây dựng một AI digital twin với Tools và Agent Loop mà không cần bất kỳ Agent Framework nào.

---

## 1. Chuẩn bị dữ liệu

### 1.1. File LinkedIn PDF

Trước tiên, trong thư mục `twin/` bạn cần chuẩn bị file `linkedin.pdf` – đây là bản tải xuống PDF từ trang hồ sơ LinkedIn của bạn.

> **Lưu ý quan trọng:** Đặt tên file **hoàn toàn viết thường** (`linkedin.pdf`, không phải `LinkedIn.pdf`). Khi deploy lên server Linux, hệ thống **phân biệt hoa/thường** (case-sensitive), nên sai tên file sẽ gây lỗi khó hiểu.

Ngoài ra, tạo file `twin/summary.txt` – một đoạn ngắn mô tả về bản thân bạn. Ví dụ trong bài thực hành:

```
My name is Nguyen Hung Cuong. I'm a student, software engineer and data scientist. I'm originally from Da Nang, VietNam, but I moved to Ho Chi Minh City, VietNam in 2023.
I love all foods, particularly French food, but strangely I'm repelled by almost all forms of cheese...
```

---

## 2. Packages sử dụng

```python
from dotenv import load_dotenv
from openai import OpenAI
from pypdf import PdfReader
from IPython.display import Markdown, display
import gradio as gr
import json
```

| Package | Mục đích |
|---|---|
| `dotenv` | Đọc biến môi trường từ file `.env` |
| `openai` | Python client để gọi LLM (ở đây dùng OpenRouter) |
| `pypdf` | Đọc nội dung từ file PDF |
| `gradio` | Tạo giao diện chat nhanh chóng |
| `json` | Parse JSON khi xử lý tool calls |

---

## 3. Khởi tạo OpenAI Client & Đọc dữ liệu

```python
import os
from dotenv import load_dotenv
from openai import OpenAI
from pypdf import PdfReader

load_dotenv(override=True)
openrouter_api_key = os.getenv('OPENROUTER_API_KEY')

openai = OpenAI(
    base_url="https://openrouter.ai/api/v1",
    api_key=openrouter_api_key
)
```

### Đọc file LinkedIn PDF

```python
reader = PdfReader("twin/linkedin.pdf")
linkedin = ""
for page in reader.pages:
    text = page.extract_text()
    if text:
        linkedin += text

print(linkedin)
```

**Kết quả:** Toàn bộ văn bản từ LinkedIn profile được đọc vào biến `linkedin`. Dữ liệu thô, không có cấu trúc – nhưng LLM rất giỏi xử lý dữ liệu phi cấu trúc.

### Đọc file summary.txt

```python
with open("twin/summary.txt", "r", encoding="utf-8") as f:
    summary = f.read()

print(summary)
```

**Kết quả:**
```
My name is Nguyen Hung Cuong. I'm a student, software engineer and data scientist...
```

---

## 4. Sidebar: 3 Khái niệm nền tảng quan trọng

### 4.1. System Prompt

Phần đầu tiên của input gửi lên LLM – thiết lập **vai trò**, **phong cách** và **context** cho toàn bộ cuộc trò chuyện. LLM đã được training để hiểu rằng phần được đánh dấu là `system` chính là bối cảnh tổng quan.

### 4.2. Conversation History (Lịch sử hội thoại)

Toàn bộ lịch sử cuộc trò chuyện được gửi lên LLM dưới dạng một **list các dict**:

```python
messages = [
    {"role": "system",    "content": "You are a helpful assistant"},
    {"role": "user",      "content": "Hi, my name is Cuong"},
    {"role": "assistant", "content": "Hello, Cuong! Nice to meet you."},
    {"role": "user",      "content": "What's my name?"},
]
```

### 4.3. Illusion of Memory (Ảo giác bộ nhớ)

LLM là **stateless** – mỗi lần gọi API là một lần hoàn toàn độc lập. Không có "bộ nhớ" thực sự. Trick là: **mỗi lần gọi, ta luôn gửi kèm toàn bộ lịch sử hội thoại**, nên LLM *có vẻ* nhớ những gì đã nói.

#### Demo: LLM không nhớ nếu không có history

```python
# Lần 1: Giới thiệu tên
messages = [
    {"role": "system", "content": "You are a helpful assistant"},
    {"role": "user",   "content": "Hi, my name is Cuong"}
]
response = openai.chat.completions.create(model="openrouter/free", messages=messages)
print(response.choices[0].message.content)
```
**Output:** `Hello, Cuong! It's nice to meet you. How can I assist you today?`

```python
# Lần 2: Hỏi tên – nhưng KHÔNG có history
messages = [
    {"role": "system", "content": "You are a snarky, witty assistant"},
    {"role": "user",   "content": "What's my name?"}
]
response = openai.chat.completions.create(model="openrouter/free", messages=messages)
print(response.choices[0].message.content)
```
**Output:** `I don't actually know your name! I'm an AI assistant and I don't have access to personal information about you unless you choose to share it with me directly during our conversation.`

> **Nhận xét:** LLM không biết tên vì không có lịch sử cuộc trò chuyện. Mỗi lần gọi là hoàn toàn độc lập.

#### Demo: Tạo ảo giác bộ nhớ bằng cách đưa history vào

```python
messages = [
    {"role": "system",    "content": "You are a snarky, witty assistant"},
    {"role": "user",      "content": "Hi, my name is Cuong"},
    {"role": "assistant", "content": "Well hi there, Cuong. It's nice to meet you."},
    {"role": "user",      "content": "What's my name?"},
]
response = openai.chat.completions.create(model="openrouter/free", messages=messages)
print(response.choices[0].message.content)
```
**Output:** `Your name is Cuong. I've got it right this time...`

---

## 5. Xây dựng Digital Twin

### 5.1. System Prompt cho Digital Twin

```python
system_prompt = f"""

# Your role

You are a digital twin running on a website, chatting with visitors of the website.
You represent the person who's website you are on.
You answer questions related to their career, background, skills and experience.

Here are the details of the person you are representing:

{summary}

If asked, you explain clearly that you are an AI that is the digital twin of this person.

# Context

Here is a summary of the person's LinkedIn profile so that you can answer questions:

{linkedin}

# Rules

Engage with the user. Be professional and engaging, as if talking to a potential client or future employer who came across the website.
Avoid answering questions that are not related to the user's career, background, skills and experience;
steer the conversation back to professional topics.
Always stay in character as the digital twin of the person you're representing.
Important: if you don't know the answer, say so. Never make up an answer.
"""
```

> **Kỹ thuật:** Sử dụng **f-string** để nhúng trực tiếp dữ liệu `summary` và `linkedin` vào system prompt. Dùng **Markdown headings** (`#`) để phân chia các section rõ ràng.

### 5.2. Hàm `chat()` đơn giản (chưa có Tool)

```python
def chat(message, history):
    messages = [{"role": "system", "content": system_prompt}] + history + [{"role": "user", "content": message}]
    response = openai.chat.completions.create(model="openrouter/free", messages=messages)
    return response.choices[0].message.content
```

Kiểm tra:
```python
chat("Please summarize who you are", [])
```

**Output (tóm tắt):**
```
I am the digital twin of Nguyen Hung Cuong, a dedicated Information Technology student 
at the University of Information Technology, Ho Chi Minh City, Vietnam. Core Focus: 
passionate about becoming a Java Developer or Web Developer...
```

### 5.3. Giao diện chat với Gradio

```python
import gradio as gr
gr.ChatInterface(chat).launch(inbrowser=True)
```

Gradio tự động tạo giao diện chat. Mỗi khi người dùng gửi tin nhắn, nó gọi hàm `chat(message, history)`:
- `message`: nội dung tin nhắn hiện tại
- `history`: toàn bộ lịch sử hội thoại (đã ở format OpenAI)

**Kết quả thực tế từ bài thực hành (Cell 28):**

> Tôi hỏi: `"Can you do backend?"`
>
> LLM trả lời:
> ```
> Yes—I have hands-on experience building backend services. In my projects I've worked with:
>
> Java + Spring Boot for RESTful APIs and microservices
> Node.js (Express) for lightweight services and real-time features
> Relational databases such as SQL Server and PostgreSQL for data persistence
> Containerization with Docker to package and deploy backend components
> These skills let me design, implement, test, and maintain scalable server-side logic...
> ```

---

## 6. TOOLS – Phần quan trọng nhất

> **Bản chất của Tool Calling:** Tool calling thực ra chỉ là **prompts + JSON + if statement**. Không có gì magic ở đây. Agent framework chỉ là lớp helper để ta không phải viết JSON boilerplate này thủ công.

### 6.1. Viết hàm Python thực hiện công việc

Hàm `record_email_tool` ghi email của người dùng vào file `emails.txt`:

```python
def record_email_tool(email):
    print(f"Tool called to record an email: {email}")
    with open("emails.txt", "a", encoding="utf-8") as f:
        f.write(email + "\n")
    return "Email received"
```

> **Giải thích tham số `"a"` (append mode):** Mở file ở chế độ ghi thêm – nếu file chưa tồn tại thì tạo mới, nếu đã tồn tại thì ghi tiếp vào cuối (không xóa nội dung cũ).  
> **`encoding="utf-8"`:** Đảm bảo hoạt động đúng trên cả Windows và macOS.

Kiểm tra:
```python
record_email_tool("test@testy.com")
```

**Output:**
```
Tool called to record an email: test@testy.com
'Email received'
```

**File `emails.txt` sau khi chạy:**
```
test@testy.com
```

### 6.2. Bước 1: Mô tả Tool bằng JSON Schema

LLM cần biết tool nào có sẵn và cần truyền tham số gì. Ta mô tả bằng JSON:

```python
record_email_tool_json = {
    "name": "record_email_tool",
    "description": "Use this tool to record that a user provided their email address",
    "parameters": {
        "type": "object",
        "properties": {
            "email": {
                "type": "string",
                "description": "The email address of this user"
            }
        },
        "required": ["email"],
        "additionalProperties": False
    }
}

tools = [{"type": "function", "function": record_email_tool_json}]
```

**Output khi in `tools`:**
```python
[{'type': 'function',
  'function': {'name': 'record_email_tool',
   'description': 'Use this tool to record that a user provided their email address',
   'parameters': {'type': 'object',
    'properties': {'email': {'type': 'string',
      'description': 'The email address of this user'}},
    'required': ['email'],
    'additionalProperties': False}}}]
```

> **Giải thích:** JSON này được gửi kèm trong input đến LLM (thực ra được nhúng vào system prompt). LLM sẽ đọc `description` để quyết định **khi nào** cần gọi tool này.

### 6.3. Bước 2: Hàm `chat()` với Tool (dùng `if`)

```python
def chat(message, history):
    messages = [{"role": "system", "content": system_prompt}] + history + [{"role": "user", "content": message}]
    response = openai.chat.completions.create(model="openrouter/free", messages=messages, tools=tools)
         
    if response.choices[0].finish_reason == "tool_calls":
        message = response.choices[0].message
        tool_call = message.tool_calls[0]  # Chi xu ly 1 tool call (hack!)
        email = json.loads(tool_call.function.arguments).get("email")
        record_email_tool(email)
        messages.append(message)
        messages.append({
            "role": "tool",
            "content": "Email recorded",
            "tool_call_id": tool_call.id
        })
        response = openai.chat.completions.create(model="openrouter/free", messages=messages, tools=tools)
            
    return response.choices[0].message.content
```

**Luồng hoạt động:**

1. Gửi messages + tools đến LLM
2. Kiểm tra `finish_reason`:
   - Nếu là `"tool_calls"` → LLM muốn gọi tool
   - Lấy tên tool và tham số từ response
   - **Chính code của ta** gọi hàm Python thực sự
   - Thêm kết quả tool vào messages
   - Gọi LLM lần 2 để lấy câu trả lời cuối
3. Trả về nội dung câu trả lời

**Kết quả thực tế từ bài thực hành (Cell 36):**

> Tôi hỏi: `"I'd like to get in touch. My email is helloworld@gmail.com"`
>
> LLM trả lời: `"I've noted your email address. I'll be in touch soon. In the meantime, feel free to explore my portfolio or reach out with any specific questions."`
>
> File `emails.txt` được ghi thêm: `helloworld@gmail.com` ✓

**Van de: Khi nho luu nhieu email cung luc -> LOI!**

> Tôi hỏi: `"I have 3 email that need to be recorded: cuong@gmail.com, teo@gmail.com, camhuong@gmail.com. please record"`
>
> LLM ngay lập tức bị lỗi.

**Nguyên nhân:** LLM thực sự cố gọi **3 tool calls** (một cho mỗi email), nhưng code của ta chỉ xử lý `tool_calls[0]` – tool call đầu tiên. OpenAI API báo lỗi vì các tool calls còn lại không được trả lời.

---

## 7. Agent Loop – Giải pháp cho vấn đề nhiều Tool Calls

### 7.1. Khái niệm Agent Loop

> **Agent = LLM + Tools + Loop**

Thay vì dùng `if`, ta dùng `while` – LLM tiếp tục được gọi lại miễn là nó muốn gọi thêm tools. Đây chính là **Agent Loop**.

### 7.2. Bước 3: Hàm `chat()` với Agent Loop (dùng `while`)

```python
def chat(message, history):
    messages = [{"role": "system", "content": system_prompt}] + history + [{"role": "user", "content": message}]
    response = openai.chat.completions.create(model="openrouter/free", messages=messages, tools=tools)
         
    while response.choices[0].finish_reason == "tool_calls":  # if -> while
        message = response.choices[0].message
        messages.append(message)
        for tool_call in message.tool_calls:  # Duyet TAT CA tool calls
            email = json.loads(tool_call.function.arguments).get("email")
            record_email_tool(email)
            messages.append({
                "role": "tool",
                "content": "Email recorded",
                "tool_call_id": tool_call.id
            })
        response = openai.chat.completions.create(model="openrouter/free", messages=messages, tools=tools)
            
    return response.choices[0].message.content
```

**Hai thay đổi nhỏ, ý nghĩa lớn:**

| Thay đổi | Phiên bản cũ (`if`) | Phiên bản mới (`while`) |
|---|---|---|
| Keyword | `if finish_reason == "tool_calls"` | `while finish_reason == "tool_calls"` |
| Số tool calls | Chỉ xử lý 1 (`tool_calls[0]`) | Duyệt tất cả (`for tool_call in tool_calls`) |

**Kết quả thực tế từ bài thực hành (Cell 38):**

> Tôi hỏi: `"Hi, I have 3 email that need to be recorded: cuong@gmail.com, teo@gmail.com, camhuong@gmail.com. please record"`
>
> LLM trả lời:
> ```
> I've successfully recorded all three email addresses:
>
> cuong@gmail.com
> teo@gmail.com
> camhuong@gmail.com
>
> All emails have been saved to the system. Is there anything else you'd like me to help you with
> regarding your digital twin profile or career information?
> ```

**File `emails.txt` sau khi chạy:**
```
test@testy.com
helloworld@gmail.com
cuong@gmail.com
teo@gmail.com
camhuong@gmail.com
```

**Log từ Jupyter Notebook:**
```
Tool called to record an email: cuong@gmail.com
Tool called to record an email: teo@gmail.com
Tool called to record an email: camhuong@gmail.com
```

---

## 8. Tóm tắt – Bản chất của Agentic AI

```
Agentic AI = LLM + Tools + Loop
           = Prompts + JSON + if/while statement
```

| Thành phần | Cách triển khai thực tế |
|---|---|
| **LLM** | Gọi `openai.chat.completions.create()` |
| **Tools** | JSON Schema mo ta ham + `if/while finish_reason == "tool_calls"` |
| **Loop** | Vòng lặp `while` để LLM gọi nhiều tools |
| **Agent Framework** | Thu vien giup tu dong tao JSON Schema, quan ly loop – ta khong can viet tay nua |

> **Insight quan trọng:** Agent Frameworks (LangGraph, CrewAI, AutoGen...) không làm gì thần kỳ. Chúng chỉ là lớp **abstraction** giúp ta không phải viết boilerplate JSON và if/while thủ công. Bên dưới, mọi thứ vẫn là prompts, JSON và câu điều kiện.

---

## 9. Sơ đồ luồng hoạt động

```
Người dùng nhập tin nhắn
        |
        v
  [Ham chat()]
  Ghep: system_prompt + history + user_message
        |
        v
  Goi LLM (lan 1) voi tools
        |
        v
  finish_reason == "tool_calls"? --No--> Tra ket qua cho nguoi dung
        |
       Yes
        |
        v
  Duyet tung tool_call trong message.tool_calls:
    - Goi ham Python tuong ung
    - Them ket qua vao messages
        |
        v
  Goi LLM (lan 2) voi messages da bo sung
        |
        +----------------------------+
                                     |
                            (while loop tiep tuc)
```

---

## 10. Bài tập (Exercises)

### Bài tập 1: Thêm LLM Evaluator (Guardrail)

Sau khi LLM tạo ra câu trả lời, dùng **một LLM call khác** để kiểm tra xem câu trả lời có liên quan đến công việc/career không. Nếu không đúng chủ đề, yêu cầu tạo lại.

Pattern này gọi là: **Evaluator-Optimizer** hoặc **LLM as a Judge** – đây cũng là cách xây dựng **Guardrail**.

### Bài tập 2: Áp dụng vào công việc thực tế

Xây dựng AI Assistant với Tools cho lĩnh vực của bạn:
- Trả lời câu hỏi về sản phẩm/dịch vụ
- Tool ghi lại email khách hàng muốn được liên hệ
- Tool cung cấp thông tin bổ sung từ database

---

## 11. Kết quả đạt được trong Lab này

- [x] Tạo **Digital Twin** – AI đại diện cho bản thân bạn
- [x] Hiểu **System Prompt**, **Conversation History**, **Illusion of Memory**
- [x] Xây dựng **Tool** đầu tiên (ghi email ra file)
- [x] Mô tả Tool bằng **JSON Schema**
- [x] Implement **Tool Calling** với `if` statement
- [x] Implement **Agent Loop** với `while` statement
- [x] Tạo **giao diện chat** với Gradio
- [x] Hiểu bản chất: **Agent = Prompts + JSON + if/while**
