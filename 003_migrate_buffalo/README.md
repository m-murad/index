<!-- __JSON: egrunner script.sh # LONG ONLINE

### Intro

[Buffalo](https://gobuffalo.io/en) is a popular Go web development eco-system, "designed to make the
life of a Go web developer easier." Buffalo uses dep for dependency management. This guide looks at
the steps require to migrate from using dep to vgo. This guide is based on [a PR Russ
opened](https://github.com/gobuffalo/buffalo/pull/1074) against the Buffalo project.

### Getting started

As ever, start by ensuring vgo is installed and up to date:

```
{{PrintBlock "go get vgo" -}}
```

For this guide we will work known commits of Buffalo and dep so the guide remains reproducible. We
will be using:

```
{{PrintBlock "pinned commits" -}}
```

### The migration

Let's perform this migration in a clean GOPATH:

```
{{PrintBlock "setup" -}}
```

Next, let's install dep and ensure we have our desired commit:

```
{{PrintBlock "install dep" -}}
```

Now, establish baseline buffalo behaviour, by using dep ensure and then running tests.

```
{{PrintBlock "baseline" -}}
```

Now use vgo build (or any other vgo command) to turn dep's Gopkg.lock into a populated go.mod file:

```
{{lineEllipsis (PrintBlock "vgo build") 8 -}}
{{lineEllipsis (PrintBlock "cat go.mod") 8 -}}
```

Run tests to see that baseline behaviour hasn't changed:

```
{{PrintBlock "vgo test" -}}
```

At this point we would now git add go.mod and git commit.

### Version details

```
{{PrintBlockOut "version details" -}}
```

-->

### Intro

[Buffalo](https://gobuffalo.io/en) is a popular Go web development eco-system, "designed to make the
life of a Go web developer easier." Buffalo uses dep for dependency management. This guide looks at
the steps require to migrate from using dep to vgo. This guide is based on [a PR Russ
opened](https://github.com/gobuffalo/buffalo/pull/1074) against the Buffalo project.

### Getting started

As ever, start by ensuring vgo is installed and up to date:

```
$ go get -u golang.org/x/vgo
```

For this guide we will work known commits of Buffalo and dep so the guide remains reproducible. We
will be using:

```
$ buffaloCommit=354657dfd81584bb82b8b6dff9bb9f6ab22712a8
$ depCommit=3e697f6afb332b6e12b8b399365e724e2e8dea7e
```

### The migration

Let's perform this migration in a clean GOPATH:

```
$ mkdir /tmp/gopath
$ export GOPATH=/tmp/gopath
$ export PATH=$GOPATH/bin:$PATH
$ cd /tmp/gopath
```

Next, let's install dep and ensure we have our desired commit:

```
$ go get -u github.com/golang/dep/cmd/dep
$ cd src/github.com/golang/dep/cmd/dep
$ git checkout $depCommit
HEAD is now at 3e697f6a... Merge pull request #1832 from rousan/issue-1749-multiple-dir-in-gopath
$ go install
```

Now, establish baseline buffalo behaviour, by using dep ensure and then running tests.

```
$ cd /tmp/gopath
$ go get -tags sqlite github.com/gobuffalo/buffalo
$ cd src/github.com/gobuffalo/buffalo
$ git checkout $buffaloCommit
HEAD is now at 354657d... Updated SHOULDERS.md
$ go install -tags sqlite
$ dep ensure
$ go test -tags sqlite ./...
ok  	github.com/gobuffalo/buffalo	0.255s
ok  	github.com/gobuffalo/buffalo/binding	0.078s
?   	github.com/gobuffalo/buffalo/buffalo	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/build	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/destroy	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/generate	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/updater	[no test files]
ok  	github.com/gobuffalo/buffalo/generators	0.028s
ok  	github.com/gobuffalo/buffalo/generators/action	0.082s [no tests to run]
?   	github.com/gobuffalo/buffalo/generators/assets	[no test files]
?   	github.com/gobuffalo/buffalo/generators/assets/standard	[no test files]
?   	github.com/gobuffalo/buffalo/generators/assets/webpack	[no test files]
?   	github.com/gobuffalo/buffalo/generators/docker	[no test files]
?   	github.com/gobuffalo/buffalo/generators/grift	[no test files]
?   	github.com/gobuffalo/buffalo/generators/mail	[no test files]
ok  	github.com/gobuffalo/buffalo/generators/newapp	0.121s
?   	github.com/gobuffalo/buffalo/generators/refresh	[no test files]
ok  	github.com/gobuffalo/buffalo/generators/resource	0.025s
?   	github.com/gobuffalo/buffalo/generators/soda	[no test files]
?   	github.com/gobuffalo/buffalo/grifts	[no test files]
ok  	github.com/gobuffalo/buffalo/mail	0.037s
?   	github.com/gobuffalo/buffalo/meta	[no test files]
ok  	github.com/gobuffalo/buffalo/middleware	0.268s
ok  	github.com/gobuffalo/buffalo/middleware/basicauth	0.060s
ok  	github.com/gobuffalo/buffalo/middleware/csrf	0.033s
ok  	github.com/gobuffalo/buffalo/middleware/i18n	0.042s
?   	github.com/gobuffalo/buffalo/middleware/ssl	[no test files]
ok  	github.com/gobuffalo/buffalo/middleware/tokenauth	0.066s
?   	github.com/gobuffalo/buffalo/plugins	[no test files]
ok  	github.com/gobuffalo/buffalo/render	0.040s
ok  	github.com/gobuffalo/buffalo/worker	0.015s
```

Now use vgo build (or any other vgo command) to turn dep's Gopkg.lock into a populated go.mod file:

```
$ vgo build -tags sqlite
vgo: creating new go.mod: module github.com/gobuffalo/buffalo
vgo: copying requirements from /tmp/gopath/src/github.com/gobuffalo/buffalo/Gopkg.lock
vgo: finding gopkg.in/yaml.v2 v2.2.1
vgo: finding gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405
vgo: finding gopkg.in/mail.v2 v2.0.0-20180301192024-63235f23494b
vgo: finding gopkg.in/alexcesaro/quotedprintable.v3 v3.0.0-20150716171945-2caba252f4dc
vgo: finding google.golang.org/appengine v1.0.0
...
$ cat go.mod
module github.com/gobuffalo/buffalo

require (
	dmitri.shuralyov.com/text/kebabcase v0.0.0-20180217051803-40e40b42552a
	github.com/ajg/form v0.0.0-20160802194845-cc2954064ec9
	github.com/dgrijalva/jwt-go v0.0.0-20180308231308-06ea1031745c
	github.com/dustin/go-humanize v0.0.0-20180421182945-02af3965c54e
...
```

Run tests to see that baseline behaviour hasn't changed:

```
$ vgo test -tags sqlite ./...
vgo: downloading github.com/markbates/willie v0.0.0-20180320154129-67934945178e
vgo: downloading github.com/ajg/form v0.0.0-20160802194845-cc2954064ec9
vgo: downloading github.com/markbates/hmax v0.0.0-20170213234856-800e180dcd16
vgo: downloading github.com/stretchr/testify v1.2.1
vgo: downloading github.com/davecgh/go-spew v1.1.0
vgo: downloading github.com/pmezard/go-difflib v1.0.0
vgo: downloading github.com/spf13/cobra v0.0.3
vgo: downloading github.com/spf13/pflag v1.0.1
vgo: downloading github.com/markbates/deplist v0.0.0-20170926152145-5ae023fef618
vgo: downloading gopkg.in/mail.v2 v2.0.0-20180301192024-63235f23494b
vgo: downloading github.com/nicksnyder/go-i18n v1.10.0
vgo: downloading github.com/pelletier/go-toml v1.1.0
vgo: downloading github.com/unrolled/secure v0.0.0-20180416205222-a1cf62cc2159
vgo: downloading github.com/dgrijalva/jwt-go v0.0.0-20180308231308-06ea1031745c
ok  	github.com/gobuffalo/buffalo	0.219s
ok  	github.com/gobuffalo/buffalo/binding	0.059s
?   	github.com/gobuffalo/buffalo/buffalo	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/build	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/destroy	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/generate	[no test files]
?   	github.com/gobuffalo/buffalo/buffalo/cmd/updater	[no test files]
ok  	github.com/gobuffalo/buffalo/generators	0.126s
ok  	github.com/gobuffalo/buffalo/generators/action	0.096s [no tests to run]
?   	github.com/gobuffalo/buffalo/generators/assets	[no test files]
?   	github.com/gobuffalo/buffalo/generators/assets/standard	[no test files]
?   	github.com/gobuffalo/buffalo/generators/assets/webpack	[no test files]
?   	github.com/gobuffalo/buffalo/generators/docker	[no test files]
?   	github.com/gobuffalo/buffalo/generators/grift	[no test files]
?   	github.com/gobuffalo/buffalo/generators/mail	[no test files]
ok  	github.com/gobuffalo/buffalo/generators/newapp	0.030s
?   	github.com/gobuffalo/buffalo/generators/refresh	[no test files]
ok  	github.com/gobuffalo/buffalo/generators/resource	0.047s
?   	github.com/gobuffalo/buffalo/generators/soda	[no test files]
?   	github.com/gobuffalo/buffalo/grifts	[no test files]
ok  	github.com/gobuffalo/buffalo/mail	0.057s
?   	github.com/gobuffalo/buffalo/meta	[no test files]
ok  	github.com/gobuffalo/buffalo/middleware	0.350s
ok  	github.com/gobuffalo/buffalo/middleware/basicauth	0.055s
ok  	github.com/gobuffalo/buffalo/middleware/csrf	0.081s
ok  	github.com/gobuffalo/buffalo/middleware/i18n	0.050s
?   	github.com/gobuffalo/buffalo/middleware/ssl	[no test files]
ok  	github.com/gobuffalo/buffalo/middleware/tokenauth	0.066s
?   	github.com/gobuffalo/buffalo/plugins	[no test files]
ok  	github.com/gobuffalo/buffalo/render	0.044s
ok  	github.com/gobuffalo/buffalo/worker	0.014s
```

At this point we would now git add go.mod and git commit.

### Version details

```
go version go1.10.2 linux/amd64 vgo:2018-02-20.1
vgo commit: 416f375193bdc882469e470452be5d856646df76
```

<!-- END -->