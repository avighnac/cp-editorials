https://codeforces.com/contest/1915/problem/F

The sum of the total greetings is equal to half the sum of the number of times each person greets another person (we're dividing by two because if $a$ greets $b$, then $b$ also greets $a$).

Let's analyze the greetings of an arbitrary person. Let this person's starting and ending points be $a_i$ and $b_i$ respectively.

When will this arbitrary person greet another person with starting and ending points $a_j$ and $b_j$ respectively?

Well, to begin with, if $[a_i, b_i] \, \cap [a_j, b_j] = \varnothing$, persons $i$ and $j$ never meet.

Let $|x| = b_x - a_x$. If $|i|=|j|$ then $i$ and $j$ never meet, as the relative distance between them never decreases, since they start walking at the same time, walk the same distance, and stop walking at the same time.

Let's now look at two cases:
### Case #1: $[a_i, b_i] \, \cap [a_j, b_j] \neq \varnothing, a_i < a_j$
Colloquially, this means that $i$ starts to the left of $j$. Let us further split this into two cases.

If $|i| < |j|$, then $i$ will finish their journey before $j$ does. Notice that while both of them are in motion, the distance between them remains constant, as both of them move one unit to the right per second. It begins to decrease only when one of them finishes their journey. $i$ is to the left of $j$ before the journey starts, this will still be true after $i$'s journey finishes. After this, $j$ continues moving to the right, and so they never meet.

If $|i|>|j|$, then $j$ will finish their journey first. $i$ will only be able to greet $j$ if $b_i > b_j$ ($i$'s journey ends at a further point than $j$'s journey). In other words, $[a_j,b_j] \in [a_i,b_i]$.

### Case #2: $[a_i, b_i] \, \cap [a_j, b_j] \neq \varnothing, a_j < a_i$
$j$ starts to the left of $i$.

If $|i|>|j|$, then $j$ finishes their journey first. $i$ continues moving to the right, and they never meet.

If $|i|<|j|$, then $i$ finishes their journey first. $j$ will only be able to greet $i$ if $b_j > b_i$. In this case, $[a_i, b_i] \in [a_j, b_j]$.

This reduces the problem to the classic problem of counting how many ranges are contained within each range in a set of ranges. Once this is found, the answer is simply the sum of all $x_i$ where $x_i$ is the number of ranges $[a_j, b_j] \in [a_i, b_i]$. This works since even though $i$ is the smaller range in the second case, each greeting is counted twice, once from the perspective of $i$ and once from the perspective of $j$. We're only summing all of the first case, and since either only $[a_i, b_i] \in [a_j, b_j]$ or $[a_j, b_j] \in [a_i, b_i]$, each greeting is only counted once.