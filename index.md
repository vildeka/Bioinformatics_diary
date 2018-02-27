# *Thuesday 27.02.2018*
I was wrong!!
After a helpful hour in the lab this morning I finaly have my finnished code for the outpu. Turuns out the dictionary was pretty neat after all. David showed me how to use it to itterate over the sequence and topology at the same time. The code was also functiononing last night, I just thought it looked wrong with 0 inside the word_seq. But this makes totaly sense as it now contains all ofthe sequences and thus have 0's falanking each of the sequences. (I was wery tired last evening at the end).
```
word_seq = ['0PY', 'PYT', 'YTV', 'TV0', '0EP', 'EPA', 'PAT', 'ATI', 'TI0', '0MN', 'MNI', 'NI0']
topology = [3, 3, 2, 2, 2, 3, 2, 2, 2, 3, 3, 1]
```
My input lookes like this:
Shows: 3 examples 
```
 x = features 
 y = topology
 
 x = [array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,      y = [ 3, 3, 1 ]
             0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
             0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0]), 
      array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
             0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 
             0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]), 
      array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 
             0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
             0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0])]
```
A *feature* is the same as the encoding of a word. 
An *example* is the feature + the topology.

Each array is actually one a single line and deciptes a single feature. But to make it easier to visulazie I show 20 0's on each line. 20 0's deciptes the encoding for one amino acid. 

My finished code looks like this:
```
#===================parser========================
import pandas as pd
import numpy as np
def parse_fasta(filename):
    parse_dict = {}
    #sequence = ""
 #write FAST into a three lists, header, sequence and topology. Removes \n and >
    with open(filename, 'r') as f:
        for x, line in enumerate(f):
            if line[0] == ">":
                key = line[1:-1] #alternative to remove both ">" and "\n"
            elif x % 3 == 1: # gives every second line (% called modulus %3)
                A = line.strip("\n")
                #sequence.append(line.strip("\n"))
            elif x % 3 == 2: # gives every third (line ==0 indicates starting at first line. ==2 indicates starting at third line)
                B = line.strip("\n")
                #newA = [for i in A ]
                parse_dict[key] = [A, B]

    return (parse_dict)

#================input vectors (words/features and topology)==============

def input_words(seq_dict, window=3):
    padding = window//2
    
    # dictionary of aa:
    vals = np.identity(20, dtype=int)
    keys = list("ACDEFGHIKLMNPQRSTVWY")
    aa_dict = dict(zip(keys, vals.T))
    aa_dict['0'] = np.zeros(20, dtype=int)
    
    # dictionary of topology:
    topo = {'H':1, 'S':2, 'C':3}
    
    #list of words:
    word_seq = []
    
    #final input:
    topologies = []
    features = []
    
    for sequence, topology in seq_dict.values(): # put on later to make it go through all seqyences, 
        for i in range(len(sequence)):
            topologies.append(topo[topology[i]])
            if i > padding and i < len(sequence) - padding - 1: #-1 because you want second to last element 
                word_seq.append(sequence[i-padding:i+padding+1])#+1 is to get the last element as well [icluded:notincluded]
            elif i <= padding:
                # head
                print(i)
                this_word = sequence[:i + padding + 1] #[:2] = PT, [:3]= PTV
                zeros_needed = window - len(this_word)
                word_seq.append('0' * zeros_needed + this_word)
                print(this_word)
            else:
                # tail
                print(i)
                this_word = sequence[i-1:] #[43-1:]= ASC, [44-1]= SC
                zeros_needed = window - len(this_word)
                word_seq.append(this_word+'0' * zeros_needed)
                print(this_word)          
             
    print(word_seq)
    print(topologies)
    words2 = []
  
    for word in word_seq:    
        words2.append(list(map(lambda n: aa_dict[n], word)))    
        #words[word] = list(map(lambda n: aa_dict[n], word))    
        
        for v in words2:
            b = np.concatenate(v)
            features.append(b)
    print(features)
```
# *Monday 26.02.2018*
After working a whole day (09:00-18:30) I still dont have a working input for the SVM. I have spendt most of the day on the sliding window, to make it work with the dictionary created with the parser. seems it was a big mistake using a dictionary. it has mostly caused a lot of extra time and headaches. I should have gone for simple lists. My unfunctioning code is now looking like this:
```
#=========================input vectors (words)==============

def input_words(parse_dict):

    vals = np.identity(20, dtype=int)
    keys = list("ACDEFGHIKLMNPQRSTVWY")
    #print(keys)
    aa_dict = dict(zip(keys, vals.T))
    aa_dict['0'] = np.zeros(20, dtype=int)
    #print(aa_dict)
    
    words= {}
    seq_dict = parse_fasta("dataminix2.txt")
    values = list(seq_dict.values())
    
    seq_list = []
    for l in values:
        seq_list.append(l[0])
    print (seq_list)
   
    window = 3
    padding = window//2
    
    word_seq = []
    for sequence in seq_list: # put on later to make it go throu all seqyences, messing up the rest of the code. look at 0 in word_seq
        for i in range(len(sequence)):
            if i > padding and i < len(sequence) - padding - 1: #-1 because you want second to last element 
                word_seq.append(sequence[i-padding:i+padding+1])#+1 is to get the last element as well [icluded:notincluded]
            elif i <= padding:
                # head
                print(i)
                this_word = sequence[:i + padding + 1] #[:2] = PT, [:3]= PTV
                zeros_needed = window - len(this_word)
                word_seq.append('0' * zeros_needed + this_word)
                print(this_word)
            else:
                # tail
                print(i)
                this_word = sequence[i-1:] #[43-1:]= ASC, [44-1]= SC
                zeros_needed = window - len(this_word)
                word_seq.append(this_word+'0' * zeros_needed)
                print(this_word)
        
        print(word_seq)
    
    words2 = []
    
    for word in word_seq:    
        words[word] = list(map(lambda n: aa_dict[n], word))    
        x = []
        for v in words.values():
            b = np.concatenate(v)
            x.append(b)
        #print(x)
        words2.append(x)
    print(words2)

```
# *Sunday 25.02.2018*
I worked on the windows and I have more or less achived a finnished code, but I think I must tweak the format a little. Also very close to finnishe the topology vector. 

