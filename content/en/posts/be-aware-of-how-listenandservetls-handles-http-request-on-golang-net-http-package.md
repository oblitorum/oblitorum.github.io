---
title: "Be Aware of How ListenAndServeTLS Handles Http Request on Golang net/http Package"
date: 2022-04-04T17:53:38+08:00
draft: false
tags: [Golang]
enableToc: false
meta_image: images/be-aware-of-how-listenandservetls-handles-http-request-on-golang-net-http-package-1.png
---

On version >= 1.12, Golang changes its way to handle HTTP request when you serve with ListenAndServeTLS method. The difference is shown on below image, which is taken from this [link](https://github.com/golang/go/compare/go1.11.13...golang:go1.12).

![golang 1.11.13 and 1.12 diff image](/images/be-aware-of-how-listenandservetls-handles-http-request-on-golang-net-http-package-1.png)

As we can see, on version < 1.12, it just returns without writing anything in response, which makes a 'malformed HTTP response' error when you use Get or Client.Do with net/http package. And on version >= 1.12, it will return an 400 response with 'Client sent an HTTP request to an HTTPS server.' message.

Some third-party packages haven't noticed this difference yet, they may use 'malformed HTTP response' error to check if server setup TLS or not. For example, [this one](https://github.com/percona/pmm-client/blob/cd42b1da19df0a278302a74d475db1e8e1fe0cb1/pmm/check_network.go#L365) uses this way, it will cause a problem if the server is built with Golang version >= 1.12.
