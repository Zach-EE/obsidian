# Bash Shell
## Cheat Sheet
![[Pasted image 20220718070624.png]]
## Reference
[Bash Manual: Bash Variables](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html#index-PROMPT_005fDIRTRIM)

## bash_aliases:
```bash
######################################################################
#	           ██████╗  █████╗ ███████╗██╗  ██╗
#	           ██╔══██╗██╔══██╗██╔════╝██║  ██║
#	           ██████╔╝███████║███████╗███████║
#	           ██╔══██╗██╔══██║╚════██║██╔══██║
#	           ██████╔╝██║  ██║███████║██║  ██║
#	           ╚═════╝ ╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝
######################################################################
#Django Aliases:
alias djr='python3 manage.py runserver'
alias djm='python3 manage.py makemigrations'
alias djs='python3 manage.py migrate && python3 manage.py runserver'
alias dja='python3 manage.py startapp'
alias djp='django-admin startproject'
alias djenv='python3 -m pipenv install django && python3 -m pipenv shell'

#Github Aliases:
alias gs='git status'
alias ga='git add .'
alias gaa='git add --all'
alias gc='git commit -am'
alias gl='git log --oneline'
alias gb='git checkout -b'
alias gd='git diff'
alias gp='git push -u origin'

#Python Dev Aliases:
alias pipenv='python3 -m pipenv'
alias py='python3'

#############################################
############## Random Commands ##############
#############################################
# Move to the parent folder.
alias ..='cd ..;pwd'

# Move up two parent folders.
alias ...='cd ../..;pwd'

# Move up three parent folders.
alias ....='cd ../../..;pwd'

# Press c to clear the terminal screen.
alias c='clear'

# Press h to view the bash history.
alias h='history'
  
# Make a directory and all parent directories with verbosity.
alias mkdir='mkdir -p -v'

#############################################
############ ApplicationCommands ############
#############################################
# Launch Firefox-Dev Browser
alias ffd='firefox_dev'

# View the calender by typing the first three letters of the month.
# sudo app install ncal
alias jan='cal -m 01'
alias feb='cal -m 02'
alias mar='cal -m 03'
alias apr='cal -m 04'
alias may='cal -m 05'
alias jun='cal -m 06'
alias jul='cal -m 07'
alias aug='cal -m 08'
alias sep='cal -m 09'
alias oct='cal -m 10'
alias nov='cal -m 11'
alias dec='cal -m 12'

# Display the directory structure better.
# sudo apt install tree
alias tree='tree --dirsfirst -F'
```

