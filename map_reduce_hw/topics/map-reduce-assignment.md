# Overview

This is the **Hadoop MapReduce** assignment with a Python solution. You can read the complete coursera assignment here, this is a reduced version on how to run the solution.

# Sorting procedure

When selecting the top N items in a list, sorting is necessary. Use the following steps to sort:

1. Sort the list ASCENDING based on Firstly value then Secondly on the key. If the key is a string, sort lexicographically.

2. Select the bottom N items in the sorted list as Top items.

For example, to select top 5 items in the list: `{"A": 100, "B": 99, "C":98, "D": 97, "E": 96, "F": 96, "G":90}`, first sort the items ASCENDING:

<code-block>
"G":90

"E": 96

"F": 96

"D": 97

"C":98

"B": 99

"A": 100
</code-block>

Then, the bottom 5 items are `A, B, C, D, F`.

Another example, to select 5 top items in the list `{"43": 100, "12": 99, "44":98, "12": 97, "1": 96, "100": 96, "99":90}`

<code-block>
"99":90

"1": 96

"100": 96

"12": 97

"44":98

"12": 99

"43": 100
</code-block>

Then, the bottom 5 items are `43, 12, 44, 12, 100`.

**If an exercise specifies a different way of sorting, follow the specific instruction.**

---

# Python submission

1. Requirements

This assignment will be graded based on Python 3.6. 

