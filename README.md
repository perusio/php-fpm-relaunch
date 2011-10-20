# Relaunch a php-fpm when Nginx finds a 502

## Introduction

This shell script is to be used as a CGI with
 [thttpd](http://www.acme.com/software/thttpd/) for automatically
relaunching php-fpm whenever [Nginx](http://nginx.org) encounters a
502. This gives you piece of mind when you're developing a site and
getting the hang of php-fpm is proving somewhat challenging.

## Further elaboration

A simple README is hardly the place of describing such an involved
process. Bear in mind that we're exploring more or less
[obscure](http://wiki.nginx.org/HttpCoreModule#post_action) Nginx
directives and using thttpd in a rather strange way.

In a sense we're using
[`post_action`](http://wiki.nginx.org/HttpCoreModule#post_action) to
implement a [webhook](http://www.webhooks.org) that **notifies**
thttpd which in turn re-launches php-fpm.

The script also can launch php-fpm if it's not running. Hence it works
in both cases:

 1. php-fpm is stuck in a long, too long computation and it's better
    to put it out of it's misery and relaunch it so that we have our
    site back. Just catch the first 502 that shows up and fire up the
    relaunch.
    
 2. php-fpm it's not running, hence we'll get a 502 at the outset.
 
 This is in very broad strokes how the thing operates.
 
 There are some security measures in place:
 
  1. The script makes use of
     [`super`](http://www.ucolick.org/~will/RUE/super/README)
     providing a setuid root wrapper so that only the user that
     operates the php-fpm pool(s) in question can do the relauching.
     No **root** owned file or process was used on the making of this
     setup.
     
  2. It uses an internal redirect and an
     [`internal`](http://wiki.nginx.org/HttpCoreModule#internal) Nginx
     location.
     
  3. thttpd is bound to 127.0.0.1 (or ::1 if you prefer IPv6) and runs
     in an unprivileged port that should be blocked at the firewall
     level. In this case `8888`.
     
     You can get the thttpd config on another github [repo](https://github.com/perusio/thttpd-config).  
 
## TODO
 
 1. Make a blog post describing the all process and configuration and
    detail.
 
  2. Make the script talk with
     [RRDtool](http://oss.oetiker.ch/rrdtool/) in order to have
     **nice** graphs showing the distribution of 502s on a time
     series.
