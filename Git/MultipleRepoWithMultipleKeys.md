Using [github's process](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent), I generated a new ssh key and added it to my profile on github

I added a config file in my .ssh folder, and I referenced my work ssh key and my personal ssh key as was outlined in other answers.
```
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

ssh-add --apple-use-keychain ~/.ssh/work_key

ssh-add --apple-use-keychain ~/.ssh/personal_key

I verified that both keys were added with ssh-add -L

Then I [verified the connection](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection) with ssh -T git@github.com and ssh -T git@personal, and both worked!

After that, I was able to clone repos using my personal key with this command: git clone git@personal:my_username/repo.git (swapping out the git@github.com:... that I copied from my github repo for git@personal:...)