**Click [HERE](https://github.com/gutyoh/Hadoop_MapReduce_MP4) to visit the GitHub repository with the code of the Python files used below, along with the Dockerfile:**

2. Procedures

Step 1: Pull the **[Hadoop MapReduce Assignment Docker image](https://hub.docker.com/repository/docker/hrosch/hadoop_map_reduce/general)**:

<code-block>
docker pull hrosch/hadoop_map_reduce:latest
</code-block>

**NOTE: it may take a long time to build the Docker image since it automatically installs Hadoop, you can take a look at the Dockerfile [here](https://github.com/gutyoh/Hadoop_MapReduce_MP4/blob/master/Docker/Dockerfile).**

Step 2: 

Run the docker container and move to the directory with the Python solution:

<code-block>
docker run -it hrosch/hadoop_map_reduce bin/bash
cd home/MP4_HadoopMapReduce_Template/PythonTemplate
</code-block>

Step 3:

We're ready to run the Python solutions, below you will see the description of each part of the assignment and what Python files it uses to do the Map and Reduce processes.

---

## Exercise A: Top Titles

In this exercise, you will implement a counter for words in Wikipedia titles and an application to find the top words used in these titles. You will need to use the following files:  _TitleCountMapper.py_, _TitleCountReducer.py_, _TopTitlesMapper.py_, _TopTitlesReducer.py_

Your application takes a list of Wikipedia titles (one in each line) as an input and first tokenizes them using provided delimiters. After that, it makes the tokens lowercased, then removes common words from the provided stopwords. Next, your application selects the top 10 words, and finally, saves the count for them in the output. Use the method in section 3 Sorting to select top words.

First, you should tokenize Wikipedia titles, make the tokens lowercased, remove common words, and save the count for all words in the output with _TitleCountMapper.py_ and _TitleCountReducer.py_

**To run the solution/application for Exercise A, execute the following commands:**

<code-block>
cd home/MP4_HadoopMapReduce_Template/PythonTemplate
rm -r preA-output_Python/
rm -r A-output_Python/
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files TitleCountMapper.py,TitleCountReducer.py -mapper 'TitleCountMapper.py stopwords.txt delimiters.txt' -reducer 'TitleCountReducer.py' -input dataset/titles/ -output ./preA-output_Python 
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files TopTitlesMapper.py,TopTitlesReducer.py -mapper 'TopTitlesMapper.py' -reducer 'TopTitlesReducer.py' -input ./preA-output_Python/ -output ./A-output_Python
cat A-output_Python/part-00000
</code-block>

Because of the possible problems with special characters, we will not check the output for this part. We will only check the top words and their counts.

**Here is the output of the solution that selects the top 10 words:**

<code-block>
route   771
station 800
disambiguation  893
john    936
state   940
county  1020
school  1065
new     1077
de      1684
list    1948
</code-block>

---

## Exercise B: Top Title Statistics
In this exercise, you will implement an application to find some statistics about the top words used in Wikipedia titles. You will need to use the following files: _TopTitleStatisticsMapper.py_, _TopTitleStatisticsReducer.py_

**Your output from Exercise A will be used here.** The application saves the following statistics about the top words in the output: "Mean", "Sum", "Minimum" and "Maximum", and "Variance" of the counts. All values should be floored to be an integer. For the sake of simplicity, simply use Integer in all calculations.

**To run the solution/application for Exercise B, execute the following commands:**

<code-block>
cd home/MP4_HadoopMapReduce_Template/PythonTemplate
rm -r B-output_Python/
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files TopTitleStatisticsMapper.py,TopTitleStatisticsReducer.py -mapper 'TopTitleStatisticsMapper.py' -reducer 'TopTitleStatisticsReducer.py' -input ./A-output_Python/ -output ./B-output_Python
cat B-output_Python/part-00000
</code-block>

**Here is the output of the application that selects the top 10 words:**

<code-block>
Mean    1113
Sum     11134
Min     771
Max     1948
Var     136010
</code-block>

---

## Exercise C: Orphan Pages
In this exercise, you will implement an application to find orphan pages in Wikipedia. You will need to use the following files: OrphanPagesMapper.py, OrphanPagesReducer.py

Your application takes a list of Wikipedia links (not Wikipedia titles anymore) as an input. All pages are represented by their ID numbers. 

Each line starts with a page ID, followed by a list of all the pages that the ID links to. **The following is a sample line in the input:**

<code-block>
2: 3 747213 1664968 1691047 4095634 5535664
</code-block>

In the above sample, page `2` has links to pages `3`, `747213`, and so on. Note that links are not necessarily two-way. The application should save the IDs of orphan pages in the output. Orphan pages are pages that have no incoming links from other Wikipedia pages. This means that they do not appear in the right-hand side after the colon in any line, as they are pages that are not linked to from any other pages in the Wikipedia dataset.  Please sort the output in increasing order.

**To run the solution/application for Exercise C, execute the following commands:**

<code-block>
cd home/MP4_HadoopMapReduce_Template/PythonTemplate
rm -r C-output_Python/
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files OrphanPagesMapper.py,OrphanPagesReducer.py -mapper 'OrphanPagesMapper.py' -reducer 'OrphanPagesReducer.py' -input dataset/links/ -output ./C-output_Python
cat C-output_Python/part-00000
</code-block>

**Here is the output of the application that selects the top 10 words:**

<code-block>
Mean    1113
Sum     11134
Min     771
Max     1948
Var     136010
</code-block>

**To check a part of the output of Exercise C, you can run:**

<code-block>
head C-output_Python/part-00000
</code-block>

**After running the above command, the expected output should be the following for Exercise C:**

<code-block>
2
14
24
51
68
83
103
107
149
158
</code-block>

---

## Exercise D: Top Popular Links

In this exercise, you will implement an application to find the most popular pages on Wikipedia. You will need to use the following files: _LinkCountMapper.py_, _LinkCountReducer.py_, _TopPopularLinksMapper.py_, _TopPopularLinksReducer.py_

If you have finished **Exercise A**, _LinkCountMapper.py_, _LinkCountReducer.py_ should produce output similar to _TitleCountMapper.py_, _TitleCountReducer.py_ in **Exercise A**. Instead of printing the count for each title, _LinkCountMapper.py_ and _LinkCountReducer.py_ should output the link count for each page `P` or the number of pages linked to `P`. 

Be careful about the output format produced by _LinkCountMapper.py_ and _LinkCountReducer.py_ since their output is supposed to become the input of _TopPopularLinksMapper.py_. So if you use a tab to separate the page ID and its link count, your _TitleCountMapper.py_ should also consume its input in this way.

Your application takes a list of Wikipedia links as input. All pages are represented by their ID numbers. Each line starts with a page ID,  followed by a list of all the pages the ID links to. 

**The following is a sample line in the input (_same input as in Exercise C_):**

<code-block>
2: 3 747213 1664968 1691047 4095634 5535664
</code-block>

In the above sample, page `2` has links to pages `3`, `747213`, and so on. Note that links are not necessarily two-way. The application should save the IDs of the top 10 popular pages and the number of links to them in the output. A page is popular if more pages are linked to it. Use the method in section 3 Sorting to select top links.

**To run the solution/application for Exercise D, execute the following commands:**

<code-block>
cd home/MP4_HadoopMapReduce_Template/PythonTemplate
rm -r linkCount-output_Python/
rm -r D-output_Python/
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files LinkCountMapper.py,LinkCountReducer.py -mapper 'LinkCountMapper.py' -reducer 'LinkCountReducer.py' -input dataset/links/ -output ./linkCount-output_Python
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files TopPopularLinksMapper.py,TopPopularLinksReducer.py -mapper 'TopPopularLinksMapper.py' -reducer 'TopPopularLinksReducer.py' -input ./linkCount-output_Python -output ./D-output_Python
cat D-output_Python/part-00000
</code-block>

**Here is the output of the application that selects the top 10 popular links:**

<code-block>
2367662 637
1804986 643
1613751 647
687324  693
3766300 700
1921890 721
481424  729
84707   1060
5302153 1532
88822   1676
</code-block>

---

## Exercise E: Popularity League

In this exercise, you will implement an application to calculate the rank of pages in a league using their popularity. You will need to use the following files:  _PopularityLeagueMapper.py_, _PopularityLeagueReducer.py_

**The popularity of a page is determined by the number of pages in the whole Wikipedia graph that link to that specific page. (Same number as Exercise D)**

**The input for this exercise will be the output from LinkCountReducer.py (linkCount-output_Python) from Exercise D.**

The application also takes a list of page IDs as an input (also called a league list). The goal of the application is to calculate the rank of pages in the league using their popularity.

A page's rank is the number of pages in the league with strictly less (not equal) popularity than the original page. **Hint:** pay careful attention to strictly less (not equal) part of the line when assigning ranks. Please sort based on the key in decreasing order.

**To run the solution/application for Exercise E using the dataset _league.txt_, execute the following commands:**

<code-block>
cd home/MP4_HadoopMapReduce_Template/PythonTemplate
rm -r E-output_Python/
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files PopularityLeagueMapper.py,PopularityLeagueReducer.py -mapper 'PopularityLeagueMapper.py dataset/league.txt' -reducer 'PopularityLeagueReducer.py' -input ./linkCount-output_Python -output ./E-output_Python
cat E-output_Python/part-00000
</code-block>

**Here is the output of the application that uses the dataset _league.txt_:**

<code-block>
5300058 2
3294332 4
3078798 4
2370447 1
1804986 6
81615   3
3       0
</code-block>

**We can also use another dataset _league2.txt_, and get different ranks of pages in a league. To do this execute the following commands:**

<code-block>
cd home/MP4_HadoopMapReduce_Template/PythonTemplate
rm -r E-output_Python/
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-2.9.2.jar -files PopularityLeagueMapper.py,PopularityLeagueReducer.py -mapper 'PopularityLeagueMapper.py dataset/league2.txt' -reducer 'PopularityLeagueReducer.py' -input ./linkCount-output_Python -output ./E-output_Python
cat E-output_Python/part-00000
</code-block>

**Here is the output of the application that uses the dataset _league2.txt_:**

<code-block>
5115901 4
4861926 0
4189215 6
1650573 2
774931  0
88822   7
75323   5
66877   3
</code-block>


