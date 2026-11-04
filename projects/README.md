# Introduction
In this project, we implement the Burrows–Wheeler transform (BWT) and use it to find all exact matches of a query in a reference sequence. We first build the transform by sorting all rotations of the reference, then compute it from a suffix array. We use character counts and the LF mapping to search the query from right to left, narrowing the range of possible matches before using the suffix array to recover their starting positions.

We also apply run-length encoding to the transformed string and decode it to recover the BWT. This project demonstrates how aligners such as Bowtie and BWA use FM indexes to search for exact matches.

# Pseudocode
```
FUNCTION BWT(string)
    Add "$" if the string does not already contain it
    Generate and sort all cyclic rotations
    RETURN the last character of each sorted rotation joined into a string

FUNCTION suffix_array(string)
    Append "$" if needed
    List all suffixes in starting-position order
    Sort the suffixes lexicographically
    RETURN each sorted suffix's index in the original list

FUNCTION BWT_from_suffix_array(text, suffix_positions)
    Append "$" if needed
    For each suffix position, take the character immediately before it
    For position 0, wrap around to the last character
    RETURN the selected characters joined into a string

FUNCTION cal_count(string)
    Count the frequency of each character
    Walk through the characters in sorted order, keeping a running total
    RETURN the number of characters that sort before each character

FUNCTION cal_occur(bwt_string)
    Scan the BWT from left to right, keeping a running count for each character
    At each position, record the counts through that position
    RETURN the occurrence table

FUNCTION update_range(lower, upper, count, occur, character)
    Find where the character's block starts in the first column using count
    Use occur to count copies of the character strictly before each boundary
    # LF mapping preserves the order of each character's occurrences
    Add each occurrence count to the start of the character's block
    RETURN the new lower and upper boundaries

FUNCTION find_match(query, reference)
    Append "$" if needed
    Build the suffix array and BWT from the reference
    Build the count table from the reference and the occurrence table from the BWT
    Start with the full suffix array as the candidate range

    FOR each query character, from right to left
        IF the character is absent from the count table
            RETURN an empty list
        Narrow the range using update_range
        IF lower >= upper
            RETURN an empty list

    RETURN the sorted reference positions in the range [lower, upper)

FUNCTION run_length_encode(bwt_string)
    Group consecutive copies of each character into runs
    Write each run as the character followed by its length
    RETURN the encoded string

FUNCTION run_length_decode(encoded)
    Read each character and all the digits that follow it
    Repeat the character according to that count
    RETURN the reconstructed BWT string

FUNCTION main(reference, query)
    Compute the direct BWT and the BWT from the suffix array
    Build the count and occurrence tables from the suffix-array BWT
    Find the query's exact match positions in the reference
    Encode the BWT and decode the encoded string
    Print the suffix-array BWT, the match positions, and the encoded string
```

# Successes
* Our team successfully replicated all the examples in this project and the HTML file that the professor provided on canvas. This includes creating the suffix array, finding the BWT string, calculating the counts and occurrences matrices, and finally encoding and decoding the BWT string. We built a simple driver function `main` that showcases the successes we had with the project. 

# Struggles
* We noticed that if the original string contains digits, our run-length decoder can confuse them with run counts. In the future, we could put the counts in brackets so that 1[3] means three copies of the digit 1. This would not be an issue with DNA.

# Personal Reflections
## Group Leader
**Selin Uenal**  
This project was the first one that I got to be a project leader, so 7 projects in to the semester, I finally got to learn about the other side of approving PRs in GitHub. By now, I was pretty familiar with the process, but I'm glad I got to get that prespective finally. This project overall was not too difficult, once I understood the algorithm a bit better. The HTML file and assigned readings were definitely extremely helpful, as well as learning from my group members' approach to the code. 

## Other members
**Ahmed Salman**  
Doing this algorithm by hand is so much easier to understand than just reading about it. If you didn't understand the algorithm or not confident enough in it, I highly recommend the good old pen and paper. While doing it by hand, you should be able to more clearly identify the required steps for each part of the process. Other than the concerns we had about performance, we also had difficulties considering different edge cases. I call them edge cases but they could be core functionalities of the program. The current program we developed cannot handle numbers in the original string, because the way the run length encoding works is by using integers to represent the counts of every letter in front of it, this means that a run length encoding of a string of integers such as `1122222222222211` would break the code as it would slap more integers next to it. This is especially true if there are long repeats more than 10 of any given integer. That said, if the code is used only for DNA, RNA, or protein sequences or any string that only contains letters, then it should be able to handle it correctly. I do have a simple idea that could fix that problem. What if the counts for each character was contained between brackets? for example, instead of writing `a2nb$2a` as the BWT for banana, why not `a(2)nb$(2)a`? How does that fix the numbers problem? like this, the BWT for the random numbers I wrote above would be `212$11121` which is pretty much impossible to read, but if it was written like this `(2)12$1(11)21` it becomes easier to tell that the number 1 at the start is repeated twice and the number 2 near the end is repeated 11 times. That was just a quick thought that I had. but as my team noted, this would increase the size of the run length encoded string quite a bit if there aren't many repeats so I am not sure how "worth it" it is. And since most of our work is on datasets that do not contain numbers we elected not to apply that.

**Justin Gubbens**  
I found the process of this project to be easier than most. Our group spent our first synchronous meeting going through the algorithm on a Teams whiteboard, running through the steps of the algorithm using an example string entirely by hand before comparing our answer to the given one. In this, we were able to take the parts that we individually understood well and combine them so that we all ended up with a strong conceptual understanding of the algorithm. We also discussed edge cases, such as numbers being in the reference string, and potential memory-saving tactics such as diagonalization of the rotations list and not storing the entire list when creating the suffix array. With this level of conceptual understanding, I did not struggle with the code. Overall, I think our group had a great workflow for this project. Meeting to discuss and understand the algorithm by working through an example is something I hope to continue doing with my future projects.

# Generative AI Appendix
Claude (Sonnet 5.5) was used to help make sure our pseudocode matched our final code. We gave Claude our pseudocode draft and finalized code and asked: "Does our pseusocode match our finalized code?". The output told us it mostly matched, with minor fixes, which were revised in the final version of this README.