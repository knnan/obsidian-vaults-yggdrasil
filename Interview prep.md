## Pirate King guide

[String](https://leetcode.com/discuss/study-guide/2001789/Collections-of-Important-String-questions-Pattern)
[Two Pointers](https://leetcode.com/discuss/study-guide/1688903/Solved-all-two-pointers-problems-in-100-days)  
[Sliding Window](https://leetcode.com/discuss/study-guide/1773891/Sliding-Window-Technique-and-Question-Bank)  
[Binary Search](https://leetcode.com/discuss/study-guide/786126/Python-Powerful-Ultimate-Binary-Search-Template.-Solved-many-problems)  
[Recursion](https://leetcode.com/discuss/study-guide/1733447/Become-Master-In-Recursion)  
[Binary Tree](https://leetcode.com/discuss/study-guide/1212004/Binary-Trees-study-guide)  
[Backtracking](https://leetcode.com/discuss/study-guide/1405817/Backtracking-algorithm-%2B-problems-to-practice)  
[DFS](https://leetcode.com/discuss/study-guide/1072548/A-Beginners-guid-to-BFS-and-DFS)  
[BFS](https://leetcode.com/discuss/study-guide/1072548/A-Beginners-guid-to-BFS-and-DFS)  
[Graph](https://leetcode.com/discuss/study-guide/2360573/Become-Master-In-Graph)  
[Linked List](https://leetcode.com/discuss/study-guide/1800120/Become-Master-In-Linked-List)  
[Sort](https://algodaily.com/lessons/merge-sort-vs-quick-sort-heap-sort)  
[Trie](https://leetcode.com/discuss/study-guide/931977/Beginner-friendly-guide-to-Trie-Tutorial-%2B-Practice-Problems)  
[Stack](https://leetcode.com/discuss/study-guide/2347639/A-comprehensive-guide-and-template-for-monotonic-stack-based-problems)/Queue  
[Priority Queue](https://leetcode.com/discuss/study-guide/1360400/Priority-queue-%2B-problems-to-practice)  
[Dynamic Programming](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns)*


# Neetcode

https://neetcode.io/practice

-   [Dynamic programming patterns](https://leetcode.com/discuss/general-discussion/458695/dynamic-programming-patterns) — the explanations here are sparse, but the author grouped different types of problems into categories so you can get more targeted practice.
-   [Partition subset problem](https://leetcode.com/problems/partition-equal-subset-sum/solutions/462699/Whiteboard-Editorial.-All-Approaches-explained) — multiple well-written solutions for this classic problem.
-   [Binary search patterns](https://leetcode.com/discuss/general-discussion/786126/Python-Powerful-Ultimate-Binary-Search-Template.-Solved-many-problems) — using a relatively simple template, you can solve almost every binary search question. The explanations here are well-written. Note, my [Two Sigma interviewer](https://medium.com/@SantalTech/550k-offer-two-sigma-software-engineer-experience-2022-93938470628) wanted me to use Python’s built-in binary search (`bisect.bisect_left`) instead of writing binary search from scratch, so familiarize yourself with that library too.
-   [Sliding window problems](https://leetcode.com/problems/frequency-of-the-most-frequent-element/solutions/1175088/C++-Maximum-Sliding-Window-Cheatsheet-Template!/) — a collection of sliding window questions and a generalizable approach to solving them.
-   [This video on binary search](https://www.youtube.com/watch?v=tgVSkMA8joQ) gave me a different perspective on what binary search was doing and helped me understand the concept better.

# System Design

I’m ranking these by increasing complexity; start from the top and work your way down.

-   [https://github.com/donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer)
-   [https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers](https://www.educative.io/courses/grokking-modern-system-design-interview-for-engineers-managers)
-   [System Design by Alex Xu, Vol. 1](https://amzn.to/3X3ZGRC) — a bit high-level, but gives you an idea on how to structure your answer and exposes you to the breadth of questions that might be asked.
-   [System Design by Alex Xu, Vol. 2](https://amzn.to/3X3ZGRC) — the sequel to the first book that is more in-depth and asks harder questions. Several of my system design interview questions came directly from this book so I’d highly recommend working through these. In reality, your interviewer will likely ask followups that aren’t covered as in-depth as they’d like to see, so you’ll need to supplement your reading with tech blogs and whitepapers.
-   [Amazon’s DynamoDB White Paper](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf). Understand Amazon’s K/V store architecture and how it services the top e-commerce site in the world.
-   Company tech blogs. Don’t really understand eventual consistency, or CDNs, or rate limiting? Search for a keyword and “tech blog” and see how real companies do it in production.
