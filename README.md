# README.md

I'm using

```
$ ~/bin/hugo env
hugo v0.125.0-a32400b5f4e704daf7de19f44584baf77a4501ab+extended darwin/amd64 BuildDate=2024-04-16T15:04:41Z VendorInfo=gohugoio
GOOS="darwin"
GOARCH="amd64"
GOVERSION="go1.22.2"
github.com/sass/libsass="3.6.5"
github.com/webmproject/libwebp="v1.3.2"
```

on a reasonably up to date Mac.

I have a whole big pile of pages that are the same, except that they
have links and names and _etc_ that are unique-per-page.

I would like to be able to have many content pages, each of which sets
some page specific parameters in its front matter and then pulls in a
big shared Markdown file that references those parameters.

The big shared Markdown file is complex enough that it's better to use
Markdown than to try to manage the corresponding HTML by hand.  I
think that this means that I can't solve my problem with a partial or
template.  In particular, the big shared Markdown file includes
shortcodes.

I almost have a solution via RenderShortcodes (see below), but it
doesn't quite work, the included page can't access parameters from the
including page.

What I have in mind is something like dynamic scoping in a programming
language, searching back up through the call stack at run time to find
the value of a variable.

My immediate need would be satisfied if I could access the calling
page's parameters.  Alternatively, being able to provide a map of
values through the shortcode to RenderShortcodes could also work.

This is a little test case that has the structure that I want.  It
defines an "include" shortcode that invokes RenderShortcodes and also
outputs the calling and called pages' parameters.  There are two
pages, `p1.md` is the "includer" (it generates output) and `p2.md` is
the "includee".

This demo site started life as the test data for the
`TestRenderShortcodesBasic` test for the RenderShortcodes feature.  I
deleted some bits and added others to illustrate my point.

## Running the demo/test case

Run `hugo` in the top level directory.

You should see an error like (ellipses are bits from my directory
layout):

```
ERROR Param "p1" not found: [...]/content/p2.md:9:15"
```

If you read through the resulting `public/p1/index.html` you can see
that the `p1` parameter is available in `p1.md` but is not in `p2.md`.

## A related, minor problem

I can't figure out how to avoid having `p2.md` rendered as a page of
it's own.  I've tried:

- Adding it to `ignoreFiles` (or the `module` equiv.) keeps the page
  from being generated, but also seems to keep it from being included
  in `p1.md`.
- I've put it in `/assets` and tried accessing it via `Resources`, but
  that doesn't return a `Page` so `RenderShortcodes` isn't available.

Thoughts?

Interesting links:

- [RenderShortcodes doc](https://gohugo.io/methods/page/rendershortcodes/)
- [Release notes for v0.117.0](https://github.com/gohugoio/hugo/releases/tag/v0.117.0)
- [Commit that added RenderShortcodes](https://github.com/gohugoio/hugo/commit/ade7ec818798c0e5507d4fb6cc5b4396262a85d6)
