### Unbounded Explained
It means that no matter how far in the direction of the gradient (or in the opposite direction if you are minimizing) you go, there will always be a better feasible solution than the one you already found. In this case the cost of the solution will go to +oo or -oo.

### Complete Slackness
if an inequality constraint holds with equality at the optimal point, the constraint is said to be binding, as the point cannot be varied in the direction of the constraint even though doing so would improve the value of the objective function.

<br>
This correspondence suggests that variables in one
problem are complementary to constraints in the other. We talk about a
constraint having slack if it is not binding. For an inequality constraint, the
constraint has slack if the slack variable is positive. For a variable constrained
to be non-negative, there is slack if the variable is positive. The term complementary
slackness refers to a relationship between the slackness in a primal
constraint and the slackness (positivity) of the associated dual variable. 

<br>
The complementary slackness conditions guarantee that the values of the
primal and dual are the same. In the diet problem, the pill seller guarantees
that pills are no more expensive than food. CS guarantees that when you solve
the problem, pills cost exactly the same as food for those foods that you actually
buy. (By the way, nothing rules out the possibility that there is a food that costs
exactly as much as its nutritional content, but that you don’t buy the food. If
this happens, you have no slack in both primal variable and dual constraint.)


### column generation
https://xijunlee.github.io/2017/10/12/%E4%BB%8E%E5%8D%95%E7%BA%AF%E5%9E%8B%E6%B3%95%E5%88%B0%E5%88%97%E7%94%9F%E6%88%90%E7%AE%97%E6%B3%95/
https://www.cnblogs.com/skabyy/p/13397869.html

### reference
* [1] https://www.quora.com/What-is-the-meaning-of-unbounded-in-linear-programming-problems What is the meaning of unbounded in linear programming problems?
* [2] https://en.wikipedia.org/wiki/Constraint_(mathematics) Constraint (mathematics)
