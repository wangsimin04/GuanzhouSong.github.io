---
layout: post
title:  Leetcode-Array
date:   2017-05-26 18:50:00 +0800
categories: 不周山
tag: LeetCode
---

* content
{:toc}


附上地址：https://leetcode.com/tag/array/

### Num1. Two Sum

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```java
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];
        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i<nums.length;i++){
           if(map.containsKey(target-nums[i])){
                result[0]=Math.min(i, map.get(target-nums[i]));
                result[1]=Math.max(i, map.get(target-nums[i]));
                return result;
           }
           map.put(nums[i], i);
        }
        return result;
    }
```
这道题要求O(n),直接遍历一般都不会是正确答案。这道题的主要思想是，先取一个数n，检测HashMap中是否存在target-n，没有的话就把n放进HashMap，检测数组中下一个n。

有点不清楚的是，这道题中HashMap.containsKey不知道是不是O(n)的，不然就和直接遍历没两样了。

### Num11. Container With Most Water

Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0).

Find two lines, which together with x-axis forms a container, such that the container contains the most water.

Note: You may not slant the container and n is at least 2.
```java
int maxArea(int* heights, int n) {
    int water = 0, *i = heights, *j = i + n - 1;
    while (i < j) {
        int h = *i < *j ? *i : *j;
        int w = (j - i) * h;
        if (w > water) water = w;
        while (*i <= h && i < j) i++;
        while (*j <= h && i < j) j--;
    }
    return water;
}
```
It's in C but the main idea is the same. First you start two lines **i** & **j**, being the start and the end accordingly. Then using **w** to record the max volumn of water. Next move **i** and **j** toward the middle, keeping moving unless the next line is greater than the current one, do for both **i** and **j**. Then calulate **min(i,j)*|i-j|** compare with **w** and then w = max(w,min(i,j)\*|i-j|)
Finally return w.
这道题的主要就是从两边进行搜索，这也是后面很多题目用到的方法。


How this approach works?

Initially we consider the area constituting the exterior most lines. Now, to maximize the area, we need to consider the area between the lines of larger lengths. If we try to move the pointer at the longer line inwards, we won't gain any increase in area, since it is limited by the shorter line. But moving the shorter line's pointer could turn out to be beneficial, as per the same argument, despite the reduction in the width. This is done since a relatively longer line obtained by moving the shorter line's pointer might overcome the reduction in area caused by the width reduction.

### Num15. Three Sum

Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

Note:

The solution set must not contain duplicate triplets.

Example:

Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]


一开始以为只要用**Num1. Two Sum**的方法遍历一遍数组，O(n^2)看上去也不错。但是忽略的一点是three sum可能有重复的数组whereas two sum中已经有前提保证两个数不相等丙炔结果唯一。因此不能用two sum现成的代码。

换个思路，既然都已经O(n^2)了，为什么不排序呢，排序只需要O(nlogn),影响不大。所以排序以后就简单了很多了。
```java
public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        int i=0;
        while(i<nums.length-2){
            if(nums[i]>0) break;
            int j= i+1;
            int k = nums.length-1;
            while(j<k){
                int sum = nums[i]+nums[j]+nums[k];
                if(sum==0){
                    ArrayList<Integer> a = new ArrayList();
                    a.add(nums[i]);
                    a.add(nums[j]);
                    a.add(nums[k]);
                    result.add(a);
                }
                if(sum<=0) while(nums[j]==nums[++j]&&j<k);
                if(sum>=0) while(nums[k]==nums[--k]&&j<k);
            }
            while(nums[i] == nums[++i] && i < nums.length - 2);
        }
        return result;
    }
```

排序以后，从头开始检索，注意不需要检索所有的数字，因为要sum==0，必须有个数字为负数，所以当第一个检索的数字>0以后就没必要检索了，直接continue出while就行。后面用到的思想和前一题一样，从两头开始找。碰到sum==0，记录并加入数组。sum>0，说明大的数字大了，往左边移动，sum<0则是小的数字小了，往右移动，当两个碰到以后，就没必要再检索了。注意！即使sum==0，两头也需要移动，即
 ```
 if(sum<=0) while(nums[j]==nums[++j]&&j<k);
 if(sum>=0) while(nums[k]==nums[--k]&&j<k);
 ```
一开始忘记了，导致死循环。

