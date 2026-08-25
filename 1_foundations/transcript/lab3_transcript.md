Well to my great delight we are back in casa and I'd like you to go back to the

OneFoundations folder and come to lab 3 which is day four's lab and which is where we will

have some fun with tools. Okay so we're going to build something over today and tomorrow. By the

end of the week we'll have something with real value for you. It's the start of the lab that

will last two days and I will go through more tomorrow about how you actually use this for

value. But today we're going to hand crank our own agent loop from scratch without using any

agent framework. No agent framework required. First up we have to do some groundwork. There

is a folder here a subfolder called twin. It might have a few more files by the time you get to it

but right now for me it's only got two and those are two that you also have to create and the first

of them is called linkedin.pdf and it's a pdf download of my linkedin profile and you should

do the same but get a pdf download of yours. Okay this is what linkedin looks like if you go to your

linkedin homepage and then you click on your name on the left it will bring up your profile page

like this and just to show you that for me anyway there's a triple buttons here. I click there and

there's a save to pdf option to save this to a pdf. If you don't have that for some reason

perhaps because you're in a different plan than me, different type of linkedin, different region,

whatever, then just print this to pdf or just use any resume pdf or just write some notes about

yourself and save it as a pdf. Anything like that would be great. It doesn't need to be a pdf because

we can read it in text file but it's nice for us to try out a pdf. So give that a shot do that and

while you're on your linkedin page I might just mention that very easy thing to do would be to

connect with me or follow me or whatever it lets you do. That would be lovely. I would very much

appreciate it and all my other socials as well. I'm just trying to get to use axe and I have a

youtube channel too so any of them while you're on the internet. Not like I haven't mentioned that

before. All right now that we've done that let's press on. So you put that in linkedin.pdf in twin

make sure you spell it right. By the way a nasty little trick be sure that you you spell linkedin.pdf

all in lowercase and you might think well that doesn't matter these pcs and macs are case

insensitive it can be in any uppercase capital X or lowercase it doesn't matter but it will matter

because we are actually going to be deploying this later and we're going to deploy it to a

linux box for free but those linux boxes are case sensitive and there's some really sneaky

problems that people have found when they've got this not spelt in lowercase so watch out for that

uh in fact all of the files we put here should be in lowercase so linkedin.pdf is my linkedin

profile i've also got another file i've created called summary.txt remember you can just right

click here and say new file to create a new one i created summary.txt and it starts my name is

adonna i'm blah blah blah and it says that i hate cheese which is something that uh some

people when they hear that i hate cheese they're like in that case you are dead to me you are dead

to me cheese lovers out there can't stand the fact that i dislike cheese if you're a cheese hater out

there uh with me on this then please do message me we cheese haters have to stick together and

have to bandy up against these people that are trying to push their moldy milk on us all right

anyways i digress this is summary.txt uh come into this reboot i put and then write something

similar for yourself you don't need to change create a new file like i said there you can just

edit this one change it so that it reflects you make it just very short couple of sentences about

yourself that's going to be what we will need for building what we're about to do which is a digital

twin of ourselves and i'm kind of giving the game away here but of course where we're going to go

with this is it's ultimately going to become something that you could deploy to be like a

career twin that could potentially answer questions to employers or clients about yourself and your

skills and so on it is perhaps the resume of the future and it's become quite common i first came

out with this like a year ago and i did it because it was an idea from from someone i work with it

was really terrific and since i've done that i think a lot of people have been building these

digital twins and we're going to do a really great one a really powerful one but you should

hopefully be able to take it in a different direction and make it really personal for you

so building a digital twin is a great fun thing to do okay but you've now created your linkedin.pdf

your summary.txt and we're ready to get to work so we're going to use a few different packages

we're going to use gradio which is a wonderful package for user interfaces i cover it a lot in

my core track and other courses we're just going to use it here just go along with it if you don't

know it if you do know it you know you love it too i'm sure and we're also going to use a package

called pypdf that reads pdf files and a question i get quite often is how did i pick those packages

and how do you go about setting up a new project and deciding what packages to use and i've answered

that pretty thoroughly in question 37 linked in the faq and the short answer is you have to put

in the work as i explain but anyways i'm now going to do some imports you can look them up if you're

not sure what they do and then i'm going to load in my .emv files all my environment variables and

i'm going to create the openai python client library and of course if you want to use a

different python client library if you sorry the same python client library a different llm then

you should do so use the instructions in guide 9 that you're now probably very familiar with

but i've run that and now i'm going to load in the pdf and turn it into text so look it's very

straightforward this is the code you might wonder why i don't like type it out for you it's because

