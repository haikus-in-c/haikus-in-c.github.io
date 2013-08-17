---
layout: post
title: "Yodle Programming Challenge: Triangle Sums"
author: "Ruchir Khaitan"
comments: true
category: posts
---





Today's puzzle is an exercise in recursion and iteration, and the costs and benefits of both. The task is simple, given a triangle of numbers, here defined as an arrangement of numbers where the first row has one number, the second row has two, and the nth row has n numbers, the task is to find the maximum total going from top to bottom using only adjacent numbers, or the maximum path sum. A sample triangle and top down path are shown below. The challenge is to do this for a large triangle, with at least a hundred rows, where there are so many top down paths that brute force solutions are impossible.

{% highlight latex %}

		1			max path: 1
	  3   5				  	  5	
	6   7   2			      7
					total:	 13 

{% endhighlight %}

There are two key questions that need to be answered in order to solve this problem. Firstly, how are we going to represent the triangle so that the program can use the data? One initially attractive solution is to use a tree structure, since the shape of a triangle suggests a tree almost immediately and each node would only have two children. However, this approach falls short, as though each parent node would have two child nodes, most of those nodes would be shared between two parents, which could lead to memory usage and implementation complications. Instead, lets examine what we know. We know that each triangle will only ever have n rows, and that each nth row will have n numbers. This means that if the triangle is represented as a text file, then just by counting the number of lines we can know that there will be n(n+1)/2 elements, which can be stored in an array.


{% highlight java %}
	int* triangle = malloc(sizeof(int)*(numLine*(numLine+1))/2 + 1);
    		while(fgets(buf, sizeof(buf), file)) {  
        		num =(int)  strtol(buf, &numEnd, 10);
			do {
	     			triangle[j] = num;
             			j++;
	     		} while(num = (int) strtol(numEnd, &numEnd, 10) );
    		}		
    		fclose(file);  //Continued below
{% endhighlight %}

So then the second question is whats the most efficient way to compute the maximum path? One approach is the pure recursive approach which I also like to call the top-down approach. Here, the basic procedure is: start at the top, and find the maximum path sum from the left and right children and return the greater sum + root node (the node the method was called on) value. In the base case, when a node doesn't have any children, it simply returns its own value. One advantage of the array implementation is that to go from parent to child, we dont need links, we can use array offsets because once we know the array index of a node, its left child's index is the array index + its row number and the right child's index is the left child's index + 1. 


{% highlight java %}
	long recPathSum(int* root,int rowNum,int triSize,long currSum ) {
    		long leftSum, rightSum;
    		if(rowNum == triSize) 
			return currSum + *(root);
    		int* child1 = root + rowNum;
    		int* child2 = child1+ 1;
    		currSum += *root;

    		leftSum = recPathSum(child1, rowNum+1,triSize,currSum);
    		rightSum = recPathSum(child2, rowNum+1, triSize, currSum);
    		if (leftSum > rightSum) 
			return leftSum;
         	return rightSum;
	}
{% endhighlight %} 

This algorithm works for small triangles but it takes far too long for larger ones. Why? Here recursion fails because the program doesn't reduce the amount of computation needed with each recursive step. Say we have a 100 row triangle and we run this function. After one level of recursion, we have two 99 row triangles. Another level of recursion deeper, we get four 98 row triangles. Thus, in this instance, recursion is actually making the problem exponentially larger.

Another, much faster solution is the bottom-up way, which I also call the iterative solution. The key insight is that the second to last row of numbers is also a row of small triangles each with only two rows. For those, finding the maximum path sum is easy. Note - Theres no need to write new code for that computation, we can reuse the recursive method. So, we replace each sub-triangle's root node with a number representing the maximum path sum through it. Thus, the bottom two rows can be resolved into one row of numbers corresponding to the maximum path sums from each of the nodes on that row. From there, we can move up to the next row being careful to always look only one row below the one we are currently on, and we can quickly work our way up to the top. Eventually, the root node will contain the maximum path sum. 

{% highlight java %}

	long iterativePathSum(int* root, int triSize) { 
		int i, j , k;
    		for (i = triSize - 1; i>0; i--) {
        		int* offset = root + (i*(i-1))/2;   
         		for(j = 0; j<i; j++) {
             			*(offset+j) = (int)recPathSum((offset+j),i,i+1,0); 
        		}
    		}
    		return *(root);

	} 
{% endhighlight %}

Obviously, this approach is destructive and totally overwrites the data stored in the array, but copying over an array of elements doesnt take too much time or space, and so, in this case, the benefits far outweigh the costs.



