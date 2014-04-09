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
  * Search the median of two sorted array
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
As stated before, binary search can not be used in unsorted array. However there is a special situation where an array is 'rotated sorted.', for example{7, 11, 13, 17, 2, 3, 5}.

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
###Kth in one rotated array
To a normal sorted array, it can be done in O(1). To a rotated sorted array, we can use binary search to find the minimum value in O(log n). Then we can find find the kth element.  
Find the minimum(the array is considered as ascending orginally):

<pre class="prettyprint linenums">
public int findIndexOfMinValue(int[] A){
	int left=0;
	int right=A.length-1;
	if(right<left)
		return -1;
	while(left<right){
		int mid=left+(right-left)/2;
		if(A[mid]>A[right])
			left=mid+1;
		else
			right=mid;
	}
	//at this point, left is equal to right
	return left;
}
</pre>

And base on the position of the minimum value, we have calculate the Kth element's position;

<pre class="prettyprint linenums">
public int findKthElement(int[] A, int k){    
	if (k > A.length) return -1;    
	int base = findIndexOfMinVaule(A);    
	int index = (base+k-1)%A.length;    
	return index;
	}
</pre>
<br/>
###Kth in two sorted arrays(not rotated)
Brute force, like merge sort, compared from the beginning of each array. This will take O(m+n) time.  
By binary search, we can have a O(log(m+n)) algorithm.  
Some pre-condition: m+n>0, k>=1&&k<=m+n, and there is no duplicates among the two arrays.
<pre class="prettyprint linenums">
//return the value of kth element in union of two sorted array
public int findKthElement(int[] A, int m, int B[], int n, int k) {    
	int i = int(double(m)/(m+n)*(k -1));    
	int j = (k-1) - i;        
	//A[i] or B[j] is the Kth element, return it    
	if ((j <= 0 || B[j-1] < A[i]) && (j >= n || A[i] < B[j]))        
		return A[i];    
	if ((i <= 0 || A[i-1] < B[j]) && (i >= m || B[j] < A[i]))        
		return B[j];        
	//A[i] is too small, get rid of lower part of A and higher part of B    
	if (0 < j && A[i] < B[j-1])        
		return findKthElement(A+i+1, m-i-1, B, j, k-i-1);        
	//B[j] is too small, get rid of higher part of A and lower part of B    
	else //if(i > 0 && B[j] < A[i-1])         
		return findKthElement(A, i, B+j+1, n-j-1, k-j-1);}
</pre>

##Caculate sqrt
Calculate sqrt without using Math.sqrt(), and just return an integer.  
Brute force: go through 0 to n, find n^2 ≤ x < (n+1)^2, O(n)   
Binary Search: O(log n)
<pre class="prettyprint linenums">
 public int sqrt(int x) {
        if(x<=0) return 0;
        if (x==1) return 1;
        long high=x;
        long low=0;
        while((high-low)>1){
            long mid=low+(high-low)/2;
            if((mid*mid)>x)
                high=mid;
            else
                low=mid;
        }
        return (int)low;
   }
 </pre>
 Please noticed that there is better algothrim to calculate sqrt.
 
##Search the median of two sorted array
There are some solutions online that can only be applied when two array have the same length.  
A more general way is to using <b>findKthElement</b>. We can easily have the median when the total length is odd is ((m-n)/2+n+1)th element, while even is the mean of ((m-n)/2+n)th element and  ((m-n)/2+n+1)th element.

##Search on mutiple server
Problem: there are multiple severs that holds arrays, there is no communication between servers, and you have a laptop that can request data from those servers, how to find the Kth smallest integer?  
This is a complexity form of search on two sorted array. One of a normal way is to have a data structure that store K element, compare with each server, fill with the smallest element between the data structure and server.  
One of a problem is we don't know if the array on every server is sorted or not. But we can have the array sorted simultaneously. Which will have O(nlog n).  
And maintain a sorted array which size is K on the laptop, and compare it with every server, using  <b>findKthElement</b>, keep the new k elements into the array, it should have a complexity of O(nlog k), n is the number of server.