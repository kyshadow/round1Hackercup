# round1Hackercup
Here are the solutions to the Hacker Cup 2017 Round 1 problems. If you had a rejected solution and want to find out where you went wrong, read on and download the official input and output!
The problems in this round were written by Jacob Plachta.



Pie Progress

Every day we want to buy the K cheapest pies, where K might be 0. Buying one pie incurs a $1 tax, buying two pies incurs a $4 tax, buying three pies incurs a $9 tax, and so on. Note that the marginal amount of tax added for each additional pie is $1, $3, $5, $7, ...
We can factor this tax into each pie. So if the five available pies on one day (sorted in non-decreasing order of price) cost $1, $1, $1, $2, and $4, we can instead say that, after tax, they cost $2, $4, $6, $9, and $13.
In order to eat a pie on the first night, we need to buy at least one pie on the first morning, so we should buy the cheapest of the available ones. For the second night’s meal, we either need to have bought the cheapest pie from the second day, or the second-cheapest pie from the first day, whichever is cheaper (after tax). In general, on day D we should buy the cheapest pie that we haven’t yet purchased from all days 1 through D.
We can maintain all of the available after-tax pie prices in a priority queue. Every day, we sort the current day’s pies by increasing cost, factor in the taxes, push all of these prices onto the priority queue, and then greedily purchase the pie that’s at the top of the queue (the cheapest one), popping it off.
There are O(N * M) pies, so adding them all to the priority queue over the course of the algorithm will take O(N * M log(N * M)) time.
It’s also possible to use dynamic programming with the following recursive formulation. Let f(D, P) be the minimum amount of money we need to spend to ensure that we can eat a pie every day given that we’re currently on day D, and we have P spare pies that haven’t been eaten. The answer is then f(1, 0).
f(D, P) = 0 if D + P >= N + 1
We have enough pies to eat already.
f(D, P) = the minimum of the following, for all p from 0 through M:
f(D + 1, P + p) + the cost of buying the p cheapest pies on day D
That is, we can try buying any amount of pies.
However, note that we must buy at least one pie if P = 0. In that case, p ranges from 1 through M instead.
Since there’s never a reason to be holding on to more than N pies, there are N^2 possible inputs to this function, and each output can be computed in O(M) time, so the total time complexity is O(N^2 * M).





Fighting the Zombies

Let D be the square on which we’ll cast the Destroy function-spell. The only goal of the Move function-spell will be to move as many other zombies into D as possible (while hopefully not moving any zombies out of D in the process), so that they can also be destroyed. The zombies which will get moved into D must themselves all lie within another square of side length R, as they otherwise wouldn’t all fit into D after being translated by the same offset. Let’s call that other square M. If we find two (possibly overlapping) squares M and D with side length R such that the number of zombies in the union of these squares is maximized, that must be an upper bound on the number of zombies we can possibly destroy.
It can be shown that, given any such pair of squares, it’s always possible to construct a circle which will encompass some or all of the zombies in M, such that after the Move function-spell, all of the zombies initially within either of the squares will end up inside D. This is based on the idea that a very large circle, with its center very far away from the squares, can effectively be used as a line to divide the points as needed.
When the two squares are disjoint, there are many lines to choose from which separate the two squares (even if they’re close together). So, we can draw a large circle which acts like one such line, such that it includes all the zombies from M and none from D. We then translate the zombies from M into D and destroy them all.


https://scontent.fsst1-2.fna.fbcdn.net/v/t1.0-9/15940794_1384016158297235_6325824452633493390_n.jpg?oh=615e772e17b24fa084e4b6fd9900fe92&oe=59140D10
Disjoint squares

When the two squares intersect, we can similarly draw a large circle which includes all of the zombies which are in M but not in D, which can then be translated such that they end up in D and are destroyed. Note that the circle may need to also include some zombies which are initially in D, but if it includes as few of them as possible, then they’ll still remain within D after being translated.


https://scontent.fsst1-2.fna.fbcdn.net/v/t1.0-9/15977779_1384016361630548_8940500870355050949_n.jpg?oh=b886c9da97c8b9843e64a762676894f4&oe=58D6D38F
Intersecting squares

Now, all we need to do is find an optimal pair of squares M and D. Without loss of generality, every candidate square has a zombie on its top edge, and a zombie on its left edge (we can always translate an optimal square such that this is true). So, every pair of zombies defines a candidate square, of which there are O(N^2). We can iterate over every pair of candidate squares, and count the number of the zombies in the union of each pair. This produces a simple O(N^5) solution, which is sufficient given the bounds. This time complexity can be improved to O(N^3 log N) by trying to fix M to be each of the O(N^2) candidate squares, and finding the corresponding optimal D square in O(N log(N)) time using a combination of a line sweep and a segment or interval tree.





