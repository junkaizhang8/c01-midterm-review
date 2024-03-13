# C01 Review Questions

## Table of Contents
- [Question 1](#q1)
- [Question 2](#q2)
- [Question 3](#q3)
- [Question 4](#q4)
- [Question 5](#q5)
- [Question 6](#q6)
- [Additional Notes](#additional-notes)

#### <u>Q1</u> <a id="q1"></a>

For each of the following quality attributes, provide a brief description and identify two measures that help promote it.
- Modifiability
- Availability

#### <u>Q1 Answer</u>

**Modifiability**: The ability to modify code easily
- Adhering to SOLID principles
  - Single Responsibility Principle
    - Promotes cohesion
    - Changing Class A will not change Class B as responsibilities are split
  - Dependency Inversion Principle
    - Dependency on abstractions
    - We can change implementation of concrete classes that depend on abstractions
- Readability
  - Readable code is easy to modify
  - Good variable names
  - Commenting
    - Over-commenting is not necessarily helpful

**Availability**: The ability for the system to be up and running all the time
- Peer-to-peer architecture
- Error-handling
- Replication
  - Have multiple copies of the server on multiple machines; combine with a load balancer to connect clients to the "least busy" server

#### <u>Q2</u> <a id="q2"></a>
Draw the case diagram corresponding to the following description:

*Consider a system that allows users to order books online. Users have the option to browse through a collection of books, place orders, and track their order status. During the ordering process, users can choose to pay through a credit card or Paypal. The system also offers administrators the functionality to manage the book collection. They can add, modify, or remove books from the collection. In addition, they can conduct book searches, which yield identical search results as those seen by users. However, admins have the option to edit or delete any of the books presented in the search results.*

#### <u>Q2 Answer</u>
![image](./images/use_case_diagram.jpg)

#### <u>Q3</u> <a id="q3"></a>
Draw the sequence diagram corresponding to method `withdraw`:
```java
public class ATM {
  private ArrayList<Transaction> transactions = new ArrayList<Transaction>();

  public void withdraw(Client client, Bank bank) {
    double amount = client.getAmount();
    Transaction transaction = new Transaction("withdraw", client, amount);
    boolean isAuthorized = bank.validateWithdrawal(client, amount);
    transaction.setAuthorization(isAuthorized);
    if(isAuthorized)
      transactions.add(transaction);
  }
}
```
#### <u>Q3 Answer</u>
![image](./images/sequence_diagram.jpg)

#### <u>Q4</u> <a id="q4"></a>
Consider the following code:

```java
public static void main(String [] args){
  int a = readInput();
  int b = readInput();
  int c = readInput();
  int median = c;
  if(b < c){
    if(a < b)
    median = b;
    else if(a < c)
    median = a;
  }
  else{
    if(a > b)
    median = b;
    else if(a > c)
    median = a;
  } 
  System.out.println(“median = ” + median);
}
```
Find all the def-use pairs that are covered when running the code using the test case (a=2, b=1, c=3).

#### <u>Q4 Answer</u>
**Note:** The standalone `else` is not a statement

(c, s3, s4)

(b, s2, s5)

(c, s3, s5)

(a, s1, s6)

(b, s2, s6)

(a, s1, s8)

(c, s3, s8)

(a, s1, s9)

(median, s9, s14)

#### <u>Q5</u> <a id="q5"></a>
Using the *zero-failure* testing model, determine the number of test hours needed to meet the goal of having no more than an average of 0.05 failure per 1000 lines of code. You can assume that:
- The program being tested consists of 40000 lines of code
- 12 failures have occurred so far over a total test time of 300 hours
- No failures have been reported during the last 20 hours of testing

#### <u>Q5 Answer</u>
Refer to Motorola's *Zero-Failure Testing*:

Num of further test hours needed =
$\dfrac{\ln(\frac{fd}{0.5 + fd})\cdot th}{\ln(\frac{0.5 + fd}{tf + fd})}$, where
- $fd$ = target failure density (e.g. 0.03 failures per 1000 LOC)
- $tf$ = total test failures observed so far
- $th$ = total testing hours up to the last failure

In our case, the variables would be defined as:

$fd = 0.05 \cdot 40 = 2$

$tf = 12$

$th = 300 - 20 = 280$

Substituting into the equation, the number of further test hours needed is:

$\dfrac{\ln(\frac{2}{0.5 + 2})\cdot 280}{\ln(\frac{0.5 + 2}{12 + 2})} = 36.27$

which can be rounded up to 37 hours.

#### <u>Q6</u> <a id="q6"></a>
The Surface-area-to-volume-ratio (denoted SA:V) is a practical concept that is employed in a variety of fields such physics and biology. Given an object with surface area s and volume v, the value of SA:V is simply s/v. The code below computes the SA:V of a cube and a ball, but it contains duplication regarding method `computeSurfaceAreaToVolumeRatio`. You are required to:
1) Identify a design pattern that would help resolve this issue, and
2) Re-implement the code accordingly.
```java
class Cube{
  double sideLength;

  public Cube(double sideLength) {
    this.sideLength = sideLength;
  }

  public double computeSurfaceArea() {
    return 6*Math.pow(sideLength, 2);
  }

  public double computeVolume() {
    return Math.pow(sideLength, 2);
  }

  public double computeSurfaceAreaToVolumeRatio() {
    return computeSurfaceArea()/computeVolume();
  }
}

class Ball{
  double radius;

  public Ball(double radius) {
    this.radius = radius;
  }

  public double computeSurfaceArea() {
    return 4 * Math.PI * Math.pow(radius, 2);
  }

  public double computeVolume() {
    return 4 * Math.PI * Math.pow(radius, 3)/3;
  }

  public double computeSurfaceAreaToVolumeRatio() {
    return computeSurfaceArea()/computeVolume();
  }
}
```
#### <u>Q6 Answer</u>
We have to make use of the Template Method design pattern to fix the duplication issue.
The following code utilises this design pattern.
```java
abstract class ThreeDimensionalShape{
  public abstract double computerSurfaceArea();

  public abstract double computeVolume();

  public double computeSurfaceAreaToVolumeRatio() {
    return computeSurfaceArea()/computeVolume();
  }
}

class Cube extends ThreeDimensionalShape{
  double sideLength;

  public Cube(double sideLength) {
    this.sideLength = sideLength;
  }

  @Override
  public double computeSurfaceArea() {
    return 6*Math.pow(sideLength, 2);
  }

  @Override
  public double computeVolume() {
    return Math.pow(sideLength, 2);
  }
}

class Ball extends ThreeDimensionalShape{
  double radius;

  public Ball(double radius) {
    this.radius = radius;
  }

  @Override
  public double computeSurfaceArea() {
    return 4 * Math.PI * Math.pow(radius, 2);
  }

  @Override
  public double computeVolume() {
    return 4 * Math.PI * Math.pow(radius, 3)/3;
  }
}
```

#### <u>Additional Notes</u> <a id="additional-notes"></a>
**Top down Integration Testing:**
- Pros: Can be parallelized
- Cons: Requires a lot of stubs

**Bottom Up Integration Testing:**
- Pros: Does not require any stubbing
- Cons: Has to be done in sequence — from low level to high level