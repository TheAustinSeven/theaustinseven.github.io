---
layout: post
title:  "Playing With A Top 500 Supercomputer!"
date:   2015-11-26 21:10:07 -0500
tags: [Conte, Purdue, HPC, C++]
image: "/images/2015/conte.jpg"
---
It's not everyday that someone allows you to run programs on a Top 500 Supercomputer, but I was very fortunate and got to do just that. I was given the opportunity to run programs in the standby queue of Conte, currently the 86th most powerful supercomputer in the world(at the time of writing), and the most powerful at a college(again, at the time of writing). Now before your brain explodes at the incredible power given to a mere student, understand that I wasn't exactly given power over the entire behemoth. I was given the ability to submit programs to queue that would wait until programs that were running on partitioned segments of the computer were finished, and then would allow me to run a program on up to 6 nodes, or 96 cores. Although this isn't nearly as awesome as having complete control of a computer such as Conte, it is still pretty freaking cool.

When I was given access, I had a hard time coming up with what I wanted to do first with my newfound resources. The usual choice is to run a quick hello world program with MPI. I, however, being my usual sarcastic self, wanted to do something borderline outrageous. It took me a little thinking, but eventually I figured it out. I was going to use a supercomputer to bogosort a list. I know, I can already hear the cringing.

For everyone who is not familiar with bogosort, the algorithm randomizes a list, checks to see if it's sorted, and repeats that process until the list happens to be sorted. This leads to a best case time complexity of O(1), worst case of O(infinity), and an average of O(n!). Needless to say, this algorithm is horribly inefficient.

Even though this program sounds like a cheap joke with no upside, I should say that this program helped me learn all of the essentials of MPI. It also gave me the opportunity to find a few cool tricks I didn't know about before.

The first trick I learned was the Fisher-Yates shuffle. This shuffling algorithm is unique in that it both runs in O(n) time and O(1) space. Essentially the algorithm runs through an array and for each index: a random index not yet visited is chosen and the elements at the two indexes are swapped, or if the indexes are the same, the element remains in place. I implemented this as shown below. I ended up running backwards through the array so I could just call modulus on the random number.
{% highlight c++ %}
void shuffle_array(int * list, int size){
  int placeholder;
  int index;

  for(int i = size; i > 0; i--){
    index = std::rand() % i;
    placeholder = list[index];
    list[index] = list[i - 1];
    list[i-1] = placeholder;
  }
}
{% endhighlight %}

The other thing I learned, aside from basic MPI skills, was using `MPI_Probe` and `MPI_Iprobe` efficiently. A frighteningly large number of examples I saw online had the master thread iterate through each and every other thread and call MPI_Probe on each and every one of them. This just seemed plain wrong to me. Luckily I found out that you could replace the `source` parameter with `MPI_ANY_SOURCE`. Then if there are any available messages, you can extract the source from the status. I put a quick example below. This led from for loops to a simple check as to whether there were any available messages which was not only more efficient, but also much cleaner code, which becomes even more necessary when running anything with threads.
{% highlight c++ %}
while(sorted_list_size < SORT_RANGE){
    MPI_Probe(MPI_ANY_SOURCE, DATA_TAG, MPI_COMM_WORLD, &status);
    MPI_Recv(list_packet, PARTITION_SIZE, MPI_INT, status.MPI_SOURCE, DATA_TAG, MPI_COMM_WORLD, MPI_STATUS_IGNORE);
    sorted_list = merge_arrays(sorted_list, sorted_list_size, list_packet, PARTITION_SIZE);
    if(next_to_send < SORT_RANGE){
      MPI_Send(list + next_to_send, PARTITION_SIZE, MPI_INT, status.MPI_SOURCE, DATA_TAG, MPI_COMM_WORLD);
      next_to_send += PARTITION_SIZE;
      cout << "Node " << status.MPI_SOURCE << " sent restock.\n";

    }
    else{
      MPI_Send(list, 1, MPI_INT, status.MPI_SOURCE, TERMINATE_TAG, MPI_COMM_WORLD);
      cout << "Node " << status.MPI_SOURCE << " sent termination.\n";
    }
  }
{% endhighlight %}

To see the rest of my code: [Here](https://github.com/TheAustinSeven/ClusterComputing/tree/master/mucking_around/Bogosort) is the github folder.

To see the best guide I found on MPI: [http://mpitutorial.com/](http://mpitutorial.com/)
