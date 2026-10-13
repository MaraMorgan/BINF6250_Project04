# Introduction
In this program, we implement genome assembly using de Bruijn graphs. Each read is broken into k-mers, and each k-mer forms a directed edge from its prefix to its suffix. The prefix and suffix are (k-1)-mers that act as nodes of the graph and reads that share (k-1)-mers become connected through the graph. This allows us to reconstruct the origianl sequence without comparing every read to eachother.
To assemble the sequence, we use an Eulerian walk, a path that visits every "edge" exactly once, while "nodes" may be re-visited. Each walk produces a contig, and we applied this to a small toy example as well as a yeast sequencing dataset.

De Bruijn graphs are used to determine overlaps between sequencing reads, with nodes as kmers and edges as the connections between them, 
and are commonly used on large datasets to this day. 



# Pseudocode
General structure:
1. Adding and removing edges
2. Constructing the graph
3. Finding the path
4. Reconstructing the sequence
5. Calculating the statistics
6. Outputting a FASTA file

   
```
add_edge
add right to the list of neighbors for left in self.graph
access self.graph[right] so right is a node even without outgoing edges
since build_graph is what loops through the edges, we only need to define what a left and right node would be
    self.graph left and append to right
    No return statement because we update attributes

remove_edge
    remove right from the list of neighbors in self.graph 
    No return statement because we update attributes


build_graph_from_reads
    for each read within our reads
        for each position i from 0 to len(read) - k
            take the k-mer starting at i
            left = the k-mer without its last letter (prefix)
            right = the k-mer without its first letter (suffix)
            call add_edge(left, right)
        no return statement


eulerian_walk
    if a seed is given, seed the random number generator
    Initialize the tour as an empty list
    #while the current node still has outgoing edge
        Select random position in the node's list of edges
        Remove that edge to get to the next node, but save its position
        Use recursion to call eulerian walk on next_node with the same graph (no new seed)
        add the list returned by the recursive call to the tour
    once the current node has no edges left, add it to the tour
    Return the tour as a list of (k-1)-mers traversed.


assemble_contigs
    if a seed is given, seed the random number generator
    make a full copy of the graph so it remains unchanged during the walk
    count the in-degree and out-degree of all the nodes
    initialize an empty list for the contigs
    round 1: for each node with more outgoing than incoming edges that still has edges
        run eulerian_walk from that node on the copy 
        reverse the tour
        convert the tour to sequence and add to contigs list
    round 2: for every node that still has edges left
        run eulerian_walk from that node on the copy
        reverse the tour
        convert the tour to a sequence and add to contigs list
    return the list of contigs



tour_to_sequence
    Establish that the tour is not empty otherwise return an empty string
    start from the beginning of the sequence with the first (k-1)-mer
    for each node in the tour after the first
        add the last letter to the sequence
    return the sequence
 

get_assembly_stats


write_fasta

```



Talking points for the successes and struggles section: DRAFT

# Successes
Adding and removing edges, and constructing the graph, was very straightforward. 

Conflicting schedules between all group members did not hinder effective communication and collaboration 

# Struggles
Once again, there were some slight hiccups with using GitHub, but we all get better with each project as we gain familiarity. 

The concept of recursion, thinking in object oriented programming

Confusion on handling duplicate edges 

Where to incorporate generators

# Personal Reflections
## Group Leader: 
### Mara
This was my second time as a group leader, and I feel it was easier to manage than the first time. 
My collaborators contributed some excellent considerations while we were talking through the concepts outlined in the lecture, particularly the Eulerian walk and recursion. 


## Collaborators:
### Katelyn
Our group worked together to finalize the pseudocode for `eulerian_walk`, and we all worked on completing the functions from there before comparing results to create the final code. The hardest part of this project for me was understanding recursion. At first, it didn't make sense that a function could call itself, but using an example helped me understand how the tour ultimately gets built in reverse. I also feel like this project helped me better understand how a graph can be stored as a dictionary, where the nodes are keys and the values are a list of nodes it points to. Handling the seed was also tricky, since the function calls itself and we needed the results to stay reproducible without resetting the seed on every call. When testing, I found that overlapping reads were creating duplicate edges, which made the assembled contigs incorrect on the toy example. On the GitHub side, I accidentally opened my first pull request to main instead of the PR branch, which showed me that I still have some practice to do when it comes to using GitHub with collaborators. I now know that I need to always double-check the base branch before submitting. Overall, our group worked well together, and everyone contributed helpful ideas when we talked through the concepts, especially when figuring out how the Eulerian walk and edge handling should work.

### Trivedi

# Generative AI Appendix
Katelyn:
I used Claude (Anthropic) during this project for the following:
- Git and GitHub: Helped with forking, pulling updates from the group repo, and fixing the problem with opening a pull request to the wrong branch
- Pseudocode review: Used it to get feedback on the group's initial eulerian_walk pseudocode and used it to help draft pseudocode for assemble_contigs
- Code: Provided guidance on the functions, and explained recursion, how the graph is stored as a dictionary, and how to handle the random seed. I wrote parts of the functions with step-by-step guidance to help with parts I didn't understand, and some code, including the seed handling, graph-copying, and second loop in assemble_contigs, was provided by Claude after we struggled to troubleshoot in these areas.
- Testing: Claude helped run our code on the doctests and toy example
- README: Helped provide feedback on the pseudocode to ensure it correctly reflected our final code as well as make edits to my personal reflection.