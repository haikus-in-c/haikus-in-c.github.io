---
layout: post
title: "No Easy Questions"
author: "Ruchir Khaitan"
comments: true
category: posts
---

Today, I'm going to subvert a key principle of this blog for a little bit, namely the idea that an educational or even interesting probem has to be somewhat complex, hence the term programming puzzles. The problem I'm going to examine today is simply: given a list of n objects (the code is only going to handle integers but the ideas apply equally well to all data), find the number of unique elements it contains. This question is simple enough that one doesn't need to be a compuuter scientist to solve it. All one has to do is, at each element, scan the remainder of the list. If you can find another element that matches the one you were looking at, obviously it's not unique. The code below expresses this algorithm in C:

{% highlight java %}
	int naive_find_num_unique(int* array, int size){
        	int i, j, unique, num;
        	num = 0;
        	for( i =0; i<size; i++){
                	unique = 1;
                	for(j=i+1; j<size; j++){
                        	if(array[i]== array[j])
                                	unique = 0;
                	}
                	if(unique)
                        	num++;
        	}
        	return num;
	}
{% endhighlight %} 

You'll notice that I named this function "naive_find_num_unique" for a reason. This function, while very easy to understandd and very memory efficient, has horrible performance, because of its O(n^2) time complexity, which occurs because given an input of size n, for each element i in n, we need to read the remainder of the list. Since the number of steps we need to read varies from (n-1) at the front, to zero at the end, meaning an average of about n/2 steps taken n times, it's clear that the algorithm takes k/2 * n^2 + b*n +c steps - but what matters is the O(n^2). While this is bad, in the context of this problem this performance actually isn't too far from a theoretical maximum since in order to find the answer, any solution must look at every value at least once, and therefore, the optimal solution must be O(n). 
Before we totally dismiss this most naive solution perhaps I should stress that it's still not worthless, rather this algorithm, because of its simplicity, is really good for quick and dirty initial solutions or for a system that has no memory to spare like some embedded systems. However, the question still remains: how low can you go (time complexity-wise)?

Its clear that in order to improve the performance of this algorithm, we need to speed up the slowest step. As it stands, the slowest step is scanning the remainder of the array, to check whether an equivalent value exists. One way to speed up this process is with a bit vector,which represents sets of data using single bits, a 1 meaning the element is in the set, and a 0 meaning it isnt. Our way forward is now clear, first, we initialize an array of bits to zero. Then, for each nth element in our array of data value, we check the corresponding nth bit in the bitvector. If this bit is zero, then that indicates that this element hasn't yet been seen and so we raise that bit, and scan the rest of the array, looking for equivalent elements, and if any are found, raising the bits at those indices. On the other hand, if the corresponding bit is high, then that indicates that we have already seen this element and so we can move forward. 
{% highlight java %}
	int find_unique2(int* array, int size) {
        	int i, j, num;
        	num =0;
        	char* bvector = (char*) calloc(size, sizeof(char));
        	for(i = 0; i<size; i++){
                	if(!bvector[i]){
                        	num++;
                        	bvector[i] = 1;
                        	for(j =i+1; j<size; j++){
                                	if(array[i] == array[j])
                                        	bvector[j] = 1;
                        	}
                	}	
        	}
        	free(bvector);
        	return num;
	}
{% endhighlight %}

