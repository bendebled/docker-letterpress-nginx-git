# docker-letterpress-nginx-git

Minimal image for hosting a Letterpress on a Nginx webserver with Automatic Git Deployment functionalities including Webhooks

## Basic Usage

```
docker run -d \
--name MyLetterpress \
-e 'GIT_REPO=git@bitbucket.org:user/repo.git' \
-e 'GIT_EMAIL=email@example.com' \
-e 'GIT_NAME=gitUser' \
-e 'GIT_HOOK_TOKEN=myBlog' \
-p 8555:8555 \
-p 80:80 \
-e 'SSH_KEY=BIG_LONG_BASE64_STRING_GOES_IN_HERE' \
--restart unless-stopped bendebled/docker-letterpress-nginx-git
```

Note : changing *site_dir* in *letterpress.config* has no effect when using this docker image.

## Configuration

### Available Configuration Parameters

The following flags are a list of all the currently supported options that can be changed by passing in the variables to docker with the -e flag.

- **GIT_REPO** : URL to the repository containing your source code
- **GIT_BRANCH** : Select a specific branch (optional)
- **GIT_EMAIL** : Set your email for code pushing (required for git to work)
- **GIT_NAME** : Set your name for code pushing (required for git to work)
- **SSH_KEY** : Private SSH deploy key for your repository base64 encoded (requires write permissions for pushing)
- **TEMPLATE_NGINX_HTML** : Enable by setting to 1 search and replace templating to happen on your code
- **DOMAIN** : Set domain name for Lets Encrypt scripts
- **GIT_HOOK_TOKEN** : Auth-Token used for the [docker-hook](https://github.com/schickling/docker-hook) listener

### Dynamically Pulling your letterpress from git

One of the nice features of this container is its ability to pull your letterpress from a git repository with a couple of environmental variables passed at run time.

**Note:** You need to have your SSH key that you use with git to enable the deployment. I recommend using a special deploy key per project to minimise the risk.

### Preparing your SSH key
The container expects you pass it the __SSH_KEY__ variable with a **base64** encoded private key. First generate your key and then make sure to add it to github and give it write permissions if you want to be able to push code back out the container. Then run:
```
base64 /path_to_your_key
```
**Note:** Copy the output be careful not to copy your prompt

To run the container and pull code simply specify the GIT_REPO URL including *git@* and then make sure you have also supplied your base64 version of your ssh deploy key:
```
sudo docker run -d -e 'GIT_REPO=git@git.ngd.io:ngineered/ngineered-website.git' -e 'SSH_KEY=BIG_LONG_BASE64_STRING_GOES_IN_HERE' bendebled/docker-letterpress-nginx-git
```
To pull your letterpress and specify a branch add the GIT_BRANCH environment variable:
```
sudo docker run -d -e 'GIT_REPO=git@git.ngd.io:ngineered/ngineered-website.git' -e 'GIT_BRANCH=stage' -e 'SSH_KEY=BIG_LONG_BASE64_STRING_GOES_IN_HERE' bendebled/docker-letterpress-nginx-git
```

## Docker-hook

`docker-hook` listens to incoming HTTP requests and triggers your specified command.

It is included in `/usr/bin/docker-hook` 

There is a listener script copied to `/usr/bin/hook-listener` that will auto-start

## Thanks to

* [eduwass/docker-nginx-git](https://github.com/eduwass/docker-nginx-git) - Nginx with Git auto-pull and webhooks
* [phusion/baseimage-docker](https://github.com/phusion/baseimage-docker/) - Performance oriented base image
* [ngineered/nginx-php-fpm](https://github.com/ngineered/nginx-php-fpm) - Git push/pull functionalities
* [schickling/docker-hook](https://github.com/schickling/docker-hook) - Git Webhook listener
