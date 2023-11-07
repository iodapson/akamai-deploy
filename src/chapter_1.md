1. First, go to the web-address below to create a new Akamai account, and instance:
   https://cloud.linode.com/linodes

2. Click the 'Linodes' section.

3. Click 'Add a Linode' to add a new Linode server

4. Make sure you are on the 'Distributions' server.

5. You might wanna change your distribution to an 'Ubuntu LTS' or more preferably, Docker since this guide would walk you through deploying a containerized application.

6. Select a region. You might wanna choose San Francisco so you have access to more services.

7. Choose your instance. I recommend 'Nanode' (the most gentle one for your pocket).

8. Give your instance a name and password. No need for backup. And wait for a few minutes.

9. Click 'Launch LISH console'. Make sure that you are on 'Weblish'

10. Inside the LISH console, type `root` as the username value, then enter, then type in your root-password value, then enter. You should now see something like this - 'root@localhost:-#'.

Note: The best and recommended way to connect to your Linode is via SSH.

Note: You can add new ip-addresses to your Akamai web server instance.

- To add an ip-address to your Linode server instance, click on your server instance name, then the 'Network' tab, scroll down then select 'ipv4' (private or public). Then click 'Allocate'.
- Now is also a good time to purchase yourself a domain name from domain registrars such as 'Namecheap', and 'Porkbun'.
