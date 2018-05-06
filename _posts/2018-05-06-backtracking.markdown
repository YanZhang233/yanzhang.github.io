---
layout: post
title:  Backtracking
date:   2018-05-06 18:00
description: Backtracking Algorithm. # Add post description (optional)
img: backtracking.jpg # Add image post (optional)
tags: [Algorithm, Leetcode, Backtracking]
author: Yan Zhang # Add name author (optional)
---
Backtracking is a general algorithm for finding all solutions by incrementally building candidates to the solution, and abandoning a candidate("backtracking") as soon as it determines the current candidate impossible to give a valid solution.

Actually, this algorithm uses recursion. And the easy template code is as follows:

```java
private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, int start){
    if(the tempList satisfies the solution) {
      list.add(new ArrayList(tempList));
    }
    for(int i = start; i < nums.length; i++){
        tempList.add(nums[i]);
        backtrack(res, tempList, nums, i + 1);
        tempList.remove(tempList.size() - 1);
    }
}
```

Once the tempList satisfies the solution, we add it into the result set; otherwise, we
proceed to build the answer by adding nums[i]. After finding the valid solution or realizing
the nums[i] which we just added cannot lead to a valid answer, we backtrack by removing the current nums[i], to find other possible solutions.

**So, the key concept of backtracking is just to try every possible answer and discard those invalid.** It is kind of like brute force. Therefore, if we have to judge every possible circumstances to determine the result set, we can think about using backtracking. Certainly, different problem varies a little bit on the template.

## Subsets

