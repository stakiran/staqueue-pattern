# Staqueue Pattern
[Japanese](index.md) | [English](index_EN.md) | [GitHub](https://github.com/stakiran/staqueue-pattern/)

## About the Staqueue Pattern
### About Staqueue
**Staqueue** refers to Stack and Queue. More specifically, it refers to information management using these two data structures.

With a staqueue, you use stacks and queues to build the information management structure you want. For example, you might create two lists: queue1 for your regular task list, and stack1 for interruptions. If you set the priority to `stack1 > queue1`, your operation becomes: "Normally, process tasks from queue1 in a FIFO manner, but when an interruption comes in, process everything from stack1 in a LIFO manner." It's simple, but it forms the basis for handling both regular tasks and interruptions.

Staqueue has several principles:

- Information management means managing personal information, by an individual, for an individual
    - Information can be anything such as TODOs, tasks, ideas, and things to read later, as long as it is expressed as text
    - Management includes storing, browsing, searching, **processing (popping/dequeuing)**, and moving items to another staqueue. **It does not include editing**
- Because it uses stacks and queues, it has the following constraints
    - A stack is LIFO: the most recently added item is processed first
    - A queue is FIFO: the earliest added item is processed first
    - No random access (operating on an item at an arbitrary position). However, browsing and searching are possible
    - Once an item is processed (taken out), it is deleted (you can add it again if needed)
- There is priority
    - There are priority layers, and you process staqueues starting from the higher layers

Examples of how to use staqueue:

- Personal information management (manual)
- Personal information management (as a data structure when turning it into software)
- A data structure for AI agents

### About the Staqueue Pattern
This is a整理 of frequently appearing patterns, similar to design patterns.

The format is as follows:

- Overview
    - The further left, the higher the priority; layers are separated by `|`
- About task processing
- About adding tasks

Here is an example of an overview. For instance, the following:

```
Stack1 | Queue1
```

In practice, it means:

```
High Prio <---   ---> Low Prio

(Layer1)   (Layer2)
 Stack1  |  Queue1
```

## ====

## List of Staqueue Patterns
### Interruption-Handling Pattern
```
Stack1 | Queue1
```

- (Task processing)
    - First, process Stack1 until it becomes empty
    - Once Stack1 is empty, process Queue1
- (Adding tasks)
    - Normally, add to Queue1
    - When an interruption comes in, add to Stack1

This is the most basic pattern. You use a stack to handle interruptions, but use a queue for normal operations.

### Category Pattern
```
Queue-Entrance | Queue1
                 Queue2
                 ...
```

- (Task processing)
    - Pick a QueueXX as appropriate and process it
- (Adding tasks)
    - First, add to Queue-Entrance
    - Sort items from Queue-Entrance into a QueueXX
    - Try to keep Queue-Entrance empty

See Appendix 1 for how to create QueueXX.

### Project Pattern
```
stack-inbox | queue-inbox | stack1  queue1
                            stack2  queue2
                            ...     ...
```

- (Task processing)
    - First, process stack-inbox until it becomes empty
    - Move items from queue-inbox to projectXX. Do this frequently and keep it as empty as possible
    - For each projectXX, there are three variants: stackXX, queueXX, and stackXX + queueXX; process them when working on that project
- (Adding tasks)
    - Add urgent items such as interruptions to stack-inbox
    - Add everything else to queue-inbox

You prepare a staqueue per project, and accept/sort items into them using the "Interruption-Handling Pattern." However, when it's an interruption, you do not sort; you have it processed directly.

### xxx Pattern
```
```

- (Task processing)
    - ...
    - ...
    - ...
- (Adding tasks)
    - ...
    - ...
    - ...

### xxx Pattern
```
```

- (Task processing)
    - ...
    - ...
    - ...
- (Adding tasks)
    - ...
    - ...
    - ...

### xxx Pattern
```
```

- (Task processing)
    - ...
    - ...
    - ...
- (Adding tasks)
    - ...
    - ...
    - ...

## ====

## Appendix
### Appendix 1: How to Create Categories in the Category Pattern
Do whatever you like, but there are some well-known axes, so it's helpful to use them as references.

- (Context axis)
    - By project
    - By person
    - By approximate deadline
        - For example, four buckets: "do today," "do by tomorrow," "do this week," and "later is fine"
        - Because dates roll forward every day, this requires daily updates. Doing it manually is tough
- (Subjective axis)
    - By subjective difficulty
        - For example: high (burns out your brain, your nerves, or both), medium (takes effort but doable), low (doable if you spend the time)
    - Three buckets: like, dislike, neutral/don't care
    - Two buckets: want to do, don't want to do
    - Three buckets: Must, Should, Want

You may also find useful my curated "List of Task Attributes":

- [List of "Task Attributes" for Implementing Task Management - DEV Community](https://dev.to/stakiran/list-of-task-attributes-for-implementing-task-management-5e87)