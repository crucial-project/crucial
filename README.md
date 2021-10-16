# Crucial
_Stateful Distributed Applications over Serverless Architectures_

Function-as-a-Service (FaaS) enables programmers to develop cloud applications
as individual functions that can run and scale independently.
However, storage and compute resources must be separated in FaaS.
Applications that require support for mutable state and synchronization,
such as machine learning and scientific computing, are hard to build.

Crucial is a system to program highly-concurrent stateful applications on
serverless architectures. It keeps a simple programming model and allows to
port effortlessly multi-threaded algorithms to FaaS.

## Programming

Crucial associates each thread in a multi-threaded application with a FaaS
function invocation. We call this new form of thread _cloud thread_.

With our execution abstraction 
[here](https://github.com/crucial-project/executor),
a regular Java thread creation such as

```java
Thread t = new Thread(runnable);
```

can be run as a cloud function with a simple change:

```java
Thread t = new CloudThread(runnable);
```

To remotely share objects, Crucial maintains them in a separated in-memory
store, the [DSO layer](https://github.com/crucial-project/dso).
You designate shared objects by annotating their declaration with:

```java
@Shared(key="foo")
Foo bar = new Foo();
```

These objects are wait-free and linearizable, meaning that concurrent method
invocations behave as if they were executed by a single thread.
Shared objects are simple POJOs with the restriction that they must implement
Externalizable and have a no-arg constructor.<sup>1</sup>

You can find application examples in our
[examples repository](https://github.com/crucial-project/examples).

## Architecture

![arch](./images/architecture.png "Crucial architecture")

Crucial encompasses three main components:

 1. the FaaS computing layer that runs the cloud threads
 2. the DSO layer (distributed shared object) that stores the shared objects
 3. the client application

The client application creates and manages the execution of cloud threads, and
all of them access the the shared data using the DSO layer.

## Publication

"On the FaaS Track: Building Stateful Distributed Applications with Serverless
Architectures", Daniel Barcelona-Pons, Marc Sánchez-Artigas, Gerard París,
Pierre Sutra, Pedro García-López. ACM/IFIP International Middleware Conference,
Davis, CA, USA, December 9-13, 2019.

-----

1. Objects must be Externalizable in order to support cluster topology changes.
