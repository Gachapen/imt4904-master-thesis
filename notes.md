# Master Thesis Notes

# 2017-11-21
- Found statistical significant positive correlation between human and technical ranking
  - Used geometric mean aggregation (Group Aggregation Techniques for Analytic Hierarchy Process and Analytic Network Process: A Comparative Analysis)
  - Then used Kendall's Tau (A Similarity Measure for Indefinite Rankings)

# 2017-11-20
- 1998Basak: If pairwise comparison is only done one way, it is called "reciprocal"
- https://link.springer.com/article/10.1007/s10726-015-9448-4#Sec11

## 2017-10-31
- The pairwise experiment only gives us a relative ranking, and thus only tests if people and the fitness evaluation agree. How do we test that the generator generates good plants? An absolute evaluation by the users?

## 2017-10-30
- Successfully ran experiment application with 8 >0.99 scored plants.
- Results from ranking:
  - Seems to be a correlation between realism and pleasure.
  - The best plant is far apart from the remaining plants
  - The 5th, 6th, 7th and 8th are the worst plants and are grouped close together.
  - The second, third and fourth plants are far apart from the best and the remaining plants, kinda like a "medium".
  - The second plant is significantly more realistic than pleasing, while the others have similar realism and pleasure.
- Analysis of the plants:
  - The worst plants seem to be shorter than the other plants,
    - with max segments at 2, compared to 3-5, and
    - growing more sideways.
  - The best plant has more leaves than the medium plants (~20 compared to ~10).
  - The best plant has two main branches, compared to only 1 in the medium plants (may cause more leaves).
  - The best plant is more curvy than the medium plants, with two of the medium being particularly straight.
    - The middle medium plant could be curvy, but does not have enough segments to show this.
  - Compared to the third plant, the second plant's realism is much better than its pleasure.
    - Subjectively I would say that the improvement in both metrics is caused by realistic-looking branching, but the straightness of the plant makes it less pleasing.
  - The second plant has branches not only at the bottom like the fourth plant has. The best also has branches along the main branch.
- Improving fitness:
  1. Put only the eight plants into the `model` directory.
  2. Print their initial scores.
  3. Implement fitness measure for
    - number of leaves (overall? When is it too much?),
    - height (in segments?),
    - proportions (grow more upwards),
    - curviness (how?), and
    - branching out from branches (could be improved by leaf metric?).
  4. Print the new scores.
  5. Iterate until technical ranking is similar to manual ranking.
- Added foliage evaluation for fitness that rewards plants with more leaves. The order after this is much more similar to the manual evaluation, but the third is now behind the seventh, meaning it is missing some other reward (curviness?)



## 2017-10-13
- Generated several plants with GE to be used for analyzing the fitness evaluation.
- Discovered that there was a fault in the parallel GE implementation, causing the GE to use a wrong cached fitness for an individual. Fixed this.

## 2017-10-12
- Implemented parallel GE
  - Parallelized tournament selection, gene duplication, crossover and mutation.
  - This was to more quickly find plant examples to use for analyzing the fitness evaluation.
- Discovered that duration comparison of GE and Random is NOT VALID.
  - GE duration is greatly underestimated (possibly 8 times faster than statistics show).
  - This is because GE and Random were parallelized in different ways.
    - GE measured the duration of one sample running on a single thread.
    - Random measured the duration of one sample running on ALL threads.
  - The new parallel GE (ge-par) implementation shows correct durations.
    - Comparing the new measurements (ge-par) in 'ge-comparison-2.csv' (with SA distribution), the GE is not longer slower than random, but rather FASTER.

## 2017-10-11
- Implemented heuristic plant visualization.
  - Brown branches and green leaves.
  - Thicker branches closer to the root.
  - Leaves at each segment with random rotation.
  - Greatly improves aesthetics (IMO)

## 2017-10-04
- Compared performance of random generation with GE.
  - Uniform grammar distribution.
  - 11 samples per method (random and GE)
  - Each random sample generated 160800 plants and extracted the best score.
  - Each GE sample ran 200 generations of 800 population size, 2 tournament size, 1.0 mutation rate, 0.5 crossover rate, and extracted the best score from the final population.
  - GE's performance is statistically different from random (both t and Wilcox tests, p < 0.05).
  - GE's mean is ~0.98, while random's mean is ~0.85.
  - Additionally GE runs faster than random. Most likely because GE doesn't have to evaluate all individuals (because of tournament selection), while random does.
  - Data in `ge-comparison.csv`
- Compared performance of random generation with GE using a non-uniform distribution.
  - Same method as above, just with grammar distribution found by SA: `distribution-sa-2017-09-07T09:15:45+02:00`
  - No difference between performance of GE and random.
  - Both methods means are ~0.99.
  - Random runs faster than GE. Most likely because the plants are faster to evaluate with this distribution, thus the extra work and bad parallelization shows in GE.
  - Data in `ge-comparison-2.csv`

## 2017-09-30
- Implemented and ran duplication-sampling program.
- Found that gene duplication (with pruning) worsens the performance.
  - Why is this? The GE paper recommends using them.
  - Maybe the implementation is wrong.
  - Does pruning by itself worsen the performance? It should not, as it only removes unused genes.
  - Does pruning interact with crossover and mutation? If it does, maybe a different combination of the rates of all three is better.


## 2017-09-29
- Implemented ge-recombination-sampling to sample different crossover and mutation rates.
- Found that the interaction between crossover and mutation have a significant effect on the performance (ANOVA), and that the best performances comes from a crossover rate of 0.5 and mutation rate of 1.0 (0.98 mean score and 0.04 SD).
- It seems to benefit the most from mutation, and higher mutation rates improves performance. Thus exploration is a very important aspect.
- It also benefits from crossover, but when the rate goes from 0.5 to 1, the performance decreases.
- Implemented gene pruning and duplication as genetic operators. Not yet tested.
- The fitness distributions of the populations seem to be approximately normally distributed with the addition of the bump at 0.

## 2017-09-28
- Re-implemented GA based on 1995Blickle with option to add custom genetic operators. This was to make sure that I could use the findings in their paper as a motivation for my choices.
- Chose to use tournament selection because we prefer time. (could optionally ALSO test ranking selection, as it supposedly is better but slower).
- Re-ran the ge-size-sampling for the new GA implementation and found new results.
  - It generally performed better than the previous implementation, but also slower (much larger selection size).
  - 800 population size and 200 generations performed the best and within a reasonable time. Bigger sizes do not improve performance, but are slower. Smaller sizes perform worse.
- Implemented and ran ge-tournament-sampling to sample tournament sizes
  - It stops at tournament size 8, after 2 and 4, and there is not statistically significant difference between them. Interestingly the duration is also very similar.
  - Thus, tournament size 2 will be used.

## 2017-09-26
- Interesting note from 1995Blickle: Applying to tournaments sequentially is equal to applying one tournament with a tournament size equal to the multiplicative of the two tournament sizes.
- Want to avoid premature convergence, so truncation selection is not good (why though? What's the benefit of premature convergence?).
- Do we want high or low selection variance? Don't know, so use tournament selection because it is in the middle?
- Tournament selection also has a low time complexity compared to the others.
- Ranking selection better for big problems as others may converge too early.
- Time vs quality, which do we prefer? Complex fitness calculation, so maybe time is important. Also it is meant for real-time applications, so time is actually quite important in the end...

## 2017-09-25

### GE
- GE with 3200 generations and 400 population size reaches an high average score and uses a reasonable amount of time. There is barely any gain by increasing the parameters any further, while the time cost increases. See 'ge-size-sampling.png'.

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
