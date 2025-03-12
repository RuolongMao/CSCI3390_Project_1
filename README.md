## Bitcoin mining with Spark
The key to mining Bitcoin is to solve a puzzle involving the SHA-256 hash function, where SHA stands for "security hash algorithm" and 256 denotes the output of the hash function as having 256 bits (or, equivalently, a 64-digit hexadecimal number). Given a Bitcoin header string `S`, the puzzle is to find a positive integer `x` (called "nonce") such that the concatenation `xS` is hashed to a hexadecimal number with `k` leading zeros. The parameter `k` is known as the difficulty of the puzzle. The actual Bitcoin difficulty is currently 11. [Here](https://emn178.github.io/online-tools/sha256.html) is a working exhibit of an SHA-256 calculator.  

Let's look at an example of the mining process to clarify. Say we hash the string `this_is_a_bitcoin_block` with the SHA-256 function, which produces `5de97c4b0b4fd55c033fb1de4723de24b8fea9c6caa09af43008e0412ee2847a`. Now, we set the nonce to 20 and prepend it to the string, giving us `718507355this_is_a_bitcoin_block`. The new string hashes to `0436786C81ABE313C104D821BE4EE96D3A88C3A3B93C54C8BBED9C6EE25DB8D3`, thus solving the puzzle for `k = 1`. Subsequently, a value of 457 for the nonce solves the puzzle for `k = 2` since `457this_is_a_bitcoin_block` hashes to `004306ef8f43e38fb17bce7cb96e568ed904e334dafb3cd69568a27ac564e08c`.  

Your mission (and yes, you have to accept it) is to run **project_1** with Spark to determine the nonce for varying difficulties of `k` with one of the following strings:
```
// If you're working in a group of three
this_is_a_bitcoin_block_of_yourEagleId1_and_yourEagleId2_and_yourEagleId3

// If you're working in a pair
this_is_a_bitcoin_block_of_yourEagleId1_and_yourEagleId2

// If you're working alone
this_is_a_bitcoin_block_of_yourEagleId
```

The program accepts three parameters: the header string `S`, the difficulty `k`, and the number of trials `n`. For each trial, it will generate a random number between 1 and 2<sup>32</sup>-1 for the nonce `x` and hash `xS`. The program distributes the `n` trials evenly among the compute nodes and executes them in parallel. If a valid nonce is found for difficulty `k`, `xS` as well as its hash value will be outputted.  

Pass in the arguments by appending them to the `spark-submit` command you ran earlier. For example:
```
// Linux
spark-submit --class project_1.main --master local[*] target/scala-2.12/project_1_2.12-1.0.jar this_is_a_bitcoin_block_of_12345678 2 100

// Unix
spark-submit --class "project_1.main" --master "local[*]" target/scala-2.12/project_1_2.12-1.0.jar this_is_a_bitcoin_block_of_12345678 2 100
```
In GCP, simply include the arguments in the **Arguments** field of the job you're submitting.  

## Reporting your findings
You'll be submitting a report along with your code that provides commentary on the tasks below.  

1. **(4 points)** Run the program on your local machine to solve cases `k = 2,3,4,5,6`. For each `k`, provide `xS`, its hash value, the total time elapsed, and the number of trials.  
2. **(3 points)** Run the program on GCP to solve the case `k = 7`. Provide `xS`, its hash value, the total time elapsed, and the number of trials. Describe your cluster's configuration (number of machines, number/type of cores, etc.) and your process for estimating the number of trials needed in order to find the nonce.  
3. **(3 points)** Modify **one** line of code in **src/main/scala/project_1/main.scala** so that the program generates the potential nonce from 1 to `n` (the number of trials) instead of randomly. Discuss whether or not this is more efficient than the randomized approach.

## Submission via GitHub
Delete your project's current **README.md** file (the one you're reading right now) and include your report as a new **README.md** file in the project root directory. Have no fear—the README with the project description is always available for reading in the template repository you created your repository from. For more information on READMEs, feel free to visit [this page](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/about-readmes) in the GitHub Docs. You'll be writing in [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown). Be sure that your repository is up to date and you have pushed all changes you've made to the project's code. When you're ready to submit, simply provide the link to your repository in the Canvas assignment's submission.

