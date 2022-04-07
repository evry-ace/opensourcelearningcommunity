# How to set up conditional git configuration

In git, you can configure different behaviour for different repositories, in a hierarchical fashion, using conditional configurations.

(There is a system wide git configuration file as well, but for the sake of brevity, we will not go into that.)

In your home directory, you will find your "global" git configuration file `~/.gitconfig`

("global", because it's only for your user profile)

In this file, you will place all your default settings. My global git config looks like this (`# With comments`):

```sh
> cat ~/.gitconfig
[user]
	name = Thomas Qvidahl
	email = thomas.qvidahl@tietoevry.com

[includeIf "gitdir:~/code/"]
  path = ~/code/.gitconfig

[init]
	defaultBranch = main
```

The conditional setup is the `includeIf` statement, which checks to see which git repo you are currently in.

In my case, if I'm inside the `~/code/` directory, the .gitconfig for that directory is included.

Now, let's take a look at my `code` directory:

```bash
> tree ~/code
/Users/thomasqvidahl/code
├── myc
├── nidarossquash
├── private
├── research-client
└── telecom-client
```

It is organized after my different clients as well as a private folder.

The `~/code/.gitignore` contains configuration for each folder in the tree:

```sh
> cat ~/code/.gitconfig
[includeIf "gitdir:~/code/myc/"]
  path = ~/code/myc/.gitconfig

[includeIf "gitdir:~/code/nidarossquash/"]
  path = ~/code/nidarossquash/.gitconfig

[includeIf "gitdir:~/code/private/"]
  path = ~/code/private/.gitconfig

[includeIf "gitdir:~/code/research-client/"]
  path = ~/code/research-client/.gitconfig

[includeIf "gitdir:~/code/telecom-client/"]
  path = ~/code/telecom-client/.gitconfig
```

Then, under each client folder, I have a separate `.gitignore` file with the directives for all repos belonging to that client.

For instance, Telecom-client requires each and every git commit to be signed with GPG. To acheive this, the `~/code/telecom-client/.gitignore` file looks like this:

```sh
> cat ~/code/telecom-client/.gitconfig
[user]
  name = Thomas Qvidahl
  email = thomas.qvidahl@tietoevry.com
  signingkey = 60AABBCCDDEEFFGG
[commit]
  gpgsign = true
[gpg]
  program = gpg
```

The name and email address is redundant, as it is already set in global gitconfig, but is included here to make sure the Telecom-client setup isn't inadvertently altered by a change in the global setup.

So looking at the git config for a repo under the Telecom-client folder, it looks like this (`#with comments`):

```bash
# System gitconfig:
credential.helper=osxkeychain
# Global gitconfig:
user.name=Thomas Qvidahl
user.email=thomas.qvidahl@tietoevry.com
includeif.gitdir:~/code/.path=~/code/.gitconfig
# code gitconfig:
includeif.gitdir:~/code/myc/.path=~/code/myc/.gitconfig
includeif.gitdir:~/code/nidarossquash/.path=~/code/nidarossquash/.gitconfig
includeif.gitdir:~/code/private/.path=~/code/private/.gitconfig
includeif.gitdir:~/code/research-client/.path=~/code/research-client/.gitconfig
includeif.gitdir:~/code/telecom-client/.path=~/code/telecom-client/.gitconfig
# telecom-client gitconfig:
user.email=thomas.qvidahl@tietoevry.com
user.signingkey=6081AB7993E2921E
commit.gpgsign=true
gpg.program=gpg
# Global gitconfig again:
init.defaultbranch=main
```

And there you have it - an accommodating and scalable setup for conditional git configurations.
