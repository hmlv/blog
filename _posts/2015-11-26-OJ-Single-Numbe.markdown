---
layout: post
title:  "Single Number"
date:   2015-11-26 17:40:00
categories: OJ
---


这是leetcode上的一组题，共3道。题目的大意就是在一组数中，除了一个数出现p次以外，其他数都出现k(k>1)次，其中p>1且p%k!=0。这3道题的思想就是位操作。重点是学习这个[思想][key2]。

##Single Number1##
[Problem][pro1]: Given an array of integers, every element appears twice except for one. Find that single one.
Note: Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
这道题目比较简单，利用异或（Xor）的特性（相同为0，不同为1），即两个相同的数字异或的结果为0，所以把整个数组异或一遍，就可以得到结果。

代码：

    class Solution {
        public:
            int singleNumber(vector<int>& nums) {
                int len = nums.size();
                if(len==0)
                {
                    return 0;
                }
                int ret = nums[0];
                for(int i = 1; i < len; i++)
                {
                    ret ^= nums[i];
                }
                return ret;
             }
     };

##Single Number2##
[Problem][pro2]: Given an array of integers, every element appears three times except for one. Find that single one.
Note: Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
这道题目的Discuss里面有一个非常好的[解答][key2]。这个解答的思想就是回归到数字在计算机中存储的方法，按bit存储。

*思路*：题目可以归结为只有一个数字出现p次(p>1 && p%k!=0)，而其他数字出现k次。假设我们使用的是32位整型，那么我们只需要统计这个array中所有数字的32个bit中，每个bit上"1"的出现次数，对于每一个bit，当遇到一个"1"，这个bit上的计数器就累加1，当累加器数值达到k的时候，累加器需要重置为0，最后累加器不为零的，这些位组成的数字就是我们要找的。

*注*：对于32位整型和k，如果k转换成2进制需要m位，这并不意味着我们需要32个m位的计数器，而是m个32为的数字，这些数字上的bit相对应，组成32个m位的计数器。m位计数器的原理：第i(i!=1)位（最高位），只有当第i-1到第1位和输入的数据位为1，第i位需要加1。

代码：

    class Solution {
	public:
		int singleNumber(vector<int>& nums) {
			int first_bit = 0;
			int second_bit = 0;
			int mask = 0;
			int len = nums.size();
			for(int i = 0; i < len; i++)
			{
				second_bit = second_bit ^ (first_bit & nums[i]);
				first_bit =  first_bit ^ nums[i];
				mask = second_bit & first_bit; //统计哪些位为（11），即这个位已经出现了3次“1”，那么这个位的计数清零（以下两行代码）
				second_bit = second_bit & (~mask);
				first_bit = first_bit & (~mask);
			}
			return first_bit | second_bit;
			
		}
	};

##Single Number3##
[Problem][pro3]: Given an array of numbers nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.
For example:
Given nums = [1, 2, 1, 3, 2, 5], return [3, 5].
Note: 
1. The order of the result is not important. So in the above example, [5, 3] is also correct.
2. Your algorithm should run in linear runtime complexity. Could you implement it using only constant space complexity?

这道题就是第一题的延伸，还是要利用异或的特性，当我们将数组中的数字都异或后，得到的结果就是两个只出现一次的数字的异或结果。这个异或的结果很重要，由于这两个数字中是独立的，所以异或结果中的每一个为"1"的bit都代表着在这个bit上这两个数字的值不一样，即只有一个数字在这个bit上的值是"1"。所以我们只需要用异或结果中不等于0的任何一位，就可以将整个数组划分成两部分，这两部分中各含一个single number。

代码:
	
	class Solution {
	public:
		vector<int> singleNumber(vector<int>& nums) {
			vector<int> ret{0,0};
			int len = nums.size();
			int diff = 0;
			for(int i = 0; i < len; i++)
			{
				diff ^= nums[i];
			}
			diff = diff & (-diff);
			for(int i = 0; i < len; i++)
			{
				if(nums[i] & diff)
				{
					ret[0] ^= nums[i];
				}
				else
				{
					ret[1] ^= nums[i];
				}
			}
			return ret;
		}
	};


[pro1]: https://leetcode.com/problems/single-number/
[pro2]: https://leetcode.com/problems/single-number-ii/
[pro3]: https://leetcode.com/problems/single-number-iii/
[key2]: https://leetcode.com/discuss/31595/detailed-explanation-generalization-bitwise-operation-numbers
