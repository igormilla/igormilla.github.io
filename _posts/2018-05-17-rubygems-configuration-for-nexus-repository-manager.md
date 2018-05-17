---
layout: post
title: "Missing bits of RubyGems configuration for Nexus Repository Manager"
date: 2018-05-17 19:09:40 +0700
author: igormilla
sitemap: false
keywords: "nexus repository manager user tokens ruby gem rubygems"
description: "Nexus Repository Manager. RubyGems"
---
# Missing bits of RubyGems configuration for Nexus Repository Manager

The other day I was trying to push a gem to a private gem repository
on Nexus Repository Manager. I followed [official documentation][1],
but I kept getting `Unauthorized` exception:

    $ gem nexus my.gem
    Uploading 1 gem to Nexus...
    Unauthorized

In our nexus setup we have [User Tokens][2] enabled, and the part on how
to configure `nexus-gem` to work with User Tokens was completely missing
from documentation. After some digging I found that `nexus-gem` has a config file
at `~/.gem/nexus`, which looked like this:


    ---
    :url: https://nexus.site.com/repository/gems
    :authorization: Basic aSBsb3ZlIHBvdGF0b2Vz=

I think the file was generated when I was attempting to do:

    gem nexus --url https://nexus.site.com/repository/gems --credential "account:password" my.gem

The solution was to replace `:authorization` token generated with your
username:password pair with User Token, which you can find at:

    https://nexus.site.com/#user/usertoken

Click `Access user token` button, copy the code just under:
_Use the following for a base64 representation of "user:password"_ line,
and paste it inside `~/.gem/nexus` file:


    ---
    :url: https://nexus.site.com/repository/gems
    :authorization: Basic <YOUR USER TOKEN>=


[1]: https://help.sonatype.com/repomanager2/ruby%2C-rubygems-and-gem-repositories
[2]: https://help.sonatype.com/repomanager3/security/security-setup-with-user-tokens