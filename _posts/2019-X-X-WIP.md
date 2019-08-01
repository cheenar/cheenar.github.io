---
layout: post
title: (C) Leetcode 205, Isomorphic Strings
published: true
---

I recently tried out problem [205](https://leetcode.com/problems/isomorphic-strings/) on Leetcode, Isomorphic Strings. This is designated as a Leetcode Easy, designed for those preparing for interviews to get some practice with basic data structures and algorithms. 

I have been trying to improve my C/C++ skills so I always attempt to solve Leetcode problems in either language, and if I can't, I will try using Python or Java and then translate my code back to C, in some cases implementing functions and data structures when needed. 

For 205, you are given two input strings of equal length, *s* and *t* where you will see if a mapping between *s* and *t* exist such that any mapping that did exist would be bijective in nature, or, in other words, if you can make a substitution for each letter in *s* that would correspond to a letter in *t* such that no two letters are mapped to the same new letter or that two of the same letters do not have different mappings. 

I thought of this problem as a set problem where we essentially want to see if the set of characters in *s* maps onto the characters in *t* (do all the characters have a mapping onto that) an that each of those mappings are one to one (do all the mappings only correspond to a single new destination).

For my first implementation, I went about by creating a buffer of size 26 that would hold the alphabet, a-z, and a buffer to hold the permutation alphabet that way a mapping could be determined between the two. I decided to use two buffers because I could mark changes in the alphabet and the changes in the permutation at the same time. My initial code essentially tested if each alphabet character in string *s* had a mapping in the permutation array yet, and if not, mapped it to the corresponding character in *t*, and if one already existed, ensure that it was the same mapping. This passed the initial test between egg -> add however when I ran it against the submission grader, I failed. 

```
bool iso(char *s, char *t) {
    char alphabet[256];
    char permutation[256];
    
    // setup the initial alphabet
    for (int i = 0; i < 256; i++) {
        int ch = i;
        alphabet[i] = ch;
        permutation[i] = '\0';
    }
    
    int length = strlen(s); //assume s.len == t.len
    
    for (int i = 0; i < length; i++) {
        if (permutation[s[i]] == '\0' || permutation[s[i]] == alphabet[t[i]]) {
            permutation[s[i]] = alphabet[t[i]];
        } else {
            return false;
        }
    }
    
    return true;
}

bool isIsomorphic(char * s, char * t){
    return iso(s,t) ;
}
```

There were two problems that arose in my implementation. The first, not all characters were in the alphabet. I had to expand my buffer to the full size of ASCII, 255. The second problem was testing aa -> ab. 

In this case, **ab -> aa** is essentially mapping the a character to itself and then mapping b to a. This breaks our one-to-one case becaues in this case, the *function* that maps from *s* to *t* is mapping two different characters to the same one. This passes our function since to our permutation array, we have mapped two different characters to two different values. What we want is some kind of check that we haven't already used a letter in the alphabet for our mappings. 

![mapping problem] (https://i.imgur.com/CwAPsEu.jpg "original")

