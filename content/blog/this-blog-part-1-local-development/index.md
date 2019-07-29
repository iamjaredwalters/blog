---
title: 'This Blog: Part 1 - Local Development Environment'
date: '2019-01-16T18:25:32.169Z'
---
> This is a 3 Part series about building this blog. Checkout the others here:
>
> Link Part 2: Tooling, Testing, Wallaby
>
> Link Part 3: CI/CD

Alright. Let's build a blog.

Saddle up. It's time to build a blog. On steroids. Well, the blog won't be *on* steroids. But it will be _on_ steroids, if you get my drift. 

The kind of steroids that turn you into a hulking mass of power built to do one thing. Make static html and css. Here's the diagram.

[insert bad diagram - diagram description: Developers like diagrams] 

What we need
[simple diagram: html, css]

What we want
[complex diagram]

3 column diagram with headings: local and ci/cd.


Every blog starts with a good diagram
[insert bad diagram]

Ooooooh boy.

## Intro
Giddyup cowfolk. We ride.

## The Plan
Gotta build this static blog, bb. Let's start with the local environment. 

* Gatsby JS
* Reverse proxy
* TLS
* Custom Domain Name
* Docker Everything

Alright. Let's kick things off with this blog itself.  All I need is a static site, 
let's give [Gatsby](https://www.gatsbyjs.org/) a whirl. Gonna need a reverse proxy so
for those pretty urls and might as well through in https for good measure. 
Dockerize everything. Chant it with me. 

Lets get cookin. 

## Step 1

* reverse-proxy for ssl
    * nginx
    * generate-cert.sh
    * dnsmasq
        * sudo touch /etc/resolver/test
        * fix this command: cat namespace 127.0.0.1
        
Locally, I want to be able to use a domain name to view my blog. Let's 
throw in https, too. We'll need 3 things:
* dnsmasq - local DNS server
* nginx - reverse-proxy to route domain names and terminate TLS/SSL
* openSSL - generate self-signed cert for local dev

In order to keep our machine pure and our development environment reproduceable
we'll use Docker to handle everything.

The setup will look like this: dnsmasq will route certain TLD's to an
nginx reverse-proxy. Reverse proxy will terminate SSL (tls?) and route 
traffic to the app. 

### lots of yml
```yaml
version: '3.7'

services:
  nginx:
    image: jwilder/nginx-proxy
    container_name: nginx-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro					
      - certs:/etc/nginx/certs
    
  dnsmasq:
    image: andyshinn/dnsmasq
    container_name: dnsmasq
    restart: always
    depends_on:
      - nginx
    entrypoint:
      - dnsmasq
      - -k
      - --address=/test/127.0.0.1
    cap_add:
      - NET_ADMIN
    ports:
      - "53:53/tcp"
      - "53:53/udp"
  
  generate-cert:
    build:
      context: .
    volumes:
      - certs:/etc/nginx/certs

volumes:
  certs:

networks:
  default:
    external:
      name: nginx-proxy
```

## Step 2
1. Make Gatsby docker image
    1. cd docker-gatsby/
    2. touch docker-compose.yml
    3. add yaml
    4. docker build -t artofoverengineering/gatsby:latest -f ./Dockerfile .
    5. docker ps, voila
3. docker-compose.yml

### 1.5
```bash
$ docker image ls
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
artofoverengineering/gatsby              latest              d304c5a0bef7        3 hours ago         112MB
```
### 1.3
```yaml
# docker-compose.yml

version: '3.7'

services:
  gatsby:
    image: artofoverengineering/gatsby
    command: develop
    expose:
      - '8000'
    volumes:
      - .:/app
    environment:
      VIRTUAL_HOST: artofoverengineering.test

networks:
 default:
   external:
     name: nginx-proxy
```

$ docker build -t artofoverengineering/gatsby:latest -f ./Dockerfile .

## Step 3
* Make docker-compose
```bash
$ docker-compose up -d
```

## Summary
Look at all the docker

## Next
Part 2: Tooling, Testing, Wallaby


Far far away, behind the word mountains, far from the countries Vokalia and
Consonantia, there live the blind texts. Separated they live in Bookmarksgrove
right at the coast of the Semantics, a large language ocean. A small river named
Duden flows by their place and supplies it with the necessary regelialia.

## On deer horse aboard tritely yikes and much

