---
layout: post
title: "Two Months with Hermes Agent: What I Built, What Broke, What Stuck, and What worked!"
tags: ai gpu rpi agents
---

<div style="text-align:center;">
<img align="center" src="/images/hermes.png" height="90%" width="90%"/>
</div>
<br/>

There's a particular kind of satisfaction that comes from building something in your home lab that you actually use every single day. Not a proof-of-concept that runs once and sits dormant on a shelf. Not a Docker container you fire up to show someone at a meetup and then forget. Something that genuinely changes how you operate — that has quietly become load-bearing infrastructure in your life before you even noticed it happened. That's what the last two months with Hermes has been for me.

I want to write this down while it's still fresh, because the evolution from "interesting GitHub repo I bookmarked" to "thing I WhatsApp at 11pm from the couch while my kid is asleep" happened faster than I expected, and the path there is worth documenting honestly — including the dead ends, the economics, and the weirdly personal feeling of watching a piece of software slowly learn how you think.

## How It Started: The Promise of Persistent, Long-Running Agents

I came to Hermes the way most people in the home lab community find tools — through a combination of late-night Reddit threads and a specific frustration I couldn't shake. I had been experimenting with various LLM wrappers and chat interfaces for months, and the pattern was always the same: great for one-off questions, useless for anything that required context to persist, completely hopeless for tasks that needed to happen while I was asleep or at work. Even I ended up creating my own using flutter app and spring boot backend, named it "Ghargadi" 😬.

The pitch for Hermes was different. When I first read through its documentation, what jumped out wasn't the model support or the tool integrations — it was the framing around *long-running tasks* and *cron-like repetitive scheduling*. Those two phrases hit differently for someone with a home lab full of Raspberry Pis and a mental list of automation tasks that never quite got built. Here, supposedly, was an agent framework that wasn't trying to be a fancy chatbot. It was trying to be something closer to a local employee with a persistent memory, an alarm clock, and root access to your terminal.

I carry an AI Champion role within the division, so I spend a lot of professional bandwidth thinking about where AI actually delivers leverage versus where it just creates impressive demos. Hermes looked like the former. I decided to give it a serious two-month trial on my home infrastructure.

## Meet Blackboy and Redboy: The Hardware Foundation

Before I get into the software, I need to introduce the hardware, because the choice of host matters enormously to how Hermes actually behaves in production.

I have a small cluster of Raspberry Pi 5 units running in my home lab, each connected to my router via a 1Gbps LAN cable. I named two of my favourites after their case colours — Blackboy and Redboy — and yes, I am completely aware that this is the kind of naming convention that makes professional sysadmins quietly judge you, and I have no regrets. Blackboy(8GB), in particular, has become the primary host for the Hermes deployment, and the reason I chose the Pi 5 over other options is worth spelling out.

The Pi 5 is a low-power, always-on single-board computer. It draws somewhere between five and twelve watts under typical load. It sits on the shelf above my desk and runs continuously, twenty-four hours a day, seven days a week, without meaningfully affecting my electricity bill in a way I'd notice. Compare that to my primary workstation — which carries an RTX 5070 Ti, a Ryzen 9 9950X, and the full thermal footprint you'd expect from that combination — and you start to see the architectural logic immediately. Running an agent that needs to be available at 3am, that needs to fire scheduled tasks at 6am, that needs to respond to a WhatsApp message while I'm sitting in a meeting in Pune — that's not a workstation job. That's a Pi job.

The Pi handles local file operations, network scripting, environment control, and all the persistent state management Hermes needs. It's the local nervous system. What it doesn't do — and shouldn't do — is run frontier-class language models. That ceiling is real, and hitting it was the first thing I had to work through.

## The Model Story: From Local GPUs to OpenRouter

When I first set up Hermes, my instinct was to run everything locally. I have the hardware for it. The RTX 5070 Ti handles inference beautifully, and I had several capable open-source models already pulling their weight: Gemma 4 for its surprisingly strong reasoning, GPT-OSS 20B for general-purpose tasks, and Qwen3 35B A3B for the heavier analytical work. Locally, all of them ran well. The quality was there.

The problem was the economics and the ergonomics. Running Hermes as a truly always-on agent — the kind that could fire a scheduled research task at 6am without my intervention — meant keeping my full workstation powered up overnight. With a 5070 Ti and a 9950X idling, that's not a minor ask. It's loud, it's warm, and it's wasteful when most of that silicon is sitting completely idle between scheduled tasks. More fundamentally, it defeats the entire point of hosting the agent on a Pi 5 in the first place.

