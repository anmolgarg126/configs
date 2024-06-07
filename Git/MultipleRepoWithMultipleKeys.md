Using [github's process](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent),
I generated a new ssh key and added it to my profile on github

I added a config file in my .ssh folder, and I referenced my work ssh key and my personal ssh key as was outlined in
other answers.

```shell
cd ~/.ssh
touch config
subl config -> opens file in sublime
```

```
Host github.com
  HostName github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/work_key
  
Host personal
  HostName github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/personal_key
```

Then, I added both keys to my ssh-agent with ssh-add:

```shell
ssh-add ~/.ssh/work_key
ssh-add ~/.ssh/personal_key
```

I verified that both keys were added with ssh-add -L

Then
I [verified the connection](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)
with ssh -T git@github.com and ssh -T git@personal, and both worked!

```shell
ssh-keyscan github.com >> ~/.ssh/known_hosts
ssh -T git@github.com
ssh -T git@personal
```

If everything is OK, you will see these messages:

```
Hi {Work user name}! You've successfully authenticated, but GitHub does not provide shell access.
```

```
Hi {Personal user name}! You've successfully authenticated, but GitHub does not provide shell access.
```

After that, I was able to clone repos using my personal key with this command: git clone git@personal:
my_username/repo.git (swapping out the git@github.com:... that I copied from my github repo for git@personal:...)

```shell
git clone git@personal:org1/project1.git /path/to/project1
cd /path/to/project1
git config user.email "{email}"
git config user.name  "{User name}"
```

If you already have the repo set up, after the ssh config instructions, you need change the URL of origin, just do:

```shell
cd /path/to/project2
git remote set-url origin git@personal:org2/project2.git
git config user.email "{email}"
git config user.name  "{User name}"
```

If you are creating a new repository on local:

```shell
cd /path/to/project3
git init
git remote add origin git@personal:org3/project3.git
git config user.email "{email}"
git config user.name  "{User name}"
git add .
git commit -m "Initial commit"
git push -u origin master
```

> **_NOTE:_** use git pull git@personal:my_username/repo.git, git push git@personal:my_username/repo.git, every git
> command followed by **git@personal:my_username/repo.git** if you face any account mismatch issue.


## Addon:

The bash script that prompts for your git account. [For reference](https://gist.github.com/oanhnn/80a89405ab9023894df7)

```shell
#!/bin/bash

# silent prompt
read -p 'GIT profile: ' profile

# switch
case $profile in
  superman)
    git config user.email "superman@example.com"
    git config user.name "superman" 
    git config user.signingKey "superman_gpg_public_key"
    ;;
  oanhnn)
    git config user.email "oanhnn@example.com"
    git config user.name "oanhnn" 
    git config user.signingKey "oanhnn_gpg_public_key"
    ;;
  # default case: raise error
  *)
    >&2 echo "ERR: Unknown profile: $profile"
    exit 1
esac
```