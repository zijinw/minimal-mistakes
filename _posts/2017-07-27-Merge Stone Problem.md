---
layout: single
title:  "Merge Stone Problem"
date:   2017-07-27 22:00:00
categories: Algorithm
---

# Merge stone problem's logic and code

![](https://raw.githubusercontent.com/zijinw/BlogPic/master/pictures/MergeStones.jpg)

	public class Solution {
    	public int minCost(int[] stones) {
    	    // The stones array is not null
    	    int[][] m = new int[stones.length][stones.length];
    	    for (int i = 0; i <= stones.length - 1; i++) {
    	        for (int j = i; j >= 0; j--) {
    	            if (j == i) {
    	                m[j][i] = 0;
    	            } else if (j == i - 1) {
    	                m[j][i] = stones[j] + stones[i];
    	            } else {
    	                m[j][i] = Integer.MAX_VALUE;
    	                int tempSum = partialSum(stones, j, i);
    	                for (int k = j; k <= i - 1; k++) {
    	                    m[j][i] = Math.min(m[j][i], m[j][k] + m[k + 	1][i] + tempSum);
    	                }
    	            }
    	        }
    	    }
    	    return m[0][stones.length - 1];
    	}
    	private int partialSum(int[] array, int i, int j) {
    	    int res = 0;
    	    for (int k = i; k <= j; k++) {
    	        res += array[k];
    	    }
    	    return res;
    	}
	}

Here actually we have duplicate work in calculating partialSum. Using the DP, we can also maintain a 2-D array that stores the partialSum from j to i, instead calculating the whole length all the time. Using this method, a better solution is as follows.

	public class Solution {
    	public int minCost(int[] stones) {
    	    // The stones array is not null
    	    int[][] m = new int[stones.length][stones.length];
    	    int[][] partialSum = new int[stones.length][stones.length];
    	    for (int i = 0; i <= stones.length - 1; i++) {
    	        for (int j = i; j >= 0; j--) {
    	            if (j == i) {
    	                m[j][i] = 0;
    	                partialSum[j][i] = stones[i];
    	            } else if (j == i - 1) {
    	                m[j][i] = stones[j] + stones[i];
    	                partialSum[j][i] = partialSum[j][j] + partialSum	[i][i];
    	            } else {
    	                m[j][i] = Integer.MAX_VALUE;
						// We can do this because of the property of our for-for loop: we always can build up this matrix from small difference between j and i to large difference between j and i
    	                partialSum[j][i] = partialSum[j][j] + partialSum	[j + 1][i];
    	                for (int k = j; k <= i - 1; k++) {
    	                    m[j][i] = Math.min(m[j][i], m[j][k] + m[k + 	1][i] + partialSum[j][i]);
    	                }
    	            }
    	        }
    	    }
    	    return m[0][stones.length - 1];
    	}
	}




