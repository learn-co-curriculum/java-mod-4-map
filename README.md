# Map

## Learning Goals

- Use the `map` operation on streams
- Use the `flatMap` method

## What is Mapping?

Mapping is one of the most common operations. When a collection is mapped, all
the elements are changed to new ones according to a provided mapping function.

```java
import java.util.List;
import java.util.Arrays;

public class Example {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Jake", "Jill", "Kate", "Ezra", "Maddie");
        List<String> namesUpper = names.stream()
                                    .map(String::toUpperCase)
                                    .toList();
        System.out.println(namesUpper);
    }
}
```

```
[JOHN, JAKE, JILL, KATE, EZRA, MADDIE]
```

The `map` method takes a method reference and uses it to change every element in
the names list.

## Using `flatMap`

The intermediate methods like `filter` don’t work on streams of Collections.
These methods only work on primitive types or objects. The `flatMap` method
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
        Stream<Integer> flattenedStream = listStream.flatMap(s -> s.stream());
        Stream<Integer> evenStream = flattenedStream.filter(n -> n % 2 == 0);
        evenStream.forEach(System.out::println);
    }
}
```

```java
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

Notice that we’ve replaced the `s -> s.stream()` expression in the `flatMap`
method to `Collection::stream`.
