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

**Configuration**

By default, glab follows the XDG Base Directory Spec: global configuration file is saved at ~/.config/glab-cli. Local configuration file is saved at .git/glab-cli in the current working git directory. Advanced workflows may override the location of the global configuration by setting the GLAB_CONFIG_DIR environment variable.

`glab config set --global editor vim` To set configuration globally

`glab config set editor vim` To set configuration for current directory (must be a git repository)

`glab config set editor vim --host gitlab.example.org` To set configuration for a specific host. Use the --host flag to set configuration for a specific host. This is always stored in the global config file with or without the global flag.

**Environment variables**

- GITLAB_TOKEN: an authentication token for API requests. Setting this avoids being
prompted to authenticate and overrides any previously stored credentials.
Can be set in the config with 'glab config set token xxxxxx'
- GITLAB_URI or GITLAB_HOST: specify the url of the gitlab server if self hosted (eg: https://gitlab.example.com). Default is https://gitlab.com.
- GITLAB_API_HOST: specify the host where the API endpoint is found. Useful when there are separate [sub]domains or hosts for git and the API endpoint: defaults to the hostname found in the git URL
- REMOTE_ALIAS or GIT_REMOTE_URL_VAR: git remote variable or alias that contains the gitlab url.
Can be set in the config with 'glab config set remote_alias origin'
- VISUAL, EDITOR (in order of precedence): the editor tool to use for authoring text.
Can be set in the config with 'glab config set editor vim'
- BROWSER: the web browser to use for opening links.
Can be set in the config with 'glab config set browser mybrowser'
- GLAMOUR_STYLE: environment variable to set your desired markdown renderer style
Available options are (dark|light|notty) or set a custom style
https://github.com/charmbracelet/glamour#styles
- NO_COLOR: set to any value to avoid printing ANSI escape sequences for color output.
- FORCE_HYPERLINKS: set to 1 to force hyperlinks to be output, even when not outputing to a TTY==
