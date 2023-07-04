---
layout: post
author: Abhinav Saxena
tags: [overview, moonwalk]
---
## Thesis Abstact
In this thesis by articles, we present a research paper that we submitted for theWebSci’23 conference and is now under review. In addition to the article itself, in the thesis, we provide further detail regarding the motivation, background, literature review and research. The aim of this thesis is to provide a method that can facilitate the work of individuals combating online human trafficking. The majority of trafficking victims report being advertised online, this explains why online sex trafficking has been on the rise in the past few years. On the other hand, the use of OnlyFans as a platform for adult content has increased exponentially in the past three years, and Twitter has been its main advertising tool. Since we know that traffickers usually work within a network and control multiple victims, we suspect that there may be networks of traffickers promoting multiple OnlyFans accounts belonging to their victims. Based on these observations, we decided to conduct the first tstudy looking at organized activities on Twitter through OnlyFans advertisements. Preliminary analysis of this space shows that most tweets related to OnlyFans contains generic text, making text-based methods less reliable. Instead, focusing on what ties the authors of these tweets together, we propose a novel method for uncovering coordinated networks of users based on their behaviour. Our method, called Multi-Level Clustering (MLC), combines two levels of clustering. In the first level, we detect communities based on username Mentions and shared URLs, while the second level is done through two different approaches: i- the Partial Intersections (PI) of URLs and Mention communities ii- Joint Clustering (JT) by applying a subraph dense detection algorithm. We additionally successfully proved that our JT approach applied on synthetically generated data (with injected ground truth) shows a superior performance compared to competitive baselines. Furthermore, we apply the MLC to real-world data of tweets pertaining to OnlyFans and analyse the detected groups and show that our Partial Intersections provides good quality clusters (high entropy of OnlyFans accounts). Our paper and our thesis end with a discussion where we show carefully chosen examples of organized clusters and provide multiple interesting points that supports our method.


## Lists

Unordered:

- Fusce non velit cursus ligula mattis convallis vel at metus[^2].
- Sed pharetra tellus massa, non elementum eros vulputate non.
- Suspendisse potenti.





Here below is my full Thesis

<embed src="/images/Maricarmen_Thesis__Copy__with_articles-4-ENG.pdf" width="100%" height="850px" />

# Sample heading 1
## Sample heading 2
### Sample heading 3
#### Sample heading 4
##### Sample heading 5
###### Sample heading 6

Mauris viverra dictum ultricies. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod.



#
## Code

Now some code:

```
const ultimateTruth = 'follow middlepath';
console.log(ultimateTruth);
```

And here is some `inline code`!

## Tables

Now a table:

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

## Images

![theme logo](http://www.abhinavsaxena.com/images/abhinav.jpeg)

This is an image[^4]

---
{: data-content="footnotes"}

[^1]: this is a footnote. You should reach here if you click on the corresponding superscript number.
[^2]: hey there, don't forget to read all the footnotes!
[^3]: this is another footnote.
[^4]: this is a very very long footnote to test if a very very long footnote brings some problems or not; hope that there are no problems but you know sometimes problems arise from nowhere.