a coding agent would write this for you in a heartbeat but you can see you just create pdf

reader linking to linkedin.pdf and then we just read in all the pages there's a page.extract text

it's how we pull the text we yank out just the raw text and if i now print the contents of that

variable you'll see that it has all the text from my linkedin profile and of course it's not

particularly nicely structured it's just spewed out there with like skills meshed in with jobs

and summaries but luckily llms are fantastic at handling unstructured data and interpreting it in

an intelligent way so it doesn't matter that this is just all laid out as a bit of a mess

okay now we're also going to open the summary the summary file and read that in and i can also add a

bit of code we can just print the summary and just check that that came through okay print summary

and there it is my name is ed blah blah blah and i'm repelled by almost all forms of cheese don't

hate me okay we're now going to do a quick sidebar i need to distract you for one second so hold this

thought hold where we are i just want to cover three foundational points if you know these three

points already like if you've taken my core track course then just put me on 2x zoom through this

but very briefly i want to mention system prompts conversation history and something that i call the

illusion of memory which are all basic building blocks for working with llms when you send a

message an input prompt to an llm we think of it just as a bunch of text which it's trying to extend

but in fact it can be a little bit structured you can organize that text into different sections

and those sections are referred to when we call the api in that list of dictionaries that each

dictionary represents a different section that's kind of separated out in the input sequence and

the first section is sometimes called the system prompt it's marked in a special way as the system

prompt and it's important information that tells the llm the overall framing the overall role that

it's playing the style it should take and any background information that will help it to

generate tokens that's in the system prompt and when i say it tells the llm that if you're wondering

what i mean all i'm saying is that all the times the llm was trained with tons and tons and tons of

data it was organized that way or at least in many of the times it was trained it was organized that

way and so it has learned to understand that the part of the input that is cordoned off and

identified as the system that part it should consider to be setting the overall situation

it giving the overall context for how it should reply to the user's message that's the system

prompt now what comes next in the input sequence that goes to an llm is a series of messages so far

we've only been sending llms a user message a single user message and it's responded to but

you can actually pile up in there a user message and then what the assistant said back and then

the next user message and what the assistant said back to that assistant meaning gpt so the user's

message its reply the user's message its reply and all of that is sometimes known as the conversation

history the series of messages to and fro ending with the final message from the user that the llm

should respond to and that series of messages is known as the conversation history and the third

point again probably very obvious to you is that there is something i call the illusion of memory

which is to say that every time you call an llm it is of course completely stateless gpt is out

there running it takes an input sequence it gives you the output it doesn't know that it was called

with a different input sequence in the past in fact the one in openai's cloud has been called

millions of times a second probably with different input sequences but the trick is that when you give

it your input sequence you always put in the full conversation history of this particular

interaction so far so it says like user assistant user assistant and then finally the user's message

and when it's generating the next tokens to come it takes into account the whole conversation so

far of course and so it appears to have a memory of what's been going on in the conversation and

that's why when you're talking to chat gpt you can just sort of refer back to what you just said and

the conversation continues it certainly seems to remember what you just said but of course it's a

trick it's because every time you press enter in chat gpt the whole contents of the conversation so

far is sent to chat gpt and some people will say hang on isn't that unfair does that mean that

we're having to pay for all the tokens or the input tokens every time that we call an LLM and

the answer is well well yes but that is what you want you want to generate tokens and you want to

do the compute you want to calculate based on everything that's been said so far so that the

the output can be something intelligent and reasonable and it hasn't apparently forgotten

everything that's said before so yes you pay for the input tokens and you want to you want to do

those calculations if you don't want to you can always just only send it the last message but

you're going to have a weird conversation if you do that and of course there's an added technicality

you probably know that tools like chat gpt have other forms of what they call memory which is

just meaning that it takes extra information from prior conversations and adds that into the system

prompt so that's all part of the input sequence as well it contains the overall system prompt

setting the scene and including any background memory and then the conversation history and that

gives the full illusion of memory and to make it concrete for you a few cells with apologies to

those that know it let's have this set of messages a list of dicks this is the way we provide it in

the open ai api and when it's actually calling the gpt model it just flattens this into one sequence

and we're going to say you're a helpful assistant that is the system part of the message and then

the user is hi my name is ed okay and now we're going to call openai.chat.completions.create

you remember that now the model we'll use is gpt 5.4 nano we'll pass in those messages and we will

print response.choices0.message.content off it goes to gpt hi ed nice to meet you how can i help you

today very nice okay now we're going to play with the system prompt let's change the system to you

