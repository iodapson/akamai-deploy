SSH ACCESS TO AKAMAI & AKAMAI CONFIGURATION

---

ACCESSING YOUR AKAMAI INSTANCE VIA SSH

1. Preferably download and install and open the Linux Ubuntu WSL if you're on Windows. This guide assumes that you have the Linux Ubuntu WSL version 22.04 installed.

2. SSH into your Akamai instance using the shell command:

```sh
ssh root@<your-akamai-instance-pub-ipv4-address>
```

3. Type 'yes' if asked to confirm SSL connection and pressed 'Enter'.

4. Type in your Akamai instance' root password and press 'Enter'.

5. Check if Docker is running by entering command `$ docker ps`. If this command is unrecognized, it means your Akamai instance does not have the Docker engine in it, and you will have to do a little more research on how to do so, and then come back to the remainder of this guide.

---

ALIASING THE HOSTNAME OF YOUR AKAMAI INSTANCE FROM ITS PUBLIC IPV4 ADDRESS TO A NEW, AND MEMORABLE DESIRED NAME (E.G, 'my-first-akamai-instance')

6. First ensure that your Akamai instance is up to date by entering the command:

```sh
apt update
```

then

```sh
apt dist-upgrade
```

7. Now get ready to alias the hostname of your Akamai instance from its assigned IPv4 address number-digits to a memorable name, for example 'my-first-akamai-instance'.

8. The first step to take to change the hostname is by entering command:

```
hostnamectl set-hostname my-first-akamai-instance
```

9. Enter command `$ hostname` to output the current hostname and confirm that the hostname indeed got changed to 'my-first-akamai-instance'.

10. Open the host in order to update the host file with the new hostname just created by entering command:

```
nano etc/hosts
```

---

11. Edit the host file. Under '127.0.0.1 localhost'. First input a newline, then paste/type '<your-akamai-instance-pub-IPv4 address> my-first-akamai-instance'. Note that for this example, the new hostname is 'my-first-akamai-instance'. Then add a newline again, and paste/type '<your-akamai-instance-pub-IPv6-address> <the-new-hostname>'

12. Save the changes made to the host file by pressing 'Ctrl' + 'O'.

13. Confirm the changes by pressing the 'Enter' key.

14. Exit out of the nano editor by pressing 'Ctrl' + 'X'.

---

SETTING YOUR AKAMAI INSTANCE TIMEZONE

15. Begin setting up a new timezone for your Akamai instance by first entering command `$ timedatectl list-timezones` to list timezones and find a timezone that match your current timezone.

16. Took note of the name of the desired matching timzone's name, and then press 'q' and the 'Enter' key to exit out of the list.

17. Apply the desired timezone, e.g, 'Africa/Lagos' by entering command:

```
timedatectl set-timezone Africa/Lagos
```

18. Confirm that the the timezone was successfully changed by entering command `$ date`. In the case of timezone 'Africa/Lagos' for example, it should output a date that is the current UTC+1 datetime.

19. Reboot your Akamai instance from Akamai account' dashboard in order to make the change permanently take effect. Note that you can change the timezone again at anytime you wish.

20. Enter command `$ exit` to close the SSH connection to the Akamai instance. Why not take a break if you haven't already :)

---

AUTOMATING SYSTEM-WIDE UPGRADES IN YOUR AKAMAI INSTANCE, CREATING A NEW CUSTOM USER AND PASSWORD TO ACCESS YOUR AKAMAI INSTANCE, & GRANTING THE NEWLY ADDED CUSTOM USER ACCESS TO EXECUTE COMMANDS VIA SUDO USERGROUP

21. Begin the process to secure your Akamai instance by safeguarding access to it by first running command `$ apt update && apt dist-upgrade`.

22. Enter command `$ apt install unattended-upgrades` to install a package called 'unattended-upgrades' that would automatically install updates to your akamai/linode instance. Depending on your your Akamai instance' base-system (hopefully Ubuntu 22.04), you would find that this package has been already being pre-installed in your Akamai instance.

23. Enter the following command `$ useradd -m -s /bin/bash <your-akamai-instance-custom-user's-name> && passwd <your-akamai-instance-custom-user's-name>` to create a new custom user for your Akamai instance besides user 'root'.

24. Enter a password value for the new custom user. Keep note of this password safely somewhere.

25. Confirm that a new user was indeed added to your Akamai instance by entering command `$ ls /home`. The newly created custom user should be listed as a directory.

---

26. Also confirm that the new user '<your-akamai-instance-custom-user's-name>' had its password set by entering command `$ cat /etc/passwd`. It should print out something similar to this: '<your-akamai-instance-custom-user's-name:x:1000:1000::/home/[new-user-name]:/bin/sh' at the bottom part of the output.

27. Enter command `$ which sudo` to confirm that your Akamai instance has 'sudo' installed on it. It should output the directory of its sudo installation which is in '/usr/bin/sudo'.

28. Enter command `$ visudo` and press down the down arrow-key until you see '%admin ALL=(ALL) ALL', and '%sudo ALL=(ALL:ALL) ALL'. Seeing groups ('%admin', and '%sudo') confirms that these groups (sudo, and admin) have previledges to execute any command across all of your Akamai instance system.

29. Press 'Ctrl' + 'X' to exit out of the 'visudo' file opened with the Nano editor.

