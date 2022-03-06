---
title: A few thoughts on randomness
date: 2017-01-02T11:03:00-0400
tags: technology
---

I want to share a couple of interesting examples of randomness that might surprise your intuition (if you haven't heard of them before).

# The birthday paradox

The birthday paradox describes the unintuitively high chance that two people in a group share the same birthday, even when the group is relatively small. See [Wikipedia](https://en.wikipedia.org/wiki/Birthday_problem) for more details. See my code [on GitHub](https://github.com/rynemccall/lightning-talk/blob/master/random/birthday/demo.py) for the entirety of the code examined below.

## Empirically simulate birthday collision

First, let's create a function that empirically simulates how many people must be added to the group to trigger the first birthday collision.

```
def simulate_number_of_people_in_room_before_collision():
    """

    :return: Number of people added to room to achieve first collision
    :rtype: int

    """

    unique_birthdays = set()
    all_birthdays = []

    while True:
        birthday_ordinal = random.randint(0, 365 - 1)

        unique_birthdays.add(birthday_ordinal)
        all_birthdays.append(birthday_ordinal)

        if len(unique_birthdays) != len(all_birthdays):
            return len(all_birthdays)
```

This function works by creating a list (allows duplicates) and a set (does not allow duplicates) to record the randomly chosen birthday ordinal (day number of the year) for the most recently added person to the group. We can quickly determine if this new person has the same birthday as someone else by comparing the number of items in the list and set. If they are different, the group has two people with the same birthday.

## An aside

The above counting strategy is an example of the pigeonhole principle in action.

So, what is the Pigeon Principle? The pigeonhole principle says that if you have n items (pigeons) and m containers (pigeonholes), if n > m, at least one container (pigeonhole) contains more than one item (pigeon).

<figure align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5c/TooManyPigeons.jpg/296px-TooManyPigeons.jpg" alt="Illustration of the pigeonhole principle" />
  <figcaption>
    <p>
      The pigeonhole principle, demonstrated with real pigeons
      <br />
      Pigeons-in-holes.jpg by en:User:BenFrantzDale; this image by en:User:McKay <a href="http://www.gnu.org/copyleft/fdl.html">GFDL</a> or <a href="http://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA-3.0</a>, via Wikimedia Commons
    </p>
  </figcaption>
</figure>

What are some other implications of this principle? Well, if you live in a major city, you can use the pigeonhole principle to prove that at least two people in your city have the same number of hairs on their head. On average, humans have between 90,000 and 150,000 hairs on their head, depending on hair color. Assuming there is an upper biologic limit of 1 million hairs on a head (over 6 times the upper end of the average), if you live in a city of more than 1 million people, you can prove via the pigeonhole principle that at least two people have the *exact* same number of hairs on their heads, without examining any heads at all. In reality, many people share the same number of hairs on their head because 1) the birthday paradox and 2) hair count (likely) follows a normal distribution.

## Run the simulation

Okay, so back to the birthday paradox.

Let's run 100,000 instances of the simulation so we can get a good understanding of the distribution.

```
NUMBER_OF_SIMULATIONS = 100000

simulations = [
    simulate_number_of_people_in_room_before_collision()
    for _ in xrange(0, NUMBER_OF_SIMULATIONS)
]
```

## Construct a histogram

Now that we've ran the simulation 100,000 times (it took me 3.5s on my 2015 MBP), let's construct a histogram to see what happened.

### Code

```
def generate_histogram(s):
    plt.hist(
        s,
        bins=xrange(max(s)),
        color='green',
    )

    plt.ylabel('Number of simulations')
    plt.xlabel('Number of people in room when first birthday collision occurs')

    plt.axvline(
        float(sum(s)) / NUMBER_OF_SIMULATIONS,
        color='red',
        linewidth=2,
        linestyle='--'
    )

    plt.show()
```

### Output

![Histogram showing simulation results]({{ site.url }}/assets/media/birthday-paradox-histogram.png)

I've added a vertical line (dashed, in red) to show the average number of people in the room when the first birthday collision occurred.

## Construct a CDF

### Code

```
def generate_cdf(s):
    normed_counts, bin_edges = np.histogram(s, bins=max(s), normed=True)
    cdf = np.cumsum(normed_counts)

    plt.plot(
        bin_edges[1:],
        cdf,
        linewidth=2,
    )

    plt.ylabel('CDF')
    plt.xlabel('Number of people in room when first birthday collision occurs')

    plt.axhline(
        .5,
        color='red',
        linewidth=2,
        linestyle='--'
    )

    plt.show()
```


### Output

![CDF showing simulation results]({{ site.url }}/assets/media/birthday-paradox-cdf.png)

I've added a horizontal line to show where the CDF crosses the 50% threshold. 

As you can see above, it is even money that an average size classroom has two people with the same birthday. It is very rare to get 50 people in a room and not have at least one shared birthday.

# Shuffling

Now, let's look at a slightly flawed implementation of the [Fisher-Yates shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle) via a demonstration.

## Code

```
import copy
import random

NUMBER_OF_SIMULATIONS = 100000


def shuffle(items):
    """ A biased implementation of the Fisher-Yates shuffle

    :return: List[items]
    :rtype: List[Any]

    """

    items = copy.deepcopy(items)

    for i, _ in enumerate(items):
        swap_index = random.randint(0, len(items) - 1)
        temp = items[swap_index]

        items[swap_index] = items[i]
        items[i] = temp

    return items

items = ['a', 'b', 'c']

shuffles = [
    shuffle(items)
    for _ in xrange(0, NUMBER_OF_SIMULATIONS)
]

for item in items:
    for i in xrange(0, len(items)):
        print "Number of times {} is in position {}: {}".format(
            item,
            i,
            len([
                x for x in shuffles
                if x[i] == item
            ])
        )

    print ''
```

## Output
```
Number of times a is in position 0: 33179
Number of times a is in position 1: 33583
Number of times a is in position 2: 33238

Number of times b is in position 0: 37213
Number of times b is in position 1: 29807
Number of times b is in position 2: 32980

Number of times c is in position 0: 29608
Number of times c is in position 1: 36610
Number of times c is in position 2: 33782
```

## Analysis

At first glance, our implementation seems simple and robust. We loop over the entire list, exchanging the current element we are visiting with one in a randomly chosen position. If we just look at the positional frequency of the first element in the deck of items that we are shuffling, our implementation seems fine. But, when we look at the position frequency for all the remaining items, we notice that this is actually a biased implementation. It is easy to empirically see that our implementation is flawed.

But, is there a counting strategy (similar to the pigeonhole principle) that can help us understand what is going on? If we look at our implementation for the example input, we see that we "shuffle" three times and in each of those shuffles, the current item that we are visiting may be exchanged to any of the three positions. So, our algorithm has 3^3 = 27 potential outcomes. But, if we consider the possible permutations (order matters) of three items, it is 3! = 6. Since the 27 equally likely outcomes of our algorithm cannot be evenly divided into the 6 possible permutations of 3 items, our algorithm must be biased. 

# Takeaways

* Randomness can surprise our intuition
* Randomness and cryptography are first cousins; it isn't obvious when you make a mistake, but it is fatal
* Counting strategies can help us understand problems where our intuition fails us