are a snarky witty assistant hi my name is ed the same user message we've only changed the system

message so i update that by executing it recognize that you can you can update a cell which is

redefining the same variable and what matters is which cell you executed last so if you forgot to

execute this cell messages would still be set to the prior version that's an important trick with

using jupyter notebooks that's worth remembering okay now i run this cell and now we should see a

snarky answer hi ed nice to meet you try not to make the universe too awkward while you're here

i love snarky gpt all right now we've just told it twice my name is ed so it knows my name

so let's keep the same system prompt and ask it what's my name okay i execute the messages i

execute the call to gpt and it says i don't know your name this chat hasn't given it to me

tell me what you want me to call you and i'll use it it's interesting that it actually explicitly

refers to this chat so there you go you see it doesn't remember the previous call all it has is

this context that's it so it doesn't know that i'm ed so again apologies to those that know all

this stuff already but now we can have this longer set of messages roll system content you're a snarky

witty assistant the same system message roll user hi my name is ed roll assistant well hi there ed

it's nice to meet you roll user what's my name as if there's been this conversation all of that

and it doesn't need to be the actual response we got from gpt it doesn't know you could put

whatever you want in there uh i could say well hi there ed but i'd rather call you something else

and it will it will work uh but there we go uh we will now see what it says what's my name your name

yes i'm that kind of observant a nice snarky answer to a very predictable set of questions

all right and again if this is the first time you're exploring this then you should take a

moment to iterate on this try asking different questions try experimenting with this get a

really good sense for what you can do in the system prompt what you can do with the user assistant

user pattern the conversation history get a really good sense of it and if you do want to go even

deeper then of course look at the first week or two of the core track okay with that on to the

main plot let's get to tool calling

Okay, it's action stations. Here we are back in the lab in the main plot again.
Okay, it's action stations. Here we are back in the lab in the main plot again.

We've got our LinkedIn profile in a variable called LinkedIn. We've got a summary in variable

summary. We are now going to construct a system prompt and test it out before we add in a tool.

Here is the system prompt. In the system prompt, I'm using f strings again that you can look up if

you're not sure, and I'm constructing something. I'm using markdown headings to lay out the

different sections in my system prompt. I have a section called your role where I say you are

a digital twin running on a website chatting with visitors of the website. You represent the person

whose website you are on. You answer questions related to their career, background, skills,

and experience. Here are the details of the person you're representing, and I shove in the summary.

If asked, you clearly explain that you're an AI that is the digital twin of this person. So I give

it that framing. Then the next section, I say context. Here is a summary of the person's LinkedIn

profile so that you can answer questions. And there is the LinkedIn shoved in there. And then

finally, some rules. Engage with the user. Be professional and engaging as if talking to a

professional potential client, future employer. Avoid answering questions not related to the user's

career. This is like a double negative, which isn't great prompting. You should always try and

focus on positives, but I'm sure it's fine. If you want to tweak this, then that might be one

thing that could be improved right there. Always stay in character as the digital twin and the

person you're representing. Represent the person. Important, if you don't know the answer, say so.

Never make up an answer. If the user asks about something not in the context, say you don't know.

So that is our prompt. We're going to show you what that looks like visually, which is kind of

the way that the LLM will read it in. It's got these three headings, your role, context, and

rules, and you can read through it. And for sure, you should be tweaking this. Don't only change

this LinkedIn and summary, but also don't take my prompts here as gospel. This is a starting point.

This is for you to take as your canvas to iterate on it and make them better. But that is the prompt

that I'm going with for now. Let's give it a quick try. So I'm putting together messages. It's a list

of dictionaries. First of all, the system prompt, which is just exactly the system prompt right here,

and then a user message. Hi, please tell me about yourself. Okay, let's give that a try. We will call

openingai.chat.completions.create. We're going to use GPT 5.4 mini. You can use nano if you'd

rather spend even less, but this is very cheap anyway. And there you go. Hi, I'm Ed Donner's AI

digital twin. I can speak on behalf, blah, blah, blah, blah, blah. And there we have, it's clearly

understood that system prompt. We're able to interact. So I'm now going to package this call

to an LLM into one function that I'm going to call chat, which is quite a common function name for

this particular function. And this function is going to be one that takes two arguments, message

and history. Message will just be the text that a user wants to ask the LLM. And history will be

the list of dictionaries that represents the whole conversation so far in OpenAI format,

the list of dictionaries. And what we'll do is we will construct a new messages object that will

start with our system prompt, the one we just defined, add in this history, and then add in

the latest message from the user. So after we've done this, what will be in messages is going to

look a bit like, if I score all the way back up again, it's going to look a bit like this whole