Again there are a few caveats. First, this isn't a true bit vector, rather it's a byte vector, meaning that I'm using eight times more memory than I ought to be, to the outrage of system engineers everywhere. In my defense, I'm using four times less than I would be representing the data as an array of integers, I'm only making one call to malloc(), and on the overwhelming majority of modern computers, memory is pretty much always available in excess. 
Oh and yeah, this function's much faster, because now it only scans ahead sometimes rather than all the time. Despite this improvement however, the worst case runtime (which occurs when each element in the array is unique) is equivalent to the earlier solution. If you allow my logic that an array of n elements has m distinct elements contained within it, where n and m are both positive integers, then the runtime here will be O(mn), which is a better indicator than O(n^2). Note however that since m = n/k where k is constant, the two are actually identical asymptotic upper bounds. 
Basically, while I've made searches faster, they still average out to a O(n) operation. The only way to escape this is to use a more complex data structure that lets me search faster, whether it contains what I'm searching for or not. 
One such data is a binary search tree. Trees in computer science are like trees in nature flipped pside down, with a "root" at the top, and "leaves" at the bottom. Each node of the tree has at most two "children" and one "parent", hence binary tree, except leaves, which by definition have no children since they are the endpoints of the tree and the root, which has no parent. Finally, what makes it a binary search tree is the property that at any node n, n's left descendants (left chid, plus its kids, plus their kids) only contain values smaller than that contained in n, while n's right descendants only contain values larger than n. If this all sounds a bit insane, don't worry, because this is one of those times where code makes more sense than English ever could.
{% highlight java %}
	struct btree_node{
        	int value;
        	struct btree_node* parent;
        	struct btree_node* left;
        	struct btree_node* right;
	};

	struct btree_node* create_node(struct btree_node* parent, int x){
        	struct btree_node* newNode = (struct btree_node*) malloc(sizeof(struct btree_node));
        	newNode->value = x;
        	newNode->left = NULL;
        	newNode->right = NULL;
        	newNode->parent = parent;
        return newNode;
	}
	void insert_node(struct btree_node** node,int x, struct btree_node* parent){
        	if(*node == NULL){
                	*node = create_node(parent, x);
                	return;
        	}

        	if(x<(*node)->value) {
                	insert_node(&((*node)->left), x, *node);
                	return;
                }

        	if(x>(*node)->value) {
                	insert_node(&((*node)->right), x, *node);
                	return;
        	}
        	return;
	}

	struct btree_node* bst_search(struct btree_node* node, int x){
        	if(node==NULL)
                	return NULL;
        	if(node->value == x)
                	return node;
        	if(x<node->value)
                	return bst_search(node->left, x);

        	return bst_search(node->right, x);
	}
{% endhighlight %}    
   
Now, the organization of data within a binary search tree lets us easily perform a much faster binary search, letting us potentially halve the number of options we have to look at with every step. Of course, this comes with some negatives, as now insertions require a search to find the appropriate location to place the element. Also, the data structure requires more memory managements, meaning more malloc()s and frees()s.Unfortunately, in order to achieve speed gains in computer science, we often have to trade that against negatives like higher memory use, or more complex implementations. However, in this case, once the data structure is fully set up (the entire code is on [Github][github]), then the algorithm to compute the number of unique elements is essentially the same as that used above, only with a binary search tree in place of a bitvector. 
{% highlight java %}
int find_bst(int* array, int size){
        int i, num;
        struct btree_node* root = init_tree(array[0]);
        num = 1;
        for(i=0;i<size; i++) {
                if(!bst_search(root, array[i])){
                        num++;
                        insert_node(&root, array[i], NULL);
                }
        }
        //print_rec(root, 0);
        free_tree(root);
        return num;
}

{% endhighlight %} 
Now, this function takes O(n* log n) since there are n elements, and because of the nature of the binary searches required for insert and search, which are both O(log n) operations as if n were to double, that would only really add one more level or one more node for those functions to go through. At this point however, I have to address the fact that these are not facts/laws that are true always, but rather assumptions that are true in general. Imagine an evil user inputing 1, then 2, then 3 and so on. The root would have one child which would have one child and so on. That's not a tree, that's a grapevine! It's more like a linked list, but that doesn't really extend the botanical annalogy. Still, in the worst case, these operations are still O(n) meaning in the worst case, I'm not any better off than when I started. 
That sucks. Really, if I'm going to spend so much time building a system that performs at a high level, I can't let it get tripped up by something as common as a set of sorted inputs. That could happen at any time. Of course, if I could check my inputs in advance, I could check in linear time if it was sorted, and then compute the number of unique elements in linear time as well (by comparing adjacent values) but I want a different option, a data structure that works for me in O(log n) or better for inserts and searches no matter what. 
One such structure is the red-black tree, which is a self-balancing tree, meaning that, if the tree starts to form a nonoptimal, vinelike shape, it will automatically balance itselfback into a more even tree where most elements have two children. One important thing to note here is that self-balancing means that the "root" node can change from time to time, and so we need a tree struct to go along with a node struct.
The full details of a red-black trees theory are fairly complex and probably better explained [here][wikiredblack]. The two key takeaways are that implementing a red-black tree requires rotations, which are symmetrical operations that take a node X and its left child Y and transform it into a node Y with a right child X (corresponding to a right rotation of X) or vice versa, (which corresponds to a left rotation of Y). Basically all rotations on a node, whether left or right are given in context of a node and its right or left child respectively. I've attached the code for a left rotation below to help clarify. 