30. Enter the command `$ usermod -aG sudo <your-akamai-instance-custom-user's-name>` to add the custom user's name to group 'sudo' which can execute any command across your Akamai instance.

---

31. Enter command `$ groups <your-akamai-instance-custom-user's-name>` to output the groups that '<your-akamai-instance-custom-user's-name>' belongs to, and confirm that 'sudo' is indeed one of them.

32. Enter command `$ su - <your-akamai-instance-custom-user's-name>` to switch from the current default user 'root' to '<your-akamai-instance-custom-user's-name>. It should successfully switch to the new user '<your-akamai-instance-custom-user's-name>' on the terminal as '<your-akamai-custom-user's-name>@my-first-akamai-instance'.

Please Note: Switching to the new user' <your-akamai-instance-custom-user's-name>' means that you'll need to precede every command you make inside your Akamai instance with a 'sudo', especially if such command requires wirte access to something inside your Akamai instance.

---

OPTIONAL: SSH ACCESS TO YOUR AKAMAI INSTANCE FROM THE WSL TERMINAL ON LOCAL DEV MACHINE

33. First exit out of your Akamai instance which should bring you back to your WSL terminal interface once again. Then enter command `$ ssh-keygen -t ed25519 -C <your-dev-email-address>` to generate a new SSH keypair to automate SSH connections to your Akamai instance right from inside your locally installed WSL.

34. Confirm the location inside your WSL to store the generated SSH keypair, it should be similar to this - '/home/<your-local-wsl-user's-name>/.ssh/<your-chosen-akamai-ssh-access-keypair-name>'.

35. Optionally provide a passphrase for the generated ssh key pair.

---

36. Enter command `$ ls -l ~/.ssh` to confirm the existence of the generated ssh-key pair. This command should print out keypairs by their name '<your-chosen-akamai-ssh-access-keypair-name>' and '<your-chosen-akamai-ssh-keypair-name>.pub' as output.

37. Enter the command to copy over the public key of the generated ssh keypair over to your Akamai instance under/for user <your-akamai-instance-custom-user's-name> inside your Akamai instance.

```
$ ssh-copy-id -i ~/.ssh/<chosen-ssh-keypair-name>.pub <your-akamai-instance-custom-user's-name>@<your-akamai-instance-pub-ipv4-address>
```

38. Enter 'yes' to confirm the ssh connection to your Akamai instance.

39. Enter the password of/for '<your-akamai-instance-custom-user's-name>' on your Akamai instance in order to gain access to the instance.

40. Now you can SSH into your Akamai instance from the terminal or your local WSL installation. To do so now, first exit out of your Akamai instance and re-login using SSH command:

```
$ ssh <your-akamai-instance-custom-user's-name>@<your-akamai-pub-ipv4-address>`
```

---

REMOVE USER ROOT AND ITS PRIVILEGES FROM ALKAMAI INSTANCE

41. Log-in to your Akamai instance, e.g, 'your-first-akamai-instance' using the command `$ ssh <your-akamai-instance-custom-user's-name>@<your-first-akamai-instance-pub-ipv4-address>`, then provide the password for '<your-custom-user's-name>' OR SSH access passphrase, as the case may be.

42. Run command `$ sudo apt update && sudo apt dist-upgrade` to update the package registry list and perform an actual upgrade of all the packages inside the Akamai instance. Please note again that henceforth, a prefix of 'sudo' is needed for every command you'll make inside your Akamai instance.

43. Provide the password for 'your-first-akamai-instance' Akamai instance.

44. Begin the process of removing user 'root' access to the Akamai instance for security reasons by entering the command `$ sudo nano /etc/ssh/sshd_config` which opens up the ssh config file for editing.

45. Press the down-arrow key multiple times until you reach option-value 'PermitRootLogin yes', and changed it to 'PermitRootLogin no'.

---

46. Enter a newline (just for aethestics), and provide a new option-value after comment line '#MaxSessions 10' with the value - 'AllowUsers <your-akamai-instance-custom-user's-name>'. Note that you could add more users other than '<your-akamai-intance-custom-user's-name>', you just need to separate each custom user's name with a space.

47. Press 'Ctrl' + 'O' to save the file, then press 'Enter' to confirm the default name to save the file as, then press 'Ctrl' + 'X' to exit out of Nano.

48. Enter command `$ sudo systemctl restart sshd` to restart the ssh daemon service which wouldn't break user '<your-akamai-instance-custom-user's-name>' SSH connection pipe to your Akamai instance while restarting. Note however that you will be asked to enter the password for user '<your-akamai-instance-custom-user's-name>'.

49. Open a new Ubuntu 22.0.4 WSL terminal and SSH into '<your-akamai-instance-custom-user's-name>@<your-akamai-instance-name-alias-or-ipv4-address> just to make sure that the ssh connection still works. You should be logged in successfully which makes it safe to assume that user '<your-akamai-instance-custom-user's-name>' SSH connection access to the Akamai instance was broken.

---

50. You may check for some ports being listened to by your Akamai instance to make sure that only safe and permitted services are running on the Akamai instance. The accepted services may look similar to the following; '127.0.0.53%lo:domain', '127.0.0.1:38551', '127.0.0.53lo:domain', '0.0.0.0:ssh', '45.33.13.203:ssh', and '[::]:ssh'.
