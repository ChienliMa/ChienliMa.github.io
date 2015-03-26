---
layout: post
title: "Leetcode battle field: largestNum (and TDD?)"
date: 2015-03-12 17:38:50 +0800
comments: true
categories: [LeetcodeBattlefield]
---

Today I solve another problem on leetcode, the process of solving this issue just like the old saying: “踏破铁鞋无觅处，得来全不费功夫”, which means people spared a lot of efforts on a problem but it was solved in a unexpected simple way.

Ok, let's talk about the problem:

```
  Given a list of non negative integers, arrange them such that they form the largest number.
  
  For example, given [3, 30, 34, 5, 9], the largest formed number is 9534330.
  
  Note: The result may be very large, so you need to return a string instead of an integer.
```
  
So the first thing I need to do is transfer integers into strings, which will help in the following comparasion. Next, I sorted the numbers based on their first digits because obsiviousl, ```9**``` is greater than ```1**```. Aftr that, I extracted numbers with same first digits and do the comparation. 


In the comparation, those numbers will be combine to form the biggest number they can. This is the most important part. At first, I wrote a complicated algorithm which did comparasion using  ```a[:length] > b[:length]```, and lots of code handle conner case -- one of the number is longger. The code went really long like 

This code went well expect for the test cases like ```[8308, 830]``` or ```[101,10]```: No matter how I compared thoes numbers, they are 'equal'. But when I combined them with different order, the result differed. I spent a long time trying to figure```(playing)``` a```(3ds)``` method```(game)```. Suddenly, I came out with a idea: "why not do the comparision by combine digits in different order and see which result is larger?". It Works, and the final algorithm became really simple:

```python
class Solution:
    # @param num, a list of integers
    # @return a string
    def largestNumber(self, num):
        num = [ str(n) for n in num ]
        # sort by the first digit 
        sorted_num = sorted(num, key = lambda x : x[0], reverse = True)
        
        # for number with same 'first number', combine them to get biggest
        result = ''
        for i in xrange(9, -1, -1):
            i = str(i)
            num_group = [ num for num in sorted_num if num[0] == i]
            if len(num_group)>0:
                result += self.maxNum( num_group ) 
        # check if number is zero
        if int(result) == 0:
            result = '0'
        return result
        
    def maxNum( self, nums ):
        """
        Given numbers(list of string) with same first digits, 
        combine them to get max number(string).
        """
        result = ''
        while len(nums) != 1:
            # init a winner and its index
            win = nums[0]
            win_index = 0
            win_length = len(win)
            
            # find a num, if added to result, yield biggest number
            for index in xrange(1, len(nums)):
                num = nums[index]
                result1 = win + num
                result2 = num + win
                if result1>result2:
                    num_win = False
                else:
                    num_win = True

                if num_win:
                    win = num
                    win_index = index
                    win_length = len(num)
                    
            # append the winner to the result
            result += nums.pop(win_index)
        # append the last number
        result += nums[0]
        return result
```

---------------------
###About TDD:

In Last day's interview, I was asked about my idea of TDD(Test Drive Development), I just answered:"I think it is usual, I wrote test for my codes.", which equals to answered nothing. These two days I developed leetcode's answer with the help of OnlineJudge's test cases, I did realise that writing test cases norishes a developer. 

Every time programmers write tests，they try covering every special cases they know， therefore codes pass their own tests are their best products within their ability. But when things come to production, codes will prossibly encouter cases that make it fail, which, just like a hole in developer's mind. Every time developer find such a case, it find a hole in his mind he never knows, then he fixes it. Day after dats, after millions of holes are fix, his mind hardly has hole. Therefore, his products, his codes, will become unbelievably stable.

That's my understand of TDD -- Test Drive Developer.

Just for fun, don't take it seriously. :)
  
