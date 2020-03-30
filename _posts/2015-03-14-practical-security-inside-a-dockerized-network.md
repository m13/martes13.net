---
layout: post
title: Practical Security inside a Dockerized Network
post_id: 227
categories: 
- challenges
- docker
---

Imagine you have a bash shell in a machine but you realize it's a docker container. How would you proceed to go deeper?

### Step 1. Understand your container

Usually you won't be able to use 0-days against the kernel but you won't find extra security measures either (expect when running a non-root user). Vectors attacks or methodologies haven't changed at all but this context is a bit special.

You are in a container which can disappear at any moment. It's probably stateless and has a single responsibility. The operative system is quite safe by default because it uses minimum capacities to operate. Forget to leave a bind shell (or reverse) and come back later. They only need to patch, build, push, pull and deploy. Maybe 10 minutes? Time runs against you.

As soon as you obtain a bash shell (somehow), you must reverse the definition of this container. There are 2 elements to guess:


- **Dockerfile**
: Instructions writes files, so you only need to diff against the original image. Even there is a public registry, using a basic image (ex. busybox, ubuntu, debian, fedora, ...) could be enough.

```bash
$ find / -type f ! -path "/sys*" ! -path "/proc*" -exec md5sum {} +
```

- **docker arguments**
: These are the keys for pivoting. Try to rebuild the original "docker run ..." execution line.

| Argument             | Tools                     |
|----------------------|---------------------------|
| Volumes              | $ mount<br>$ df           |
| Links                | $ net ip<br>$ cat /etc/hosts |
| Environment (+ports) | $ env                     |

### STEP 2. Find the weakest point

The most common vulnerability, now and forever, will be default and wrong configurations: human mistakes. Understand the platform and try to extract sensitive data using the designed flow. Probably it's so lose that you will be able to dump the full database.

I have seen several docker related projects use this line:

```bash
$ docker run -it -v **/var/run/docker.sock:/var/run/docker.sock** ubuntu
```

(or as a Host:Port with or without SSL). Install socat for connecting to the sock:

```bash
$ apt-get install socat
$ socat TCP4-LISTEN:1337,fork UNIX-CONNECT:/var/run/docker.sock &
```

Then, read the doc: https://docs.docker.com/reference/api/. You have several options. For example, you could export another containers as raw data or 
[create a new container mounting the root directory as a folder](http://blog.nibblesec.org/2014/09/abusing-dockers-remote-apis.html). You can successfully compromise the full machine in this scenario and most importantly you will have a lot of critical information to use against the internal network.

Another far more complex vector is to take advantage of ephemeral processes that can be running in our compromised machine and open new vulnerabilities into another machines. For example, a Jenkins uses our compromised development machine to build and push a new image into the registry. You can use tools like `inotify`, detect this process, and if there is a shared volume (data-pattern), change the code before it's pushed.

### STEP 3. Complementary software

In real production environments it is not common to execute raw docker commands for starting processes. These tools are quickly identified as they don't expect you there.


**Docker-compose** sets as environment variables all the linked containers with their ports. However, tools like this are going to be hell for an intruder. It's pretty easy to set volumes as read-only, limit cpu, memory, etc.


**Supervisord** is a process control system which will work as pid 1 and manage the sub-processes in that container. If you are so lucky to have enough access you will find the logs of all running applications in /var/log/supervisor/ . In the case that the app was running directly as a command/entry-point I think you cannot obtain this information.


**Docker-registry** is a private repository and is without a doubt your goal. Maybe you can scan the whole internal network searching the default port 5000 open, or you can try to guess their backend (s3?) and retrieve the images, or you can access to the Redis instance used as cache, etc.

[The registry doesn't provide directly Auth](https://docs.docker.com/reference/api/registry_api/#authorization). It has to be provided by extra tools (nginx, ..) or what is called Index Auth service. So if you find the HTTP/s API, start with GET /v1/search and dump everything. You will also be able to push something.

An example of what I'm talking about: [http://blog.programster.org/2015/03/17/run-your-own-private-docker-registry/](http://blog.programster.org/2015/03/17/run-your-own-private-docker-registry/) . It's protected from outside (SSL + firewall) but once you are in one container you have free access to any image.


**Consul** provides information about domain resolution and a key/value storage. It has a security model for MITM and other attacks, but also an ACL feature. However, ACL are disabled by default. (It's another great tool that needs to be mentioned)

There are a bunch of new tools appearing which use docker to optimize a specific kind of task. They use to have some kind of security in mind but must be enabled and properly configured. Quick and dirty feature development, not enough QA work, using beta or alpha version, etc are the keys for finding a bogus system.

### Bonus. Denial of Service

If you want to directly crash the machine (which uses more than your container), probably the easier option is:

```bash
$ dd if=/dev/zero of=/fileOrPartition (or /mounted_volume_file)
Docker is able to limit the resources of the CPU, Memory, SWAP, ... but it still doesn't let you limit the harddrive. Probably you are able to get it full and something will crash.
```

### Conclusion

This article was quite simple but I don't like when people talks about the security in docker from only a theoretical viewpoint. Hope you enjoyed it. (And yes, to obtain a shell in a container is NOT trivial).

More advice here: [https://github.com/GDSSecurity/Docker-Secure-Deployment-Guidelines](https://github.com/GDSSecurity/Docker-Secure-Deployment-Guidelines)