---
layout: post
title:  "Something About Binary Search"
date:   2014-04-08 14:32:16
categories: algorithm
tags: [java, algorithm, binary search]

---
#Binary Search

Binary Seach is a very useful algorithm. Today, I will summarize it, as a reminder for myself. This blog will includes the following:

  * What is Binary Search
  * The implementation
  * Search for upper bound and lower boud
  * Search for interval
  * Search in rotated sorted-array
     * Kth in one array
     * Kth in two array
  * Caculate sqrt
  * Search on mutiple server
  * Summary

When we study an algorithm, not only we need to understand the basic idea and implementation of it, but also we should try to analysis the complexity of it, including time and space complexity,especially time complexity.


##What is Binary Search?

Binary Search is a search algorithm. There are two prerequisite:

  * array
  * sorted

We can not use binary search on LinkedList. And we should also notice that whether the array is ascending or descending does not matter.

###Java Implemention  

1.implement using recursion:

<pre class="prettyprint linenums">
public int binarySearch(int[] array, int left, int right, int target){
    //int left=0;
    //int right=array.lenth-1;
    if(right<left) return -1;
    int mid=left+(right-left)/2;
    if (array[mid]> target)
        return    binarySearch(array, left, mid -1, target);
    if (array[mid]< target)
        return    binarySearch(array, mid+1, right, target);
     //if (midValue == target)
    return mid;
}
</pre>
<br/> 
  
2.implement without recursion

As we know, all of the recursion can be implemented by STACK as well. Therefore, BinarySearch can be implemented by stack, even without stack. Because binary search is a form of tail recursion.

<pre class="prettyprint linenums">
public int binarySearch(int[] array, int target){
    int left=0;
    int right=array.length-1;
    while(left <= right){
        int mid = left+(right-left)/2;
        if (array[mid] > target)
            right = mid - 1;
        else if (array[mid] < target)
            left = mid + 1;
        else //found the target
            return mid;
    }
    //the array does not contain the target
    return -1;
}
</pre>
</br>

##Search for upper bound and lower boud

For now, we only use binary search to find the target value. We can use binary search for boundaries, which means find a value that just greater(or smaller) than the target.  
For example, we have an array:  int array = {2, 3, 5, 7, 11, 13, 17};  
And a target: int target = 7;  
Then the upper bound is 11; the lowever bound is 5.

###Upper bound
<pre class="prettyprint linenums">
//Find the fisrt element, whose value is larger than target, in a sorted array 
public int bSearchUpperBound(int[] array, int target){
    int left=0;
    int right=array.length-1;
    //Array is empty or target is larger than any every element in array 
    if(left > right || target >= array[right]) return -1;
    int mid = left+ (right - left) / 2;
    while (right > left){
        if (array[mid] > target)
            right = mid;
        else
            left = mid + 1;   
        mid = left+ (right - left) / 2;
    }
    return mid;
}
</pre>
The major difference between upperbound search and target search is target search has only two comparsion: greater or not greater.  
When we come across with some integer greater than the target, it might be the value we are looking for, therefore we need to keep it's index. That's why when:  
if (array[mid] > target)  
high=mid;  
instead of mid--;

###Lower bound
<pre class="prettyprint linenums">
//Find the last element, whose value is less than target, in a sorted array 
public int BSearchLowerBound(int[] array, int target)
{
    int left=0;
    int right=array.length-1;
    //Array is empty or target is less than any every element in array
    if(right < left  || target <= array[left]) return -1;
    int mid = left + (right-left + 1) / 2; //make mid lean to large side
    while (left < right){
        if (array[mid] < target)
            left = mid;
        else
            right = mid - 1; 
        mid = left + (right-left + 1) / 2;
    }
    return mid;
}
</pre>
It's basicallu same as the upperbound search. We need to be careful about the line 8，make the mid lean to large side is to avoid infinite loop(consider when left == right-1, and array[left] < target).  

##Search for interval
Consider a situation that duplicates are existed in an array, while the array is still sorted. We can use binarySearch to check if target exists in array, however it will randomly return one of poisition. In this situation, we may need to return the interval of the target value.

<pre class="prettyprint linenums">
//return type: Pair<int, int>
//the fisrt value indicate the begining of range,
//the second value indicate the end of range.
//If target is not find, (-1,-1) will be returned
public Pair<int, int> SearchRange(int A[], int n, int target) 
{
    Pair<int, int> r=new Pair<int, int>(-1, -1);
    if (A.length <= 0) return r;
    int lower = BSearchLowerBound(A, target);
    lower = lower + 1; //move to next element
    if(A[lower] == target)
        r.first = lower;
    else //target is not in the array
        return r;
    int upper = BSearchUpperBound(A, target);
    upper = upper < 0? (A.length-1):(upper - 1); //move to previous element
    //since in previous search we had check whether the target is
    //in the array or not, we do not need to check it here again
    r.second = upper;  
    return r;
}
</pre>
The time complexity is O(log n) + O(log n), therefore is O(log n).

##Search in rotated sorted-array
As I stated before, binary search can not be used in unsorted array. However there is a special situation where an array is 'rotated sorted.', for example{7, 11, 13, 17, 2, 3, 5}.

<pre class="prettyprint linenums">
int SearchInRotatedSortedArray(int[] array, int target) {
    int left=0;
    int right=array.length-1;
    while(left <= right){
        int mid = left+ (right - left) / 2;
        if (target < array[mid]){
            if (array[mid] < array[right])//the higher part is sorted
                right = mid - 1; //the target would only be in lower part
            else{ //the lower part is sorted
                    if(target < array[left])//the target is less than all elements in low part
                        left = mid + 1;
                    else
                        right = mid - 1;
                }
        }else if(array[mid] < target){
        if (array[left] < array[mid])// the lower part is sorted
                left = mid + 1; //the target would only be in higher part
            else{ //the higher part is sorted
               if (array[right] < target)//the target is larger than all elements in higher part
                    right = mid - 1;
                else
                    left = mid + 1;
                }
        }else //if(array[mid] == target)
            return mid;
    }
    return -1;
}
</pre>