{% highlight java %}
	//Here rb_wrap is a wrapper class for red black trees 
	//and rb_tree is a class for reb-black tree nodes that's identical to btree_node 
	void left_rotate(struct rb_wrap* rb_tree, struct rb_tree* node){
        	if(!node)
                	return;
        	struct rb_tree* right_child = node->right;
        	if(!right_child)
                	return;
        	//printf("performing left rotate on node value: %d\n", node->value);
        	node->right = right_child-> left;
        	if(right_child->left)
        	if(node->parent){
                	if(node == node->parent->left){
                        	node->parent->left = right_child;
                	}
                	else {
                        	node->parent->right = right_child;
                	}
        	}
        	if(rb_tree->root == node)
                	rb_tree->root = right_child;

        	right_child->left = node;
        	node->parent = right_child;
	}


{% endhighlight %}
The second key takeaway is that insertions become horrendously complicated, with a long sequence of rules, checks and cases (5 when you take away left/right duplicates) and transformations that are difficult to remember, hard to implement correctly, and add a real world operational cost not visible when you're just looking at big-O runtimes. Now, when we use red-bllack trees in the algorithm to determine the number of unique values, it will have O(n log n) performance, but is it worth it if I could never code red black trees without consulting a book or two?
Fortunately, there is a another option, a balanced data structure thats easy to understand and easy to get running. It's called skip lists, and the best analogy I can give for them actually goes to something very dear to my New Yorker heart (I've lived there two whole years): the subway. Say you want to go from Times Square to Williamsburg. Only a fool (or a tourist) would take local lines all the way there as the express trains save time by "skipping" stops that aren't very popular. Now imagine that there were a third line, with stops at lets say Times Square, the bridge (which is kind of like a midpoint), and somewhere in Williamsburg. For you, this hypothetical third line would be the best option since it "skips" even more stops. A skip list is like this expanded subway, only with K lines instead of two or three, and a base linne that connects all the data and is basically an ordered linked list. 

From there search should be pretty intuitive (definitely for the New Yorkers). You search just as you would travel: take the best express as far as it can take you wthout going over, then the next, then the next, until finally you reach your stop. Inserts should be familiar to anyone who's dealt with sorted linked lists; you just do a search until you figure out between which two nodes your element should go and then you re-route the links to attach the new element in the middle. 
Really, the only remaining question is how do we determine how many express lines (levels) a node is connected to? Sure, every node connects at least on the base levels, but what about the ones above that, the expresses? In New York's subway, and subways worldwide, this is setted by a popularity contest. Since everyone loves Times Square, its connected to (every) express. Columbia, not so much. Unfortunately, we can't know which elements are going to be accessed most often in all cases, so that approach goes out the window.   
Insted, we just toss coins. Fifty-fifty shot it connects to level two. If it does, fifty-fifty shot it connects to level three, and so on. That way, each level up will contain approximately 1 half of the nodes compared to the level belo it, and to search, going to the first node in the topmost level would put you square in the middle of the base linked list (on average). Then you could go down and search as you please in O(log n) time on average. This amortized analysis of average runtime is slightly different from the average case/worst case performance of a binary search tree because here, there is no worst case. The performance tends to O(log n) because the data structure isn't fully deterministic, and coin tosses tend to be pretty close to even heads and tails. It's possible, though unlikely, that you could insert ten elements and have them all only link on the base level leading to a worst case O(n) runtime. But the difference is, this result isn't repeatable, it's just random. 
This solves all the conceptual problems, but leaves us dependent on random number generators to dictate our coin toss. I decided instead that a coin toss is the same as a bit being low or high, or a number being odd/even and made the number of levels a given node has dependant on the size of the linked list at the moment. Essentially, if a node is the third node inserted into a skip list, it will connect only at the bottom level (level 0) since it doesn't divide evenly by two, or to put it another way, its zero order bit is high. Instead, if its the fourth node to be inserted, it will connect on level 0, 1, and 2, since it divides by 2 and four, or to put it another way, its second order bit is the first high bit. This method is faster than using a random number generator at every "coin toss" and I guess all that's left is to show you the code.
{% highlight java %}
struct skip_node{
        struct skip_node** array;
        int data;
};