## You must do the following to receive full credit
1. Create your report in the ``README.md`` and push it to your repo
2. In the report you must include your full name and your partner's full name, in addition to collaborators
3. Submit a link to your repo on the canvas assignment

## Late Submission Penalties
Please refer to the course policy.


# Group Project 1 - Bitcoin Mining with Spark
## Group 7
## Member: Hoiting Mok, Ruolong Mao

### Q1: Run the program on your local machine to solve cases k = 2,3,4,5,6. For each k, provide xS, its hash value, the total time elapsed, and the number of trials.

**Hash String Used:** `this_is_a_bitcoin_block_of_35641890_and_45459077`

| k  | xS | Hash Value | Time Elapsed | Number of Trials |
|----|-----------------------------------------------|------------------------------------------------------------------|--------------|----------------|
| 2  | 1584511766this_is_a_bitcoin_block_of_35641890_and_45459077 | 006e73d833eaef0adaa6e0d0a094376c4fdf519e4d96b0aed3daf697464c40da | 2s | 100 |
| 3  | 335471923this_is_a_bitcoin_block_of_35641890_and_45459077  | 000811dc0226bde01964623f22f37b2c391e8910e046ecf73dc8af6c0b93fa74 | 2s | 600 |
| 4  | 988540602this_is_a_bitcoin_block_of_35641890_and_45459077  | 0000303fd42f3a8ddfa253b3b7e62a19861c791cc7190da07238e31f7cbb8ff2 | 2s | 65,536 |
| 5  | 1016176955this_is_a_bitcoin_block_of_35641890_and_45459077 | 00000b8c5bc0e2a71c7d226d34cb890cdb71542ec9a3127a256736e14f4272e3 | 2s | 400,000 |
| 6  | 1231536268this_is_a_bitcoin_block_of_35641890_and_45459077 |  000000c21d5c0f18bc90fa434a7ae396dcb26df6b47c910b7e13cafe08c245d2 | 5s | 4,000,000 |

#### Commentary
Toward the earlier, smaller values of k, we were able to solve the cases using the example command (k=2, n=100) and gradually incrementing the number of trials in multiples of 100 (k=3, n=600). Every case with their corresponding number of trials are tested on our two machines with at least three times of success respectively. However, starting from k=4, the required number of trials increased significantly. Finally, for k=6, we used 4,000,000 trials and observed an increase in computation time. This is because the difficulty of finding a qualified hashed number from the SHA-256 hash family increases exponentially as k goes larger.

---

### Q2:

**Cluster Configuration:**
- Number of machines: **2**
- Machine type: **n2-standard-4**

**Results for k=7:**
- **xS:** `679105772this_is_a_bitcoin_block_of_45459077_and_35641890`
- **Hash Value:** `00000003dfd79803a7b5d6ea1174db10b8e2b89ef402dc316d3e2dab5d19d05f`
- **Time Elapsed:** **803s**
- **Number of Trials:** **268,435,456 (2^28)**

#### Commentary
To estimate the required number of trials for k=7, we explored on the nature of SHA-256 hashing. 
Each hexadecimal digit in an SHA-256 hash represents 4 bits and has a \( \frac{1}{16} \) probability of being zero. Since the first \( k \) hex digits must all be zero, the probability of this occurring is:

\[
\left(\frac{1}{16}\right)^k = \frac{1}{2^{4k}}
\]

This means that, on average, \( 2^{4k} \) trials are required to find a valid nonce.
The GCP cluster allowed us to handle this computation efficiently within **803 seconds**.

---

### Q3: Code Modification and Efficiency Analysis

**Original:**
```scala
val nonce = sc.range(0, trials).mapPartitionsWithIndex((indx, iter) => {
  val rand = new scala.util.Random(indx + seed)
  iter.map(x => rand.nextInt(Int.MaxValue - 1) + 1)
})
```

**Modified:**
```scala
val nonce = sc.range(1, trials)
  //val rand = new scala.util.Random(indx + seed)
  //iter.map(x => rand.nextInt(Int.MaxValue - 1) + 1)
```
