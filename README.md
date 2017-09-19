# cf-redirect

Small app to redirect traffic from one domain to another.


## Usage

In `manifest.yml`, change `TARGET_DOMAIN` to the domain you want to redirect _to_. Change `host` to the hostname you want to redirect _from_.


    $ cf push -f manifest.yml

That's it!


## Conventions

Instead of forking this repo to simply have a redirect to your app, you can copy
these files to a `redirects/<redirect-from-domain>` dir in the app you're
redirecting _to_. This way all the redirects to your app are stored in a single
repo.

Now you can deploy like so.

    $ cf push -f redirects/redirect-from-domain.apps.cloud.gov/manifest.yml


## A note on permanence

The redirect uses a temporary 302 redirect. In some cases you might want to use
a [permanent
redirect](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.2),
for example, when the application has been permanently assigned a new URI. Note
that without any caching directive, browsers may cache this response indefinitely
which makes it very difficult to re-use the old URI for anything else. If you
choose to use the 301 redirect, we suggest you include cache control to prevent
the 301 being cached indefinitely.

```
# in nginx.conf
expires 3600;
return 301 $scheme://$target_domain$request_uri;
```

## Use cases

### Serve a notice

Maybe there are a few files you want to serve, rather than redirect. Just
include them in your directory. The nginx config will look for html files first,
then fallback to the redirect. This can be used to serve an index.html that
might have a notice about the redirect. You can use a meta-refresh to redirect
the user after a few seconds.

Include this line in your index.html `<head>` to redirect the user after 10
seconds.

    <meta http-equiv="refresh" content="10;url=https://your-target-domain.example.com">
