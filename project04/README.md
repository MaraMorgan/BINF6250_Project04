# Introduction
In this program, we implement genome assembly using de Bruijn graphs. Each read is broken into k-mers, and each k-mer forms a directed edge from its prefix to its suffix. The prefix and suffix are (k-1)-mers that act as nodes of the graph and reads that share (k-1)-mers become connected through the graph. This allows us to reconstruct the origianl sequence without comparing every read to eachother.
To assemble the sequence, we use an Eulerian walk, a path that visits every "edge" exactly once, while "nodes" may be re-visited. Each walk produces a contig, and we applied this to a small toy example as well as a yeast sequencing dataset.

De Bruijn graphs are used to determine overlaps between sequencing reads, with nodes as kmers and edges as the connections between them, 
and are commonly used on large datasets to this day. 



# Pseudocode
General structure:
0. Setup: raise the recursion limit, define reverse_complement
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
    if no tour list was passed in, create an empty one 
    while the current node still has outgoing edges
        pick a random edge from the node's list
        remove it and save where it goes (next_node)
        remove the matching edge on the other strand (_remove_reverse_complement_edge)
        call eulerian_walk on next_node, passing the same tour list and no seed
    once the current node has no edges left, add it to the tour
    return the tour as a list of (k-1)-mers in reverse order


# Helper functions:
_remove_reverse_complement_edge
    rebuild the k-mer from left + the last letter of right
    take its reverse complement and split it into rc_left and rc_right
    if that edge exists in the graph, remove it

_split_tour
    start the first contig with the first node of the tour
    for each pair of neighboring nodes in the tour
        if there is a real edge between them in the original graph, extend the contig
        otherwise, close the current contig and start a new one
    add the last contig
    return the list of contigs

assemble_contigs
     if a seed is given, seed the random number generator
    copy the graph, including each neighbor list, so the original stays unchanged
    count how many edges come into each node (in-degree)
    starting nodes = nodes with more outgoing than incoming edges
    delete the in-degree counts to free up memory
    for each starting node, then every other node in the graph
        if the node still has unused edges
            call eulerian_walk
            reverse the tour into forward order
            split it into contigs with _split_tour and add them to the list
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
Understanding the algorithm before writing any code was a big part of this project for our group. We talked through how reads get broken into k-mers, why the nodes are (k-1)-mers, and how an Eulerian walk uses every edge exactly once. Having taken Genomics last semester helped me a lot here, since we had covered de Bruijn graphs and genome assembly in that course. I already had a feel for why repeats create forks in the graph and why low coverage leaves gaps, so a lot of this project was connecting those concepts to the actual code: how a graph can be stored as a dictionary, how recursion builds the tour in reverse, and how object oriented programming lets the methods share the same graph through self.

Most of the hard parts came from bugs that only showed up once we started testing. One of the first was incorrect stitching of edges, where the walk would finish one branch of a fork and jump to another, and those pieces got joined into a fake contig even though there was no real edge between them. We fixed this with a helper that checks every pair of neighboring nodes against the original graph and splits the contig wherever there's a jump. Once I had the full code working on the yeast dataset, the results still didn't look right: we were getting way more contigs than expected and an assembly much larger than the yeast genome. I eventually realized this was because the simulated reads come from both DNA strands, so every region shows up in the graph twice, once as its reverse complement, and I hadn't accounted for that at first. Removing the matching reverse complement edge during the walk brought the assembly size back down to about the size of the genome. Running yeast also exposed a recursion limit problem, where the walk went over a million calls deep on long chains and the notebook looked frozen with no error.

Debugging and testing with Claude helped me find these errors and the gaps in my logic. Instead of changing everything at once, I made one change at a time and tested it on the toy example and small simulated datasets before moving on, which made it much easier to tell which change fixed what. I'm also getting more comfortable with GitHub, especially fetching my groupmates' changes before building on top of them and keeping large data files out of the repo with a .gitignore.
# Generative AI Appendix
Katelyn:
I used Claude (Anthropic) during this project for the following:
- Git and GitHub: Helped with forking, pulling updates from the group repo, and fixing the problem with opening a pull request to the wrong branch
- Pseudocode review: Used it to get feedback on the group's initial eulerian_walk pseudocode and used it to help draft pseudocode for assemble_contigs
- Code: Provided guidance on the functions, and explained recursion, how the graph is stored as a dictionary, and how to handle the random seed. I wrote parts of the functions with step-by-step guidance to help with parts I didn't understand, and some code, including the seed handling, graph-copying, and second loop in assemble_contigs, was provided by Claude after we struggled to troubleshoot in these areas.
- Testing: Claude helped run our code on the doctests and toy example
- README: Helped provide feedback on the pseudocode to ensure it correctly reflected our final code as well as make edits to my personal reflection.

Trivedi:
I used Claude AI in this project for the following:
- Explained my understanding of the De Brujin Graphs concept and asked Claude to help me make my understanding stronger.
- Elaborate more on the Object oriented concepts of python programming and how it can be used in the De Brujin Graphs algorithm.
- Come up with test and debug code for my functions and methods.
- Handle Errors like recursion limit and removing reverse complement strand, which caused the output to almost double in size.
- Using helper functions as methods in a python class.