list right here, this thing here. It's going to have a system prompt, a user message, and then

potentially an assistant response, and then another user message with the final message to the user.

That's what we just packaged up and put into that variable messages. That is what we put right here.

And now that we've done that, we simply call response is OpenAI.chat.completions.create,

and we pass in those messages. I've switched this to GPT 5.4 nano at this point. Maybe I'll put this

to mini for now. You can feel free to experiment with different models there, whatever you'd rather

do. And then I return response.choices0.message.content. So that's now in this function

called chat. And every time you call chat with a message and history, you'll get back that answer.

So for example, I can add in some code, and I can now call chat. I can call chat with a message,

please summarize who you are and have no history. And if I run this, then it's going to call this

function you see right here. It's packaging up the messages. It's calling OpenAI. And you're

getting I'm Ed Donner, or more precisely, I'm an AI digital twin of Ed Donner. That is working.

The chat function is perfect. And I make a little note here that if you're not using OpenAI models,

it's possible later you'll get some error running this when it complains when it's calling the

model that there's something else in messages that it's not expecting. If you see that,

this is the line of code. Take this line of code right here and just insert that at the top of the

chat function right there. And you might find, look out for that error message, because if you're

going to use something, I think Grok with a Q does this. If you see that error, you might get that in

other examples later when we have chat functions. And always using this line, it's just a nice way

of scrubbing that history object so that it only contains role and content and nothing else. So

it's a good trick. Okay, now it's time to bring up a user interface. We're going to bring up a

chat screen to allow us to do this interactively with the UI. And we're going to use the fabulous

Gradio package for this. And of course, I cover Gradio a lot on my CoreTrack course. You're fed up

with me mentioning that, I'm sure. But otherwise, just go along with it. You can pick it up as we

go. Gradio is so easy to use. With Gradio, you can tell it, I want a chat interface. I want you to

make a chat interface. And all you have to do is tell it the callback function, chat, which has

a method signature like this. And as long as you've written one like this, it can just use that

as its way of calling. Whenever in the user interface, the user enters in a message,

it's going to call back to this function to figure out what the AI should reply, to figure out what

should appear in the chat. So that's what we're going to do now. And just before that, I just want

to show you how Gradio works in case you are new to it. And apologies, those that have seen me do

this a thousand times. But I'm going to change this chat function. So instead of calling an AI,

it just returns bananas every time. That's all it's going to do, the string bananas. That's the

new chat function. Now I'm going to call this Gradio function to spring up a user interface.

Up it comes. This is our Gradio user interface. It has like a chat box here and a chat conversation

here. And I'm going to say, hi there. And Gradio calls that callback. And what comes back is

bananas, the result of running the chat function. And I'm going to say, nice to meet you. Meet you.

And I send that and it says bananas because the chat function just returns bananas. All

Gradio does is brings up this interface and repeatedly calls the chat function. And the

first parameter it sends is the contents of this box. And the second parameter is all of this box,

the whole of the history in open AI format. And that's all Gradio does. It's a UI layer

calling the chat callback. Actually, I can't resist one more example. We'll go back here.

Don't hate me for this. But if we just go back to the chat function and instead of return bananas,

we can return you said plus message. Look at this. So have a think about what it's going to do.

If we do that, we have to remember to execute that again so the chat function is redefined.

And then we come back here and run this again. Bring up a new interface. And I say,

hi there. And it says, you said, hi there. And I'll say, OK. I'll say bananas. And it says,

you said bananas. There we go. If we go back again here, you'll see that what's going on

is that this chat interface is calling this chat function. It's passing in the message.

It's passing in the history in that widget. And I'm just returning you said, whatever the message

is. And so that's what displays as the response from the AI. And now I remove this. And we have

a proper chat function. It's not going to return bananas. It's not going to say you said whatever.

But it's, in fact, going to call openai.chat.completions.create. It's going to call

our model. And we'll see the results. All right. Here we go. Remember to execute the cell that

defines the chat function after you change it. And then run this to bring up the new interface.

And say, hi there. And we're hoping for no bananas. And no, you said hi there. But we're

hoping for this. Hi there. Great to meet you. I'm Ed Donner's AI digital twin. Very nice. OK.

What should we say? Tell me about a challenge you faced in your career. Very sort of interviewee

kind of question. Let's see how it handles it. Let's see what it does. Well, it looks like it's

very detailed. But I'm sure it's great. JP Morgan stuff and building Nebula and finding and scaling

untapped. It's given quite a long answer there. But certainly, it all looks pretty accurate from

