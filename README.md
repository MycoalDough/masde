# MASDE — Multi-Agent Social Deduction Engine

A **multi-agent AI simulation** that places autonomous language-model agents inside an *Among Us*-inspired social deduction environment.

Built with **Unity/C# and Python**, MASDE explores how multiple AI agents behave when they must reason under partial information, communicate with one another, form beliefs, accuse other agents, deceive opponents, and collectively make decisions through voting.

Rather than building a traditional chatbot where one model responds to one user, MASDE creates an environment where **multiple autonomous agents continuously observe, reason, act, and interact with each other**.

---

## Overview

Social deduction games create an unusual AI problem.

Agents do not have access to the full state of the world.

Instead, each agent must make decisions based on:

- what it personally observes
- what other agents claim happened
- previous interactions
- suspicious behavior
- incomplete or conflicting information
- its hidden role and objectives

This creates a setting where successful behavior requires more than basic pathfinding or scripted decision trees.

Agents must reason about questions such as:

```text
Who was near the eliminated player?

Does another agent's story contradict what I observed?

Who has behaved suspiciously across multiple rounds?

Should I reveal what I know?

Should I lie?

Who should I vote for?
```

MASDE provides a sandbox for experimenting with these kinds of **multi-agent reasoning and emergent social behaviors**.

---

# Core Gameplay

The Unity simulation implements an *Among Us*-style game loop with autonomous agents capable of:

- navigating the environment
- completing game actions
- killing other agents
- using vents
- triggering sabotages
- discovering events
- calling meetings
- discussing suspicions
- making accusations
- defending themselves
- voting
- reacting to other agents' statements

Each AI participant operates from its own perspective rather than receiving unrestricted global information.

This creates a partially observable environment where agents have to construct their own understanding of what is happening.

---

# High-Level Architecture

```text
                    ┌──────────────────────────────┐
                    │          Unity / C#          │
                    │                              │
                    │      Game Simulation         │
                    │                              │
                    │  • World state               │
                    │  • Player movement           │
                    │  • Tasks / interactions      │
                    │  • Kills / vents             │
                    │  • Meetings                  │
                    │  • Voting                    │
                    │  • Agent observations        │
                    └──────────────┬───────────────┘
                                   │
                             JSON Messages
                                   │
                    ┌──────────────▼───────────────┐
                    │            Python            │
                    │                              │
                    │    Multi-Agent AI System     │
                    │                              │
                    │  • Observation processing    │
                    │  • Agent context             │
                    │  • Decision generation       │
                    │  • Dialogue generation       │
                    │  • Voting decisions          │
                    └──────────────┬───────────────┘
                                   │
                              Decisions
                                   │
                                   ▼
                              Back to Unity
```

Unity acts as the simulation layer while Python acts as the reasoning layer.

The two systems communicate through an event-driven JSON protocol.

---

# Agent Loop

Each autonomous agent follows a cycle similar to:

```text
Observe environment
        ↓
Receive relevant events
        ↓
Update agent context
        ↓
Reason about current situation
        ↓
Choose action / dialogue
        ↓
Return structured response
        ↓
Unity executes decision
        ↓
Environment changes
        ↓
Repeat
```

This allows each agent to operate continuously rather than only responding during scripted dialogue sequences.

---

# Partial Observability

One of the central ideas behind MASDE is that agents should **not know everything**.

If every agent received the complete game state, social deduction would become trivial:

```text
Agent sees killer ID
        ↓
Agent reports killer
        ↓
Game solved
```

Instead, information is restricted based on what each agent could reasonably know.

For example, an agent may know:

```text
I saw Player 3 enter Electrical.
I later saw Player 5 leave Electrical.
A body was reported there.
Player 3 claims they were in MedBay.
```

But the agent is not simply told:

```text
Player 3 is the impostor.
```

The AI therefore has to reason from evidence rather than querying a hidden answer.

---

# Multi-Agent Reasoning

MASDE is designed around interactions between **multiple independent AI agents**.

This introduces behavior that does not appear in ordinary single-agent LLM applications.

Agents can:

- disagree about events
- interpret the same evidence differently
- influence each other's beliefs
- defend themselves when accused
- intentionally mislead other players
- build or lose trust
- coordinate implicitly
- change their decisions based on group discussion

The interesting behavior comes from the interaction between agents rather than from any one model acting in isolation.

---

# Meetings and Dialogue

When a meeting occurs, agents transition from physical gameplay into a social reasoning phase.

Each participant can generate dialogue based on:

- its observations
- prior events
- previous statements
- current suspicions
- accusations made by others
- its own role and objectives

Conceptually:

```text
Game Events
    +
Agent Memory
    +
Current Discussion
    +
Hidden Objective
        ↓
   LLM Reasoning
        ↓
Dialogue + Vote
```

This creates discussions where information can propagate between agents and alter their decisions.

---

# Deception

Social deduction creates an especially interesting challenge for AI because truthful behavior is not always optimal.

An innocent agent generally wants to provide accurate information.

An adversarial agent may instead need to:

- fabricate an alibi
- redirect suspicion
- selectively reveal information
- accuse another player
- agree with an existing theory
- exploit uncertainty within the group

This turns language generation into part of the agent's **decision-making strategy**, rather than treating dialogue as purely cosmetic output.

---

# Unity ↔ Python Communication

The simulation and AI reasoning system are separated into different runtimes.

```text
Unity / C#
    ↕
Newline-delimited JSON
    ↕
Python
```

Unity sends structured observations and gameplay events to the Python process.

Python evaluates those events and returns structured commands such as agent actions, dialogue, or voting decisions.

The protocol supports both individual messages and batched responses, allowing multiple
