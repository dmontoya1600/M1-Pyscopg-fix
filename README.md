# Using Python With Rosetta
## M1 psycopg2 fix


### Problem with M1
The architecture the M1 mac uses is incompatible with many essential programming tools including psycopg2 (an essential part in getting python to work with the postgres database).

### Change architecture
Run the following command in your terminal:
``` 
arch
```
This command will either output **arm64** (meaning you're currently using the M1 native terminal) or **i3864** (meaning that you're already running under Rosetta)

### For those that already have Rosetta set up
(If your output for the previous step was arm64 skip this step)
Run the following command in your terminal:
```
which brew
```
if the output for this is **usr/local/bin/brew** then congratulations! You're already using the rosetta version of brew.


### If your arch result was arm64
#### Step 1
Open VS Code and press ``` cmd + shft + p``` this will open a bar on the top of VSC. Type in "Shell command" and select the option that states "install 'code' command in PATH".
(this is to make sure the ```code``` command still functions in the terminal when we start the process of manipulating the zshrc and zprofile files)
**Now close and reopen your terminal to apply changes.**

#### Step 2 (deleting pyenv and pipenv)
(If you haven't installed pyenv and pipenv then skip to step 3)
Type the following into the terminal:
`code ~/.zshrc` 
Comment out the following lines if you have them
`eval "$(pyenv init -)"` 
`eval "$(pyenv init --)"`

Now in your terminal type in:
`code ~/.zprofile`
and do the same thing there as you did with the zshrc file. ( comment out any eval(pyenv) )

**Now close and reopen your terminal to apply changes.**

Execute the following command in your terminal:
`rm -rf $(pyenv root)`
now you can `brew uninstall pyenv` 
and `brew uninstall pipenv`

**Now close and reopen your terminal to apply changes.**

To make sure pyenv and pipenv were deleted properly, type the following into your terminal:
`which python`
if the output is NOT /Users/{YOURUSERNAME}/.pyenv/shims/python

Then congrats you've succesfully removed pyenv and pipenv from your life... for now.

#### Step 3
Type the following into the terminal:
`code ~/.zshrc` 
In this file we are going to comment out any PATH that begins with 'opt/homebrew'
(opt/homebrew is the path to the M1 version of homebrew and will interfere in the way of brew installations)

> Example of the type of paths we want to delete or comment-out:
`export PATH="/opt/homebrew/opt/openssl"
`

Type the following into the terminal:
`code ~/.zprofile` 
and do the same thing here as you did with the zshrc (remove all opt/homebrew paths)


#### Step 4 Install Homebrew on Rosetta
In your terminal type the following:
`cd /usr/local/Homebrew`
**If succesfully entered the directory then skip this step!**
but if you got something like 
```cd: no such file or directory: /usr/local/Homebrew/``` then this step is for you.

In your terminal enter:
`env /usr/bin/arch -x86_64 /bin/zsh --login` 
(save this command in your notes incase you ever need it)

(if the previous command didn't work then run the following to install rosetta`/usr/sbin/softwareupdate --install-rosetta --agree-to-license`)

this will switch your arch from arm64 to x86_64. 

If you type `arch` into your terminal it should now be `i3864` or `x86_64` (anything other than `arm64` means it was successful)

##### *If you want this to be permanent make sure to right click the terminal icon on your desktop (if it's not there then drag and drop it there) and click on "get info" and check the "open using rosetta" option.*

Now that we're in the correct arch, we can begin installing homebrew.

Enter the following into the terminal:
`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

Close and open your terminal now and enter the following:
`cd /usr/local/ ; ls`
you should see Homebrew inside there now. 

#### Step 5 
Run the following command in the terminal:
`which brew`

It should output `/usr/local/bin/brew`

If it doesn't then close and open the terminal.

Now follow the entire installation process for pyenv on macOS and then follow the entire installation process for pipenv on macOS in that order. You'll find these instructions on week 17 and 18 on a/A open. 

After all of that is complete you can open your zshrc file `code ~/.zshrc` and paste the following in there.
`eval "$(pyenv init -)"` or uncomment it if you haven't commented-out. 

now open the zprofile file `code ~/.zprofile` and paste the following into it (if it's not already there)

`export PYENV_ROOT="$HOME/.pyenv"`
`export PATH="$PYENV_ROOT/bin:$PATH"`
`eval "$(pyenv init --path)"`

Now close and open your terminal to apply the changes.

To make sure everything is set type the following into the terminal.

`file $(which python)`

This should now output `/Users/damian/.pyenv/shims/python: Bourne-Again shell script text executable, ASCII text`

If you see that output then congratulations! You've succesfully set up python under rosetta!

#### Step 6
The final step is regarding postgres. Since you're using the intel version of brew we also need to re-install postgres.
in the terminal run the following commands one at a time:
`brew install postgresql`
|
`brew services start postgresql`
|
`psql -U «your user name» postgres`
|
you'll be inside psql now so exit that using 
`\q`
and now run 
`createdb`
If you get an error saying "database <<user name>> already exists" then no worries it just means that your existing postgreSQL was detected and no need for further work.

## You're all set!
