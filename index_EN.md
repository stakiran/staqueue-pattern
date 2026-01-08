# Staqueue Pattern
[Japanese](index.md) | [English](index_EN.md) | [GitHub](https://github.com/stakiran/staqueue-pattern/)

## About the Staqueue Pattern
### About Staqueue
**Staqueue** refers to a Stack and a Queue. More specifically, it refers to information management using these two data structures.

With a staqueue, you create the information management structure you want using stacks and queues. For example, you can create two: `queue1` for your regular task list and `stack1` for interruptions. If you set the priority to `stack1 > queue1`, the operation becomes: "Normally, process tasks in `queue1` in FIFO order, but when an interruption comes in, process everything in `stack1` in LIFO order." It's simple, but it's the foundation for handling both everyday tasks and interruptions.

Staqueue has several principles:

- Information management means managing information of the individual, by the individual, for the individual
    - Information can be anything such as TODOs, tasks, ideas, things to read later, etc., but it is assumed to be expressed as text
    - Management includes storing, viewing, searching, **processing (pulling out)**, and moving to another staqueue. **Editing operations are not included**
- Since it's a stack and queue, the following constraints apply
    - A stack is LIFO: the most recently added information is pulled out first
    - A queue is FIFO: the earliest added information is pulled out first
    - No random access (operating on an item at an arbitrary position). However, viewing and searching are possible
    - Information pulled out once is deleted (re-adding is possible)
- There are priorities
    - There are priority layers, and you process staqueues from higher layers first

Examples of using a staqueue:

- Personal information management (manual)
- Personal information management (data structures when implementing in software)
- Data structures for AI agents

### About the Staqueue Pattern
Like design patterns, this is a整理 of commonly occurring patterns.

### Staqueue Pattern Format
```
(Describe the overall structure of this pattern here)
```

- (Processing information)
    - Write which staqueue to process
    - Processing means "pulling out information and turning it into action." Pulled-out information disappears
    - **This does not mention when to process**. It could be ad hoc, periodic, reactive (e.g., when a notification arrives), etc., but that's out of scope here
- (Adding information)
    - Write how to add information to this structure
    - This can be considered adding from outside. Often, there is a staqueue at a higher layer that serves as the entry point
- (Moving information)
    - Write when, and from which staqueue to which staqueue, information is moved
    - This can be considered internal movement. Often it moves toward lower layers, but it can also go the other way

### Format Example
Here is an example of an overall structure.

```
Stack1 | Queue1
```

In practice, this means the following:

```
High Prio <---   ---> Low Prio

(Layer1)   (Layer2)
 Stack1  |  Queue1
```

I wrote "priority," but the higher the priority, the more the pattern tends to be "easier to process" and "more likely to serve as an entry point." Also, information is often moved in order toward lower priority. This is only a tendency: sometimes you move from right to left, and sometimes you process on the low-priority side.

## ====

## ●task
- Separate into processing, moving, and adding?
    - Processing: pull out and execute
    - Moving: move to another staqueue
    - Adding: put into this staqueue pattern
- Pattern categorization can come later

## Staqueue Pattern List
### FIFO-Driven Pattern
```
queue-main
queue-temp1
queue-temp2
...
```

- (Processing information)
    - queue-main
- (Adding information)
    - Add to queue-main
- (Moving information)
    - If you want random access to queue-main or want to reorder items, use queue-tempXX

This is the most basic pattern. Instead of using lists such as a task list, you use queues. In other words, you operate in FIFO (tasks added earlier are pulled out earlier), and you don't do random access.

However, FIFO alone can be inconvenient, so it's acceptable to use Queue-Temp as well. The number of Temps is arbitrary, but fewer is better. The fewer you have, the more it indicates you are conforming to the staqueue pattern.

As shown, **operating in a FIFO-driven way is the most fundamental of fundamentals**.

### Guard Stack Pattern
```
stack1 | queue1
```

- (Processing information)
    - Process stack1 with priority. Process until it becomes empty
    - When stack1 is empty, process queue1
- (Adding information)
    - Add information to queue1
    - When an interruption comes in, add it to stack1
- (Moving information)
    - None

This is one of the most basic patterns. With FIFO-driven alone, you can't handle high-urgency items such as interruptions, so you introduce a stack.

The guard stack pattern itself (for simplicity) has a single Queue, but of course it can be combined with FIFO-driven as well.

### Later Pattern
```
queue-main | stack-later
```

- (Processing information)
    - Process queue-main
- (Adding information)
    - Add information to queue-main
- (Moving information)
    - Move items you want to do later from queue-main to stack-later
    - To process "later" items, move them back from stack-later to queue-main

This pattern implements "Later"—in other words, "do it later."

In FIFO-driven, you used multiple queues within the same priority level, but in the Later pattern you place a stack one layer lower. That means "later" items are pulled out starting from the most recently added. Once it piles up, you can't see the bottom, which makes you more inclined to process it sooner.

### Category Pattern
```
queue-entrance | queue1
                 queue2
                 ...
```

- (Processing information)
    - Process queueXX
- (Adding information)
    - Add to queue-entrance
- (Moving information)
    - Move from queue-entrance to an appropriate queueXX. Also, try to keep queue-entrance empty

This is a frequently used pattern. In many cases, a single queue is not enough, so you create multiple queues—but the question becomes what perspective to split them by. The category pattern provides a straightforward solution.

For how to create queueXX, see Appendix 1.

### Project Pattern
```
stack-inbox | queue-inbox | stack1  queue1
                            stack2  queue2
                            ...     ...
```

- (Processing information)
    - Process stack-inbox with top priority. Process until it becomes empty
    - Process project staqueues (stackXX, queueXX, or stackXX+queueXX) when doing that activity
- (Adding information)
    - Add highly urgent items such as interruptions to stack-inbox
    - Normally, add to queue-inbox
- (Moving information)
    - Move from queue-inbox to project staqueueXX

First, you prepare a staqueue for each project, and place a single queue-inbox to distribute items among them. It resembles the category pattern, but while the category pattern uses only queues, the project pattern can use three types: stack, queue, and stack+queue. See the difference below.

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

And to handle interruptions, you also add the guard stack pattern—specifically, by placing stack-inbox.

We each have n "projects," and we should be operating by switching, one at a time, which project we work on and when. This pattern assigns one project staqueue per project, and adds category-pattern-like sorting and guard-stack-like interruption protection on top.

### xxx Pattern
```
```

- (Processing information)
    - ...
- (Adding information)
    - ...
- (Moving information)
    - ...

## ====

## Appendix
### Appendix 1: How to Create Categories in the Category Pattern
Do it however you like, but there are well-known axes, so it's good to use them as references.

- (Context axis)
    - By project
    - By person
    - By rough deadline
        - For example, four queues: "do today," "do by tomorrow," "do this week," and "later is fine"
        - Since dates update every day, you need daily updates. Doing it manually is tough
- (Subjective axis)
    - By subjective difficulty
        - For example: high (abuses your brain, nerves, or both), medium (takes effort but doable), low (doable if you spend time)
    - Three queues: like, dislike, neutral/don't care
    - Two queues: want to do, don't want to do
    - Three queues: Must, Should, Want

Also, the "Task Attributes List" I compiled may be helpful:

- [List of "Task Attributes" for Implementing Task Management - DEV Community](https://dev.to/stakiran/list-of-task-attributes-for-implementing-task-management-5e87)