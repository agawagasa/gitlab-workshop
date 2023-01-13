**Prerequisites**

- root priviledges
- Gitlab account

**Install Git**

`sudo apt-get update`

`sudo apt install git`

**Install Gitlab**

`sudo apt-get install -y curl openssh-server ca-certificates tzdata perl postfix`

`curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash`

`sudo EXTERNAL_URL="https://gitlab.example.com" apt-get install gitlab-ee`

**Install Glab**

**GLab** is the open-source Gitlab CLI tool. You will use GLab to utilize Gitlab built-in functionalities. 

`curl -sL https://j.mp/glab-cli | sudo sh`

**You will need to authenticate GLab**

`glab auth login` 
start the interactive login setup

`glab auth login --stdin < myaccesstoken.txt` 
authenticate against gitlab.com by reading the token from a file

`glab auth login --hostname salsa.debian.org --stdin < myaccesstoken.txt` authenticate against a self-hosted GitLab instance by reading from a file

`glab auth login --hostname gitlab.example.org --token xxxxx` authenticate with token and hostname 