a quick eyeball. But let's try it on something else. What food do you hate? Let's see how it

handles that. Almost all cheeses. There we go. It got it right. Now, you may say, oh, but we

tried to prompt it in the system prompt to only answer questions about experience. But we did also

prompt it about cheese as well in the system prompt. So we've given it some conflicting

information there. And that's why it's still quite happy to answer a question about my dislike of

cheese, because it was there in the prompt. So this is the kind of thing that requires

experimentation. If you want to strictly, strictly stop it from answering questions that aren't

career related, then you'll need to iterate. And that's a great thing to do. And actually, even

beyond iterating on prompts, there's something even better that you can do to really check that

it doesn't answer something that's not related to your work. And have a think about what that

might be. It's going to be the first exercise at the end of this lab to implement that. But it's

something that we have already met. That's another thing that you could do. Okay, but for now,

onwards, we are going to build our first ever tool. So we are going to start by writing some

Python code, some basic boilerplate Python code. And in the spirit of sometimes typing,

I am going to type this one myself. We're going to have a method, and it's going to be a function

that is going to be able to record that we've got access to somebody's email, that they want

us to get in touch with them later. So it's going to be a tool that's going to be called record

email tool. We don't need to have the word tool in the title, but we might as well. So we remember

what we're doing here. And it's going to take a single parameter called email, and it's going

to record it. So let's start by printing that we've received this tool called to record an email. I

will use cursor to help me. Otherwise, you'll watch me typing, and it'll be super frustrating.

All right. So what we're going to do now is we're going to write that to a file. I'm just going to

write this email to a local file. So I'm going to say with open, let's call it emails.txt,

emails.txt. And we'll open it in append mode A, which means it creates if it doesn't exist.

Otherwise, it adds to it. And we do that encoding is UTF-8 so that it works on PCs as well as Macs.

Otherwise, many of you will complain. And I'll press tab. We will then write to file that we got

that email, followed by an empty line and a new line, and then say the email was received. There

we go. I half wrote that function record email tool. So let me show you that that works. Let's

use that. Let's call record email tool for an email testee, testee.com. Let's see what happens

if I run that right now. I've done that, and bam, a new little file appeared there called emails.txt.

And it looks dark gray because I have it in gitignore, so you don't all check it into GitHub

by mistake. Click on there, and there you will see that test at testee.com is in there. If you

don't believe me, I'll come back here, and I'll run it a second time. And it says email received,

test at testee.com. And if I go back over here, it's in there twice. Our function works.

So we're about to call a tool. And we're going to do it in three simple steps. One, two, three.

And in fact, we'll be calling the tool after the second step. Then we'll be adding to it in the

third step. And I want to just forewarn you that what you're about to see is kind of hokey. It's

janky stuff. It's clunky. All these words, hokey, janky, clunky. You'll see what I mean when you

see it. Tool calling is just a bit of a hack. It's going to be about shoving something in a

system prompt. It's going to be about an if statement. At the end of the day, what comes

back from OpenAI, we're going to have to say, if OpenAI wants us to use a tool,

then we will call the tool. And then we will call it again, OpenAI again, with the results of the

tool. So it's just quite clunky. And the thing to keep in mind is that that's because an LLM is

just something that generates tokens. And this whole tool calling thing is the kind of plumbing

that we put around that, the scaffolding around prompting that allows us to give this appearance

that the LLM is able to decide to call a tool. But at the end of the day, it's prompts, JSON,

and if statements. That's what makes tool calling. Prompts, JSON, and if. That's it.

And you will see that for sure right now. So the first step is we need to be able to describe

to the model that we have a tool available to it, a bit like I did in that prompt to chatGBT.

But the way that you do that is by describing it with some JSON. And the reason you use JSON

is because in all of its training data, it's given this kind of JSON format, a JSON schema

that describes this function that it can call. And so it's expecting it in that format. And this

is what it looks like. It's just something to see and get used to. It's kind of hokey. We're

going to see it a couple more times tomorrow. So this is record email tool JSON. It's a blob

of JSON. It has the name, which is we're calling it record email tool, a description. This is used

in the prompts to tell GPT when should it use this tool. Use this tool to record that a user

provided their email address. Now, you don't need to say use this tool to. You could just have it be

record that a user. You could be as shorter for sure. Sometimes it's helpful to be really explicit

about it. You can experiment. But these days, often because models have been trained so well,

people just give it, keep it very short and concise. And then parameters is telling the LLM

what does it need to pass back. Just like I had get ticket price Paris in my artificial example

before, we say that there's parameters. There's one parameter and it's called email and it's type

