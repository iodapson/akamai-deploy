AUTOMATING CODE DEPLOYMENTS TO PRODUCTION ON AKAMAI

1. This guide would run your app's code as Docker containers in production mode, therefore, make sure to have a decent 'Dockerfile; and 'docker-compose.yml' file for your app's code in order to run your app as Docker containers. If you followed the axum template / axum-leptos template / axum-yew template creation steps, then you've already fulfilled this requirement.

2. Optionally create a DockerHub account to host your application docker images inside DockerHub. This guide does not utilize DockerHub.

- In case you do decide to utilize DockerHub, you can get a GitHub Actions workflow to publish a new docker image to DockerHub each time you push new code changes to GitHub.
- You can get the GitHub workflow file doing this here: https://docs.github.com/en/actions/publishing-packages/publishing-docker-images
- Make neccessary changes like setting a new docker image name with your DockerHub account. You can find this on line 31 or so ('with.images: <specify-your-dockerhub-account-repo-name>/<specify-your-docker-image-name>').
- Configure your GitHub account to contain your DockerHub account username and password as GitHub secrets. Ideally name the secrets as 'DOCKER_USERNAME' and 'DOCKER_PASSWORD'.

Preferably start here:

3. Log-in to your Akamai instance and create a new script to stop currently running container -> create new docker images - and run them as containers.

- First cd into your project's workspace, which in this case is `~/{{your-custom-workspace-name}}`, then create a new '.sh' file named 'docker-maintenance.sh' inside it by running command `nano docker-maintenance.sh` This file should be created right inside the code repo directory for your project inside your Akamai instance.
- Next up, edit the opened script with the following contents:
  ```sh
  # Remove the currently running image
  sudo docker-compose down
  # Note: In case you used `docker run ...` command to manually spin up your previous docker container, then use `docker stop <container-name>` instead of 'docker-compose down'.
  # Remove the current Docker image inside your system
  sudo docker rmi <your-app-docker-image>
  # Optionally remove the existing volume
  sudo docker rm volume <your-app-docker-db-data>
  # ? Maybe pull code changes too from GitHub. !todo(later)
  # Build new Docker image
  sudo docker build -t <your-app-docker-image>:<tag-number-goes-here-e.g-1.0>
  # Note: In case you make use of a remote docker repo, first pull the docker image from dockerhub using command `docker pull <the-docker-hub-repo-name>/<the-docker-hub-image-name>`
  # ...then run a new docker container out of it using command `docker-compose up -d` OR `docker run -t -d -p 80:8070 --name <the-docker-hub-repo-name>:<the-tag-name>` if for some reason are not using a 'docker-compose.yml' file.
  sudo docker-compose up -d
  ```
- Save the shell script using command 'Ctrl' + 'O', and then exit out of the nano editor using command 'Ctrl' + 'X'.

4. Permit the shell-script 'workspace-docker-maintenance.sh' to be executed on your machine using command:

   ```
   sudo chmod +x workspace-docker-maintenance.sh
   ```

5. Test-run the script inside your Akamai instance using command;

```
bash workspace-docker-maintenance.sh
```

<small>The above script's execution essentially stops your Docker containers, and deletes the image and volumes associated with it.</small>

6. Still inside your Akamai instance, make sure to rerun `$ docker-compose up -d` from inside the root level of the directory containing your app's code bring your code live once again.

7. Create a new Git branch name 'production' directly from your GitHub remote repo. You don't need anything (like a 'README.md') included with it.

8. Go back to your Akamai instance; inside the root-level of your application's code (workspace directory), apply Git commands - `$ git pull`, `$ git checkout production`.

9. Open a terminal your local dev machine, navigate to the root-level directory of your app's codebase, and type Git commands - `$ git pull`, `$ git checkout production`.

10. Now you need to create a GitHub Actions workflow for Git branch 'production' to allow a remote SSH connection to your Akamai instance, and run a script to update your server running on your Akamai instance anytime that you make code changes made to Git branch 'production' from inside your local dev machine:

- Note, we'll use the 'ssh-action' from Github repo - https://github.com/appleboy/ssh-action
- Rename the existing '.github/workflows/rust.yml' file inside your local dev machine to 'ci-cd.yml', and then change its content to;

```yaml
# Inside 'ci-cd.yml'
name: Check code & Publish code-changes to Akamai production via remote SSH

on:
  push:
    branches: ['production']

env:
  CARGO_TERM_COLOR: always

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - name: Build
        run: cargo build --verbose
      - name: Run tests
        run: cargo test --verbose

  ssh_into_akamai_production_instance:
    name: Building code inside Akamai
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: executing remote ssh commands to Akamai to run Docker-maintainance
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          password: ${{ secrets.PASSWORD }}
          port: ${{ secrets.PORT }}
          script: bash workspace-docker-maintenance.sh
```

10. Create the necessary GitHub secrets credentials used in the new 'ci-cd.yml' GitHub Actions workflow- 'HOST' (will be your Akamai instance public IPV4 address or connected domain name), 'USERNAME' (will be the local user you created on your Akamai instance), 'PASSWORD' (your Akamai instance local user' password). Provide the value of '22' as your 'PORT', which is the default used in servers.

11. If you haven't already done so, add your '.git' and 'target' to '.dockerignore'. Apply this change to branch 'production'.

12. Make sure to commit the changes you made on your branch 'production' on your local dev machine, and then push the changes to your remote repo. You'll encounter the error: Job "Building code inside Akamai" fail with the following repeated error log - "err: sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper". Fix this error in the next step.

13. Edit and change the first line of the 'workspace-docker-maintenance.sh' bash script from `sudo docker-compose down` to `echo '<your-akamai-instance-user-password>' | sudo -S docker-compose down`. Effecting this change would make your 'workspace-docker--maintenance.sh' file look similar to the following;

```sh

  # Remove the currently running image
  echo '<your-akamai-instance-user-password>' | sudo -S docker-compose down
  # Note: In case you used `docker run ...` command to manually spin up your previous docker container, then use `docker stop <container-name>` instead of 'docker-compose down'.
  # Remove the current Docker image inside your system
  sudo docker rmi <your-app-docker-image>
  # Optionally remove the existing volume
  sudo docker rm volume <your-app-docker-db-data>
  # Now let's pull code changes from GitHub
  git pull
  # Build new Docker image
  sudo docker build -t <your-app-docker-image>:<tag-number-goes-here-e.g-1.0>
  # Note: In case you make use of a remote docker repo, first pull the docker image from dockerhub using command `docker pull <the-docker-hub-repo-name>/<the-docker-hub-image-name>`
  # ...then run a new docker container out of it using command `docker-compose up -d` OR `docker run -t -d -p 80:8070 --name <the-docker-hub-repo-name>:<the-tag-name>` if for some reason are not using a 'docker-compose.yml' file.
  sudo docker-compose up -d
```

14. You need to edit your previously created '~/.ssh/config' (SSH configuration file) to allow the script inside your Akamai instance to make an SSH connection to your remote repo without typing the passphrase associated with your SSH keys. If you don't make this change, then you would have to manually enter command to pull code changes inside Git branch 'production' from your remote repo, and then manaually enter the command to execute your `workspace-docker-maintenance.sh' script file to deploy the changes on your Akamai instance.

```
Host *
  IgnoreUnknown Usekeychain,AddKeysToAgent
  AddKeysToAgent yes
  IdentityFile ~/.ssh/access_to_axum_workspace
  UseKeychain yes
```

Congratulations. You now have a set-up that works for serving a website / web-app to production using Akamai Cloud. You can now merge code changes to local Git branch 'production' then the changes remote Git branch 'production', and have it automatically deployed live on your Akamai instance.

Let's go back to our Rust application code, and add features we can run on 'production' simply by pushing the changes to Git branch 'production' on your remote repo.
