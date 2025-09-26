# Divide and Conquer

![Parallel Pipeline](/images/fig5.png)

As a final example (you are surely getting tired of juicy code and want to delve back into philological discussions about comparative abstractive norms), let’s do a little supercomputing. Then coffee. Our supercomputing application is a fairly typical parallel processing model. We have:

- A ventilator that produces tasks that can be done in parallel
- A set of workers that process tasks
- A sink that collects results back from the worker processes

In reality, workers run on superfast boxes, perhaps using GPUs (graphic processing units) to do the hard math. 

The ventilator generates 100 tasks, each a message telling the worker to sleep for some number of milliseconds.

The worker application receives a message, sleeps for that number of seconds, and then signals that it’s finished.

The sink application collects the 100 tasks, then calculates how long the overall processing took, so we can confirm that the workers really were running in parallel if there are more than one of them.

The average cost of a batch is 5 seconds. When we start 1, 2, or 4 workers we get results like this from the sink:

- 1 worker: total elapsed time: 5034 msecs.
- 2 workers: total elapsed time: 2421 msecs.
- 4 workers: total elapsed time: 1018 msecs.

Let’s look at some aspects of this code in more detail:

The workers connect upstream to the ventilator, and downstream to the sink. This means you can add workers arbitrarily. If the workers bound to their endpoints, you would need (a) more endpoints and (b) to modify the ventilator and/or the sink each time you added a worker. We say that the ventilator and sink are stable parts of our architecture and the workers are dynamic parts of it.

We have to synchronize the start of the batch with all workers being up and running. This is a fairly common gotcha in ZeroMQ and there is no easy solution. The zmq_connect method takes a certain time. So when a set of workers connect to the ventilator, the first one to successfully connect will get a whole load of messages in that short time while the others are also connecting. If you don’t synchronize the start of the batch somehow, the system won’t run in parallel at all. Try removing the wait in the ventilator, and see what happens.

The ventilator’s PUSH socket distributes tasks to workers (assuming they are all connected before the batch starts going out) evenly. This is called load balancing and it’s something we’ll look at again in more detail.

The sink’s PULL socket collects results from workers evenly. This is called fair-queuing.

![Fair Queuing](/images/fig6.png)

The pipeline pattern also exhibits the “slow joiner” syndrome, leading to accusations that PUSH sockets don’t load balance properly. If you are using PUSH and PULL, and one of your workers gets way more messages than the others, it’s because that PULL socket has joined faster than the others, and grabs a lot of messages before the others manage to connect.