string. And the description of that parameter is it's the email address of this user and it's

required. And that's it. There's nothing else. That is a blob of JSON that describes the function

that we want GPT to be able to call. Okay. And I'm taking that JSON and I'm wrapping it in a list

that has type function. The function is record email tool JSON. And just to show you what I mean,

if I just print this out, you will see that there it is. It is this block of JSON. And, you know,

it's worth looking at this, worth getting a sense of how this looks. We'll do two more examples

tomorrow. It's janky. It's a way of describing the function to be called. I'm calling it a JSON blob,

but people will notice that this is really Python lists of dictionaries, but it can be

converted into JSON. We'll get sent to the model is just raw JSON strings that looks like this.

So, it's something to get familiar with. But keep in mind, the point of agent frameworks,

or at least a point of them, is that they have helper code that creates this kind of JSON for us.

Because you can just show it a function and it can figure out how to create JSON like this.

And so, whilst we will be hand cranking this JSON, this janky stuff the next couple of days,

in the future when we're using frameworks, we'll never have to do it again. But at least you're

going to know behind the scenes exactly what the framework's doing. All it's doing is constructing

JSON like this to go along with any functions that we want to use as tools.

And at last, everybody, the rubber meets the road.

We're going to implement our tool.

We're going to rewrite the chat function to something new. It's clunky.

It's going to look clunky.

It's like seeing how a fine recipe of some delicacy

is actually made up of basic ingredients, probably

lots of salt, and quite ordinary ingredients.

It's basically a big old if statement and JSON.

That's how they work.

And there's going to be a lot of code here.

And if you're thinking, do I need to remember this?

Do I need to be able to create this myself?

Keep in mind, as I say, in the future,

agent frameworks take care of this sort of hacky stuff for us.

They abstract over it, so you don't

need to worry about too much of these if statements and JSON.

But it's good to know how it actually works.

So focus on understanding what I'm doing.

Don't necessarily memorize anything at all.

You don't need to remember this.

OK, so I'm going to show you we've

got a long chat function here.

It's bigger than the last one.

And I'm going to quickly explain it to you now.

These first two lines should look very familiar to you.

The first one is just taking the message and history that

got passed in and turning it into a list of dictionaries.

We know it well.

And this line is calling openai.chat.completions.create

with the model, with the messages.

But ooh, look at this.

There's another thing being passed in.

We are passing in the tools, the tools which

is basically the JSON.

Well, to be pedantic, it's the list of dictionaries,

Python lists and dictionaries.

But it gets converted into a JSON string.

That's what gets sent to the LLM.

And it gets shoved in the system prompt to the LLM.

So that is where we are passing in the tools.

We're telling GPT 5.4 mini, hey, you

have at your disposal some tools that you

can use should you wish.

So we then call it.

We get back a response.

And now we have a big old if statement.

We normally just go straight for response.choices0.message.content.

But we're not doing that.

We're saying if response.choices0.finishReason

is tool calls, that means that the LLM has responded

specifically that it needs to call a tool.

And it's a special response that it gives.

In that case, we need to call a tool.

And we are then going to get the message

in response.choices0.message.

We normally get .content to get the contents of the message.

But we're going to get something different, not .content,

but .tool calls, which is any tools that it wants to use.

And look, we're going to be a bit hacky here.

And we're just going to assume it's only using one tool.

So we're just going to take the first tool out of this

and shove that into a variable tool call, which

is, of course, a little bit of a hack, but we'll fix that later.

All right, and now we're going to look into that tool call.

It has something called function.arguments,

which we are just going to assume

that it's going to populate that email argument.

In theory, we should parse this.

We should see what it wants to tell us about.

But we know that the only tool we're giving it

has only one argument.

And that argument is email.

And so we know that's going to come back.

So again, this is a bit hacky.

We're just assuming that GPT is responding and populating

email, just like the get ticket price was populating Paris.

We're just assuming that it's there.

We're grabbing that and putting it in this variable email.

And then look at that.

We're just calling the function that I wrote a moment ago.

We're just manually calling this function.

It's our code calling the function, not GPT.

We're calling it.

And then we just put in our messages object the message

that we got back from GPT.

We put in the messages object the response coming back from the tool.

And then we call openai.chat.completions.create a second time.

But now we're passing in the messages

that includes its request to call a tool

and the results of calling the tool.

And if you are only half following that,

don't worry, because we're going to do it a couple more times.

So you'll have time for it to ruminate until it's completely clear to you.

One other thing I'll mention that's a bit fiddly

is that when we put the response, the result of calling

the tool in our messages object, we also

have to put in a tool call ID, which

