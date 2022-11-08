# git installation

## configuration

```powershell
# check current configuration
git config user.name
git config user.email

# global configuration
git config --global user.name "user"
git config --global user.email "email"
```

## Private Repositories and Tokens

1. Create Personal Access Tokens (PAT) on Github.com
2. When using the git clone command, reference the PAT:

```powershell
git clone https://tokenXXXXXX@github.com/account/repo.git
```

## using SSH

To start, store a public SSH key on GitHub. This is validated against a locally stored private key that Git uses to validate and establish a connection. GitHub SSH keys are created with the ssh-keygen tool that comes prepackaged with updated versions of Windows.

In Windows PowerShell, issue the following ssh-keygen command to create GitHub SSH keys:

```powershell
ssh-keygen -o -t rsa -C "windows-ssh@mcnz.com"
```

The options are as follows:

| ssh-keygen flag |                      Purpose                  |   Suggested   |
|:--------------- |:--------------------------------------------- |:------------- |
| -C              | Comments or metadata to add to the public key | Email address |
| -t              | The type of GitHub SSH key to create          | RSA           |
| -o              | Use the newest OpenSSH format                 | Leave blank   |

You will be asked for an optional passphrase. It's permissible to click enter and leave this blank.

The Windows GitHub SSH keys live in the `.ssh` folder under the current user's home directory. The following directory listing under the `.ssh` folder of a user named Cameron shows the two files created by the ssh-keygen tool:

```
PS C:\Users\Cameron\.ssh> dir
LastWriteTime      Name
-------------      ----
1/1/2022           id_rsa
1/1/2022           id_rsa.pub
```

Open the SSH public key `id_rsa.pub` in a text editor such as Notepad++, perform a Select All, and copy the key.
With the SSH key copied, log into GitHub, navigate to your account settings, and paste the public key as a new SSH key.
With the SSH keys generated, and the public key registered in your GitHub account, you can now use Git to connect to GitHub over SSH on Windows.

Simply copy the SSH URL from the GitHub page of the repository you wish to clone, and then provide that SSH link to the Git clone command.
Notice the initial git clone with GitHub SSH on Windows complains about being unable to establish the host authenticity. That warning message is expected. Just type yes and allow the GitHub SSH clone operation to continue.

Once the remote repository is cloned, you can continue to use Git as you normally would, issue commits, add submodules, rebase branches and push back and forth with GitHub. All operations that use the network will automatically use an SSH connection.
