---
layout: post
title: "Cats vs Dogs"
author: "Ruchir Khaitan"
comments: true
category: posts
---

The final [Spotify][spotify] puzzle is a heavy metal showdown that brings together two beloved pets, and a wide variety of computer science topics. Unfortunately, problem took a lot longer than it should have, because I was stubborn, and refused to let go of preconceived ideas of how to solve it even when I could clearly see that my approach was incorrect. Basically, I was looking at it all wrong. The general premise, explained in more detail [here][funk], is that in a TV game show for cats and dogs where as each week, a pet is removed based on a vote. Each person watching votes for one pet that they want to keep on the show, and one that they want to remove, and people cannot choose members of the same species for both. Most importantly, the producers will remove pets in an effort to maximize viewership, and so the question is: what is the maximum number of voters that can be satisfied, meaning that both their choices have been carried out, given a set of votes (and assuming that those votes dont change over time).

My initial idea was simplistic and based on a flawed assumption. I inferred, purely on intuition, that the removed pets would be the ones with the least positive votes, and the most negative votes and created a data structure to represent pets and voters characterizing each pet by fitness which was the number of upvotes - the number of downvotes. I like to call this the "Reddit" solution.

{% highlight c %}
	struct pet {
    		int fitness; //running tally of upvotes - downvotes
    		int contending; //whether this pet is still in
	};
	struct voter {
    		struct pet* keep;
    		struct pet* reject;   
	}; 

{% endhighlight %}
Then I simulated the game by removing the least fit pets and keeping track of the number of people satisfied at each step, and then returning the maximum number. 

{% highlight c %}
	struct pet* find_min(struct pet* c_addr, int c,struct pet* d_addr, int d) {
		//returns weakest pet out of array of c cats and d dogs in O(n)
    		int min = 1000; //random large number 
    		int i = 0;
    		struct pet* min_addr, *curr;
    		for(i = 0; i< c; i++) {
        		curr = c_addr+ i;
			if(curr ->fitness < min && curr->contending != 0 ) {
	    			min = curr->fitness;
	    			min_addr = curr;
			}
    		}	
    		for(i = 0; i< d; i++) {
        		curr = d_addr + i;
        		if(curr->fitness< min && curr->contending != 0) {
            			min = curr ->fitness;
            			min_addr = curr;
        		}
		}
		min_addr->contending =  0;   
		return min_addr;	
	} 
	int sim_game(struct voter* v_addr,int v,struct pet*c_addr,int c,struct pet*d_addr,int d) {
    		int num_satisfied = 0; 
    		int max_satisfied = 0;
    		int pets_remain = c + d - 1;
    		int i;
    		struct pet* weakest;
    		while(pets_remain){
        		weakest = find_min(c_addr, c, d_addr, d); 
			for(i = 0; i< v; i++) {
	  		if(v_addr[i].keep->contending && !(v_addr[i].reject->contending))
	        		num_satisfied++;
        		}
        		if(num_satisfied> max_satisfied) 
	    			max_satisfied = num_satisfied;

			num_satisfied = 0;
			pets_remain--;
    		}
    		return max_satisfied;   
	} 
{% endhighlight %}

Unfortunately, this approach didnt work and I was at a loss to explain why since it seemed to work for the two test inputs given, and I couldnt engineer a new test case that would break my algorithm. Eventually, I realized the flaw in my reasoning which was that I implicitly assumed that the way to maximize viewer satisfaction would be to remove the least wanted pets, which seems like the right way to go about it, except that in doing so, it shifts ones focus on the pets when it should be squarely on the pets. I realized that the voters represented a bipartite graph, with one partition being the group that liked cats, and the other being the group that liked dogs. Here the edges on the graph represented incompatible pairings, or pairs where at least one person wants to remove a pet that the other wants to keep. Such pairs cannot mutually be happy, as to give one satisfaction would be to deny the other. With this insight in hand, the problem becomes a problem of finding the maximum cardinality matching, or the maximum set of edges without any common vertices on the graph. This represents all of the incompatible people from which one person has to be removed, and so, the answer is the number of voters v - the number of edges E in the maximum cardinality matching. This method requires first splitting the voters into two arrays, representing the two partitions in the graph.

