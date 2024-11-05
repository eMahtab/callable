# Callable

```java
public interface Callable<V> {
    V call() throws Exception;
}
```

#### Callable vs. Runnable
The Java Callable interface is similar to the Java Runnable interface, in that both of them represents a task that is intended to be executed concurrently by a separate thread.

A Java Callable is different from a Runnable in that the Runnable interface's run() method does not return a value, and it cannot throw checked exceptions (only RuntimeExceptions).

Additionally, a Runnable was originally designed for long running concurrent execution, e.g. running a network server concurrently, or watching a directory for new files. The Callable interface is more designed for one-off tasks that return a single result.

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

//Callable task that calculates the sum of integers in a given range
class SumCalculator implements Callable<Integer> {
 private int start;
 private int end;

 public SumCalculator(int start, int end) {
     this.start = start;
     this.end = end;
 }

 @Override
 public Integer call() {
     int sum = 0;
     for (int i = start; i <= end; i++) {
         sum += i;
     }
     System.out.println(Thread.currentThread().getName()+" Sum from " + start + " to " + end + " = " + sum);
     return sum;
 }
}

public class CallableExample {
    public static void main(String[] args) {
        // Define the ranges to sum up
        int[][] ranges = { { 1, 100 }, { 101, 200 }, { 201, 300 }, { 301, 400 }, { 401, 500 } };

        // Create a list to hold Future objects associated with Callable tasks
        List<Future<Integer>> futures = new ArrayList<>();
        // Create a fixed thread pool with 5 threads
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        // Submit tasks to executor for each range
        for (int[] range : ranges) {
            Callable<Integer> task = new SumCalculator(range[0], range[1]);
            Future<Integer> future = executorService.submit(task);
            futures.add(future);
        }
        // Collect the results
        int totalSum = 0;
        for (Future<Integer> future : futures) {
            try {
                totalSum += future.get(); // Future.get() blocks until the result is available
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        }
        // Shutdown the executor service
        executorService.shutdown();
        System.out.println("Total Sum: " + totalSum);
    }
}
```

### Execution Output :
```
pool-1-thread-5 Sum from 401 to 500 = 45050
pool-1-thread-4 Sum from 301 to 400 = 35050
pool-1-thread-2 Sum from 101 to 200 = 15050
pool-1-thread-3 Sum from 201 to 300 = 25050
pool-1-thread-1 Sum from 1 to 100 = 5050
Total Sum: 125250
```
