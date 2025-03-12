## Group Project 1 - Bitcoin Mining with Spark
### Group 7
### Member: Hoiting Mok, Ruolong Mao
---
### Q1: Run the program on your local machine to solve cases k = 2,3,4,5,6. For each k, provide xS, its hash value, the total time elapsed, and the number of trials.

**Hash String Used:** `this_is_a_bitcoin_block_of_35641890_and_45459077`

| k  | xS | Hash Value | Time Elapsed | Number of Trials |
|----|-----------------------------------------------|------------------------------------------------------------------|--------------|----------------|
| 2  | 1584511766this_is_a_bitcoin_block_of_35641890_and_45459077 | 006e73d833eaef0adaa6e0d0a094376c4fdf519e4d96b0aed3daf697464c40da | 1s | 100 |
| 3  | 335471923this_is_a_bitcoin_block_of_35641890_and_45459077  | 000811dc0226bde01964623f22f37b2c391e8910e046ecf73dc8af6c0b93fa74 | 2s | 600 |
| 4  | 988540602this_is_a_bitcoin_block_of_35641890_and_45459077  | 0000303fd42f3a8ddfa253b3b7e62a19861c791cc7190da07238e31f7cbb8ff2 | 2s | 65,536 |
| 5  | 1016176955this_is_a_bitcoin_block_of_35641890_and_45459077 | 00000b8c5bc0e2a71c7d226d34cb890cdb71542ec9a3127a256736e14f4272e3 | 2s | 400,000 |
| 6  | 1231536268this_is_a_bitcoin_block_of_35641890_and_45459077 |  000000c21d5c0f18bc90fa434a7ae396dcb26df6b47c910b7e13cafe08c245d2 | 5s | 4,000,000 |

#### Commentary
Toward the earlier, smaller values of k, we were able to solve the cases using the example command (k=2, n=100) and gradually incrementing the number of trials in multiples of 100 (k=3, n=600). Every case with their corresponding number of trials are tested on our two machines with at least three times of success respectively. However, starting from k=4, the required number of trials increased significantly. The jump from k = 5 to k = 6 is most significant as the number of trials incrased by a factor of 10, and the time it takes for the computation to complete more than doubled. This reflects how the difficulty of finding a qualified hashed number from the SHA-256 hash family increases exponentially as k goes larger.

---

### Q2: Run the program on GCP to solve the case k = 7. Provide xS, its hash value, the total time elapsed, and the number of trials. Describe your cluster's configuration (number of machines, number/type of cores, etc.) and your process for estimating the number of trials needed in order to find the nonce.

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
Each hexadecimal digit in an SHA-256 hash represents 4 bits and has a `1/16` probability of being zero. Since the first k hex digits must all be zero, the probability of this occurring is:

**`(1/16)^k = 1 / 2^(4k)`**

So, on average, **`2^(4k)`** trials are required to find a valid nonce. Therefore, based on this thought, we chose our estimated number of trials for k=7 to be **268,435,456 (2^28)**
The GCP cluster allowed us to handle this computation efficiently within 803 seconds. 

---

### Q3: Modify one line of code in src/main/scala/project_1/main.scala so that the program generates the potential nonce from 1 to n (the number of trials) instead of randomly. Discuss whether or not this is more efficient than the randomized approach.

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
#### Commentary
We don't think it would be more efficient. In the original version, each partition initializes its own random number generator and produces nonces in a randomnized manner. In the modified version, we iterate through a sequential range of number that covers tha nonces in order. Since the hash function is randomnized, nonces should be tested in a way that also follows randomnization. Brute force searching for the solution should be randomnized for it to make sense. Also, by randomnizing, work is distributed more evenly across parallel workers. The sequential approach might lead to inefficient partitioning. Therefore, the modified approach would not be more efficient than the randomnized approach.
