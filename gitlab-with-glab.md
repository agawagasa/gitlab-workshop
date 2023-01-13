**Using GLab CLI with GitLab**

`glab alias` Create, list and delete aliases

`glab auth [git-credentials,login,status]` Manage glab's authentication state

`glab check-update` Check for latest glab releases

`glab completion` Generate shell completion scripts

`glab config` Set and get glab settings
- token: Your GitLab access token, defaults to environment variables
- gitlab_uri: if unset, defaults to https://gitlab.com
- browser: if unset, defaults to environment variables
- editor: if unset, defaults to environment variables.
- visual: alternative for editor. if unset, defaults to environment variables.
- glamour_style: Your desired Markdown renderer style. Options are dark, light, notty. Custom styles are allowed using glamour
- glab_pager: Your desired pager command to use (e.g. less -R)

`glab incident list` Work with GLab incidents

`glab issue` Work with GLab issues
Examples: 
- glab issue list
- glab issue create --label --confidential
- glab issue view --web
- glab issue note -m "closing because !123 was merged" <issue number>

Subcommands:
- board
- close
- create
- delete
- list
- note
- reopen
- subscribe
- unsubscribe
- update
- view
  
`glab label [create,list]` Manage labels on remote
  
`glab mr` Create, view and manage requests
 Examples:
- glab mr create --fill --label bugfix
- glab mr merge 123
- glab mr note -m "needs to do X before it can be merged" branch-foo
  
  Subcommands:
- approve
- approvers
- checkout
- close
- create
- delete
- diff
- for
- issues
- list
- merge
- note
- rebase
- reopen
- revoke
- subscribe
- todo
- unsubscribe
- update
- view
  
`glab release` Manage gitlab releases
`glab repo` Work with Gitlab repositories and projects
`glab schedule` Working with Gitlab schedules
`

