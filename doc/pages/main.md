# Introduction {#mainpage}

Stitch is a C++ library for communication and synchronization of threads and the outside world, suitable for real-time applications.

# Principles

The design of Stitch is guided by the following principles:

### Modularity

Instead of implementing a particular communication model (e.g. Actors, Communicating Sequential Processes, ...), Stitch provides basic building blocks. The blocks are easy to compose, and the user is free to compose them in the way most suitable for their application.

However, Stitch does provide implementations of several common [communication patterns](#patterns) built on top of the basic blocks.

### Separation of Concerns

Stitch separates two concerns:
1. Information: Producing and consuming data; addressed by [Shared Data Structures](#shared-data).
2. Time: Waiting for something to happen, for example data to be produced and consumed; addressed by the [Event System](#events).

Despite striving to separate these concerns, Stitch makes sure the classes that represent them are easy to compose. This is demonstrated by Stitch's higher-level classes implementing common [communication patterns](#patterns).

### Real-time safety

Stitch aims to be suitable for real-time applications. Its goal is to provide at least lock-free progress guarantees and linear or constant time complexity for as many operations as possible. Progress guarantees and complexity should be documented for each individual operation (work in progress).


# Shared Data Structures {#shared-data}

The foundation of thread communication are data structures that can be collaboratively used by multiple threads:

- [Waitfree_SPSC_Queue](@ref Stitch::Waitfree_SPSC_Queue): Wait-free single-producer-single-consumer bounded-size queue. Most efficient.
- [Waitfree_MPSC_Queue](@ref Stitch::Waitfree_MPSC_Queue): Wait-free multi-producer-single-consumer bounded-size queue. More efficient than the wait-free MPMC queue.
- [Waitfree_MPMC_Queue](@ref Stitch::Waitfree_MPMC_Queue): Wait-free multi-producer-multi-consumer bounded-size queue.
- [Lockfree_MPMC_Queue](@ref Stitch::Lockfree_MPMC_Queue): Lock-free multi-producer-multi-consumer bounded-size queue. More efficient than the wait-free MPSC and MPMC queues.
- [SPMC_Atom](@ref Stitch::SPMC_Atom): Lock-free single-writer-multi-reader atomic value of any trivially copyable type (regardless of size). More efficient than the generic Atom.
- [Atom](@ref Stitch::Atom): Lock-free multi-writer-multi-reader atomic value of any type (regardless of size).
- [Set](@ref Stitch::Set): An unordered dynamically-sized set of items with lock-free iteration.


# Events {#events}

An essential part of thread interaction is synchronization of threads in time. In addition, real-time applications need to wait and respond to data arriving from the outside world. The event system provides a convenient interface for waiting for and reacting to multiple types of events.

The [Event][] class is a generic representation of an event source. Instances of Event are provided by the following classes:

- [Signal](@ref Stitch::Signal): Events triggered by the application itself, used for signalling between threads.
- [Timer](@ref Stitch::Timer): Time events.
- [File_Event](@ref Stitch::File_Event): Events triggered when a file becomes ready for reading or writing.
- [File](@ref Stitch::File): A convenient class that provides File_Events as well as an interface for actual file reading and writing.

You can wait for a single event synchronously using the [wait](@ref Stitch::wait(const Stitch::Event&)) function. Waiting and reacting to multiple events is enabled by the [Event_Reactor](@ref Stitch::Event_Reactor) class.

[Event]: @ref Stitch::Event

See the [examples](examples.html#events).

# Patterns {#patterns}

Building on the shared data structures and event system, Stitch provides classes that implement common communication patterns.

The philosophy is that each thread should be agnostic of threads that it communicates with. On one hand, each thread should have all the resources for communication ready at all times. On the other hand, threads can come and go and dynamically establish communication channels.

Therefore, each thread is represented by one instance of a class, and these objects can by dynamically connected and disconnected. After a connection is established, a thread's representing object can be destroyed at any moment and is thereby automatically and safely disconnected from all peers.

- [Stream_Producer][] and [Stream_Consumer][]: Communicating streams of items from one source to multiple destinations or from multiple sources to a single destination.
  See [examples](examples.html#streams)).
- [State][] and [State_Observer][]: Communicating the latest state of one thread to multiple observers.
  See [examples](examples.html#state)).

[Stream_Producer]: @ref Stitch::Stream_Producer
[Stream_Consumer]: @ref Stitch::Stream_Consumer
[State]: @ref Stitch::State
[State_Observer]: @ref Stitch::State_Observer

