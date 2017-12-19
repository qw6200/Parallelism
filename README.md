# Parallelism

12/15/2017
Aslan Bakri, Won Kuk Lee
abakri@u.rochester.edu
wlee33@u.rochester.edu

CSC 254 Programming Languages
Assignment 6: Concurrency


How to Run:

To run with 1-48 threads: 

*In order to use shell scripts compiledata and compile, please run the following commands:
chmod +x compile
chmod +x compiledata

Run with ./compiledata
To change # of vertices, edit compiledata and change vertices value to desired number.
To change seed, edit compiledata and change seed value to desired number.

To run once with custom thread number:
Run with ./compile 
To change # of vertices, edit compile and change vertices value to desired number.
To change seed, edit compile and change seed value to desired number.
To change # of threads, edit compile and change threads to desired number.

Methodology

Creating Threads:
We first created a WorkerP class that extends Thread and then changed the DeltaSolve to DeltaSolveP which creates all of the WorkerP objects. This method assigns these workers vertices and starts them. WorkerP initializes on creation and holds a hashset of vertices that it owns based on the indices given to its creation by DeltaSolveP. Each WorkerP object contains a ConcurrentLinkedQueue so other Worker threads can communicate with one another.

Running Delta Stepping:
The run() of WorkerP calculates the DeltaSolve algorithm given to us. However, instead of looping around the buckets, it only loops through the buckets once. Furthermore, it also calls a hold to the CyclicBarrier that we created whenever it wants to satisfy all the requests sent to WorkerP by the ConcurrentLinkedQueue. This keeps going until all the Worker threads complete all the light relaxations and then continues to the heavy relaxations after which the barrier is held again to satisfy the new heavy relaxation requests for each WorkerP ConcurrentLinkedQueue. Each Worker contains a satisfyRequest function that empties its queue and relaxes all the vertices in that queue.


Data Analysis



From the above graph (if you are reading this README, sorry for not adding this picture :( ), we can see that as the threads increased and the trend of the time elapsed for Delta Stepping algorithm decreased. This shows that as the number of threads increased, the runtime actually decreased, showing an improvement.


For the same data, we can see that the speedup also increases, from approx 0.95 to 0.99, which is a small change, but definitely an improvement. We were not able to obtain a speedup of k with k threads, though we did find an increasing slope. Clearly something about our implementation of the parallel Delta Stepping was flawed because of this large disparity between expected and real speedup. However, it does show the benefits of parallel programming when computing algorithms with high runtime. We suspect that the reason why we did not have an ideal speedup is because we forced the threads to run into a barrier at the end of the light relaxation loop. As a result, this may end up making the light relaxations run at almost identical runtime to Dijkstra's algorithm-- definitely something that we must make sure to avoid next time we implement concurrency and parallelism.

Another interesting find is that generally Delta Stepping ran faster with an even number of threads, as can be seen with the test cases for 1, 2, 3, and 4 threads. This is significant because it hints at the nature of thread communication. We experienced this first hand in workshop with the “pen game”.

We discovered something else when we were testing with a very small number of vertices. In the following experiment we tested the Delta Stepping runtime against the Dijkstra’s runtime for 100 vertices.


As can be seen in the above graph, although we generally expect parallel algorithms to perform better as we add more threads, in this case it actually did significantly worse as more threads were added. We think that this is due to the processing, initialising, and managing of threads and the ConcurrentLinkedQueues taking up a larger proportion of the total number of instructions ran. As a result, this caused the algorithm to run slower as there was an increase in number of threads. This is also reflected in the decreasing rate of speedup as threads increase, shown below.

