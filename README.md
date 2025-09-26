# Divide and Conquer

![Parallel Pipeline](/images/fig58.png)

As a final example (you are surely getting tired of juicy code and want to delve back into philological discussions about comparative abstractive norms), let’s do a little supercomputing. Then coffee. Our supercomputing application is a fairly typical parallel processing model. We have:

- A ventilator that produces tasks that can be done in parallel
- A set of workers that process tasks
- A sink that collects results back from the worker processes

In reality, workers run on superfast boxes, perhaps using GPUs (graphic processing units) to do the hard math. Here is the ventilator. It generates 100 tasks, each a message telling the worker to sleep for some number of milliseconds:
