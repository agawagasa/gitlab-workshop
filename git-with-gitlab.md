**Using Git with Gitlab**

In the terminal, navigate to the local Git repository that we want to push to our remote Gitlab repository. 

`git remote add origin git@gitlab.com:username/repositoryname.git` Add a new remote Gitlab repository. The repository first has to be created in the Gitlab GUI. Then, using the **Clone** button we can obtain the repo link.

`git push -u origin master` Push the local repository to our remote Gitlab repository 

We can use standard Git commands to push, pull, branch, fork, merge etc. 