is how we're telling GPT that this result, which I'm just

putting in email recorded, I'm just putting in a fixed string,

that result is associated with this message that it sent us.

It's kind of hokey and hacky.

If in doubt, if you're not sure what's going on here, print.

Add in some print statements to print the messages

at each point, and you'll see exactly what's going on.

I very much encourage that kind of exploration.

That's why Jupyter Notebooks, these notebooks,

are so powerful, because you can just dig in,

add print statements, get yourself familiar with what's

really going on.

But anyway, enough talk.

It's time to give this a try.

OK, so here we go.

I'm running this chat interface. Here it comes.

I'm going to say hi there.

There, hi there.

Hi there, great to meet you.

OK, tell me about yourself. Something. Good stuff.

I'd like to get in touch.

In touch, I'm at a at test.com.

I'll say my email is.

Make it clearer.

My email is a at test.com. There we go.

It's doing its thing.

Let's see what happens.

There's a small pause.

Thanks, I've recorded your email address, it says.

Now let's come back to this screen.

If we look here, we'll see that it's printed,

tool call to record an email.

That was a print statement in that function I wrote, a at test.com.

But the big test is going to be for us to look in here.

Is a at test.com in there? Look at that.

We have test.testy.com twice, and then we have a at test.com. It worked.

GPT just called a tool.

But as now you see in a slightly unsavory way,

it all came down to some JSON, an if statement,

and some janky code.

Now there was a lot about this that was a bit hacky.

And one of the things in particular

is all of these assumptions about the fact

that it could only call one tool.

We just assumed that that first tool was what it was.

And also that it could only call a tool once.

We won't support it.

Then if it decides to call a tool a second time,

we will just completely ignore that

and just put the results in the chat.

So that's a bit hacky.

And let me show you that in practice.

Let me show you it going wrong.

Let's just start a fresh chat. Here it is.

It's just come up for me.

So I'm going to come here, and I'm going to say hi there.

And I'm going to say, please, please record my three email

addresses, x at test.com, y at test.com,

and z at test.com, or zed for the Brits. And send that.

And what we're going to see is an error, a big red error. Ouch.

Ouch, you don't like to see that.

At Gradio, it's sometimes a little bit hard

to work with when you get errors like this.

It seems like, OK, error.

Computer said error.

You come back here, and you can see the actual stack traces in here.

And it doesn't take too much time

to realize that it's getting confused,

because it's not seeing answers to all of its tool calls,

and it's kind of stuck.

It's because we assumed that there would only

be one tool call.

And because I gave three email addresses,

GPT tried to make three tool calls,

and we didn't support that.

So how would we support that?

What change do we need to make?

Well, basically, right now, we've

got an LLM equipped with tools.

What we need to do is put an LLM with tools in a loop.

Did you see that coming?

So it's time to have a loop LLM with tools in a loop

to achieve a goal, recording emails or whatever,

chatting with the user.

That would be an agent.

So there's just two changes we're going to make.

Here is the same chat function with two tiny changes.

See if you can spot them.

It's actually two loops, because there's

a couple of different ways that GPT might do it.

One of them is a bit of a technicality.

But as I say, last time, the tool calls was coming back,

and I was just taking the first element.

But actually, I should iterate through it

with four tool call in tool calls.

And then each one, I should process

so that if GPT decides to call multiple tools in one response,

then I can handle that properly.

So that's a for loop.

But when we talk about agent loops,

we're not usually referring to this inner for loop, which

is more of a technicality to handle

multiple concurrent tool calls.

That is part of it.

But there's a bigger loop here.

And the bigger loop, of course, is just a one-word change. Do you see it? Do you see it?

You probably see my comment about it.

But if you didn't see it, all I did

is if you look at this previous version,

the previous implementation, the previous version

had if the response is requesting to call tools,

then it does this.

That was the previous version.

I have changed that if statement.

Instead of an if, it is a while.

So every time the LLM says, I want to call tools,

it will keep looping.

It's going to keep doing this again and again and again

until the LLM is done, and it's got no more tools to call,

and it's finished.

It's got a final output.

And then that is what we return.

That is the trick, just turning the if into a while. That's it.

And then this is like a performance thing,

but it might actually call multiple at once.

So there's like a four inside here.

But that's kind of the same as this.

So that is a loop.

It's a while loop.

And now we're going to try out our while loop, which

is an LLM with tools in a loop.

And we're going to give it a goal to achieve.

And let's just take a quick peek at emails.txt

and remind ourselves it's got test.txt, a.txt, and x.txt

because it got stuck after putting that one in there.

