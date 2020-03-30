---
layout: post
title: "Potential risk using Docker Registry"
post_id: 253
categories: 
- bug
- docker
---

Some people recommend to use your own Private Registry for your images instead the public one. The real problem becomes when you do not have full control of the images. Let me expose an example.

### Chain of images

Do you know how many images do you store every time you pull an unknown image? You can use [this tool](https://github.com/CenturyLinkLabs/docker-image-graph) for a graph like mine:

[![Images chain tree](https://sergioarcos.files.wordpress.com/2015/04/docker_images2.png)](https://sergioarcos.files.wordpress.com/2015/04/docker_images2.png) *Image chain tree (Even I removed lots of old images last week)*

The truth is that when you are preparing your own image you don't think too much about the ones in the middle, don't you? But, will you notice that a layer in the middle changed?

### 1. Create an image `harmless`

```bash
$ docker pull busybox
$ docker run busybox touch /harmless
$ docker commit fbea5a2af7b4 martes13/harmless:1.0
$ docker push martes13/harmless:1.0
```

### 2. Let it be consumed

```bash
$ docker build .
Sending build context to Docker daemon 2.048 kB
Sending build context to Docker daemon
Step 0 : FROM martes13/harmless:1.0
---> 177b857f26b2
Step 1 : RUN touch /helloworld
---> Using cache
---> 3956484611c5
Step 2 : CMD /bin/sh
---> Running in 22425198de95
---> 62577fd6e477
Removing intermediate container 22425198de95
Successfully built 62577fd6e477
$ docker run -it 62577fd6e477
/ # ls
[..] harmless    helloworld [..]
```

### 3. Transform `harmless` into `harmful`

```bash
$ docker pull martes13/harmless:1.0
$ docker run martes13/harmless:1.0 touch /harmfull
$ docker commit 4dd3248e115a martes13/harmless:1.0
$ docker push martes13/harmless:1.0
```

### 4. Let update progressively all consumers

```bash
$ docker build .
[..]
$ docker run -it 7924ddc5e198
/ # ls
[..]        harmful     harmless    helloworld    [..]
```

### What happened?

Maybe you did not noticed, but we updated an old version (1.0) with a new image pointer. You could think that you are using something well-tested and that will not change, but it did. So if an evil guy is able to steal your account full of used images, he will be able to modify all versions and spread something evil to all your fans.

### Solutions

Well, [official images are out of the scope](https://blog.docker.com/tag/digital-signature/). Their signature is verified so they "cannot" be changed.

And then, in the newest version 1.6 (now we use 1.5) there is a [new feature](https://github.com/docker/docker/pull/11109) which allows to download checking the Digest (sha256), so you will be able to check if there is any change. We will have image:tag or image@digest . Sounds good.

Before that, I propose 2 solutions:

1. Create your own Private Registry and ensure that the root images do not change (read my last post)
2. Apply unit tests when your new images is built. As everything in your image is a file, just ensure that nothing unexpected is there (diff from last one?). Halt the execution until human QA verifies it if something changed.

PD: Today Docker got $95M of investment...
