# Extending Exception and RuntimeException

## Learning Goals

- Extend `RuntimeException` to create a new unchecked exception class
- Extend `Exception` to create a new checked exception class

## Introduction

We will create two new exception classes `NegativeNumberException`
and `LongStringException` to demonstrate unchecked and checked exceptions.

![new exceptions uml](https://curriculum-content.s3.amazonaws.com/6677/pillars/new_exceptions.png)

## Extending `RuntimeException`

Let's create a method that takes an integer as a parameter and prints a countdown,
for example:

```text
4
3
2
1
0
Blastoff!
```

We can't do a countdown to 0 if the parameter is negative.  We'll create a new
class `NegativeNumberException` that extends `RuntimeException`, which
means it represents an unchecked exception.  The constructor will take
a string to display when the inherited `getMessage()` method is called.

```java
public class NegativeNumberException extends RuntimeException {

    public NegativeNumberException(String message) {
        super(message);
    }

}
```

The class `UncheckedExceptionMain` shown below has the method `countDown(int start)`,
which throws a `NegativeNumberException` if the parameter value is negative.
Since the exception is unchecked, the `countDown(int start)` method need not declare it throws the exception.

The `main()` method calls the `countDown(int start)` method passing a random integer
value, which may be negative.  Since `NegativeNumberException` is unchecked, the `main()` does
not need to handle the exception.

```java
import java.util.Random;

public class UncheckedExceptionMain {
    
    // May throw NegativeNumberException (unchecked)
    public static void countDown(int start)  {
        if (start < 0) {
            throw new NegativeNumberException(String.format("Counting down from negative %d does not work" , start));
        }
        else {
            for (int i= start ; i >= 0; i--) {
                System.out.println(i);
            }
            System.out.println("Blastoff!");
        }
    }

    public static void main(String[] args) {
        Random random = new Random();
        int num = random.nextInt(-5, 5);
        // Need not handle NegativeNumberException (extends RuntimeException)
        // Method call will throw NegativeNumberException if num < 0
        countDown(num);
    }

}
```

If we run the program and a positive number is generated,
we see the output of the `countDown` method:

```text
3
2
1
0
Blastoff!
```

However, the `countDown()` method will throw the exception if a negative
value is passed as the parameter.  The program crashes since
the `main()` method does not attempt to handle the exception:

```text
Exception in thread "main" NegativeNumberException: Counting down from negative -2 does not work
	at UncheckedExceptionMain.countDown(UncheckedExceptionMain.java:8)
	at UncheckedExceptionMain.main(UncheckedExceptionMain.java:23)
```

We can prevent the runtime exception `NegativeNumberException` from
ever being thrown by simply fixing the range of random values:

```java
public static void main(String[] args) {
    Random random = new Random();
    int num = random.nextInt(0, 5);
    // Need not handle NegativeNumberException (extends RuntimeException)
    // Method call will throw NegativeNumberException if num < 0
    countDown(num);
}
```

## Extending `Exception`

Let's assume we need to enter product information into a form.
If the product name is too long (> 20 characters), the code will
throw a checked exception. 

We'll create a new class `LongStringException` that extends `Exception`.

```java
public class LongStringException extends Exception {

    public LongStringException(String message) {
        super(message);
    }

}
```

We'll have a separate class `CheckedExceptionMain`
with a method `checkProductName()`
that tests the length of the parameter string and throws
a `LongStringException` if the length exceeds 20 characters.
The `checkProductName()` method must declare it throws `LongStringException`
since it is a checked exception that must be handled.

The `main()` method prompts for a product name and then 
calls `checkProductName()`.  Since `LongStringException` is a checked exception,
we must use a `try-catch` block when we call the `checkProductName()` method.

```java
import java.util.Scanner;

public class CheckedExceptionMain {
    public static void checkProductName(String productName) throws LongStringException {
        if (productName.length() > 20) {
            throw new LongStringException(String.format("\"%s\" is too long to fit in the textbox!",productName));
        }
        else {
            System.out.println(String.format("\"%s\" entered into textbox.",productName));
        }
    }

    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        System.out.print("Enter product name:");
        String productName = scan.nextLine();

        // Must check for and handle LongStringException
        try {
            checkProductName(productName);
        }
        catch (LongStringException e) {
            System.out.println("Caught LongStringException");
            System.out.println(e.getMessage());
        }
    }
}
```

Let's test the program by entering a short product name.  No exception is thrown:

```text
Enter product name:paper towels
"paper towels" entered into textbox.
```

However, entering a long product name causes the exception to be thrown
by the `checkProductName()` method and handled by the `main()` method:

```text
Enter product name:super thick and absorbent paper towels
Caught LongStringException
"super thick and absorbent paper towels" is too long to fit in the textbox!
```