In the Subsets problem([Leetcode 78](https://leetcode.com/problems/subsets/description/)), we just construct the tempList by making different nums as the "start" element, and adding the following nums one by one.

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}

private void backtrack(List<List<Integer>> list , List<Integer> tempList, int [] nums, int start){
    list.add(new ArrayList<>(tempList));
    for(int i = start; i < nums.length; i++){
        tempList.add(nums[i]);
        // the "start" will forward 1 as we recursively call backtrack()
        backtrack(list, tempList, nums, i + 1);  
        tempList.remove(tempList.size() - 1);
    }
}
```

In the Subsets II problem([Leetcode 90](https://leetcode.com/problems/subsets-ii/description/)), we have to avoid the duplicates. The solution is if we come across the duplicate where it is not the "start" element in the tempList, we just skip it and continue to handle the following nums. **Besides, we always want to sort the nums[] array first, if considering the duplicates problem.**

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int start){
    list.add(new ArrayList<>(tempList));
    for(int i = start; i < nums.length; i++){
        if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, i + 1);
        tempList.remove(tempList.size() - 1);
    }
}
```

## Permutations

In the Permutations problem([Leetcode 46](https://leetcode.com/problems/permutations/description/)), we put these nums in different positions every time we navigating the whole array(like regarding different nums as the "start" element in the tempList).

```java
public List<List<Integer>> permute(int[] nums) {
   List<List<Integer>> list = new ArrayList<>();
   backtrack(list, new ArrayList<>(), nums);
   return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums){
   if(tempList.size() == nums.length){
      list.add(new ArrayList<>(tempList));
   } else{
      for(int i = 0; i < nums.length; i++){
         if(tempList.contains(nums[i])) continue; // element already exists, skip
         tempList.add(nums[i]);
         backtrack(list, tempList, nums);
         tempList.remove(tempList.size() - 1);
      }
   }
}
```  

In the Permutations II problem([Leetcode 47](https://leetcode.com/problems/permutations-ii/description/)), we skip the duplicates by maintaining a used[] array.

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, new boolean[nums.length]);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, boolean [] used){
    if(tempList.size() == nums.length){
        list.add(new ArrayList<>(tempList));
    } else{
        for(int i = 0; i < nums.length; i++){
            if(used[i] || i > 0 && nums[i] == nums[i-1] && !used[i - 1]) continue;
            used[i] = true;
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, used);
            used[i] = false;
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

In addition, there is a trick: !used[i - 1] and used[i - 1] has the same effect, but !used[i - 1] is more efficient. The idea is that, for "used[i - 1]", if we have the same value, then we will never have the chance to use the second one, which cause the tempList never growing to the length of list, and waste exists. But for "!used[i - 1]", we can directly skip those circumstances, where the second one as the leading element in the tempList(because [1, 1', x, x, x] is the same as [1', 1, x, x, x], and we can skip the 1' as the start of a permutation). Thus, every tempList we generate will reach to an end and return.

## Combination Sum

In the Combination Sum problem([Leetcode 39](https://leetcode.com/problems/combination-sum/description/)), **we sort the nums[] array first**, and keep adding the same reusable nums[i] until the sum >= target, then we backtrack to add the next nums[i + 1].

```java
public List<List<Integer>> combinationSum(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start){
    if(remain < 0) return;
    else if(remain == 0) list.add(new ArrayList<>(tempList));
    else{
        for(int i = start; i < nums.length; i++){
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, remain - nums[i], i); // not i + 1 because we can reuse same elements
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

In the Combination Sum II problem([Leetcode 40](https://leetcode.com/problems/combination-sum-ii/description/)), we skip the duplicates and forward the "start" + 1 as we backtrack, since we cannot reuse the same element.

```java
public List<List<Integer>> combinationSum2(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;

}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start){
    if(remain < 0) return;
    else if(remain == 0) list.add(new ArrayList<>(tempList));
    else{
        for(int i = start; i < nums.length; i++){
            if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, remain - nums[i], i + 1); // start + 1
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

In the Combination Sum III problem([Leetcode 216](https://leetcode.com/problems/combination-sum-iii/description/)), we check all the circumstances where each num in 1 ~ 9 as the start element in the tempList.

```java
public List<List<Integer>> combinationSum3(int k, int n) {
    List<List<Integer>> list = new ArrayList();
    backtrack(list, new ArrayList(), 0, 1, k, n);
    return list;
}

public void backtrack(List<List<Integer>> list, List<Integer> tempList, int sum, int start, int k, int n) {
    if(sum == n && tempList.size() == k) {
        list.add(new ArrayList(tempList));
        return;
    }
    for(int i = start; tempList.size() < k && sum + i <= n && i <= 9; i++) {
        tempList.add(i);
        backtrack(list, tempList, sum + i, i + 1, k , n);
        tempList.remove(tempList.size() - 1);
    }
}
```

## Palindrome Partitioning

In the Palindrome Partitioning problem([Leetcode 131](https://leetcode.com/problems/palindrome-partitioning/description/)), we try every nums[i] as the palindrome start and partition at every possible following index.

```java
public List<List<String>> partition(String s) {
   List<List<String>> list = new ArrayList<>();
   backtrack(list, new ArrayList<>(), s, 0);
   return list;
}

public void backtrack(List<List<String>> list, List<String> tempList, String s, int start){
   if(start == s.length())
      list.add(new ArrayList<>(tempList));
   else{
      for(int i = start; i < s.length(); i++){
         if(isPalindrome(s, start, i)){
            tempList.add(s.substring(start, i + 1)); // partition at index i
            backtrack(list, tempList, s, i + 1);
            tempList.remove(tempList.size() - 1);
         }
      }
   }
}

public boolean isPalindrome(String s, int low, int high){
   while(low < high)
      if(s.charAt(low++) != s.charAt(high--)) return false;
   return true;
}
```

By the way, there are several ways to check if a string is a palindrome.
* Navigate the string forward and backward simultaneously to see if s.charAt(forward) == s.charAt(backward).
* Reverse the string to see if it is equal to the original one.
* Recursively to check if s.substring(forward++, backward--) is a palindrome.
* Check from the center of the string, expanding the palindrome until s.charAt(forward) != s.charAt(backward). (Especially efficient for finding the longest palindrome)

At last, the backtracking algorithm is pretty different from the branch-and-bound algorithm although both of them immediately stop proceeding search when they find the current state unsatisfactory(见好就收😂). The backtracking aims to find all the possible solutions available to the problem, which involves **feasibility** function. However, the branch-and-bound is used to solve the optimization problem, which involves **bounding** function.