Now we are coming back here.

We are defining our new chat with the while loop,

and we are bringing up a new interface. Here it is.

Make it nice and big for you, and I'll say hi there.

All right, and now I'm going to say,

please record my email addresses d.test.com,

e.test.com, and f.test.com, giving it

some English to pause there.

I've put that in.

We're hoping it was very quick.

It says it's recorded all three email addresses.

There was no error this time.

Let's go back here.

We can see that three tools were called.

We gave it a goal.

It ran in a loop.

It called tools three times for d, e, and f.

And finally, we will turn over to emails.txt.

Deep breath to see whether or not, drum roll,

do we have d, e, and f.

We do have d, e, and f.

We were able to give a goal to an LLM

and have it run in a loop with tools. And guess what?

It achieved the goal. OK, wonderful.

Wow, you've just done a couple of things.

You've implemented an AI assistant, which

has tools to add functionality.

But more importantly than that, you've

implemented an agent loop.

You hand-cranked it without an agent framework,

writing the JSON, writing the while loop and the for loop

to have it be running in a loop.

And that's fantastic.

Now you know how agent loops actually work,

and you've rolled your own.

OK, some exercises for you. First exercise.

Now, I mentioned before, we might

want to be more careful about making sure

that it only answers questions that

pertain to your work experience.

What's one way of doing it?

You could iterate on the prompts more.

That would be a great idea.

Also, you could add a second LLM call, an LLM as a judge,

an evaluator pattern.

You could have a second LLM call to check

that the response is only pertaining to work matters.

And if not, then you can have a little loop

to go back and do it again.

That's a great pattern to put in place using the evaluator

optimizer pattern, a really good thing to do.

And then you're including some orchestration

along with this tool calling.

So that would be a really good activity.

And if you do that and you get it to work,

then please do make a PR to Community Contributions.

I would love to see it and celebrate it on LinkedIn,

because then you are then combining many aspects of agentic AI.

And it's worth mentioning, if you do that,

you've basically built what they call a guardrail.

You've built something that's checking the outputs

and not accepting outputs if they don't meet some criteria

that you're specifying.

And that's an example of a guardrail.

People use guardrails a lot to be all,

sounds all sorts of highfalutin stuff

they're doing with guardrails, but that's all it is.

It's just a check of the output,

often by making another LLM call.

So that would be a great thing to build.

And the second exercise is to then apply this

to your business, to your day job.

An AI assistant with tools is something

that's immediately applicable

to almost any business vertical.

Think of something that you do

that you'd like to get help with from an AI assistant,

or perhaps something that you could use with your customers,

could ask questions to,

and some tool to record email addresses,

if you just want to use the one we've got,

or a tool to provide extra information,

something like that.

You should be able to apply this to your business

and make impact.

And that's another thing that would be fabulous

to have in Community Contributions.

So I can't wait to see all of that.

And then I'll see you back in the slides for the wrap-up.

Wowza, we covered a lot today. Just to recap,

agentic AI is about putting scaffolding

around calls to LLMs.

It's sometimes called a harness, an agent harness,

but it's about figuring out

what's the best way to prompt this LLM?

How do we craft the inputs?

How can we interpret the outputs

to allow us to do things like use tools

and do it in a loop?

And by interpreting those tools,

have it appear to be autonomous.

That is what agentic AI is all about.

There are things called agent frameworks

that write some of this for you.

They're just helpers, they're utilities.

Instead of having to hand-crank JSON,

it will generate the JSON for you,

but they're not required.

You could do it all yourself manually every time

if you didn't mind writing lots of JSON,

as we did today.

That's what it would take

just to not use a framework at all.

Frameworks just make it quicker, faster, easier.

And this course is gonna be all about

using these tricks and techniques,

the way to craft inputs and interpret outputs

in order to deliver commercial value,

to apply this to commercial problems

using agentic frameworks to help us

so that you can build these autonomous AI agents.

That's what this course is all about.

And that concludes a mammoth day,

week one, day four, really huge day.

I hope if you knew this already,

then it still helps to fill in some gaps.

If you didn't know this,

I hope it's been a bit of an aha moment

to see that it's all pretty mundane behind the scenes.

It's janky JSON, it's if statements

that string this whole thing together

and allow us to run agent loops.

This concludes four orange days, four days of theory,

and it's all been leading up to our first project,

which you know by now is a digital twin

that we're gonna deploy out there on the internet.

That's all gonna happen tomorrow on week one, day five,

epic conclusion of week one.

And with that, with that,

you are 13% of the way through this course.

You're all ready for the first project.

See you tomorrow.
