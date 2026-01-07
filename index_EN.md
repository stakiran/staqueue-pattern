# Staqueue Pattern
[Japanese](index.md) | [English](index_EN.md) | [GitHub](https://github.com/stakiran/staqueue-pattern/settings/pages)

## About the Staqueue Pattern

### About Staqueue
**Staqueue** refers to Stack and Queue. In particular, it refers to information management using these two data structures.

With Staqueue, you build the information management structure you want using stacks and queues. For example, you can create two structures: `queue1` for your everyday task list and `stack1` for interruptions. If you set the priority to `stack1 > queue1`, the operation becomes: "Normally, tasks are handled in FIFO order in `queue1`, but when an interruption comes in, you handle it in LIFO order in `stack1` until it's done." It's simple, but it serves as a foundation for handling both everyday tasks and interruptions.

Staqueue has several principles:

- Information management means managing personal information, by an individual, for an individual
    - Information can be anything: TODOs, tasks, ideas, things to read later, etc., as long as it is represented as text
    - Management includes storing, browsing, searching, and **processing (taking out and moving)**. **It does not include editing operations**
- Because it's based on stacks and queues, the following constraints apply
    - A stack is LIFO: the most recently added information is retrieved first
    - A queue is FIFO: the earliest added information is retrieved first
    - No random access (accessing an item at an arbitrary position)
    - Once retrieved, information is removed (you can re-add it)
- There are priorities
    - There are priority layers, and you process Staqueues starting from the higher layers

Examples of how Staqueue can be used:

- Personal information management (manual)
- Personal information management (as a data structure when implementing in software)
- A data structure for AI agents

### About the Staqueue Pattern
This is a整理 of frequently occurring patterns, similar to design patterns.

The format is as follows:

- Overview
    - The further left, the higher the priority; layers are separated by `|`
- How tasks are processed
- How tasks are added

Here is an example of the overview. For instance, the following:

```
Stack1 | Queue1
```

In practice, it means:

```
High Prio <---   ---> Low Prio

(Layer1)   (Layer2)
 Stack1  |  Queue1
```

## Staqueue Pattern List

### Interruption-Handling Pattern
```
Stack1 | Queue1
```

- (Task processing)
    - First, process until Stack1 is empty
    - Once Stack1 is empty, process Queue1
- (Adding tasks)
    - Normally, add to Queue1
    - When an interruption comes in, add to Stack1

### Categorization Pattern
```
Queue-Entrance | Queue1
                 Queue2
                 ...
```

- (Task processing)
    - Choose an appropriate QueueXX and process it
- (Adding tasks)
    - First, add to Queue-Entrance
    - Sort items from Queue-Entrance into QueueXX
    - Aim to keep Queue-Entrance empty

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