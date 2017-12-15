![](https://github.com/dutchcoders/marija-screenshots/blob/master/marija.png?raw=true)

# Marija [![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/dutchcoders/marija?utm_source=badge&utm_medium=badge&utm_campaign=&utm_campaign=pr-badge&utm_content=badge) [![Go Report Card](https://goreportcard.com/badge/dutchcoders/marija)](https://goreportcard.com/report/dutchcoders/marija) [![Docker pulls](https://img.shields.io/docker/pulls/marija/marija.svg)](https://hub.docker.com/r/marija/marija/) [![Build Status](https://travis-ci.org/dutchcoders/marija.svg?branch=master)](https://travis-ci.org/dutchcoders/marija)

Marija is a data exploration and visualisation tool for (un)structured Elasticsearch data. Using Marija you'll be able to see relations 
between data of different datasources without any modifications to your data or index.

Currently Marija is being used to identify related spamruns, but can be used for all kind of different data sets.

Disclaimer: Marija is still in alpha, expect (many) bugs. Please report bugs in the issue tracker.

# Screenshot

![](https://github.com/dutchcoders/marija-screenshots/blob/master/Screen%20Shot%202016-11-17%20at%2009.46.31.png?raw=true)

## Install

### Using Docker

```
$ docker pull marija/marija
$ docker run -d -p 8080:8080 --name marija marija/marija
```

### Installation from source

#### Install Golang

If you do not have a working Golang environment setup please follow [Golang Installation Guide](https://golang.org/doc/install).

#### Install Marija

Installation of Marija is easy.

```
$ go get github.com/dutchcoders/marija
$ marija
```

### Installation using Homebrew (macOS)

```
$ brew tap dutchcoders/homebrew-marija
$ brew install marija
```

## Usage

There are a few steps you need to take before you can start.

* add your datasources to config.toml
* enable the datasources you want to search in using the eye icon
* use the refresh icon to refresh the list of available fields
* add the fields you want to use as nodes

* additionally you can add the date field you want to use for the histogram
* and add some normalizations (eg removing part of the identifier) using regular expressions

You're all setup now, just type your queries and start exploring your data.

## Demo

There is an online demo available at [http://demo.marija.io/](http://demo.marija.io/). 

### Enron demo

Enable the datasource enron, next click on refresh to retrieve the fields. Now you can add for example fields **to**, **recipients**, **bcc**, **cc** and **sender**. Now you can search for keywords and see the relations between the emails. When you select one or more nodes, open the table view (on the right). Here you can look at the data itself, and add columns to the view. 

### Twitter demo

Enable the datasource twitter, next click on refresh to retrieve the fields. Now you can add for example fields **in_reply_to_screen_name**, **user.screen_name**, **user.name**, **mentions** and **tags**. Now you can search for keywords and see the relations between tweets. When you select one or more nodes, open the table view (on the right). Here you can look at the data itself, and add columns to the view. 

### Blockchain demo

Enable the datasource blockchain, next click on refresh to retrieve the fields. Now you can add for example fields **input_tag**, **output** and **relayed_by**. Now you can search for bitcoin addresses (17TaZ6qkf7ot9nkFLZPV9kjbWByPfjm9c4, 1ABwEbyQ67U2PqbWJCyhL4LZYF3agxVGDe) and see the relations between transactions. When you select one or more nodes, open the table view (on the right). Here you can look at the data itself. 


## Configuration

```
[datasource]
[datasource.elasticsearch]
type="elasticsearch"
url="http://127.0.0.1:9200/demo_index"
#username=
#password=

[datasource.twitter]
type="twitter"
consumer_key=""
consumer_secret=""
token=""
token_secret=""

[datasource.blockchain]
type="blockchain"

[[logging]]
output = "stdout"
level = "debug"
```

## Features

* work on multiple servers and indexes at the same time
* different fields can be used as node identifier
* identifiers can be normalized through normalization regular expressions
* each field will have its own icon
* query indexes using elasticsearch queries like your used to do
* histogram view to identify nodes in time
* select and delete nodes
* select related nodes, deselect all but selected nodes
* zoom and move nodes
* navigate through selected data using the tableview

## Workspace

Currently only one single workspace is supported. The workspace is being stored in the local storage of your browser. Next versions will support loading and saving multiple workspaces.

## Todo

* Optimize, optimize, optimize.

## Roadmap

We're working towards a first version. 

* analyze data at realtime
* create specialized tools based on Marija for graphing for example packet traffic flows. 
* see issue list for features and bugs

## Contribute

Contributions are welcome.

### Setup your Marija Github Repository

Fork Marija upstream source repository to your own personal repository. Copy the URL for marija from your personal github repo (you will need it for the git clone command below).

```sh
$ mkdir -p $GOPATH/src/github.com/marija
$ cd $GOPATH/src/github.com/marija
$ git clone <paste saved URL for personal forked marija repo>
$ cd marija
```

###  Developer Guidelines
``Marija`` community welcomes your contribution. To make the process as seamless as possible, we ask for the following:
* Go ahead and fork the project and make your changes. We encourage pull requests to discuss code changes.
    - Fork it
    - Create your feature branch (git checkout -b my-new-feature)
    - Commit your changes (git commit -am 'Add some feature')
    - Push to the branch (git push origin my-new-feature)
    - Create new Pull Request

* If you have additional dependencies for ``Marija``, ``Marija`` manages its dependencies using [govendor](https://github.com/kardianos/govendor)
    - Run `go get foo/bar`
    - Edit your code to import foo/bar
    - Run `make pkg-add PKG=foo/bar` from top-level directory

* If you have dependencies for ``Marija`` which needs to be removed
    - Edit your code to not import foo/bar
    - Run `make pkg-remove PKG=foo/bar` from top-level directory

* When you're ready to create a pull request, be sure to:
    - Have test cases for the new code. If you have questions about how to do it, please ask in your pull request.
    - Run `make verifiers`
    - Squash your commits into a single commit. `git rebase -i`. It's okay to force update your pull request.
    - Make sure `go test -race ./...` and `go build` completes.

* Read [Effective Go](https://github.com/golang/go/wiki/CodeReviewComments) article from Golang project
    - `Marija` project is fully conformant with Golang style
    - if you happen to observe offending code, please feel free to send a pull request

## Socket API
All communication between the server and the client is done via web sockets.
All messages are encoded with JSON.

### Messages from server to client

#### INITIAL_STATE_RECEIVE
Sent immediately when client makes a connection to the server.

**Example:**
```
{
  "type": "INITIAL_STATE_RECEIVE",
  "state": {
    "datasources": [
      {
        "id": "blockchain",
        "name": "blockchain"
      },
      {
        "id": "twitter",
        "name": "twitter"
      }
    ]
  }
}
```

#### FIELDS_RECEIVE
Sent after a FIELDS_REQUEST message is sent to the server.

Contains all the available fields for the selected datasources.

**Example:**
```
{
  "type": "FIELDS_RECEIVE",
  "fields": {
    "server": "twitter",
    "index": "",
    "fields": [
      {
        "path": "text",
        "type": "string"
      },
      {
        "path": "in_reply_to_screen_name",
        "type": "string"
      }
    ]
  }
}
```

#### ITEMS_RECEIVE
Sent after an ITEMS_REQUEST message is sent to the server.

Contains the search results.

**Example:**
```
{
  "type": "ITEMS_RECEIVE",
  "items": {
    "server": "twitter",
    "query": "wilders",
    "color": "#de79f2",
    "total": 100,
    "results": [
      {
        "id": "94165573590947020",
        "fields": {
          "in_reply_to_screen_name": "",
          "in_reply_to_status_id_str": "",
          "in_reply_to_user_id_str": "",
          "lang": "tr",
          "mentions": [
            "examplemention"
          ],
          "source": "\u003ca href=\"http://twitter.com\" rel=\"nofollow\"\u003eTwitter Web Client\u003c/a\u003e",
          "tags": null,
          "text": "tweet contents",
          "user": {
            "id_str": "89846608896665190",
            "lang": "tr",
            "location": "",
            "name": "example name",
            "screen_name": "example screen name"
          }
        },
        "highlight": null
      }
    ]
  }
}
```

### Messages from client to server

#### FIELDS_REQUEST
Request which fields are available for the selected datasources.

**Example:**
```
{
  "type": "FIELDS_REQUEST",
  "datasources": [
    "twitter"
  ]
}
```

#### ITEMS_REQUEST
Perform a search query.

**Example:**
```
{
  "type": "ITEMS_REQUEST",
  "datasources": [
    "twitter"
  ],
  "query": "wilders",
  "from": 0,
  "size": 500,
  "color": "#de79f2"
}
```

## Creators

**Remco Verhoef**
- <https://twitter.com/remco_verhoef>
- <https://twitter.com/dutchcoders>

**Kevin Hoogerwerf**
- <https://keybase.io/kevinh>

## Copyright and license

Code and documentation copyright 2016 Remco Verhoef.

Code released under [the Apache license](LICENSE).