The Big Oxmox advised her not to do so, because there were thousands of bad
Commas, wild Question Marks and devious Semikoli, but the Little Blind Text
didn’t listen. She packed her seven versalia, put her initial into the belt and
made herself on the way.

- This however showed weasel
- Well uncritical so misled
  - this is very interesting
- Goodness much until that fluid owl

When she reached the first hills of the **Italic Mountains**, she had a last
view back on the skyline of her hometown _Bookmarksgrove_, the headline of
[Alphabet Village](http://google.com) and the subline of her own road, the Line
Lane. Pityful a rethoric question ran over her cheek, then she continued her
way. On her way she met a copy.

### Overlaid the jeepers uselessly much excluding

But nothing the copy said could convince her and so it didn’t take long until a
few insidious Copy Writers ambushed her, made her drunk with
[Longe and Parole](http://google.com) and dragged her into their agency, where
they abused her for their projects again and again. And if she hasn’t been
rewritten, then they are still using her.

> Far far away, behind the word mountains, far from the countries Vokalia and
> Consonantia, there live the blind texts. Separated they live in Bookmarksgrove
> right at the coast of the Semantics, a large language ocean.

It is a paradisematic country, in which roasted parts of sentences fly into your
mouth. Even the all-powerful Pointing has no control about the blind texts it is
an almost unorthographic life One day however a small line of blind text by the
name of Lorem Ipsum decided to leave for the far World of Grammar.

### According a funnily until pre-set or arrogant well cheerful

The Big Oxmox advised her not to do so, because there were thousands of bad
Commas, wild Question Marks and devious Semikoli, but the Little Blind Text
didn’t listen. She packed her seven versalia, put her initial into the belt and
made herself on the way.

1.  So baboon this
2.  Mounted militant weasel gregariously admonishingly straightly hey
3.  Dear foresaw hungry and much some overhung
4.  Rash opossum less because less some amid besides yikes jeepers frenetic
    impassive fruitlessly shut

When she reached the first hills of the Italic Mountains, she had a last view
back on the skyline of her hometown Bookmarksgrove, the headline of Alphabet
Village and the subline of her own road, the Line Lane. Pityful a rethoric
question ran over her cheek, then she continued her way. On her way she met a
copy.

> The copy warned the Little Blind Text, that where it came from it would have
> been rewritten a thousand times and everything that was left from its origin
> would be the word "and" and the Little Blind Text should turn around and
> return to its own, safe country.

But nothing the copy said could convince her and so it didn’t take long until a
few insidious Copy Writers ambushed her, made her drunk with Longe and Parole
and dragged her into their agency, where they abused her for their projects
again and again. And if she hasn’t been rewritten, then they are still using
her. Far far away, behind the word mountains, far from the countries Vokalia and
Consonantia, there live the blind texts.

#### Silent delightfully including because before one up barring chameleon

Separated they live in Bookmarksgrove right at the coast of the Semantics, a
large language ocean. A small river named Duden flows by their place and
supplies it with the necessary regelialia. It is a paradisematic country, in
which roasted parts of sentences fly into your mouth.

Even the all-powerful Pointing has no control about the blind texts it is an
almost unorthographic life One day however a small line of blind text by the
name of Lorem Ipsum decided to leave for the far World of Grammar. The Big Oxmox
advised her not to do so, because there were thousands of bad Commas, wild
Question Marks and devious Semikoli, but the Little Blind Text didn’t listen.

##### Wherever far wow thus a squirrel raccoon jeez jaguar this from along

She packed her seven versalia, put her initial into the belt and made herself on
the way. When she reached the first hills of the Italic Mountains, she had a
last view back on the skyline of her hometown Bookmarksgrove, the headline of
Alphabet Village and the subline of her own road, the Line Lane. Pityful a
rethoric question ran over her cheek, then she continued her way. On her way she
met a copy.

###### Slapped cozy a that lightheartedly and far

The copy warned the Little Blind Text, that where it came from it would have
been rewritten a thousand times and everything that was left from its origin
would be the word "and" and the Little Blind Text should turn around and return
to its own, safe country. But nothing the copy said could convince her and so it
didn’t take long until a few insidious Copy Writers ambushed her, made her drunk
with Longe and Parole and dragged her into their agency, where they abused her
for their projects again and again.

