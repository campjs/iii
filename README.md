# campjs dokku settings

A place to store dokku server customizations

## How to add your keys to the dokku server

Supply your email as a paramete to the `add@iii.campjs.com` address, and use
the password `campjs` when prompted.

``` bash
$ cat ~/.ssh/id_rsa.pub | ssh add@iii.campjs.com your@email.com
add@iii.campjs.com's password:
Adding 'your@email.com' to dokku
b3:ac:58:b6:8d:d3:27:32:59:30:3e:ab:5c:0c:55:5b
```

## How to push to the dokku server

Example of how to push to dokku server:

``` bash
$ git clone git@github.com:heroku/node-js-sample.git
$ cd node-js-sample
$ git remote add dokku dokku@iii.campjs.com:node-js-sample
$ git push dokku master
Counting objects: 296, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (254/254), done.
Writing objects: 100% (296/296), 193.59 KiB, done.
Total 296 (delta 25), reused 276 (delta 13)
-----> Building node-js-app ...
Node.js app detected
-----> Resolving engine versions

... blah blah blah ...

-----> Application deployed:
  http://node-js-sample.iii.campjs.com
```

## How to remove your keys from the dokku server

Supply your email as a paramete to the `remove@iii.campjs.com` address, and use
the password `campjs` when prompted.

``` bash
$ ssh remove@iii.campjs.com your@email.com
remove@iii.campjs.com's password:
remove@iii.campjs.com's password:
Removing 'your@email.com' from dokku
```

## How to generate keys (if you don't have them)

For newbies who don't use ssh, here's the incantation to create your own key
pair:

``` bash
$ ssh-keygen -t rsa -C "your@email.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/yourusername/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in test_rsa.
Your public key has been saved in test_rsa.pub.
The key fingerprint is:
3b:1a:42:c2:54:b4:db:9c:10:a0:56:0d:5d:ec:73:32 your@email.com
```

## Requirements

To get this server to work without internet connectivity the following repos
need to be installed:

* [eugeneware/buildstep](https://github.com/eugeneware/buildstep) - custom
  buildpack builder which will use a custom
  [node.js heroku buildpack](https://github.com/eugeneware/heroku-buildpack-nodejs)
  which bypasses the dependenciesd on [semver.io](https://github.com/heroku/semver.io)
  and [s3pository.heroku.com](http://s3pository.heroku.com) for node buildpacks.
  This needs to be built as a docker image and installed on the dokku server as
  `progrium/buildstep`.
* [semver.io](https://github.com/heroku/semver.io) - needs to be running,
  preferably on the dokku server, and also running SSL.
* [s3pository.heroku.com](http://s3pository.heroku.com) a mirror of the node.js
  linux binaries needs to running as well (preferably on the dokku server).
* [npm](https://npmjs.org) - A local mirror of npm needs to exist and be
  listening on `npm.campjs.com`. The `.npmrc` of the custom nodejs buildpack
  has been modified to point to this address.
* [dnsmasq](https://github.com/eugeneware/dnsmasq) to fool the servers into
  thinking that they are connected, we need to reroute some DNS entries to our
  local servers. There also needs to be an entry in `/etc/default/docker` to
  change the DNS server passed to docker containers to be the local `dnsmasq`
  server. (NB: This should probably be a `docker-args` hook). To be run like:
``` bash
docker run -name dnsmasq -v=/etc/dnsmasq.hosts:/dnsmasq.hosts -p='53:5353/udp' -d eugeneware/dnsmasq
```

## TODO

* Make dokku resolve against local npm server
* Local APT repo?
* Download a bunch of common build packs
* Local www server to store these instructions or even provide a web interface
  to add the keys.