# *Friday 23.02.2018*
Topic: mapping categorical data <p>
This has more or less been a day of confusion. I now understand that I undertsand very little of how to make this predictor. In some way you might call this some sort of progress. I have now wrecked the Ideea of doing the one-hot encoding from the pandas. (By by pandas!, for now annyway) it is too complicated at this stage. As i understand it, I need to make a encoding alphabet. Then a function that creates the windows. David was kind enough to enlighten me on the format of the input vetors to the SVM. So now I know how they should look. The input comes in form of two vectors for each example. One vector decipting all the windows (features) and one vector for the corresponding topology (labels) for each feature. 
```
N(zeros to add) = W/2                 sequence     0GGCA0
                = 3/2                               ||||                               
                = 1                   topology      CHHS
               
N(features) = WxN(nucleic acid states)             sequence vector:                    topology vector:
            = 3x4                                  0GG 0,0,0,0, 1,0,0,0, 1,0,0,0,      3
            = 12                                   GGC 1,0,0,0, 1,0,0,0, 0,1,0,0,      1
                                                   GCA 1,0,0,0, 0,1,0,0, 0,0,1,0,      1
                                                   CA0 0,1,0,0, 0,0,1,0, 0,0,0,0,      2
W = window
                                                   sequence alphabet:                 topology encoder:
                                                   G -> 1 0 0 0                       H = 1    
                                                   C -> 0 1 0 0                       S = 2
                                                   A -> 0 0 1 0                       C = 3
                                                   T -> 0 0 0 1
```
I have maneged to make a sequence alphabet:
```
mport numpy as np
vals = np.identity(20, dtype=int)
keys = list("ACDEFGHIKLMNPQRSTVWY")
print(keys)
dictionary = dict(zip(keys, vals.T))
#print(dictionary)
```
The output looks like this:
```
{'Q': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0]), 
'S': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0]), 
'W': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0]), 
'G': array([0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]), ...
```
Im not sure if it will be a problem that they are in array form. 

I have to continue working on the topology vector tomorow if I find the time. 

