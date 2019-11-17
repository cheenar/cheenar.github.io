---
layout: post
title: (C) Leetcode 205, Isomorphic Strings
published: true
---

### TL;DR (Hint)
We want to check that the mappings from *s->t* are properly onto and one-to-one. In order to do that, we will create a permutation array that will record how each letter in *s* is substituted in *t* and then verify that any character has not been used already (one-to-one check) by marking it in a buffer as used. 

### Process

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

In this case, **ab -> aa** is essentially mapping the *a* character to itself and then mapping *b to a*. This breaks our one-to-one case becaues in this case, the *function* that maps from *s* to *t* is mapping two different characters to the same one. This passes our function since to our permutation array, we have mapped two different characters to two different values. What we want is some kind of check that we haven't already used a letter in the alphabet for our mappings. 

<center>
<blockquote class="imgur-embed-pub" lang="en" data-id="a/eCHCCOj" data-context="false" ><a href="//imgur.com/a/eCHCCOj"></a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>
</center>

A simple trick that I was able to figure out to simply get the code to pass was to wrap the function in a __bool iso(char *s, char *t)__ function that I then return *iso(s, t) && iso(t, s)* in my leetcode test function so that way the mappings from *s --> t* are verified in both of the sets. In this case, our second check would fail since a would be mapping to both *a and b* and therefore not a proper one-to-one mapping. 

My "hack" really was to verify that our "on-to" was properly one-to-one and therefore a bijective mapping. 

To simplify the code and make it not have to create two buffers on the stack for each function call, I resorted instead creating a buffer called used that marked the alphabet characters that were taken and made the check in both directions.

```
bool iso(char *s, char *t) {
    char permutation[256];
    char used[256];
    
    // setup the initial alphabet
    for (int i = 0; i < 256; i++) {
        permutation[i] = '\0';
        used[i] = '\0';
    }
    
    int length = strlen(s); //assume s.len == t.len
    
    for (int i = 0; i < length; i++) {
        char t_val = t[i];
        char s_val = s[i];
        
        if (permutation[s_val] == '\0' || permutation[s_val] == t_val) {
            if(used[t_val] != '\0' && used[t_val] != s_val) return false;
            permutation[s_val] = t_val;
            used[t_val] = s_val;
        } else {
            return false;
        }
    }
    
    return true;
}

bool isIsomorphic(char * s, char * t){
    return iso(s,t);
}
```

### Take-aways:
* Sometimes, you have a correct solution, you just have to manipulate the arguments
* This algorithm was able to score a 0ms runtime, however, Leetcode is not exactly the best way to verify runtime. I'm sure there is a better way to approach this problem in a condensed and cleaner way, but for now, I am satisified. 

Thanks for reading my first blog post! I'm sure that I made some spelling/grammar errors. If you're interested in following my C/Python programming escapades, please consider bookmarking the blog or following me on Twitter. 