### Num16 Three Sum Closest
和上一题一样，只不过加一个min记录sum与target之间的最小距离，还要一个result记录min对应的sum。
```java
public int threeSumClosest(int[] nums, int target) {
        int min = Integer.MAX_VALUE;
        Arrays.sort(nums);
        int result =0;
        int i = 0;
        while(i<nums.length-2){
        	int j = i+1;
        	int k = nums.length-1;
        	while(j<k){
        		int sum = nums[i] + nums[j] + nums[k];
        		if(min>Math.abs(target-sum)) {
        			result = sum;
        			min = Math.abs(target-sum);
        		}
        		if(sum<=target) j++;
            	if(sum>=target) k--;
        	}
        	i++;
        }
        return result;
}
```
写的时候又忘记sum=target时要两头移动了，结果又是死循环……

### Num18 Four Sum

真无聊，其实就是不停地嵌套循环利用**three sum**和**two sum**的函数。

不过令我印象深刻的是下面这个代码（不是我写的）。
>The first time win over 100%. Basic idea is using subfunctions for 3sum and 2sum, and keeping throwing all impossible cases. O(n^3) time complexity, O(1) extra space complexity.

虽然他的代码依然是O(n^3)的，但是实际运行起来是飞快的，原因就是他排除了很多不可能的情况，例如：
```java
//数组连4个数都没有，当然返回空
if (nums == null || len < 4)
  return res;

//在已经排序的前提下，最小的四个数相加都超过了target，或者最大的四个数加起来都不到target，也没必要算下去了
if (4 * nums[0] > target || 4 * nums[len - 1] < target)
    return res;
```
虽然一个个排除特殊情况很费事，但是如果这种异常经常出现，那确实可以省下不少无谓的计算。

完整的代码如下：
```java
public List<List<Integer>> fourSum(int[] nums, int target) {
		ArrayList<List<Integer>> res = new ArrayList<List<Integer>>();
		int len = nums.length;
		if (nums == null || len < 4)
			return res;

		Arrays.sort(nums);

		int max = nums[len - 1];
		if (4 * nums[0] > target || 4 * max < target)
			return res;

		int i, z;
		for (i = 0; i < len; i++) {
			z = nums[i];
			if (i > 0 && z == nums[i - 1])// avoid duplicate
				continue;
			if (z + 3 * max < target) // z is too small
				continue;
			if (4 * z > target) // z is too large
				break;
			if (4 * z == target) { // z is the boundary
				if (i + 3 < len && nums[i + 3] == z)
					res.add(Arrays.asList(z, z, z, z));
				break;
			}

			threeSumForFourSum(nums, target - z, i + 1, len - 1, res, z);
		}

		return res;
	}

	/*
	 * Find all possible distinguished three numbers adding up to the target
	 * in sorted array nums[] between indices low and high. If there are,
	 * add all of them into the ArrayList fourSumList, using
	 * fourSumList.add(Arrays.asList(z1, the three numbers))
	 */
	public void threeSumForFourSum(int[] nums, int target, int low, int high, ArrayList<List<Integer>> fourSumList,
			int z1) {
		if (low + 1 >= high)
			return;

		int max = nums[high];
		if (3 * nums[low] > target || 3 * max < target)
			return;

		int i, z;
		for (i = low; i < high - 1; i++) {
			z = nums[i];
			if (i > low && z == nums[i - 1]) // avoid duplicate
				continue;
			if (z + 2 * max < target) // z is too small
				continue;

			if (3 * z > target) // z is too large
				break;

			if (3 * z == target) { // z is the boundary
				if (i + 1 < high && nums[i + 2] == z)
					fourSumList.add(Arrays.asList(z1, z, z, z));
				break;
			}

			twoSumForFourSum(nums, target - z, i + 1, high, fourSumList, z1, z);
		}

	}

	/*
	 * Find all possible distinguished two numbers adding up to the target
	 * in sorted array nums[] between indices low and high. If there are,
	 * add all of them into the ArrayList fourSumList, using
	 * fourSumList.add(Arrays.asList(z1, z2, the two numbers))
	 */
	public void twoSumForFourSum(int[] nums, int target, int low, int high, ArrayList<List<Integer>> fourSumList,
			int z1, int z2) {

		if (low >= high)
			return;

		if (2 * nums[low] > target || 2 * nums[high] < target)
			return;

		int i = low, j = high, sum, x;
		while (i < j) {
			sum = nums[i] + nums[j];
			if (sum == target) {
				fourSumList.add(Arrays.asList(z1, z2, nums[i], nums[j]));

				x = nums[i];
				while (++i < j && x == nums[i]) // avoid duplicate
					;
				x = nums[j];
				while (i < --j && x == nums[j]) // avoid duplicate
					;
			}
			if (sum < target)
				i++;
			if (sum > target)
				j--;
		}
		return;
}
```