struct skip_list {
        struct skip_node* head;
        int levels;
        int size;       //number inserted
        int curr_level;
};
int search_skip_list(struct skip_list* list, int value) {
        int i;
        struct skip_node* curr, *prev;
        curr = list->head;
        for(i =list->levels-1; i>= 0; i--){
                while(curr->array[i] !=NULL && curr->array[i]->data <value) {
                        curr= curr->array[i];
                }
        }
        curr = curr->array[0];
        if(curr && curr->data == value)
                return 1;
        return 0;
}
struct skip_node* insert_skip_list(struct skip_list* list, int value){
        struct skip_node** touched = (struct skip_node**) calloc(list->levels, sizeof(struct skip_node*));
        list->size++;
        struct skip_node* curr = list->head;
        int i, node_level;
        node_level = 1;
        for(i = list->levels-1; i>=0; i--){
                while(curr->array[i] != NULL  && curr->array[i]->data < value) {
                //      printf("Curr data in insert %d\n", curr->data);
                        curr = curr->array[i];
                }
                touched[i] = curr;
        }
        curr = curr->array[0];
        //printf("Final curr data in insert %d\n", curr->data);
        if(!curr || curr->data != value) {
                int exp = 1;
                int test = list->size;
                for(i = 1; i<list->levels; i++){
                        exp = exp*2;
                        if(exp <= test && test%(exp) == 0){
                                node_level++;
                        //      test = test/2;
                        }
                         else{
                                break;
                        }
                }

                struct skip_node* new_node= create_skip_node(list, value, node_level);
                if(node_level> list->curr_level){
                        for(i = list->curr_level +1; i< list->levels; i++) {
                                touched[i] = list->head;
                        }
                        list->curr_level = node_level;
                }
                for(i = 0; i< node_level; i++) {
                        new_node->array[i] = touched[i]->array[i];
                        touched[i]->array[i] = new_node;
                }
        }
        free(touched);
}
{% endhighlight %}
However, one important factor that changes by using this method is that now the data structure isn't truly randomized, since the heads/tails come at fixed intervals and so, there now exists a "worst-case" input, which I'll leave you to figure out (hint: it involves trees). Stilll, the key principles of skip lists remain, only a little more ordered, and I finally got a solution working in O(n og n) that I could explain to my little sister and/or grandma. 
Finally, I think we've done everything we can except for hash tables. I'm choosing not to cover that because frankly, I think they are a pretty large subject within data structures/algorithms more deserving of a larger post dedicated solely for them, and more importantly, so much  of a given hash tables performance boils down to its hash function, which I think that if you are going to invest the time to write your own should be tailored to perform well on expected data. That being said, I'd be remiss not to point out that hash tables could help us perform the algorithm in O(n). 
At the end of this long post, I feel a bit like a man who'd spent many hours trying to draw a perfect circle, or carve a surfboard, because my effort was largely unnecessary, given that all modern languages have good, if not great libraries for all kinds of data structures. Nobody really NEEDS to do this. However, I think learning some things, especially highly abstract stuff like this, is pretty difficult if you're just reading or listening to facts, theories or histories. In order to really understand, I have to question, to poke and prod, even grapple with an idea in the wild before I learn anything. Also, most of computer science is the analysis of either breaking complex problems into simple ones, or optimizing the hell out of solutions to "easy" problems. Google, for example, deals primarily with search, which is something every intro CS course covers. Just goes to show that even the simplest things, when made fast enough, can be worth a lot.      

[github][https://github.com/haikus-in-c/haikus-in-c/tree/master/2013.8/find_unique_element]
[wikiredblack]:http://en.wikipedia.org/wiki/Red%E2%80%93black_tree