The answer was OpenRouter, and specifically the Nemetron Super model. I'll be honest: I had some initial resistance to the idea of routing everything through an external API. There's a philosophical purity to fully local AI that appeals to anyone who's spent time in the home lab space. But once I ran Nemetron Super for a week and compared it to what I was getting locally, the resistance dissolved. The model is genuinely strong — not flashy, but dependably intelligent and remarkably efficient for continuous 24/7 use. The cost-to-quality ratio for long-running agent workloads is exactly right. And because the Pi is handling everything except the actual inference, my workstation can sleep, my electricity bill stays sane, and Hermes keeps running.

The architecture that emerged feels clean and right: the Pi 5 is the always-on local body, OpenRouter is the rented brain, and the two work together without either one needing to be something it's not. On tasks where I want maximum reasoning power — something complex enough to justify it — I can route through Claude 3.5 Sonnet or a larger model on OpenRouter. For the daily administrative drumbeat, Nemetron Super handles everything without breaking a sweat.

## The WhatsApp Gateway: When Your Agent Becomes Mobile

The moment Hermes truly became daily infrastructure for me was when I added the WhatsApp gateway.

The multi-platform messaging integration is one of Hermes' genuinely differentiating features, and I'd read about it before deployment, but I underestimated how much it would change the interaction pattern. Before the WhatsApp setup, using Hermes meant being at a terminal or at least near a device with a proper interface. After it, the agent became ambient. It became something I could reach from anywhere, in the same way I'd reach a colleague over chat.

The practical flow is this: I text my agent a task from my phone — while commuting, while waiting somewhere, at night after everyone's gone to bed — and Hermes picks it up, executes it on Blackboy, and sends me back a result. No SSH session required. No port forwarding exposed to the open internet. No context-switching to a laptop. It's clean, secure, and surprisingly natural once you get used to it. There's something slightly surreal about sending a WhatsApp message that says "check if the Docker containers on the Pi are healthy and tell me the CPU temp" and getting back a formatted status report thirty seconds later, but that's the surreal that becomes normal fast.


## The Features That Actually Matter in Daily Use

I've read a lot of Hermes documentation and a lot of blog posts that list its features as bullet points. I want to talk about them differently — as things I've actually felt the value of, in the context of real usage.

The persistent memory system is the one that surprised me most. Hermes saves its conversation and task history locally in an SQLite database using Full-Text Search with FTS5, combined with LLM-powered summarization that distils long sessions into compact, retrievable context. In practice, this means the agent accumulates a working model of how I think, what projects I care about, what I've asked before, and what worked. After two months, interactions have a different quality than they did in week one. There's less ramp-up, fewer clarifying questions, a sense that the agent has genuine context about my environment and preferences. This isn't magic — it's well-designed memory management — but the experience of it does feel qualitatively different from any stateless chat interface.

The autonomous skill creation feature is harder to describe without it sounding like marketing copy, so I'll just describe it concretely. If I ask Hermes to perform the same complex multi-step sequence of tasks repeatedly — say, pulling logs from multiple Pi nodes, formatting them, and generating a summary — it can write a new Python or Bash function to encapsulate that workflow and add it to its own codebase. It literally extends itself to match my patterns. I've watched this happen a handful of times now and it still feels like watching something unusual. The agent isn't just executing tasks; it's learning the shape of the work I keep asking it to do and building reusable tooling around that shape.

The 70-plus built-in tools cover most things I need without any configuration: file manipulation, web browsing, image processing, terminal execution. Where the built-in tools don't reach, the Model Context Protocol integration lets me bridge Hermes to external systems without touching core code. I've used this to give it structured access to local logs and Pi-hole query data, which I'll come back to shortly.

The subagents and parallel delegation capability matters more than it sounds. Some of the research tasks I've scheduled for Hermes involve pulling information from multiple sources simultaneously. Instead of doing these sequentially — browse source one, come back, browse source two — it can spawn isolated subagent processes that handle different legs of the research in parallel. The throughput difference on complex research tasks is significant, and it changes what you're willing to ask the agent to do.


## Four Workflows That Have Genuinely Changed My Routine

Let me be specific about what Hermes actually does for me every week, because general capability descriptions only go so far.

**The ChatOps Administrator.** This is the workflow I use most frequently. Blackboy runs continuously, Hermes is configured with my WhatsApp number as the gateway, and I can interact with my home server infrastructure from anywhere in the world using natural language. "Check if the Docker containers are running, verify disk usage on the data volume, and let me know if anything needs attention" — that's a real message I've sent, and it returns a real operational summary. Before this existed, any infrastructure check required either SSH access or physical presence. Now it requires a text message. The security model here is also worth noting: nothing is exposed via open SSH ports. The Pi initiates outbound connections; the attack surface stays small.