### Num26 Remove Duplicates from Sorted Array
要求in place对数组进行去重操作，只要一个指针**j**记录未重复的结果的最后一位，然后**i**管自己往后便利，碰到不一样的就nums[++j]=nums[i]，返回不重复的个数也很简单，把++j返回就行了。
```java
public int removeDuplicates(int[] nums) {
       if (nums.length==0) return 0;
       int j = 0;
       for(int i =0;i<nums.length;i++){
    	   if(nums[i]!=nums[j]) nums[++j]=nums[i];
       }
       return j+1;
}
```

### Num27 Remove Element
一样的思路，利用**j**记录当前所有不包含val的数字的最后一位。

这里发现可以有两种思路，第一种就是如果nums[i]!=val就操作，这样非常简单；但是我一开始的想法是碰到nums[i]==val再操作，结果发现整个程序不但布满了while和if，还要很小心得处理溢出。

所以如果一道题需要考虑的条件太多，尤其是添加了大量的if，可以从反方向进行思考，没准会简单很多。
```java
public int removeElement(int[] nums, int val) {
	        int j = 0;
	        for(int i=0;i<nums.length;i++){
	        	if(nums[i]!=val)nums[j++]=nums[i];
	        }
	        return j;
	    }
```
### Num31 Next Permutation
Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).

The replacement must be in-place, do not allocate extra memory.

Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1

题目都没看懂……好吧先来搞懂全排列是什么东西：
[全排列简介](https://yq.aliyun.com/articles/863)
```
在当前序列中，从尾端往前寻找两个相邻元素，前一个记为first，后一个记为second，并且满足first 小于 second。然后再从尾端寻找另一个元素number，如果满足first 小于number，即将第first个元素与number元素对调，并将second元素之后（包括second）的所有元素颠倒排序，即求出下一个序列
example:
6，3，4，9，8，7，1
此时 first ＝ 4，second = 9
从尾巴到前找到第一个大于first的数字，就是7
交换4和7，即上面的swap函数，此时序列变成6，3，7，9，8，4，1
再将second＝9以及以后的序列重新排序，让其从小到大排序，使得整体最小，即reverse一下（因为此时肯定是递减序列）
得到最终的结果：6，3，7，1，4，8，9
```
大概就是这么个意思。
```java
public int[] nextPermutation(int[] nums) {
		int i=nums.length-1;
		for(;i>=1;i--){
	         if(nums[i]>nums[i-1]){
	             break;
	         }
	      }

		//swap
		 if(i!=0){
			 for(int j = nums.length-1;j>i;j--){
		            if(nums[j]>nums[i]){
		                int t = nums[j];
		                nums[j] = nums[i];
		                nums[i] = t;
		                break;
		            }
		        }
		 }

		 // reverse
		 int first = i;
	        int last = nums.length-1;
	        while(first<last){
	            int t = nums[first];
	            nums[first] = nums[last];
	            nums[last] = t;
	            first ++;
	            last --;
	        }
	        return nums;
    }
```

### Num33 Search in Rotated Sorted Array
这道题有点绕，关键是要找到到底旋转了几个位置。

首先是查找旋转了几位，关键就是找到真正的0号位现在的位置。通过中位数进行判断，如果中位数大于最后一位数，说明真正的0号位在中位数的右边。不断二分查找，当low=high时就说明找到了。
找到后，只需要每次二分查找的时候都加上这个增加的位置然后用数组长度取模即可。
```java
public int search(int[] nums, int target) {
    int low = 0;
    int high = nums.length-1;
    int mid = (low+high)/2;

    //确定旋转了多少位置
    while(low<high){
      mid = (low+high)/2;
      if(nums[mid]>nums[high]){
        low = mid + 1;
      }else high = mid;
    }
    int ro = low;
    int realmid;
    low = 0;
    high = nums.length-1;

    //根据旋转的位置二分查找
    while(low<=high){
        mid=(low+high)/2;
        realmid=(mid+ro)%nums.length;
        if(nums[realmid]==target)return realmid;
        if(nums[realmid]<target)low=mid+1;
        else high=mid-1;
    }
    return -1;
}
```

### Num34 Search for a Range
