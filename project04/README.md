# Introduction
In this program, we implement the concept of the de Bruijn graph algorithm as it relates to sequence assembly. 
As a core concept, we utilize the Eulerian walk, which is where every "edge" is visited exactly once, while "nodes" may be re-visited. 

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
since build_graph is what loops through the edges, we only need to define what a left and right node would be
    self.graph left and append to right
    No return statement because we update attributes

remove_edge
    self.graph left and remove right
    No return statement because we update attributes


build_graph_from_reads
    for each read within our reads
        and for i in range the length of our reads, but minus k
            make a sliding window for the suffix/prefix
            # define kmer, left, and right
            call add_edge(left, right)
        no return statement


eulerian_walk
    Seed the program
    Initialize the tour
        # tour = []
    Loop body
    #while node still has outgoing edge
        Select random edge from current node
        Remove that edge to get to the next node, but save its position
        Use recursion to call eulerian walk
            # calling self.eulerian_walk
        add recursive path to list
        Exit condition: once no edges left, we store it and return it
    Return the tour as a list of (k-1)-mers traversed.


assemble_contigs



tour_to_sequence
    Establish that the tour is not empty
    start from the beginning of the sequence
    for each node in the tour
        add the node to the sequence
 

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

### Trivedi

# Generative AI Appendix
As per the syllabus