**The Morning Research Digest.** This one runs on a schedule, which is the feature that first attracted me to Hermes. I have a cron-triggered task configured to fire at 6am. It instructs Hermes to spin up parallel subagents, browse specific sources covering topics I track — AI research developments, relevant financial technology news, library releases in my stack — synthesize the findings using Nemetron Super, and deliver a formatted briefing to my WhatsApp before I wake up. Most mornings, I have a structured digest sitting in my phone by the time I look at it. The quality varies depending on what's been published, but even a mediocre digest is better than nothing, and the good ones are genuinely valuable. This was the first scheduled workflow I set up and the one that made me understand what "always-on agent" actually means in practice.

**The Development Sandbox.** I use Hermes as a local coding collaborator for the smaller tools and scripts I build for my home lab. I point it at a working directory, and because it can execute terminal commands, read codebases, maintain context across long sessions, and run local tests on the Pi, the feedback loop is tight. I've used it to review scripts, catch logic errors, write unit tests, and verify that they pass before anything gets committed. I've also given it the authority to stage and push clean builds to GitHub automatically, which removes a small but real amount of friction from the process of maintaining my personal tooling.

**The Network Guard and Log Auditor.** I run Pi-hole on a separate Pi node, and I've been experimenting with Home Assistant and Plex across a couple of other devices including a Pi Zero 2W. Through MCP, I've given Hermes read-only access to the system logs and Pi-hole query logs from these nodes. I've configured it to look for anomalies — unusual spikes in outbound traffic, patterns of repeated failed authentication attempts, DNS query behaviour that doesn't look right — and to alert me only when something actually warrants attention, not on every log line. The cross-session memory makes this particularly useful: it's building a baseline understanding of what normal looks like on my network over time, which means its anomaly detection improves as the weeks pass. I haven't caught anything genuinely alarming yet, but I've had several false-positive alerts that were still educational, and I've learned things about my own network traffic I didn't previously know.


## The Storage Question I Keep Postponing

There's one infrastructure decision I've been intentionally deferring, and I should be honest about it. Blackboy — the primary Hermes host — is still running from its SD card. I know I should upgrade to a 1TB SSD. The performance gain for SQLite operations would be meaningful given how heavily Hermes reads and writes to its memory database, and SD cards are not the right long-term medium for a write-heavy always-on system.

The problem is that every time I look at the current SSD market, I end up in a research spiral and don't buy anything. There's always a price movement pending, or a form factor question, or a compatibility concern I want to resolve first. It's a classic case of a decision that's easy in principle and somehow never gets made in practice. I'm aware this is a gap in my setup. Blackboy deserves better than an SD card, and at some point I'll stop delaying and sort it out.

## What Two Months Has Actually Taught Me

The honest summary of two months with Hermes is this: it's the first AI agent framework I've used that actually fits the shape of my life rather than asking my life to fit around it.

Most AI tooling optimises for the session — the conversation you're having right now, the task you're currently working on. Hermes optimises for continuity. It's designed to be left running, to accumulate context, to pick up where things left off, to act on your behalf when you're not watching. That's a fundamentally different design philosophy, and once you've lived inside it for a couple of months, going back to stateless chat interfaces feels like a step backwards.

The Pi 5 plus OpenRouter architecture turned out to be exactly right. Low-power always-on hardware for the local execution layer, cloud-resident intelligence for the heavy reasoning, WhatsApp as the human interface layer. The pieces fit together cleanly, and the running costs are genuinely manageable. I'm not burning £50 a month in electricity to keep a GPU warm. I'm spending a few pence a day on Pi power and a modest amount on API calls, and in return I have a capable, persistent agent that works while I sleep.

The persistent memory and autonomous skill creation are the two features that most change the long-term value trajectory. An agent that gets incrementally smarter about your specific workflows over time is categorically different from one that resets every session. After two months, I have an agent that has started to know me in some limited but genuinely useful sense — that knows which Pi node does what, that knows the shape of my morning digest preferences, that knows when I ask about infrastructure I mean my specific setup. That accumulated context has value that compounds, and I expect it to keep compounding as the months go on.

If you're running a home lab with a Raspberry Pi or two, spending time thinking about where AI actually fits into your personal infrastructure rather than just your professional one, and looking for something that rewards serious setup with serious utility — Hermes is worth the investment. Just do yourself a favour and get that SSD sorted before you deploy it. Unlike me.
