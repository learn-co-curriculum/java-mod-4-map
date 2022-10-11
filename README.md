# Map

## Learning Goals

- Use the `map` intermediate operation on streams
- Use the `flatMap` method

## What is Mapping?

- `<R> Stream<R>	map(Function<? super T,? extends R> mapper)`

Mapping is one of the most common intermediate stream operations. 
The `map` function return a new stream consisting of the results of
applying the mapper function to the elements of a stream.

The mapper function passed as an argument to the `map` method can
be any function that takes one argument and returns a value.

The example below uses the mapping function `i -> i*2` to create a new
stream that doubles the elements from the integer array.
We can pass the new stream to a terminal operation such as `forEach`.

Note the `map` method does not modify the contents of the original array.

```java
import java.util.Arrays;
import java.util.stream.IntStream;

public class Example {
    public static void main(String[] args) {
        int[] nums = {6, -2, 7, 20};

        //double each element in the stream
        IntStream numsDoubled = Arrays.stream(nums).map(i -> i*2);
        numsDoubled.forEach(System.out::println);
    }
}
```

The program prints the result of mapping:

```text
12
-4
14
40
```

Let's map a list of strings to uppercase. We will
pass a method reference as the mapper function:

```java
import java.util.List;
import java.util.Arrays;

public class Example {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Amir", "Neela", "Dean");
        names.stream()
                .map(String::toUpperCase)
                .forEach(System.out::println);
    }
}
```

The program prints the names in uppercase:

```
AMIR
NEELA
DEAN
```

The `Stream` interface has special mapping methods
when working with a collection that wraps a primitive data type,
such as `List<Integer>`. 

- `DoubleStream	mapToDouble(ToDoubleFunction<? super T> mapper)`
- `IntStream	mapToInt(ToIntFunction<? super T> mapper)`
- `LongStream	mapToLong(ToLongFunction<? super T> mapper)`

For example, if a stream is created from a list of integers, we use `mapToInt`:

```java
import java.util.List;
import java.util.stream.IntStream;

public class Example {
    public static void main(String[] args) {
        List<Integer> nums = List.of(6, -2, 7, 20);

        //double each element in the stream
        IntStream numsDoubled = nums.stream().mapToInt(i -> i*2);
        numsDoubled.forEach(System.out::println);
    }
}
```

### Mapping a user defined class

Let's explore how to map the `Employee` class:

```java
public class Employee {
    private String name;
    private double salary;
    private int age;

    public Employee(String name, double salary, int age) {
        this.name = name;
        this.salary = salary;
        this.age = age;
    }

    public String getName() { return name; }

    public double getSalary() { return salary; }

    public int getAge() { return age; }
    
}
```

We can extract the employee names by passing the method reference
`Employee::getName` as the mapper function:

```java
import java.util.List;

public class Example {

    public static void main(String[] args) {
        List<Employee> employees = List.of(
                new Employee("fred", 42000.0, 24),
                new Employee("devon", 48000.0, 27),
                new Employee("pat", 77000.0, 32));

        //map names
        employees.stream()
                .map(Employee::getName)
                .forEach(System.out::println);
    }
}
```

The program prints:

```text
fred
devon
pat
```

If we want to get the names in uppercase, we can either use a lambda
expression to get each name and then uppercase it:

```java
//map names to uppercase
employees.stream()
        .map(employee -> employee.getName().toUpperCase())
        .forEach(System.out::println);
```

This produces:

```text
FRED
DEVON
PAT
```

We can also chain a sequence of calls to the `map` method,
passing a single method reference to each.  While this
makes the code more readable, it is less efficient since
each call to `map` creates a new stream:

```java
employees.stream()
        .map(Employee::getName)
        .map(String::toUpperCase)
        .forEach(System.out::println);
```

If we call a mapper function such as `Employee::getSalary` that
returns a primitive data type `double`, we use the primitive map operation `mapToDouble`:

```java
employees.stream()
        .mapToDouble(Employee::getSalary)
        .forEach(System.out::println);
```

The program prints each salary:

```text
42000.0
48000.0
77000.0
```

## Using `flatMap`

The intermediate methods like `filter` don’t work on streams where an element
is a Collection. These methods only work on primitive types or objects. The `flatMap` method
allows us to flatten a collection stream into a stream of elements.

The following code doesn’t work because the `filter` operation can’t work on the
unflattened stream.

```java
import java.util.List;
import java.util.stream.Stream;

public class Example {
    public static void main(String[] args) {
        List<List<Integer>> list = List.of(
                List.of(1, 2),
                List.of(3, 4, 5),
                List.of(6, 7)
        );

        Stream<List<Integer>> listStream = list.stream();
        Stream<List<Integer>> evenStream = listStream.filter(n -> n % 2 == 0); // this line throws an error
        evenStream.forEach(System.out::println);
    }
}
```

Let’s try flattening the `listStream` before performing the filter operation and
observe what happens.
We use `flatMap(Collection::stream)` to flatten a stream so each element is a primitive
value or an object rather than a collection.

```java
import java.util.Collection;
import java.util.List;
import java.util.stream.Stream;

public class Example {
    public static void main(String[] args) {
        List<List<Integer>> list = List.of(
                List.of(1, 2),
                List.of(3, 4, 5),
                List.of(6, 7)
        );

        Stream<List<Integer>> listStream = list.stream();
        Stream<Integer> flattenedStream = listStream.flatMap(Collection::stream);
        Stream<Integer> evenStream = flattenedStream.filter(n -> n % 2 == 0);
        evenStream.forEach(System.out::println);
    }
}
```

```text
2
4
6
```

We can chain the `flatMap` operation like any other intermediate operation to
make the code cleaner.

```java
import java.util.Collection;
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<List<Integer>> list = List.of(
                List.of(1, 2),
                List.of(3, 4, 5),
                List.of(6, 7)
        );

        list.stream()
                .flatMap(Collection::stream)
                .filter(n -> n % 2 == 0)
                .forEach(System.out::println);
    }
}
```

