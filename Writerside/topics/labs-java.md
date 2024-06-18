# labs java

## lab 00 : explore IntelliJ IDEA {collapsible="true"}

### Step 1: Download and Install
1. Download IntelliJ IDEA

Go to the [JetBrains website ](https://www.jetbrains.com/idea/download/?section=mac) and download the Community Edition (free) or the Ultimate Edition (paid).

2. Install IntelliJ IDEA

Follow the installation instructions for your operating system.

### Step 2: Create a New Project
1. Launch IntelliJ IDEA

Open IntelliJ IDEA and click on `New Project`.

2. Select Project Type

Choose the type of project you want to create. For a Java project, select `Java` and configure the JDK.

3. Configure Project Settings

Enter the project name, location, and other settings, then click `Finish`.

### Step 3: Write Your First Java Program
1. Create a New Class

Right-click on the src directory in the Project view, select `New -> Java Class`, and name your class Main.

2. Write Code

Write a simple Java program:

```Java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello, IntelliJ IDEA!");
    }
}
```

3. Run the Program

Click the green `Run` button in the toolbar or right-click the file and select `Run Main.main()`.

### Step 4: Explore IntelliJ IDEA Features
1. Code Completion

Start typing in the editor and notice the auto-suggestions provided by IntelliJ IDEA.

2. Refactoring

Try renaming a variable or method using <shortcut>Shift + F6</shortcut> and see how IntelliJ refactors the code throughout the project.

3. Version Control

If you have a Git repository, you can integrate it by going to `VCS -> Enable` Version Control Integration" and selecting Git.

4. Debugging

Set breakpoints by clicking on the left gutter next to the line numbers, then run the program in debug mode.

5. Plugins

Explore plugins by going to `File -> Settings -> Plugins` and browse the JetBrains Marketplace.

## lab 01 : java lambda challenges {collapsible="true"}

### Exercise 1: Basic Lambda Expression
Write a lambda expression that takes two integers and returns their sum.

### Exercise 2: Lambda with Functional Interface
Create a functional interface named StringManipulator with a method manipulate that takes a String and returns a String. Write a lambda expression that converts the input string to uppercase.

### Exercise 3: Filtering with Lambdas
Given a list of integers, use a lambda expression to filter out the even numbers and print the remaining numbers.

### Exercise 4: Sorting with Lambda
Sort a list of strings by their lengths using a lambda expression.

### Exercise 5: Custom Comparator with Lambda
#Create a list of Person objects and sort them by age using a lambda expression. The Person class should have fields name and age.

### Exercise 6: Using Function Interface
Write a lambda expression using the Function interface that takes a string and returns its length.

### Exercise 7: Chaining Consumers
Create two Consumer lambda expressions. The first one prints a string and the second one prints its length. Chain these consumers together and apply them to a list of strings.

### Exercise 8: Using Predicate
Write a lambda expression using the Predicate interface that checks if a string is non-empty.

## lab 02 : error handling in Java {collapsible="true"}

1. Create a new Java project. You can use your favorite IDE or do it via the command line

   ```bash
   mvn archetype:generate -DgroupId=com.example -DartifactId=java-error-handling -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   cd java-error-handling
   ```

2. Create a main class for your application. For this example, we'll create a class that performs **simple arithmetic operations**
3. Implement Basic Error Handling with Try-Catch
4. Create a custom exception `InvalidArithmeticOperationException` for invalid arithmetic operations and enhance Error Handling with Custom Exceptions
5. Add the necessary dependencies to your pom.xml and logging for Error Handling

   ```xml
   <dependencies>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>2.0.13</version>
    </dependency>
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.5.6</version>
    </dependency>
   </dependencies>
   ```

6. Create a logback.xml file in the `src/main/resources` directory with following configuration


   ```xml
   <configuration>
      <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender" >
         <encoder>
            <pattern> 
               %d{yyyy-MM-dd HH:mm:ss} [%thread] [%X{OperationType}]  - %msg%n
            </pattern>
         </encoder>
      </appender>
      <root level="debug">
         <appender-ref ref="STDOUT" />
      </root>
   </configuration>
   ```

Add `OperationType` to log context.

7. Run the App class to see how error handling works


## lab 03 : code in java 21 {collapsible="true"}

1. Create a new Java project. You can use your favorite IDE or do it via the command line

   ```bash
   mvn archetype:generate -DgroupId=com.example -DartifactId=pattern-matching-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   cd pattern-matching-example
   ```

2. Create Simple Records : `Person.java` and `Animal.java`

   ```Java
   public record Person(String name, int age) {}
   ```
   
   ```Java
   public record Animal(int age) {}
   ```

3. Implement Pattern Matching with `instanceof` using `PatternMatchingExample.java`
 
   ```Java
    public class PatternMatchingExample {
     public static void main(String[] args) {
         Object obj = new Person("toto", 30);
   
         // Classic instanceof with casting
         if (obj instanceof Person) {
             Person person = (Person) obj;
             System.out.println("Person: " + person.name() + ", Age: " + person.age());
         }        
         if (obj instanceof Animal) {
                Animal animal = (Animal) obj;
                System.out.println("Animal, Age: " + animal.age());
        }
     }
    }
   ```
4. Update `PatternMatchingExample.java` to use record patterns
5. Enhance `PatternMatchingExample.java` to include pattern matching in switch statements

You should see the output for the Employee object

   ```yaml
   Person: toto, Age: 30
   ```