{% highlight c %}
	int main(int argc, char** argv) {
    		int times, i,j,k, cats, dogs, voters, prefer, reject;
    		int cats_filled, dogs_filled;
    		char buf[256], type, *pch;
    		struct voter* cat_voters, *dog_voters, *current;
    		int ** edge_array, *visited, *match;
    		scanf("%d", &times);
    		for(j = 0; j< times; j++) {
        		scanf("\n");
			pch =fgets(buf, 256,stdin);  
			sscanf(buf," %d %d %d", &cats, &dogs, &voters);  
			//Malloc cat_voters & dog_voters set cat_filled & dog_filled to 0
			for ( i = 0; i< voters; i++) {	
	    			pch = fgets(buf, 256, stdin);
            			type = *buf;
	    			pch = strtok(buf, " ");
            			pch = strtok(NULL, " ");
            			prefer = (atoi(buf + 1));
            			reject = atoi(pch + 1);
	    			if (type == 'C' || type == 'c' )
	        			current = cat_voters + cats_filled++;
	    			else if (type == 'D' || type =='d')
					current = dog_voters + dogs_filled++;
	    			current->like = prefer;	
	    			current->hate = reject;
	}		
        //Continued below   
{% endhighlight %}
This part of the code is pretty similar to the input gathering part of the first program. Besides the splitting of the voters into two seperate arrays, the only other difference is that struct voter is now just a pair of two integers, corresponding to the number of the pets they like and hate. After that, I used a modified adjacency matrix (modified because instead of storing a 1 to signify an edge, I stored the destination edges number. This will be important later). 

{% highlight java %}
	//Continued from above
        edge_array = (int**) malloc(cats_filled * sizeof(int *));
	for(i = 0; i< cats_filled; i++){
		edge_array[i] =(int*) malloc (dogs_filled*sizeof(int));
	    	for (k = 0; k< dogs_filled; k++) {
	        	edge_array[i][k] = -1;
	    	} 
	    	for(k = 0; k<dogs_filled; k++){
	        	if(cat_voters[i].like == dog_voters[k].hate ||cat_voters[i].hate == dog_voters[k].like)     
		    		edge_array[i][edge_filled++] = k;
	    	}
        }
        //Continued below
{% endhighlight %}
Now finally, all I had left was to compute the maximum cardinality matching and return the correct answer. To do so, I use the augmenting path algorithm, which finds it by finding an augmenting path from each vertex in one partition to another and adding it to the matching if one exists. Basically this restructures the problem of finding a maximum cardinality matching into a maximum flow problem where there is a source (connected to all the vertices in one partition) and a sink (connected to all the vertices in another partition) and has complexity O(VE). A more efficient solution, known as the Hopcroft-Karp algorithm, is possible, but a little more complex to implement.
{% highlight c %}
	//Continued from above
    	match = (int*) malloc(voters* sizeof(int));
    	visited = (int*) malloc(cats_filled * sizeof(int));
    	memset(match, 0xffff, voters*sizeof(int));
    	for(i = 0; i< cats_filled; i++) {
		memset(visited, 0, cats_filled*sizeof(int));
		dfs(i, dogs_filled, match, visited, edge_array);
    	}
    	int ans = 0;
    	for(i =0; i<dogs_filled; i++) {
		ans+= (match[i] != -1); 
    	}
    	printf("%d\n", (voters-ans), sizeof(int));
    	for(i=0; i<cats_filled; i++) {
        	free(*(edge_array+i));
    	}
    	free(edge_array); free(match); 
    	free(visited); free(cat_voters); free(dog_voters);
  
}	

int dfs(int u,int dogs_filled,int* match,int* visited,int** edge_array) {
	if(*(visited + u)) 
        	return 0;
    	*(visited + u)= 1;
    	int i;
    	for(i =0; i<dogs_filled; i++) {
       		if(*(*(edge_array+u)+ i) != -1 && (match[ edge_array[u][i] ] == -1 || dfs(match[edge_array[u][i]],dogs_filled,match,visited,edge_array ))){
	    		match[ edge_array[u][i] ] = u;
	    		return 1;
		}
    	}
	return 0;
} 
{% endhighlight %}
Here the visited array keeps track of which vertices have been tried and is reset to zero at each iteration, while the match array keeps track of the matchings (found as augmenting paths using depth first search). Keep in mind that the for lop in main passes an i which is meant to be an index to a particular vertex in one partition (cat-lovers) while dfs() iterates through the entries in that row looking for a new path and stores it as a nonzero value in the match array. At the end the answer is simply the number of voters - the number of edges in the maximum cardinality matching.

This solution is admittedly a bit more complex than anything presented here specifically, and even though I knew the principle behind the failure of my fist attempt, and I guessed that my first solution would typically give smaller answers than the second, correct one I wasnt sure, so I wrote a small Python script to generate a large, random set of inputs conforming to the specifications in the problem text to find out

{% highlight java %}

	import random
	times = raw_input()
	f = open('input.txt', 'w')
	f.write(times + '\n')
	for j in range(int(times)):
    		cats = str(random.randint(1,100))
    		dogs = str(random.randint(0,100))
    		voters = str(random.randint(0, 500))
    		f.write(cats+' '+ dogs+ ' ' + voters+ '\n')
    			for i in range(int(voters)):
        			decider = random.random()
        			cat = random.randint(1,int(cats))
        			dog = random.randint(1, int(dogs))
        			if (decider > .51):
            				f.write('C' + str(cat) + " " + 'D'+ str(dog)+ '\n')
        			else:
            				f.write('D' +str(dog) + " " + 'C' + str(cat)+'\n')

	f.close()
{% endhighlight %}

Turns out I was right, the simulation based programs answer was always a little bit lower than the bipartite matching ones. I love using Python for little test programs like this which certainly could have been implemented in C, but using Python is much more hassle-free. Note that, if you copy this code directly into your text editor, you may have to reformat it as Python is whitespace sensitive.

At this point, you may be wondering why I included the code and lengthy description of my fist wrong attempt? I did so because I think that examining my mistakes and being forced to struggle because of them is ultimately much more educational than just getting it right the first time (though in practice that outcome is always preferred). I hope that my example serves to illustrate two somewhat contradictory propositions, that one shouldnt rush into coding the first half-baked idea that comes in his head particularly on a problem labeled Difficulty:Heavy Metal, and perhaps more importantly, one shouldnt be afraid to look at a problem in a completely new light, even if it means throwing away a lot of previously made progress and starting from scratch. Sometimes, thats the only way to get to a an answer. All in all, I think this post covered a lot of ground from failure, to graph theory, to Python and this problem was a good mix of frustrating and confusing at times, but pretty rewarding at the end.

[funk]:https://www.spotify.com/us/jobs/tech/catvsdog/
[spotify]:https://www.spotify.com/us/jobs/tech/
