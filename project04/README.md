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
   a. Helper functions to remove reverse sequences and ensure real edges
4. Reconstructing the sequence
5. Calculating the statistics
6. Outputting a FASTA file

   
```
add_edge
   add right to the list of neighbors for left in self.graph
   access self.graph[right] so right is a node even without outgoing edges
   since build_graph is what loops through the edges, we only need to define what a left and right node would be
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


# Helper functions:
_remove_reverse_complement_edge
_split_tour

assemble_contigs
    if a seed is given, seed the random number generator
    make a full copy of the graph so it remains unchanged during the walk
    count the in-degree of all the nodes
    identify possible starting nodes
    initialize an empty list for the contigs
   
    for each node, iterate over the possible starts
      then iterate over all other nodes
      check if there are any unised nodes
         call eulerian_walk if yes
         reverse sequence
         split into contigs
      return the list of contigs


tour_to_sequence
    Establish that the tour is not empty otherwise return an empty string
    start from the beginning of the sequence with the first (k-1)-mer
    for each node in the tour after the first
        add the last letter to the sequence
    return the sequence
 

get_assembly_stats
   calculate:
      number of contigs: total contig count,
      total contig length: sum of all lengths,
      the longest and shortest contig: Sort from longest to shortest and display longest and shortest,
      mean length: total length / # contigs, 
      n50: initialize, iterate through all sorted contigs, add length to total, stop when a contig reaches half the assembly length
   if there are no contigs
      return zero
   return stats dictionary

write_fasta
   number the contigs
   iterate through contigs
      add the header for each
      for each contig, wrap the line so they aren't too long
         return the output file

```

## Output:
A file containing the genome assembly statistics
Stats include:
* A header with analysis date, input file, and kmer size
* Input data
* * Ex) Number of reads, average read length, total sequencing data
* De Bruijn Graph Construction
* * Ex) Graph nodes and edges, out degrees
* Assembly Results
* * Ex) Total number of contigs and assembly length, output dictionary from `get_assembly_stats`
* Top 20 Longest Contigs
* Contig Length Distribution
* Timing Summary


# Successes
As a group, we were able to meet and discuss our ideas behind the concepts of de Bruijn graph assembly, recursion, and object oriented programming. Despite conflicting schedules between all group members, effective communication and collaboration was not impeded. 

Together, we talked through and wrote the functions to add edges, remove edges, and constructing the graph. We then independently wrote the rest of the functions and compared with each other, talking through points some of us may not have thought of. This was very beneficial to us, because we all took slightly different approaches, and combining them allowed us to think through what went where and why. 

Overall, our group worked very well together to think through and implement this project. 



# Struggles
Once again, there were some slight hiccups with using GitHub, but we all get better with each project as we gain familiarity. 

While brainstorming together, there was some initial confusion on how to handle duplicate edges, which was resolved by checking if the edge was already in the graph.

Our biggest struggle was finding a bug in our code that connected invalid edges in our `assemble_contigs` function, creating fake contigs. There was intensive debugging involved, and we decided we needed to implement more checks within each function to make sure everything operates as it should. The bug ended up being resolved through a helper function that checks valid node paths in a copy of the graph against the original graph, preventing invalid paths from connecting.


# Personal Reflections
## Group Leader: 
### Mara
This was my second time as a group leader, and I feel it was easier to manage than the first time, especially keeping up to date on pull requests and checking my collaborators code. 
My collaborators contributed some excellent considerations while we were talking through the concepts outlined in the lecture, particularly the Eulerian walk and recursion. I am concurrently taking Genomics, and the first project was based on de Bruijn graph assembly, so I had the concepts still fresh in my mind and they translated well, but this project was definitely more involved. 


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
