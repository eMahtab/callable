# Callable

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
