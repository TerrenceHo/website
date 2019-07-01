+++
title = "Writing My Blog Posts with Org"
author = ["Terrence Ho"]
date = 2019-06-30T18:15:00-07:00
tags = ["emacs", "hugo", "org"]
draft = false
+++

Hugo is an awesome blog generator. However, it's primary content mode is
Markdown, and writing in `org-mode` feels much better than writing in Markdown.
`org-mode` can automatically convert to Markdown, but then I have to edit the
generated Markdown file to add YAML front-matter that Hugo consumes, which is
annoying.

Enter `[[https://ox-hugo.scripter.co/][ox-hugo]]`. It allows you to maintain and generate Hugo-compatible Markdown
in `org-mode`, and automatically generates the necessary metadata Hugo requires.
However, like all good things in life, you have to work a little to configure it
to your liking. (What's an extension without spending a couple hours of life
figuring out exactly how to make it conform to your workflow in the pursuit of
"optimization"). This is how I configure `ox-hugo` to write all my blog posts
(including this blog post!).


## Setup {#setup}


### Installation {#installation}

Install the package [`ox-hugo`](https://ox-hugo.scripter.co/). If you use `use-package` to manage your packages,
the following snippet should do the trick.

```emacs-lisp
(use-package ox-hugo
  :ensure t            ;Auto-install the package from Melpa (optional)
  :after ox)
```

I would recommend going through `ox-hugo`'s documentation for maximum
effectiveness.


### Base Directory {#base-directory}

In a Hugo website, you might have a directory structure that looks the following
layout. From now on, we will refer to this root directory as `HUGO_ROOT_DIR`.

```text
.
├── config.toml
├── content
│   └── post
│       ├── post1.md
│       ├── new-post.md
├── content_org
│   └── post.org
├── layouts
│   └── partials
├── public
│   ├── content
│   │   └── post
```

Note that I have only shown the necessary parts, your actual directory structure
will have many other files. `org` doesn't care about Hugo's directory structure,
so I added a new directory called `content-org`. This is where I store all my
`org` files for the blogs. We only need one `org` file, named post.org.


### Global Properties {#global-properties}

Open up `$(HUGO_ROOT_DIR)/content_org/post.org` in Emacs. At the very top,
insert the following (replace `<your-name=here>` with your name).

```text
#+AUTHOR: <your-name-here>
#+HUGO_BASE_DIR: ../
```

This sets the author to be your name, and more importantly sets where
`$(HUGO_ROOT_DIR)` is located at. In this case, the root directory is simply the
parent directory. If your root directory is elsewhere, adjust `HUGO_BASE_DIR`
appropriately. Setting `HUGO_BASE_DIR` is necessary for `ox-hugo` to set where
it will be exporting files.

Next, create a headline called Posts. Underneath, add the following properties.

```text
* Posts
:PROPERTIES:
:EXPORT_HUGO_SECTION: post
:EXPORT_HUGO_CUSTOM_FRONT_MATTER:
:END:
```

`EXPORT_HUGO_SECTION` sets the directory under content to generate blog posts.
Here we've set the export directory to be post, which corresponds to
`$(HUGO_ROOT_DIR)/content/post`. Under `EXPORT_HUGO_CUSTOM_FRONT_MATTER`, you
can set other YAML matter that you want to apply to all posts, such as
configuring BlackFriday options (Hugo's markdown processor).


### Posts per Subtree {#posts-per-subtree}

We've now setup the basics of `ox-hugo`. Next, we will look at how to write a
blog post.

Most blog posts intuitively are written as one file per post. However, `ox-hugo`
allows us to write all of our blog posts in one single file, and export to many
different files. Thus, all of my blog posts live in
`$(HUGO_ROOT_DIR)/content_org/post.org`, and eack blog post is exported as many
different files under `$(HUGO_ROOT_DIR)/content/post/`.

The motivation behind this lies with how `org-mode` lists work. In a `org-mode`
list we can set TODO items, and manipulate the entries using `org-mode`
shortcuts. Then in a sense, we can think of a blog post as a TODO item, and
everything underneath the TODO item is the content of the blog post itself. We
can even mark a blog post as DONE to "release" it. Below is an example of the
collapsed view of the Posts tree. There are three blog posts, with one marked
as DONE, and the rest TODO or IN-PROGRESS. (To collapse a subtree in `org-mode`,
press `S-TAB`. You can then press `TAB` on the POSTS headline to see
sub-headlines underneath it.)

```text
* Posts
** TODO Go Essentials Part 1                               :go:go_essentials:⤵
** IN-PROGRESS Microsoft Internship                    :microsoft:internship:⤵
** DONE How I started Using Emacs                                 :emcas:org:⤵
```

Thus in a nutshell, we have the Posts top level headline, then TODO items as
secondary headlines. Each TODO item is a blog post, either finished or
unfinished. Any content underneath the secondary headlines is part of the actual
blog post. In the snippet above, I have collapsed the content, leaving only a ⤵
to indicate there is data underneath the headline. All your blog posts live
under the Posts subtree. So anytime you want to edit a blog post, you open
`$(HUGO_ROOT_DIR)/content_org/post.org` and edit the appropriate subtree.


### Blog Properties {#blog-properties}

We also want to be able to add some properties to an individual blog post. We
can do so like this:

```text
* Posts
** TODO Next Blog Post
:PROPERTIES:
:EXPORT_FILE_NAME: how-i-started-using-emacs
:END:
```

This sets the file name to be exported to. There are other properties you can
set, all detailed in the `ox-hugo` docs. All the properties are translated to
Hugo YAML front-matter. In this fashion, `ox-hugo` allows you to set file
specific YAML front-matter in an `org-mode`-like way.


### Tags {#tags}

`ox-hugo` can also generate the tags in the YAML front-matter. You can set the
tags in each blog headline such as the following. This saves you the trouble of
having to write the tabs into the `PROPERTIES` themselves, since that is
slightly more troublesome. Additionally, this makes use of `org-mode` tags,
allowing `org-mode` to format your tags nicely.

```text
* Posts
** TODO Next Blog Post                                           :tag1:tag2:
```

These tags allow you to group related posts. Hugo can read these tags and link
all blog posts with the same tags, so this is a very neat feature.


### Publishing a Blog Post {#publishing-a-blog-post}

To publish a blog post, set the blog post from TODO to DONE. (For those new to
`org-mode`, you can do this by pressing `C-c C-t`. This should bring up a side
window that allows you to set the status of the TODO item.) If you have
configured your `org-mode` to output a date at which when an item was finished,
`ox-hugo` will automatically set that date at the publishing date, which is much
more convenient than setting the date manually. An example is shown in the
section below.


### Putting it all together {#putting-it-all-together}

Here is an example of the Posts subtree along with two blog posts, one finished
and one unfinished. The finished blog post has a CLOSED timestamp indicating the
date at which it was finsihed. The unfinished blog post is considered a draft to
Hugo, and so Hugo won't compile that blog post unless Hugo is set to compile the
drafts as well.

```text
#+AUTHOR: Terrence Ho
#+HUGO_BASE_DIR: ../
* Posts
** TODO Next Blog Post
:PROPERTIES:
:EXPORT_FILE_NAME: next-blog-post-name
:END:

Introductory text... Ipsum Lorem

*** First Headline

More text... Ipsum Lorem

**** First Sub-Headline

Even more text... Ipsum Lorem

** DONE Other Finished Blog Post
   CLOSED: [2019-06-17 Mon 16:53]
:PROPERTIES:
:EXPORT_FILE_NAME: other-finished-blog-post
:END:

This blog post was finished one Monday, June 17th, 2019.
```


## Converting to Markdown {#converting-to-markdown}

Lastly, once all this is set up, we will want to convert out posts to Markdown
files. Luckily, `ox-hugo` also builds in an exporter to complement `org-mode`'s
exporting capabilities.

To export a subtree as a post, press `C-c C-e`. This should bring up a side menu
detailing all the export methods available to you. Press `H H` to export the
subtree as a file (altogether, press `C-c C-e H H`). `ox-hugo` determines where
to export based on the previous settings. You can also export all subtrees at
once with `C-c C-e H A`. If you don't use the option to write your blog posts as
subtrees, but rather one blog post per `org` file, then use `C-c C-e H h`, which
exports everything in the file to Markdown. Personally, I structure my blog
posts in the subtree style, because of how well it fits the `org-mode` workflow,
but YMMV.

The exporting side view looks like the following:

<a id="orgcc6d0ea"></a>

{{< figure src="/ox-hugo/org-mode-exporting-view.png" caption="Figure 1: `org-mode` exporting view" >}}


## Conclusion. {#conclusion-dot}

I hope you've enjoyed learning how to write blog posts with `org-mode`. I've
honestly never enjoyed writing Markdown, and so this gives me a convenient and
fun way to write my future blog posts. Stay tuned for more similiar content!