Manic Moving

This problem can be solved using dynamic programming. Let f(R, P, H) be the minimum amount of gas required to complete all of the deliveries given that Wilson has R remaining sets of belongings to pick up, is currently in town P, and has H sets of belongings in his truck. Our answer is then f(N, 1, 0). We can use the following recursive formulation.
f(0, P, 0) = 0 
There are no more belongings to pick up or deliver.
f(0, P, H) = the length of the shortest path from P to the N-1th family’s destination, and then to the N-2th’s family’s destination if H = 2.
If we’ve picked up all of the belongings and just need to deliver that last 1 or 2 sets, then we do just that.
f(R, P, 0) = f(R - 1, S_i, 1) + the length of the shortest path from P to S_i, where i = N - R + 1 (the next family whose belongings are to be picked up).
If we have no belongings in the truck, we must pick up the ith family’s belongings next.
f(R, P, 2) = f(R, D_j, 1) + the length of the shortest path from P to D_j, where j = N - R - H + 1 (the next family whose belongings are to be dropped off).
If we have two sets of belongings in the truck, we must drop off the jth family’s belongings next.
f(R, P, 1) = the minimum of:
f(R - 1, S_i, 2) + the length of the shortest path from P to S_i (as above)
f(R, D_j, 0) + the length of the shortest path from P to D_j (as above)
If we have one set of belongings in the truck, we can either pick up the next family’s belongings, or drop off the current family’s belongings.
We can precompute the shortest distances between all pairs of towns by using the Floyd-Warshall algorithm, which takes O(N^3) time. We can then compute f(N, 1, 0) in O(K * N) time, as there are O(K * N) states and O(1) transitions from each state.
Other formulations are also possible. For instance, there are only a couple of possible values for P given R and H, so we can reduce the size of that dimension from O(N) to O(1), and complete the dynamic programming portion in just O(K) time.





Beach Umbrellas

Let’s first consider a variation of the problem in which the umbrellas’ circles are required to fit entirely within a specified interval, as opposed to only their centers being constrained by it. In this case, if the overall interval has a length of L, and the sum of the lengths of the umbrellas’ intervals is S = sum{ i = 1..N | 2R[i] },  then they can’t be placed at all if S > L. Otherwise, the only thing that matters is that there’s L - S empty space left over, which can be distributed in any fashion across the N + 1 gaps before/between/after the umbrellas. Assuming that the left-to-right order of the umbrellas is fixed, there are then C(L - S + N, N) valid combinations of positions for them, where C(n, k) is the choose (binomial coefficient) function.
The overall problem differs from this variation in that only the centers of the umbrellas must fall within the interval [1, M], not necessarily the entire umbrellas. In particular, parts of the leftmost and rightmost umbrellas may extend past the end of this interval. However, if we choose to place umbrellas i and j as the leftmost and rightmost ones, then we can convert the situation into the variation described above by pretending that the overall interval is actually [1 - R_i, M + R_j] — in other words, L = M + R_i + R_j - 1. At that point, the leftmost and rightmost umbrellas are fixed, and the remaining N - 2 umbrellas can be permuted in any order. As such, there are (N - 2)! * C(L - S + N, N) ways to place the umbrellas for that particular pair (i, j).
Note that the only dependence of the above formula on i and j is in the inclusion of R_i + R_j in the value of L. Therefore, if we let P_k be the number of ordered pairs (i, j) such that R_i + R_j = k (which can be easily tallied up in O(N^2) time), and if we let K be the largest radius of any umbrella, then the final answer will be sum{ k = 2..2K | P_k * (N - 2)! * C(M + k - S + N - 1, N)).
There are O(K) terms in the above summation. The remaining question is how exactly their values can be computed, and how efficiently. For starters, we should precompute the factorials from 0! up to N! (modulo 10^9 + 7), in O(N) time. Still, computing the required binomial coefficients C(n, k) may be tricky, as n will be in O(M). However, k will only be in O(N), and thanks to the modulus being a prime larger than n, C(n, k) modulo 10^9 + 7 can be computed in O(k) time as prod{ i = (n - k + 1)..n | i } / k!. In order to perform this division modulo 10^9 + 7, the extended Euclidean algorithm can be used to calculate the modular multiplicative inverse of k!. In total, then, this algorithm has a time complexity of O(N^2 + N * K), where K is again the largest radius of any umbrella.
Finally, note that the above solution revolves around fixing the leftmost and rightmost umbrellas to place, which doesn’t apply nicely if there’s only one umbrella. However, if N = 1, the answer is simply M.