# *Thursday 22.02.2018*
Today I spendt most of the day finding 5 more or less relevant articles for my project, and writing the project plan. then I started doing the one-hot encoding. I was not able to finnish it beacuse I got stuck on trying to extract the sequence from the panda into a string. (strong dislike of pandas right now)
However while searhing for a solution I accidentially came across this picture and it kinda saved my day.
![alt txt](https://img.huffingtonpost.com/asset/578d3ad91300002d0005e8cb.jpeg?cache=wkm8vunwwe&ops=scalefit_970_noupscale)
Image source:[Link](https://www.huffingtonpost.com/entry/panda-cubs-wilderness_us_578e4a97e4b0a0ae97c38363)
 
# *Thuesday 20.02.2018*
As mentioned in the last post I wanted to make the header, sequence and topology straight into the dictionary in the with loop. I now have noen this and my code currently looks like this:
```
import pandas as pd
def parse_fasta(filename):
    dictionary = {}
 #write FAST into a three lists, header, sequence and topology. Removes \n and >  
    with open(filename, 'r') as f:
        for x, line in enumerate(f):
            if line[0] == ">":
                key = line[1:-1] #alternative to remove both ">" and "\n"
            elif x % 3 == 1: # gives every second line (% called modulus %3)
                A = line.strip("\n")
            elif x % 3 == 2: # gives every third (line ==0 indicates starting at first line. ==2 indicates starting at third line)
                B = line.strip("\n")
                dictionary[key] = [A, B]

    print (dictionary)

#makes the dictionary into a panda     
    df = pd.DataFrame(data=dictionary)
    return (df)    

if __name__ == '__main__':    
    result_FASTA = parse_fasta("datamini.txt")
    print (result_FASTA)
```
Now I have to start to think about how to make the predictor. 

# *Monday 19.02.2018*
Today I made a parser for the FASTA file containing the dataset I will use to train my predictor.
I split the file content into three list containnig the identifier, aa sequence and the topology repectively.
Then I made it into a dictionary where the identifier is the key, and the sequence and topology is the two values beloning to each key.
I thin I should have made it straight into a dictionary in the whit loop, but not enitely sure how to do that.

```
def parse_fasta(filename):
    header = []
    sequence = []
    topology = []

 #writes FAST into a three lists, header, sequence and topology. Removes \n and >
    with open(filename, 'r') as f:
        for x, line in enumerate(f):
            if line[0] == ">":
                header.append(line[1:-1]) #removes both ">" and "\n"
            elif x % 3 == 1: # gives every second line (% called modulus)
                sequence.append(line.strip("\n"))
            elif x % 3 == 2: # gives every third (line ==0 indicates starting at first line. ==2 indicates starting at third line)
                topology.append(line.strip("\n"))

#makes the list into a dictionary {header:(sequence,topology)}
    variables = list(zip(sequence, topology))
    dictionary = dict(zip(header, variables))

#makes the dictionary into a panda
    dataframe = pd.DataFrame(data=dictionary)
    return (dataframe)

if __name__ == '__main__':
    result_FASTA = parse_fasta("data.txt")
    print (result_FASTA)
```
# *Friday 16.02.2018*

Yesterday was the first course day of KB8024. I made my first GitHub account and learnt how to add, commit and push
which is as I understand it the most imporatnt commands when using GitHub.


GitHub works two ways. you can either make a repository online and clone it to your computer. OR you can create the repository on your computer and then add, commit and push the new repository to GitHub.

__Make a repository localy:__
```
$ mkdir diary
$ cd diary
$ git init        #this creates the hidden folder git which makes it a repository
$ git status      
```
The `git status` should give a output looking like this:
```
# On branch master
#
# Initial commit
#
nothing to commit (create/copy files and use "git add" to track)
```
__Make the first diary entry:__
```
$ nano first_entry.md  #the format should be md for markdown
'''inside the text editior nano you write the markdown text
that you want to display in your diary post'''
$ git add first_entry.md
$ git status
```
The output for the `git status` should look like this:
```
# On branch master
#
# Initial commit
# Changes to be commited:
#    (use "git rm --cached <file>..." to unstage)
#
#          new file: first_entry.md
```
Git now know it is supposed to track the changes made to first_entry.md
Commiting:
```
$ git commit -m "write what updates was made here"#comitts everything that has been updated
```
the flag -m (for "message") enables you to give a short descriprion of the updates. when not usning
the flag the terminal will open the default text editior so that you can write a longer message
with header and longer description.
output looks like this:
```
# [master (root-commit) f22b25e] write what update wass made here
#  1 file changed, 1 insertion(+)
#  create mode 100644 first_entry.md
```
Git stores a permanent copy if the file inside the special `.git diectory`.
It is called a commit and has the identifier f22b25e

__To push the update to GitHub:__
```
$ git push
$ git log
```
`git log` give the history of what is done recently. To quit window press Q:
```
# commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
#Author: vildeka <vilde.kaldhusdal@gmail.com>
#Date:   Thu Feb 22 09:51:46 2013 -0400
#
#    write what updates was made here
```
