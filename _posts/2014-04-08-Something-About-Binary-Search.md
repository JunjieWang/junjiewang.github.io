---
layout: post
title:  "Something About Binary Search"
date:   2014-04-08 14:32:16
categories: algorithm
tags: [java, algorithm, binary search]

---
#Binary Search

Binary Seach is a very useful algorithm. Today, I will summarize it, as a reminder for me. This blog will includes the following:

  - What is Binary Search
  - The implementation
  - Search for upper bound and lower boud
  - Search for interval
  - Search in rotated sorted-array
     - Kth in one array
     - Kth in two array
  - Caculate sqrt
  - Search on mutiple server
  - Summary

When we study an algorithm, not only we need to understand the basic idea and implementation of it, but also we should try to analysis the complexity of it, including time and space complexity,especially time complexity.


##What is Binary Search?

Binary Search is a search algorithm. There are two prerequisite:
  - array
  - sorted

We can not use binary search on LinkedList. And we should also notice that whether the array is ascending or descending does not matter.

###Java Implemention
1.implement using recursion:
```
    public int binarySearch(int[] array, int left, int right, int target){
        //int left=0;
        //int right=array.lenth;
        
        if(right<left) return -1;
        int mid=left+(right-left)/2;
        if (array[mid]> target)
            return    binarySearch(array, left, mid -1, target);
        if (array[mid]< target)
            return    binarySearch(array, mid+1, right, target);
    //if (midValue == target)
        return mid;
    }
```
Dillinger uses a number of open source projects to work properly:

* [Ace Editor] - awesome web-based text editor
* [Marked] - a super fast port of Markdown to JavaScript
* [Twitter Bootstrap] - great UI boilerplate for modern web apps
* [node.js] - evented I/O for the backend
* [Express] - fast node.js network app framework [@tjholowaychuk]
* [keymaster.js] - awesome keyboard handler lib by [@thomasfuchs]
* [jQuery] - duh 

Installation
--------------

```sh
git clone [git-repo-url] dillinger
cd dillinger
npm i -d
mkdir -p public/files/{md,html,pdf}
```

##### Configure Plugins. Instructions in following README.md files

* plugins/dropbox/README.md
* plugins/github/README.md
* plugins/googledrive/README.md

```sh
node app
```


License
----

MIT


**Free Software, Hell Yeah!**


    