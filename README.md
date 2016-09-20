# Meta-Heuristics
A C# Library that makes implementing Meta-Heuristic Algorithms easy peasy. Meta Heuristic Algorithms have common properties. This Project is aimed at providing classes and interfaces that best abstract these properties and provide an easy to comprehend structure to make implementing new meta-heuristic algorithms easy.

This project has been moved [here](https://github.com/mykeels/Extensions/tree/master/Extensions/Heuristics/Meta), and is being worked on as a part of the [Extensions C# Library](https://github.com/mykeels/Extensions).

### Getting Started

You will need Visual Studio 2012 or later and Microsoft .NET Framework 4.0 or a later version.

Add a reference to Extensions.DLL in your project

Add a `using` statement to the class you intend to work with e.g.

```cs
using Extensions.Heuristics.Meta;
```

Choose a Solution Representation Data Type. This can be any data type such as char[], string, int[], byte[], etc. But this should adequately represent your solution.

Provide method declarations for the neccessary properties of the [Configuration Object](https://github.com/mykeels/Extensions/blob/master/Extensions/Heuristics/Meta/Configuration.md) for the Meta-Heuristic Class you intend to use. The Meta-Heuristic Class could be any of the ones already implemented, or one you have implemented yourself which implements the IMetaHeuristic Interface.

Create a new Instance of the Meta-Heuristic Class you intend to use, pass the Configuration Object to it using its `Create` Method, then use either `FullIteration` to perform the Optimization Sequence or `SingleIteration` if you require more control over the Algorithm.

### Example

We shall use the [8-Queen Chess Problem](https://en.wikipedia.org/wiki/Eight_queens_puzzle), one of the [Meta-Heuristic Problems](https://github.com/mykeels/Extensions/tree/master/Extensions/Heuristics/Meta/Problems) considered in the code tests.

In the [Eight Queen Chess Problem](https://mykeels.github.io/Eight-Queen-Solution-Test/), we are to fit Eight (8) Queens on a Chess Board in such a way that no queen threatens another. A good solution example is:

![](https://mykeels.github.io/Eight-Queen-Solution-Test/IMG/eight-queens-solution.PNG)

To Solve the problem, we shall follow these steps:

- First, let's consider our solution representation. An obvious way is an 8x8 two-dimensional array, perhaps with boolean values or integers in each array item. A `true` or `1` value would represent a Queen's presence in the tile (array item) while a `false` or `0` shows that no queen is present. E.g.
```json
[
[1, 0, 0, 0, 0, 0, 0, 0],
[0, 0, 0, 0, 0, 0, 1, 0],
[0, 0, 0, 0, 1, 0, 0, 0],
[0, 0, 0, 0, 0, 0, 0, 1],
[0, 1, 0, 0, 0, 0, 0, 0],
[0, 0, 0, 1, 0, 0, 0, 0],
[0, 0, 0, 0, 0, 1, 0, 0],
[0, 0, 1, 0, 0, 0, 0, 0]
]
```

  However, this leaves room for a lot of redundancy, as there'll always be at least 56 empty tiles. Talk about waste, huh? Perhaps a more efficient solution representation is a one-dimensional array of integers or bytes where its indices represent the column index of a queen in the two-dimensional array described above, and the values in each index represent the row index of the queen.
  
  ```json
  [6, 2, 1, 4, 5, 3, 7]
  ```
  
  ```cs
  byte[] arr = new byte[8]; // our solution representation
  ```
  
  With the above described solution representation, no tile is wasted and each tile gives information about the position of a unique queen on the chess board
- Next, we shall consider the function to generate an initial solution. The randomness of an initial solution goes a long way in determining the outcome of a meta-heuristic optimization. We shall set the values of every item in the solution array to a random integer between 0 and 7. Of course, this will result in a solution with a probability that some queens will threaten each other.

```cs
public static byte[] GenerateNewCandidateSolution()
```

- We will require a way to score how well a solution meets the objective. This is generally known as the objective cost or fitness function as it also indicates the quality or fitness of a solution.
  In this problem, a good fitness function would be to count the number of queens that threaten in other in a solution.
  
  ```cs
  public static double GetSolutionFitness(byte[] queens)
  ```

- We also need a way to change/tweak an existing solution in hopes of finding a better solution. This is called finding the neighbor of a candidate solution.

  ```cs
  public static byte[] FindNeighbor(byte[] queens)
  ```
  
  - It is also important to provide a means to create an exact replica of a candidate solution. This is important since C# uses a reference system which works just like pointers, so as to prevent working on the same solution multiple times when uses a population based meta-heuristic. This is refered to as the Clone Function.
  
  ```cs
  public static byte[] Clone(byte[] queens)
  ```
  
  With this, you can now create your Configuration Object:
  
  ```cs
  public static Configuration<byte[]> GetConfiguration()
  {
      Configuration<byte[]> config = new Configuration<byte[]>();
      config.CloneFunction = EightQueens.Clone;
      config.InitializeSolutionFunction = EightQueens.GenerateNewCandidateSolution;
      config.MutationFunction = EightQueens.FindNeighbor;
      config.NoOfIterations = 1500;
      config.ObjectiveFunction = EightQueens.GetSolutionFitness;
      config.SelectionFunction = Selection.RoulleteWheel;
      return config;
  }
  ```
  
  You can learn more about the configuration object [here](https://github.com/mykeels/Extensions/blob/master/Extensions/Heuristics/Meta/Configuration.md) ...
  
  For this example, let's make use of Hill Climbing, perhaps the easiest to understand and implement Meta-Heuristic out there in solving our problem.
  
  ```cs
  HillClimb<byte[]> hc = new HillClimb<byte[]>();
  hc.Create(GetConfiguration());
  byte[] food = hc.FullIteration();
  ```
  
  With luck, `food` should contain a solution if perfectly solves the Eight Queens chess problem. If it doesn't, then it should be a solution that is pretty close to solving it.
  
  For more Examples, see our [Meta-Heuristics Problem Page](https://github.com/mykeels/Extensions/tree/master/Extensions/Heuristics/Meta/Problems).
  
### The Future
  I aim to solve more challenging problems using this Library. The more problems are solved, the more confidence we can have in Meta-Heuristics.
  
### Contributions
  It'll be interesting to see Forks of this Library, and hear your suggestions.
  
Thanks! Ikechi Michael I.
