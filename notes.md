# Master Thesis Notes

## Supervisor meeting 2017-09-21
- Maybe calculate ratio between bumps in distribution samples generated with GE, and if they are similar between runs we can exclude extreme points (0) and run normal statistics on it.
- Can use current statistics as indication, not as proof.
- If randomness is important, population size is important.
- Population and generation size is kinda independent from selection.
- Estimate GE sizes first and freeze them.
- May bias towards size, because mutation is important.
- Take longest GE run from multiple runs with different sizes, then freeze the size.
- Since mutation is important, we can add more mutation methods.
- Can try different crossovers and mutations.
- Think of operators that retains the locality of the individual, but shakes it.
- Needs some motivation, at least some best effort.
- Make fair comparison of random vs GA, generate same amount of individuals.
- Leaves are important, but how should it be implemented? Options are having them part of the grammar, or adding them afterwards as a heuristic.
- Having them in the grammar may complicate the search.
- Having them as a heuristic is not a 'proper' LSystem.
- In any case, using the idea of leaves may be good for fitness evaluation.

#### Short term plan
What I should do is:
1. Sample population size and number of generations from 100 and upwards with doubling steps (x*2) and 20 samples each until the best score does not improve. Keep the last parameters that improved the score.
2. Experiment with other GA parameters with the sizes from the previous step. Maybe use a similar technique?
3. Find and think of different mutation methods that may keep the locality of the chromosome. Implement them and compare.
4. Look at the distribution of the GA runs to figure out if the statistics are applicable or not.
5. Run a new comparison between random and GA, using a population size of the GA population size + half the GA selection size * generations, and ANOVA.
6. Look at and plan how to deal with leaves and improved fitness.

## 2017-09-20

### Grammatical Evolution (GE)

##### Results
- GE works, with a 63% improvement (0.19 -> 0.82).
- The population seed in GE is an important factor (variation between GE runs is 0.17 SD (34%)).
- The number of generations is a big factor, and it should at least be ~1000.
- GE (with crossover and mutation) performs better than crossover or mutation alone, meaning that both crossover and mutation is playing important roles.
- The variance when doing only mutation is big, indicating that it is doing what it should (exploring the space).
- A large mutation rate (40%) is required for an effective evolution, showing that exploration is important.
- With an optimized grammar distribution (using SA), the seed population is much better performing, close to the result of GE runs on uniform distributions, but can not improve much.
- The variation is much lower with an optimized grammar distribution (0.03 SD, 6%), indicating that it is a more reliable starting point and that its ability to explore is limited.

##### Questions
- I have not confirmed that the data is normally distributed, and I doubt that it is because it has a tendency to create empty (score 0) plants even when its mean is good, so it might skew towards 0. Thus I wonder if the statistics may be giving a wrong image? For example, SD, SE, F-test etc. are all meant for normally distributed data. Should I analyze the distribution and run other statistical methods?
- Should I do a more systematic probing of the GE/GA parameters and selection methods? Currently I only did some experimental probing. I'm not sure how detailed I should go.

### Visual improvements

##### Idea
- I think leaves are important to have for a person to evaluate how aesthetically pleasing a plant is. A plant without leaves looks like a dead plant, which usually isn't very aesthetically pleasing. Additionally, many of the papers I've read use plants with leaves, so it puts my research closer to their level.
- Already add grammar for plant with fixed-shape leaves, fixed colors and branch shrinking. This new grammar should allow for plants like the pre-modelled bush to be generated.
- My idea is to run the whole generation process from scratch on the new grammar, i.e l1, l2, SA and GE, and then use this as the basis. Running the process shouldn't take long, but the difficult part is developing and testing new fitness measures for leaves and shrinking branches.

##### Questions
- What fitness measures should be implemented? I think I should look at the literature I've read (I have summarized them in notes) and use their methods as the baseline, then look at existing models such as the bush model to derive some more.


### Fitness improvements

##### Idea
- Using the current fitness measures, both good/natural looking plants (like the bush) and weird/artificial looking plants (like the very tall, thin, straight plants, or tiny plants) get good fitness scores. For example, the bush has a score of 0.96, while a very small Y-like plant (one stem, and two branches) has a score of 0.99. Obviously the fitness measure is not great, and should be improved.
- I should visually compare plants with good scores to find measures that can differentiate the good and bad among them.
- One idea is the plant growth. I see that the GE often finds plants that are not growing (no recursion in the LSystem), thus producing tiny plants with really good scores. To fix this, a score based on the LSystem recursion could be implemented.

##### Questions
- What improvements can be made to the fitness evaluation? Any ideas?
- How should the changes be evaluated? I guess manually be me (but somehow systematically), and then later by an experiment.
