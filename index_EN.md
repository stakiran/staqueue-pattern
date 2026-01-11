# Staqueue Pattern
[Japanese](index.md) | [English](index_EN.md) | [GitHub](https://github.com/stakiran/staqueue-pattern/)

## Overview
### Staqueue
**Staqueue** refers to Stack and Queue. More specifically, it refers to information management using these two data structures.

With staqueues, you build the information-management structure you want using stacks and queues. For example, consider using two lanes: `queue1` as your everyday task list, and `stack1` for interruptions. This enables an operation like: "Normally, process `queue1` in FIFO order; when an interruption arrives, process `stack1` in LIFO order until it's empty." It's simple, but it covers both everyday tasks and interruptions.

### Principles
Staqueue comes with several principles. You could also call them assumptions. You must understand all of them.

- What "information management" means
    - Managing information of the individual, by the individual, for the individual
    - It is self-contained and is not intended to be shown to others or seen by others
- What "information" means
    - Everything is in scope: TODOs, tasks, ideas, things to read later, etc., but they are expressed **as text**. Also, each item must be tied to some kind of action
- What "management" means
    - Includes adding information, viewing, searching, processing (taking action), and moving to another staqueue
    - **Does not include editing operations**
    - **Processing and moving are not done at the same time**. When you take an item out, what you can do is either (1) process it and delete it, or (2) move it without processing it
    - Note that editing is only allowed when moving
- Because we're using stacks and queues, the following constraints apply
    - A stack is LIFO: items added later are taken out first
    - A queue is FIFO: items added earlier are taken out first
    - No random access (operating on an item at an arbitrary position). However, viewing and searching are allowed
    - **Once taken out, an item is deleted (you can re-add it)**

### Staqueue Patterns
A整理 of frequently used patterns.

### Format
```
(Write the overall structure of this pattern here)
```

- (Information assumed in particular)
    - If there is any specific assumption about the "information" handled in this pattern, write it here
- (Processing information)
    - Write which staqueue(s) you process
    - Processing means "taking an item out and taking action." Taken-out items are removed
    - **Do not mention when to process**. It could be ad hoc, periodic, reactive (e.g., when a notification arrives), etc., but that's out of scope here
- (Adding information)
    - Write how information is added to this structure
    - This is effectively adding from outside. In many cases, the higher-layer staqueue accepts it
- (Moving information)
    - Write when and from which staqueue to which staqueue information is moved
    - This is effectively internal movement. Often it moves toward lower layers, but the opposite can also happen

### Format Examples
```
stack1
queue1
queue2
```

The above consists of three lanes: one stack `stack1` and two queues `queue1`, `queue2`.

```
stack1 | queue1
         queue2
```

This also consists of three lanes, but each part separated by `|` is called a group. It's not a strict concept; it's just a way to organize staqueues that have similar roles. In this case, you can read it as likely assigning different roles to `stack1` and the `queueXX` lanes.

```
stack1 | queue1 | queue2
```

This also consists of the same three lanes, but this time `queue1` and `queue2` are in separate groups, implying they likely have different roles.

## ====
## (Basics)
These are fundamental patterns. To manage information using staqueues, you should have these down. The key points are also summarized here:

- Use queues instead of lists → FIFO-driven pattern
- Use a stack to handle high-urgency cases such as interruptions → Guard Stack pattern
- Usually operate multiple queues and sort items based on some viewpoint → Category pattern

### FIFO-Driven Pattern
```
queue-main
queue-temp1
queue-temp2
...
```

- (Information assumed in particular)
    - None
- (Processing information)
    - Process `queue-main`
- (Adding information)
    - Add to `queue-main`
- (Moving information)
    - If you want random access to `queue-main` or want to reorder items, use `queue-tempXX`

This is the most basic pattern. **Do not use lists such as a task list**; use a queue. In other words, you operate in FIFO order (tasks added earlier are taken out earlier) and do not do random access.

However, FIFO alone can be inconvenient, so you're allowed to use `queue-temp` as well. The number of temp queues is arbitrary, but fewer is better. The fewer you need, the more your operation fits the staqueue pattern.

As shown, **being driven by FIFO is the foundation of the foundation**.

### Guard Stack Pattern
```
stack1 | queue1
```
- (Information assumed in particular)
    - None
- (Processing information)
    - Prioritize processing `stack1`. Process until it becomes empty
    - When `stack1` is empty, process `queue1`
- (Adding information)
    - Normally add to `queue1`
    - When an interruption comes in, add to `stack1`
- (Moving information)
    - None

One of the most basic patterns. FIFO-driven operation alone cannot handle high-urgency items such as interruptions, so you introduce a stack.

For simplicity, the Guard Stack pattern itself uses only one queue, but of course you can also combine it with FIFO-driven operation.

### Category Pattern
```
queue-entrance | queue1
                 queue2
                 ...
```

- (Information assumed in particular)
    - None
- (Processing information)
    - Process `queueXX`
- (Adding information)
    - Add to `queue-entrance`
- (Moving information)
    - Move from `queue-entrance` to an appropriate `queueXX`. Also, try to keep `queue-entrance` empty

A frequently used pattern. In many cases, a single queue is not enough, so you use multiple queues. The question then becomes: on what basis do you split them? The Category pattern provides a straightforward way to solve this.

For how to create `queueXX`, see Appendix 1.

## (Patterns for Individuals)
As staqueue patterns assume, these are patterns that are self-contained within yourself.

### Later Pattern
```
queue-main | stack-later
```

- (Information assumed in particular)
    - Things that tend to become "do it later"
- (Processing information)
    - Process `queue-main`
- (Adding information)
    - Add to `queue-main`
- (Moving information)
    - Move items you want to do later from `queue-main` to `stack-later`
    - To work on later items, move them back from `stack-later` to `queue-main`

A pattern to realize "Later" (i.e., "do it later").

FIFO-driven operation uses multiple queues, but the Later pattern places a stack. That means "do it later" items are taken out starting from the most recently added. Since a pile can become bottomless, it makes you more likely to feel like you should process it sooner.

### Project Pattern
```
stack-inbox | queue-inbox | stack1 or queue1 or stack1+queue1
                            stack2 or queue2 or stack2+queue2
                            ...     ...
```

- (Information assumed in particular)
    - Information related to projects
- (Processing information)
    - Process `stack-inbox` with top priority. Process until it becomes empty
    - Process the project staqueue (one of `stackXX`, `queueXX`, or `stackXX+queueXX`) when you work on that activity
- (Adding information)
    - Add high-urgency items such as interruptions to `stack-inbox`
    - Normally add to `queue-inbox`
- (Moving information)
    - Move from `queue-inbox` to `project staqueueXX`

First, prepare a staqueue for each project, and place a single `queue-inbox` to distribute items into them. This resembles the Category pattern, but while the Category pattern uses only queues, the Project pattern can use three types: stacks, queues, and a stack+queue pair. See the difference below.

```
Category pattern:

    queue | queue
            queue
            ...

Project pattern:

    queue | stack
            queue
            stack + queue
            ...
```

And to handle interruptions, you also add the Guard Stack pattern. Specifically, you place `stack-inbox`.

We each have n "projects" and are likely switching between them one by one, deciding which project to work on at any given time. This pattern assigns one project staqueue to each project, adding Category-pattern-like sorting and Guard-Stack-like interruption protection.

A caveat: it cannot handle overly complex activities. As per-project staqueues, this pattern assumes only "stack only", "queue only", or "one stack and one queue." **Do not use staqueues more complex than these.**

### Deferral Pattern
```
(Group1)   (Group2)

queue1   | stack-immediately
           queue-1hour
           queue-3hour
           queue-day
           queue-tomorrorow
           queue-3day
           queue-next-week
```

- (Information assumed in particular)
    - None
- (Processing information)
    - Process the Group 2 staqueues
- (Adding information)
    - Add to `queue1`
- (Moving information)
    - Move from `queue1` to Group 2. At that time, judge how long it can be deferred and move it to an appropriate staqueue

A pattern to defer things in a unified way. First, catch items in `queue1`, then classify them based on the subjective judgment of "how far can this be deferred?"

If deferring is difficult, use `stack-immediately`. Otherwise use queues, but adjust the deferral durations as you like. In the example above, there are six variants: within 1 hour, within 3 hours, today, tomorrow, within 3 days, and "reconsider next week." You may also use multiple `stack-immediately` stacks.

Keep the number of staqueues in Group 2 to 7 or fewer. More than that makes decisions too cumbersome and the system becomes hollow. Fewer is better. The key is to pursue: "What is the minimum number of lanes we can get away with?"

### Deferral Visualization Pattern
```
queue-distributor | queue-0
                    queue-1
                    queue-2
                    queue-3
                    queue-long
```

- (Information assumed in particular)
    - None
- (Processing information)
    - Process `queue-XX`
- (Adding information)
    - Add to `queue-distributor`. At this time, always attach a timestamp (at minimum, a date is required)
- (Moving information)
    - Move from `queue-distributor` to `queue-0`
    - Move from `queue-X` to `queue-X` (re-enqueue it)
    - Move to a queue lower than `queue-X`

A pattern to visualize how many days an item has been deferred.

First, catch items in `queue-0` and aim to keep it empty. If you can't process something on the spot, re-enqueue it into `queue-0`. At that time, look at the item's timestamp and change the destination depending on how many days have passed: if 1 day has passed, put it in `queue-1`; 2 days, `queue-2`; 3 days, `queue-3`; more than that, `queue-long`.

The other `queue-XX` lanes work the same way: when processing, take an item out; if you can process it, you're done. If you can't, either re-enqueue it into `XX`, or if more days have passed, put it into the queue for that number of days. By doing this, you gradually organize how many days items have been deferred.

### Phase Pattern
```
queue-inbox | queue-phase1 | queue-phase2 | ...
queue-NA
```

- (Information assumed in particular)
    - None
- (Processing information)
    - Process `queue-phaseXX`
- (Adding information)
    - Add to `queue-inbox`
- (Moving information)
    - Take items out of `queue-inbox` as needed and move them to `queue-phase1`
    - Handle items taken out from `queue-phaseXX` as follows:
        - If it is not suitable for Phase XX, move it to Phase XX+1
        - If it is suitable but cannot be processed now, put it back into `queue-phaseXX` (re-enqueue it)
        - If it is not suitable even for the last Phase, put it into `queue-NA` (NA = Not Applicable)

First, design your phases. This is the practice of dividing things into stages like Phase-1 → Phase-2 → .... In contexts where you use "business-like progression" such as schedules, processes, or cycles, this should appear frequently.

With that as a premise, the Phase pattern supports such practices. The key is that you'll accumulate: "things to be used in phases later than the current one", "things that should have been used in phases that have already passed", and "things that do not fit any phase." The amount of accumulation is a kind of indicator, and the idea is: **the more it piles up, the more it smells dangerous.**

### Cushion Pattern
```
queue-inbox | stack-sleep | queue-sleep
```

- (Information assumed in particular)
    - Things that would likely be low-quality or cause trouble if submitted immediately
- (Processing information)
    - Take items out of `queue-sleep`, ask yourself "Do I really process this?", and if yes, process it
- (Adding information)
    - Add to `queue-inbox` items that "you want to send immediately, but should let sit"
- (Moving information)
    - Move from `queue-inbox` to `stack-sleep`
    - Check `stack-sleep` after some time, and move taken-out items to `queue-sleep`
        - At minimum, **do not** check it at the same time you add items to `queue-inbox`
    - Check `queue-sleep` after some time, and:
        - At minimum, **do not** check it at the same time you add items to `stack-sleep`

If you send something immediately just because you want to, it rarely ends well. Letting it sit once helps you calm down and avoid unnecessary actions. This pattern is for realizing that kind of cushioning behavior.

Conceptually, you first put it into a stack and let it sit. Because it's a stack, if you put many items in, older ones sink to the bottom, and that's intentional: the more it piles up, the harder it is to see, and the easier it is to let things sit. Next, you pile it up once more: items taken out of the stack go into a queue. By the time you take them out of the queue, you will probably be calmer.

### GTD Pattern
```
queue-inbox | queue-someday    | queue-projects | queue-nextactions
              queue-documents    queue-calendar
              queue-recyclebin   queue-waiting
```

- (Information assumed in particular)
    - None
- (Processing information)
    - Process `queue-nextactions`. **You should process it ad hoc and very frequently**
    - Process `queue-calendar`. Add the schedule indicated by the taken-out item to your calendar. **You should process it frequently enough not to miss appointments**
    - Process `queue-documents`. Add the location of the referenced material to some management system, or if it is the material itself, store it in an appropriate place. Process it frequently enough that the queue does not pile up
- (Adding information)
    - Add to `queue-inbox`
    - Add to `queue-nextactions`. You will usually think of these while looking at the contents of `queue-projects`
- (Moving information)
    - Move from `queue-inbox` to subsequent queues. Sort as follows:
        - Things you can do someday → someday
        - Mere reference materials you can consult as needed → documents
        - Unnecessary things → recycle bin
        - Concretely actionable and "cleanly finishable" items → next actions
        - Not concrete, not actionable, or concrete and actionable but "not cleanly finishable" → projects
        - Things that should be managed as scheduled events → calendar
        - Things that cannot move forward until you get a reaction from someone else → waiting
    - Re-enqueue items from `queue-project` back into `queue-project` (re-enqueue them)
        - Because a project continues to exist "until it is finished or canceled as a project", you keep it alive simply by re-enqueueing it

An implementation of GTD® using staqueue patterns. Note that the higher horizons model is not covered.

*Higher horizons model: within GTD, a mechanism to manage axes such as goals, vision, and values to maintain life-wide consistency, including short-, mid-, and long-term horizons.*

GTD has three essentials: "throw everything into the inbox first", "sort them into special buckets while breaking them down into immediately actionable next actions", and "grind through the many accumulated next actions." This GTD pattern implements that straightforwardly.

Many people manage GTD with lists, but queues can actually be easier to operate. Because with a queue, you can't see inside it, it's easier to act mechanically: take an item out and either process it or move it. Of course, since you end up behaving like a machine despite being human, it may or may not suit you.

### Three-Polling Pattern
```
queue-polling1
queue-polling2
queue-polling3
```

- (Information assumed in particular)
    - Tasks like trying to engage someone who is hard to reach
- (Processing information)
    - Process `queue-pollingXX`
- (Adding information)
    - Add to `queue-polling1` items that "you want to reach out to someone about, but it's not guaranteed you can, so you need to poll"
- (Moving information)
    - If you took an item out of `queue-pollingXX` and tried to process it but couldn't, move it to `XX+1`
    - If you still can't do it in `queue-polling3`, delete it

For example, suppose you want to talk to Person A, but A is so busy that they're hard to catch even if you go to their desk. Also suppose their desk is far away and it's hard to keep going back and forth. In such cases, you'd try a few times, and if it doesn't work, you'd give up for now. This pattern can be used in such a situation.

As the name suggests, you try only three times. Manage it in `queue-polling1`, and when you attempt it, take it out from this queue. If you can attempt it successfully, you're done; if not, move it to `polling2`. Do this up to the third attempt. If it still doesn't work in `polling3`, you give up there.

This doesn't define when or which queue to process. You can be strict, or you can operate loosely like "Today I'll only look at polling2." This pattern simply guarantees "try only three times."

## (Patterns for Teams)
Staqueue patterns are "of the individual, by the individual, for the individual", but they can also be used by teams.

For example, in the "Checkout Line Pattern" you let requesters choose which queue to put their request into, and in the "Assign Pattern" you assume each member has one stack. The former is called **delegating additions**, and the latter is called **granting staqueues**.

Even in team-related patterns, you still adhere to "operating a system for yourself." On top of that, by involving other members just a little, you address team-level issues. There are several ways to do that "small involvement":

- Delegating additions: have them do the part of adding information to your staqueue
- Granting staqueues: give them a staqueue and have them operate it
- ...

### Checkout Line Pattern
```
queue1
queue2
...
```

- (Information assumed in particular)
    - Requests
- (Processing information)
    - Process `queueXX`
- (Adding information)
    - **The requester adds it, not you**. The requester adds their request to `queueXX`. They may decide where to add it after looking over all `queueXX`
- (Moving information)
    - None

This is a common sight in the real world: you look at n waiting lines and choose which one to join.

In the context of this pattern collection (personal information management), this pattern is aimed at people who request things from you: you first make your queues visible, and then the requester decides which queue to join (i.e., where to throw the request).

- Before
    - The requester probably asks you verbally, via email, chat, etc.
- After
    - The requester looks at the checkout-line queues you prepared, decides where to submit the request, and then adds it
    - By looking at the checkout-line queues, the requester can understand your current bandwidth/status

It does require the work of making queues visible and reflecting status, but the queues become a shared language. And because you can control the situation by controlling the queues, it becomes easier for both you and the requester.

### Assign Pattern
```
queue-inbox | stack-member1
              stack-member2
              ...
```

- (Information assumed in particular)
    - Work-like tasks. "Work-like" means either "a standard person in a standard situation can complete it within a specified time" or "it is time-boxed and you keep spending that specified time." Common in manufacturing and customer service
- (Processing information)
    - Member XX processes their own stack `stack-memberXX`
- (Adding information)
    - The manager or members add to `queue-inbox`
- (Moving information)
    - The manager moves items from `queue-inbox` to `stack-memberXX`

A pattern you can use when assigning tasks to n members in a situation where you just keep grinding through work-like tasks.

**Each person has one stack**. Because it's a stack, it becomes "process the most recent one first," which naturally handles interruptions. Also, if assignments accumulate at the bottom, you conclude the assignment approach is bad. It's a sign to assign less frequently or to add more stacks (i.e., members).

Note that it cannot handle non-work-like tasks.

### Take Pattern
```
queue-inbox | stack-member1
              stack-member2
              ...
```

- (Information assumed in particular)
    - Work-like tasks
- (Processing information)
    - Member XX processes their own stack `stack-memberXX`
- (Adding information)
    - The manager or members add to `queue-inbox`
- (Moving information)
    - Members move items from `queue-inbox` to `stack-memberXX`. However, if they don't think they can handle it, they return it to `queue-inbox`
        - **Limit the timing when you can return it to `queue-inbox` to "immediately after taking it out of `queue-inbox`."** Otherwise, things will be returned to the inbox arbitrarily and the system will break down.

A variant of the Assign pattern. In the Assign pattern, the manager assigns tasks to members; in this pattern, members proactively go and take tasks from the inbox.

## ====
## Appendix
### Appendix 1: How to Create Categories in the Category Pattern
There are many well-known decision axes, so here are some.

- (Context axis)
    - By project
    - By person
    - By rough deadline
        - For example, four lanes: "do today", "do by tomorrow", "do this week", "later is fine"
        - Because dates update every day, daily updates are required. Doing it manually is tough
- (Subjective axis)
    - By subjective difficulty
        - For example: high (heavily consumes brainpower and/or nerves), medium (takes effort but doable), low (doable if you spend time)
    - By subjective fatigue
        - For example: high (so tiring you have to choose the right timing), medium (can do anytime but tiring), low (can do anytime)
    - Three lanes: like, dislike, neutral/don't care
    - Two lanes: want to do, don't want to do
    - Three lanes: Must, Should, Want

You may also find useful my organized "task attribute list":

- [List of "Task Attributes" for Implementing Task Management - DEV Community](https://dev.to/stakiran/list-of-task-attributes-for-implementing-task-management-5e87)