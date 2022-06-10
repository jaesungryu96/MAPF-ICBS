# Multi-Agent Path Finding - Improved Conflict Based Search 

Improved CBS (ICBS) is an extension of the Conflict Based Search algorithm developed for the purpose of finding optimal paths for multiple agents on any given map without collisions. ICBS was presented as an extension of CBS at the IJCAI-15 Conference which incorporates several previously found improvements to CBS to accumulate their benefits.[^1] The algorithm has many practical applications, especially in the field of autonomous robotics. 

## About Our Project ##
In our version of improved CBS, we include an additional improvement to ICBS regarding the method in which collision-based constraints are created. A promising, new method for CBS-based MAPF has emerged called *disjoint splitting* which was published in May of this year (2021).[^2] Our goal was to combine the established improvements of ICBS with the up-and-coming method of constraint generation. Please see <a href="#background"><strong>Background</strong></a> below or our more detailed [report](final_report.pdf) on the subject for more information.

<!-- ![demo instance](code/demo/fig21.gif) -->
<img src="code/demo/fig21.gif" alt="demo" width="600"/>

*Animation Program for MAPF provided by EAAI, please refer to acknowledgements*


## Installation & Usage ##
### Dependencies ###
Use dependency manager pipenv
```bash
pipenv install --dev
```
### Running the Program ###
Within the `code` directory...
```bash
cd code
python3 [--instance <path>] [--solver <solver>] [--disjoint] [--batch]
```
The flags
* `--disjoint` CPS will use *disjoint splitting* rather than the default *standard splitting*
* `--batch` will run all instances available in `code/instances`

The following are options for `--solver`
* `CBS` the original CBS algorithm
* `CBS_CB` version of CBS with Prioritizing and Bypassing Conflicts
* `ICBS` Improved CBS with all improvements

To use the `test_1.txt` instance available in `code/instances` with ICBS with disjoint splitting...
```bash
python3 --instance "instances/test_1.txt" --disjoint --solver ICBS
```
<a name="background"></a>
## Background - CBS ##
Originally developed by a dedicated team of MAPF researchers at the AAAI Association, Conflict Based Search (CBS) is a two-level algorithm that guarantees optimal paths for each agent in a map internally represented as a graph. At the higher-level, CBS generates a constraint tree (CT) and conducts a search on the tree based on conflicts between agents. At the lower-level, a search is conducted to find an optimal path for an agent with a set of given constraints. The low-level search is typically conducted by a variant of the A* algorithm.

CBS resolves conflicts by generating constraints for the agents involved in the collision. For any specific collision, constraints may be generated for the paths of agents involved, to prevent the collision from occurring. Each node on the CT maintains a set of constraints from any previous collisions found by its predecessors and a set of paths consistent with the current set of constraints. Given the paths, a new conflict can be chosen to be resolved with additional constraints. Therefore, each node generated in the CT aims to resolve a specific conflict found in its current set of paths until an optimal set of collision-free paths is found, concluding our search.[^3]

## Improvements to CBS ##

#### Prioritizing and Bypassing Conflits ####
When choosing a conflict to resolve, the priority in which conflicts are chosen should be determined by whether it increases the cost of the solution. A conflict *C* may be classified as a *cardinal*, *semi-cardinal* or *non-cardinal* conflict. If as a result of the conflict, the length of the paths for both agents increase, *C* is a cardinal conflict. If only one of the agent's paths increases, *C* is a semi-cardinal conflict, and if neither paths increase it is considered non-cardinal.[^1]


If the conflict *C* is not cardinal, there is a chance that the paths generated by one of the child nodes could replace the solution in the current node without needing to generate all the child nodes. If one of the child nodes can successfully avoid conflict *C* without increasing the cost of the paths or introducing more conflicts than the current node, the current conflict *C* can be *bypassed*, therefore reducing the size of the CT.[^4]

#### Meta-Agents with Merge and Restart ####
Although, CBS is typically much more efficient than if its lower-level counterpart A* were to search without high-level guidance, there are some instances where using CBS is less efficient. If collisions occur often in a pair, or a small group, of agents, it becomes more efficient for the lower-level algorithm to search their paths as a group of agents, called the *meta-agent*. In Meta-Agent CBS, frequently conflicting agents are merged into a new meta-agent if the number of collisions is above a threshold, *B*, for MA-CBS(*B*).[^5]

To prevent repeated merging of the highly conflicting agents elsewhere in the CT, when a merge occurs at node *N*, the current CT is discarded and the search restarts with node *N* as the root node. This procedure is called *Merge and Restart*.[^1]

#### Disjoint Splitting with CBS ####
The standard method of 'splitting' a conflict into constraints creates two child nodes with a negative constraint for each agent prohibiting it from being at the location when the collision occurred. Within the two sets of paths where neither of the agents are permitted to be at the site of conflict, duplications may occur. However, a more efficient method has been discovered in which one agent is chosen to receive constraints. The agent is given a *positive* constraint, forcing the agent to carry out the traversal which led to the collision, effectively creating a negative constraint for all other agents. The same agent is also given a negative constraint in the second child node. This *disjoint* method of splitting constraints is efficient at pruning nodes that would have introduced duplicate searches.[^2]

## Acknowledgements
Code templates and guidance for implementing original CBS and all visualization tools (including the one above) for the pathfinding solutions are provided at the EAAI archive of [Model Assignments](http://modelai.gettysburg.edu/). Thank you to our Professor, Dr. Ma for introducing us to the resources we needed to make this project possible.

### References ###

[^1]:	E. Boyarski et al., “ICBS: The Improved Conflict-Based Search Algorithm for Multi-Agent Pathfinding”, in SOCS, 2015.
[^2]: J. Li, D. Harabor, P. J. Stuckey, A. Felner, H. Ma, and S. Koenig, “Disjoint Splitting for Multi-Agent Path Finding with Conflict-Based Search”, ICAPS, vol. 29, no. 1, pp. 279-283, May 2021.

[^3]: G. Sharon, R. Stern, A. Felner, and N. R. Sturtevant, “Conflict-based search for optimal multi-agent pathfinding,” Artificial Intelligence, vol. 219, pp. 40–66, 2015.

[^4]: E. Boyrasky, A. Felner, G. Sharon, and R. Stern, “Don’t Split, Try To Work It Out: Bypassing Conflicts in Multi-Agent Pathfinding”, ICAPS, vol. 25, no. 1, pp. 47-51, Apr. 2015.

[^5]: G. Sharon, R. Stern, A. Felner, en N. R. Sturtevant, “Conflict-based search for optimal multi-agent pathfinding”, Artificial Intelligence, vol 219, bll 40–66, 2015.
# CCBS
