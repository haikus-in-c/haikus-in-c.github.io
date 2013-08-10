---
layout: post
title: "Let's Get Funky"
author: "Ian Zapolsky"
category: posts
---

Today we return to [Spotify's hallowed puzzle page][spotify] for the second of the three puzzles
they offer there. This one, the middle brother in between [Reggae][reggae] and [Heavy Metal][heavy],
is called *Funk*.

Here's the problem: based on an input consisting of n songs on some album, where each song has its
own number of plays, we need to determine which song is the best (check the input format [here][funk]). 
However, while intuition tells us to assume the most played song to be the best, we have to take 
into account something called [_Zipf's Law_][zipf], which states that the earlier songs on an album 
(e.g. the first and second tracks) will be played more often than the later songs by nature of their position.

The law goes on to quantify this observation by saying that we should expect the nth song
of an album to be played roughly 1/n the number of times as the first.

I decided to approach this problem by designing a simple Song class to hold all the information
for a given song, including its plays, expected plays (based on Zipf's Law), and the ratio of
plays/expected plays. This ratio turns out to be the key to this problem because it measures the
popularity of songs not by play count (which for this problem is unhelpful), but by performance
of a song in relation to its expected play count by Zipf's Law. For example, if the first song on
an album has 100 plays, and the second has 51, then according to Zipf's Law the second is more
popular because it exceeds its expected number of plays of (1/2\*100). Note that the first song
on an album will always have a plays/expected plays ratio of 1, because it is the first track's
plays by which we measure all the rest. So in this example, the first track has a ratio of 1 while
the second has a ratio of 1.02.

{% highlight java %}
public class Song implements Comparable<Song> {

	String title;
	double plays;
	double expected_plays;
	double ratio; 

	public Song(String init_title, double init_plays) {
		title = init_title;
		plays = init_plays;
	}

	public void setRatio(double track_one_plays, double position) {
		expected_plays = (track_one_plays/position);
		ratio = (plays/expected_plays);
	}

	public int compareTo(Song other) {
		if (this.getRatio() > other.getRatio())
			return -1;
		else if (this.getRatio() < other.getRatio())
			return 1;
		else
			return 0;
	}
	
	public double getRatio() { return ratio; }

	public String toString() { return title; }
}
{% endhighlight %}

Now that we've got this Song class, all we need to do is wrap up however many Songs we
have in an array or any other data structure that allows for quick iteration and sorting. I chose
array here just because of the relative simplicity of the sample inputs, but a linked list would
also be a totally valid choice.

Below is the main method, which reads input from the command line in the [given format][funk],
constructs an array of Songs, and then iterates through the array, setting the expected plays 
and ratios of each song based on the number of plays of the first song. Then we sort by ratio
and return the first m songs (as specified in the input) in descending order of popularity.

{% highlight java %}
public static void main(String[] args) {

	Scanner scan = new Scanner(System.in);
	System.out.println("Please enter your input:");

	int n = scan.nextInt();
	int m = scan.nextInt();
	double track_one_plays = 0;
	Song[] album = new Song[n];

	/*	read input from stdin */
	for (int i = 0; i < n; i++) {
    	double plays = scan.nextDouble();
       	if (i == 0)
        	track_one_plays = plays;
        String title = scan.next();
        album[i] = new Song(title, plays);
    }

    /*  set ratios for all songs in input */
    for (int i = 1; i <= n; i++)
        album[i-1].setRatio(track_one_plays, i);

    /*  sort album by ratio using any sorting method */
    Arrays.sort(album);

    /*  print result */
    String output = "\n";
    for (int i = 0; i < m; i++)
        output += album[i]+"\n";
    System.out.println(output);
}
{% endhighlight %} 

[funk]:https://www.spotify.com/us/jobs/tech/zipfsong/
[spotify]:https://www.spotify.com/us/jobs/tech/
[zipf]:http://en.wikipedia.org/wiki/Zipf%27s_law
[reggae]:http://haikus-in-c.com/posts/spotify-programming-challenge-reggae/
[heavy]:http://haikus-in-c.com
