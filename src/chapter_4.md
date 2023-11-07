NOTE:

- You can copy any file from your windows machine, using Git Bash for example, to your Akamai server, directly.
  To do this, enter the following scp command:
  `scp <~/target-file> root@<your-akamai-instance-pub-ipv4-addr>:~/<target-dir>`

1. CD into the directory containing your Nginx installation/container inside your Akamai server

- Enter command
  `cd /etc/nginx`

2. CD to "sites-available"

- Enter command
  `cd sites-available`

3. Open file "default" using nano

- Enter command
  `sudo nano default`

4. Scroll down using the down-arrow key till you find parameter "server_name"

- Change propery-value "server*name *" to "server_name <your-domain-name>;"
  OR
- ..to "server_name <your-domain-name> www.<your-domain-name>;"
  Use the latter.

5. Right below the "server_name" parameter is the 'location' block-parameter. Edit its value like so:

```
location / {
  proxy_pass http://localhost:<your-rust-web-api-port>;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Host $host;
  proxy_set_header Connection 'upgrade';
  proxy_cache_bypass $http_upgrade;
}
```

Now save the changes by pressing 'Ctrl'+'o', and then exit out of the nano editor by pressing 'Ctrl'+'x'

6. Use command `$ sudo nginx -t` to check for the correctness of the nginx configurations you just made.

7. Restart Nginx. Use the command below to do so:
   ```
   sudo systemctl restart nginx
   ```

---

8. Activate and enable Ubuntu's firewall (ufw). Enter this command to enable ufw

   ```sh
   sudo ufw enable
   ```

   Then type 'y' and press enter to confirm "yes" choice

9. Set the firewall to allow only specified access-points. Run the following commands one at a time to do so:

   ```
   ufw allow sh
   ```

   ```
   ufw allow http
   ```

   ```
   ufw allow https
   ```

   The above commands would make ufw disallow any other access means such, as access your web-api using ports

10. Install an SSL/TLS Certficate Bot (Certbot). First make sure that you have 'snapd' installed on your machine using command `$ sudo apt install snapd`. Then run command:

```
sudo snap install --classic certbot
```

11. Run command to allow certbot to execute inside your Akamai instance

```
sudo snap install --classic certbot
```

The above command makes certbot's executable available to your Akamai instance's executables registry.

12. Connect Nginx to your certbot. Set up a certbot configuration for Nginx.

- Run `$ sudo certbot --nginx`, then press 'Enter'
- Enter your email (dev-email)
- Type and enter 'Y' to agree to terms & conditions
- Optionally enter 'Y' to opt-in for email newsletter/marketing campaigns
- Press 'Enter' key to allow "www" prefix and non "www" prefix.
