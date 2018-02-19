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
_Make the first diary entry:_
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

_To push the update to GitHub:_
```
$ git push
$ git log
```
`git log` give the history of what is done recently:
```
# commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
#Author: vildeka <vilde.kaldhusdal@gmail.com>
#Date:   Thu Feb 22 09:51:46 2013 -0400
#
#    write what updates was made here
```
