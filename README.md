# Programming challenge - The Paint Shop

This project is an answer to the 'Paint Shop' programming challenge.

```
mvn package -DskipTests=true
cd target
java -jar paint-shop-1.0-SNAPSHOT.jar src/test/resources/example1.txt
```

## Definition of the problem

> You run a paint shop, and there are a few different colors of paint 
you can prepare. Each color can be either "gloss" (G) or "matte" (M).
>
> You have a number of customers, and each have some colors they like, either gloss or matte. No customer will like more than one color in matte.
>
> You want to mix the colors, so that:
> * There is just one batch for each color, and it's either gloss or matte.
> * For each customer, there is at least one color they like.
> * You make as few mattes as possible (because they are more expensive).
>
> Your program should accept an input file as a command line argument, and print a result to standard out.
> An example input file is:
> ```
> 5
> 1 M 3 G 5 G
> 2 G 3 M 4 G
> 5 M
> ```
>
> The first line specifies how many colors there are.
>
> Each subsequent line describes a customer.  For example, the first
> customer likes color 1 in matte, color 3 in gloss and color 5 in gloss.
>
> Your program should read an input file like this, and print out either
> that it is impossible to satisfy all the customer, or describe, for
> each of the colors, whether it should be made gloss or matte.
>
> The output for the above file should be:
>
> `G G G G M`
>
> ...because all customers can be made happy by every paint being prepared as gloss except number 5

## Analysis of the problem

This is an example of a [Constraint Satisfiability Problem](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem).
It could be defined as following.

Let us call:
 ```
X1, X2, .., Xn the paints 1, 2, ..., n in Gloss finish,
```
and
```
¬X1, ¬X2, ..., ¬Xn the paints 1, 2, ..., n in Matte finish.
```

The example above in the definition of the problem could be defined as:

```
n = 5
(¬X1 ∨ X3 ∨ X5) ∧ (X2 ∨ ¬X3 ∨ X4) ∧ (¬X5)
```

We could try to find ways to simplify (reduce) the expressions and
explore [SAT solvers](https://en.wikipedia.org/wiki/Category:SAT_solvers)
but here, the best feasible strategy seems more to use a search
algorithm.

## Implementation
I decided to use a basic search strategy: we just generate all
possible combinations of paints and test each against all the consumer
tastes.
We then return the first cheapest found.


One easy way to improve the search is to try to sort the customer tastes
from the most restrictive to the least restrictive in order to rule out
a paint batch as early as possible.
Class `SearchPaintShopSolver` sorts the customer tastes by
`CustomerTaste::count` for that reason.

### Time Complexity analysis
Let's assume we have `n` paints and `k` customers.
Generating all combinations takes 2^n, so a complexity of `O(2^n)`.
For each combination, we iterate over each of the `k` customers.
So, we're at `O(k.2^n)`.

I don't think it's worth including the complexity due to the
number of paints into the customer tastes, as we'll have to make
assumptions (e.g. in average a customer likes `1/n` paints) that are:
1/ based on no data, 2/ not necessary relevant.

We could add the sorting of the solutions into the equation.
It looks like the `stream.sorted()` sort implementation of
non-already-sorted streams
(the case for the moment, and maybe an area of improvement) is a
[Timsort](https://en.wikipedia.org/wiki/Timsort), which has a wort case
of `O(n log n)`.
We could make some assumptions and add-up this part but it would not be
much relevant.

Overall time complexity is made by the core problem solving: `O(k.2^n)`.

### Space Complexity analysis
This analysis is very close to the time complexity: `O(k.2^n)` for the
code problem solving.
The Timsort Worst-case space complexity is `O(n)`.

So, again, a global complexity of `O(k.2^n)`.


## Getting Started

You can run directly:

```
mvnw package -DskipTests=true
cd target
java -jar paint-shop-1.0-SNAPSHOT.jar src/test/resources/example1.txt
```

You can also use your IDE and run the main class
`me.david.paintshop.PaintShop` directly.

### Prerequisites

This project requires a JDK 1.8.
It is a Maven project. You need a valid `mvn` installation or you may
use the provided Maven wrapper `mvnw` (which requires a `JAVA_HOME`
environment variable pointing to a JDK 1.8 home directory.

## Running the tests

You can just run the suite of tests using `mvn clean test` or use
your IDE.

## Building and Running

```
mvnw clean package
java -jar target/paint-shop-1.0-SNAPSHOT.jar src/test/resources/example1.txt
```

## Deployment

Manual deployment can be done by copying folder `lib` and jar file
`paint-shop-*.jar` from `target` folder.

## Built With

* [Maven](https://maven.apache.org/) - Dependency Management


## Authors

* **David Sanches** - *Initial work*

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Wikipedia - Constraint satisfaction problem [https://en.wikipedia.org/wiki/Constraint_satisfaction_problem]
* [Peter Norvig](http://norvig.com/), [Artificial Intelligence: A Modern Approach](http://aima.cs.berkeley.edu/)

