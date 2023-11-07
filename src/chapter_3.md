PULL REMOTE CODE REPO TO AKAMAI::

---

1. Before proceeding with this guide, now is a good time to change/add new 'AUTHORITATIVE NAMESERVER's to the domain name registrar of your choice, e.g - Porkbun. The new values of your 'AUTHORITATIVE NAMESERVER' should change to; 'ns1.linode.com', 'ns2.linode.com', 'ns3.linode.com', 'ns4.linode.com', 'ns5.linode.com'.
2. Confirm that you have deployed your source code to GitHub. Provided that you followed this guide from its starting point, you have already done so at this point. Hence, the remainder of this guide assumes that you already have a GitHub repo set-up for your source code.
3. Configure GitHub username and password for your WSL terminal. Enter commands `$ git config --global user.name "<your-name>"` AND `git config --global user.email <your-dev-email-here>`.
   - For details check - https://www.git-scm.com/book/en/v2/Customizing-Git-Git-Configuration
4. Create a new ssh-key pair (public & private key-pair) with command;
   ```sh
    ssh-keygen -t ed25519 -C "<your-email-address-here>"
   ```
   Preferably change the name and target location to store the SSH keypair, for-example - `/home/<your-akamai-instance-user-e.g-lance>/.ssh/<ssh-key-pair-custom-name>`. Let your ssh-keypair name be something like
   `access_to_dsq_workspace`. Optionally use a passphrase, which you can save in the keychain.
5. Activate your ssh-agent so you can make it know about the ssh-key that you have just created using command;
   ```sh
    eval "$(ssh-agent -s)"
   ```
   You should see something similar to the following;
   `Agent pid <an-id>`
6. Add your just created private key pair to the ssh-agent to actually let the activated ssh-agent know about the just created ssh-key. Use command;
   ```sh
    ~/.ssh/config
   ```
   ...to attempt to open a config file, but in case that the config file does not exist, simply create a new config one by entering command;
   ```sh
   sudo nano ~/.ssh/config
   ```
7. Add the following information to the just-created config file:
   ```
   Host *
     AddKeysToAgent yes
     IdentityFile ~/.ssh/access_to_axum_workspace
     UseKeychain yes
   ```
8. Enter 'Ctrl + O', then press the 'Enter' key to save the edited config file, and then enter 'Ctrl + X' to exit out of the nano editor
9. Run an ssh-add command to add the private ssh-key key pair which you have just created:
   ```
   ssh-add ~/.ssh/<the-name-of-your-private-ssh-key-pair-e.g-id_ed25519>
   ```
   The above command should out something similar to this -
   ```
   Identity added: /<the-location-of-the-added-ssh-key-pair> (<the-email-attached-to-the-added-ssh-key-pair>)
   ```
10. Now copy the public ssh keypair using the cat command

    ```
    $ cat ~/.ssh/<the-name-of-your-public-ssh-key-pair-e.g-access_to_axum_workspace.pub>
    ```

11. Begin the process of adding a new public ssh-key pair to GitHub.

    - Go to your repo on GitHub. Click the green 'Code<>' button and then click the 'SSH' tab from the pop-up, and then click on the link-text that suggests creating a new public key. At the moment of this writing, the link has text 'add a new public key' ( https://github.com/settings/ssh/new ).
    - Paste the pub SSH keypair that you just generated earlier, and provide a title value that (preferably) matches the name given to the generated SSH pub-keypair without its '.pub' suffix of course, then choose the 'authentication' option and then click save SSH key. You may be asked to re-login to GitHub if this is the first ssh key you're adding to your GitHub account.

12. Run command `ssh -T git@github.com` to set your ssh key(s) to target GitHub
13. Enter `yes` if prompted for a confirmation.
14. Clone your GitHub repo (via SSH):


    - Again, go back to your source-control (GitHub) repo, click on the green 'Code<>' button, click the 'SSH' tab from the pop-up, and this time copy the displayed SSH link to the repo. The link looks similar to this: `git@github.com:<your-github-user-name>/<your-repo's-name>.git`
    - Navigate into your Akamai instance target/favorite directory to hold your code-repo, in this case 'dsq-1-app' if you haven't already done so, and then enter command `git clone <ssh-link-to-your-GitHub-source-control-repo-e.g-dsq-workspace>`
    - Provide the passphrase for the SSH keypair if asked.

---
