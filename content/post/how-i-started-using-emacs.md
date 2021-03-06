+++
title = "How I Started Using Emacs"
author = ["Terrence Ho"]
date = 2019-06-17T16:53:00-07:00
tags = ["emacs", "org"]
draft = false
+++

This is how I started using GNU Emacs with no prior experience. This is intended
to help those who want to start using Emacs as well. I'll start from a base
setup, show you where I looked around for help, show you how I found myself
using my current configuration. While this post goes through the steps to get
started with Emacs, I definitely omitted the steps that didn't help or the parts
where I floundered around not knowing what I was doing.

My primary motivation for using Emacs was its `org-mode`, Emac's organizational
software. I heard amazing things about the spread sheets, the TODO lists,
agendas, calendars, note-taking, paper writing, life dominating capabilities, so
I had to try it out. Even if it meant entering the Church of Emacs for a short
while.


## Installing Emacs {#installing-emacs}

Even though Emacs can be used through a terminal emulator, it's best to use GUI
Emacs (unlike GUI Vim, which offers no other benefits compared to Terminal VIm)
because in GUI Emacs, you can display images, view PDF documents, interact with
the system clipboard natively out the of the box, rich text formatting options,
and more. Therefore, always use GUI Emacs, it offers so much more capabilities.

On Mac OS, you can just run `brew cask install emacs` and it should
automatically download the latest stable version. For reference, at the time of
this post I was using Emacs 26.2.

For those that want to build from source or install in a different manner,
consult [this page](https://www.emacswiki.org/emacs/EmacsForMacOS).


## To be or not to be `evil` {#to-be-or-not-to-be-evil}

For the unaware, `evil` mode is a popular Emacs Vi mode emulation layer (I'm
sure the name has no other connotation at all). I've used vim extensively in the
past, but I wasn't sure if I wanted to try and use `evil` in Emacs straight
away, since I would have no idea about shortcut conflicts, or what parts Emacs
does better than vim. Therefore, I decided to try using plain Emacs and start
from there.


## Plain Emacs {#plain-emacs}

I gave a genuine try to use pure Emacs, by going through the tutorial (`C-h t`),
however, I could not get used to it. I was too used to using HJKL to navigate
between lines. The amount of times I pressed `i` to enter Insert mode and then
forget I wasn't in vim was uncountable; same with pressing `ESC` to leave Insert
mode and then realizing I wasn't in Insert mode. I honestly cannot fathom for my
life how using `C-n` and `C-p` to move up and down lines can be part of any sane
workflow. In fact, I found that a lot of 1 key press actions in Vim's insert
mode require multiple key presses in Emacs. Vim's editing keybindings were just
too crucial to me to immediately switch.

Because of this, I bit the bullet and started researching the best way to
install `evil` into Emacs. Enter the world of Emacs configurations.


## Emacs Configuration {#emacs-configuration}

To the best of my knowledge, using the `use-package` plugin is the best way to
configure Emacs and various package options. According to the [README](https://github.com/jwiegley/use-package),
`use-package` allows you to "isolate package configuration in your .emacs file
in a way that is both performance-oriented and tidy". It seems to be widely used
and easy to install, so that's the package manager I settled on. To install
`use-package`, open the .emacs file in your home directory and paste this in.

```emacs-lisp
;; Configure package.el to include MELPA.

(require 'package)
(add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
(package-initialize)
```

This code (written in ELisp), simply requires `package.el`, Emacs' built in
package manager. It adds the package archive Melpa to the list of repositories,
an online repository store of Emacs packages. Melpa seems to be the most
complete and almost every project is one Melpa, so I only use Melpa. It's
possible to add more if needed, of course.

Now that we have initialized the default package manager, we'll use that to
install `use-package`.

```emacs-lisp
;; Ensure that use-package is installed.
;;
;; If use-package isn't already installed, it's extremely likely that this is a
;; fresh installation! So we'll want to update the package repository and
;; install use-package before loading the literate configuration.
(when (not (package-installed-p 'use-package))
  (package-refresh-contents)
  (package-install 'use-package))
```

Now whenever we want to install some other package, we can use `use-package` to
install that package.


## Install `evil` {#install-evil}

Let's use `use-package` to install `evil` get join the dark side of Emacs. Paste
this into your .emacs file.

```emacs-lisp
(use-package evil
  :config
  (evil-mode 1))
```

`use-package` searches the package archives for something called `evil`, sets
any configurations, and then activates evil mode. Now you can restart Emacs, and
you should automatically be in `evil` mode. Try typing some text and editing it
using the key bindings.

If you open up your .emacs file again, you can see that emacs generated some
extra code that looks something like this:

```emacs-lisp
(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(package-selected-packages
   (quote use-package ...)
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 )
```

This is automatically generated and you shouldn't worry about it. If you
accidentally ignore the warnings not to mess with it as I did, you can simply
delete all of it, restart Emacs, and it should be automatically regenerated.

Now that we have `evil` mode set up, let's turn to `org` mode.


## `org` mode {#org-mode}

Now as stated in the beginning, my primary goal was to be able to get started
with `org` mode, for all its organizational goodies. `org` comes preinstalled on
later versions, Emacs but if necessary, you can also just install `org` using
`use-package`. I spent a lot of time going through tutorials and various other
resouces, and a lot of them are quite good, so I'm going to list a couple
resources.

-   [Org-mode Guide](https://orgmode.org/guide/)
-   [Worg](https://orgmode.org/worg/)
-   [Org-mode Manual](https://orgmode.org/manual/index.html)

I suggest going through the Org-mode guide the most, it has the best concise
documentation of the most commonly used features.


## Configurations with `org-babel` {#configurations-with-org-babel}

One of the cooler things that `org` can do is write code blocks inline and
execute them. Take the Hello World function defined in Python below.

<a id="code-snippet--hello-world"></a>
```python
import random
print("Hello, this is a random number:", random.random())
```

In an `org`-file, it would look like this (notice that we name the code block):

```text
#+NAME: hello-world
#+BEGIN_SRC python :results output
import random
print("Hello, this is a random number:", random.random())
#+END_SRC
```

We can then call that code block by executing it. With your cursor in the
code-block, press `C-c C-c`. The output should be as follows.

```text
#+RESULTS: hello-world
: ('Hello, this is a random number:', 0.35818895069511747)
```

So you can execute arbitrary code in an `org` mode file. This is obviously very
cool and also a little dangerous. Always make sure you know what code you're
executing, because this allows arbitary code execution on your system (this is
the Emacs equivalent of falling for a phishing attack).

Even more important is that this implies that you can write Emacs Lisp in your
`org` file and then execute it. So why not write your configuration file in an
`org` file, and then execute it? This allows you to document your configuration
in the same file as your configuration file. You could theoretically do the same
for any code file using comments, but `org` mode is just so much more featured
that it just feels better to write in `org`.

In fact, my current configuration file is actually just an `org` file. My .emacs
is very minimal. Aside from the code to install `use-package`, I only have one
more manually written line:

```emacs-lisp
(org-babel-load-file "~/.emacs.d/configuration.org")
```

`org-babel-load-file` parses an org-file, extracts all the code segments, and
places it into it's own dedicated file that is then executed. so my
"~/.emacs.d/configuration.el" is generated by `org-babel` from my
"~/.emacs.d/configuration.org" file. You can do this as well. IMO, writing in
`org` just feels much better than writing pure Emacs Lisp.

For the remainder of this article, I'm going to install a couple things by
writing the installation/configuration code in `org`. You can copy this Emacs
Lisp code into your own configuration org file. In my configuration `org` file,
I always try to comment the code above it, so I know what I did reading it years
later.

For reference, my entire .emacs file:

```emacs-lisp
;; ===== Custom load definition.
;; ===== Load from configuration org file

;; Configure package.el to include MELPA.
(require 'package)
(add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
(package-initialize)

;; Ensure that use-package is installed.
;;
;; If use-package isn't already installed, it's extremely likely that this is a
;; fresh installation! So we'll want to update the package repository and
;; install use-package before loading the literate configuration.
(when (not (package-installed-p 'use-package))
  (package-refresh-contents)
  (package-install 'use-package))

(org-babel-load-file "~/.emacs.d/configuration.org")
```


## Theme {#theme}

The theme of your editor could be even more important than the actual editor
itself. After all, you gotta make your coworkers jealous when they see how cool
your system looks.

Personally, I'm a fan of the [Doom City Lights Theme](https://github.com/hlissner/emacs-doom-themes).

<a id="org640403f"></a>

{{< figure src="https://github.com/hlissner/emacs-doom-themes/raw/screenshots/doom-citylights.png" caption="Figure 1: Doom City Lights, courtesy of [hlissner](https://github.com/hlissner/emacs-doom-themes)" >}}

To install this, paste the following code into your configuration.org file.

```text
#+BEGIN_SRC emacs-lisp
(use-package doom-themes
  :init
  (load-theme 'doom-city-lights t)
  :config
  (setq doom-themes-enable-bold nil    ; if nil, bold is universally disabled
        doom-themes-enable-italic nil) ; if nil, italics is universally disabled
  (doom-themes-org-config)
  )
#+END_SRC
```


## Sane Emacs Configurations {#sane-emacs-configurations}

A lot of the default configurations for Emacs are a little outdated to say
the least. [sensible-defaults.el](https://github.com/hrs/sensible-defaults.el) is a collection of Emacs configurations that are
relatively non-intrusive and useful, including:

-   Ensuring that files end with newlines,
-   Always enabling syntax highlighting,
-   Increasing the garbage collection threshold,
-   Defaulting line-length to 80 characters,
-   Creating parent directories after saving a deeply nested file,
-   Making dired file sizes human-readable

and more. Because they are all exported as several ELisp functions, you can pick
the ones that you want to use, or simply include all of them by running

```text
#+BEGIN_SRC emacs-lisp
(load "~/code/src/github.com/hrs/sensible-defaults.el/sensible-defaults.el")
(sensible-defaults/use-all-settings)
(sensible-defaults/use-all-keybindings)
#+END_SRC
```

That's it. I find some of the configurations non-obvious, so I pick and choose
the ones I want, and add a few of my own custom configurations, but
sensible-defaults.el is still a good option to look at.


## Personal Information {#personal-information}

This is totally unnecessary, but if you want Emacs to know who you are, then
set:

```text
#+BEGIN_SRC emacs-lisp
 (setq user-full-name "Terrence Ho"
       user-mail-address "terrenceho.books@gmail.com")
 #+END_SRC
#+END_SRC
```


## `org` TODOs {#org-todos}

One of the most powerful features of `org` is it's TODO management. Any line
starting with several bullet points (Ex: **\***) is a headline. Headlines can
become TODO items if the first word on the headline is TODO or DONE.

Let's assume that we want to set more than just TODO/DONE states. I have
multiple states set up, such as TODO, IN-PROGRESS, WAITING, DONE, and CANCELLED.
Note that we also want the CANCELLED state to be equivalent to a DONE state,
since if something is cancelled, we don't want it to be done in the future.
Adapt the following code according to how you want to set up your TODOs.

```text
#+BEGIN_SRC emacs-lisp
(setq org-log-done 'time)
(setq org-todo-keywords
  '((sequence "TODO(t)" "IN-PROGRESS(i)" "WAITING(w@)" "|" "DONE(d)" "CANCELLED(c@)")))
(setq org-todo-keyword-faces
      '(("TODO" :foreground "red" :weight bold)
	  ("IN-PROGRESS" :foreground "blue" :weight bold)
	  ("WAITING" :foreground "orange" :weight bold)
	  ("DONE" :foreground "forest green" :weight bold)
	  ("CANCELLED" :foreground "magenta" :weight bold)))
#+END_SRC
```

This changes the sequence of TODO states, adding the necessary states. Notice
the "@" in the CANCELLED and WAITING states: they indicate that we can add a
note explaining what an item is waiting for or why something was cancelled.

`org` by default allows you to switch states by pressing `S-LEFT` pr `S-RIGHT`.
However, when we have multiple states, we often want to switch directly to the
desired state. Add the following code to enable this option.

```text
#+BEGIN_SRC emacs-lisp
(setq org-use-fast-todo-selection t)
#+END_SRC
```

To switch TODO states, place your cursor onto the headline, then press `C-c
C-t`. This should bring up another buffer listing the possible states. Press the
key corresponding to your desired state, and your headline should automatically
change to reflect that.


## `org` ellipsis {#org-ellipsis}

When you are hovering over a headline, you can press `TAB`. This collapses all
the text underneath the headline, and replaces the text with an ellipsis (...).
I didn't like the ellipsis, so I replaced it with a "⤵".

```text
#+BEGIN_SRC emacs-lisp
(setq org-ellipsis "⤵")
(set-face-attribute 'org-ellipsis nil :foreground "##61a49e")
#+END_SRC
```


## `org` pretty bullets {#org-pretty-bullets}

The number of the bullets in your headline indicates different levels of
headlines, and they correspond to different importance levels (\* matches to
`<h1>`, \*\* matches to `<h2>`, etc). I'd prefer to look at some prettier,
indented bullets, so I replaced them with a package called `org-bullets`.

```text
#+BEGIN_SRC emacs-lisp
  (use-package org-bullets
    :init
    (add-hook 'org-mode-hook 'org-bullets-mode))
#+END_SRC
```


## `org` file converters {#org-file-converters}

`org` can export to a bunch of different file systems. I'll go over the formats
I have set up, but in the future if you need to write a paper and transform it
into PDF or HTML format, consider writing in `org` rather than LaTex (powerful
but annoying to write) or Markdown (non-standardized and Markdown tables suck).

To convert a file, press `C-c C-e`. This should being up a side panel listing a
bunch of options to convert your files. Choose which file format to convert to,
and Emacs should output that file.


### HTML/Markdown {#html-markdown}

`htmlize` is a package that helps you convert `org` files to an HTML page.

```text
#+BEGIN_SRC emacs-lisp
   (use-package htmlize)
#+END_SRC
```


### PDF/LaTex {#pdf-latex}

`org` can export to PDF thorugh LaTex if you have a Tex toolchain available on
your system automatically.


### GitHub Flavored Markdown {#github-flavored-markdown}

GitHub has it's own flavour of markdown. Well no matter, we can export to that
too. Never directly write a README.md directly again!

```text
#+BEGIN_SRC emacs-lisp
(use-package ox-gfm)
(eval-after-load "org"
  '(require 'ox-gfm nil t))
#+END_SRC
```


### Hugo {#hugo}

This blog is generated with Hugo. I can write my blog posts in `org`, export
them to hugo compatible Markdown with front-matter intact, and publish them.

```text
#+BEGIN_SRC emacs-lisp
(use-package ox-hugo
  :ensure t            ;Auto-install the package from Melpa (optional)
  :after ox)
#+END_SRC
```


## Conclusion {#conclusion}

My journey into Emacs and Org-Mode has been quite successful. To demonstrate,
I've written this blog post using `org`, and I hopefully plan on never writing
Markdown again. My current, ever updating configuration file can be seen at
[My GitHub](https://github.com/TerrenceHo/dotfiles). Check out the ".emacs.d/configuration.org" file, GitHub can render
`org` files now!

I went from writing code manually in the .emacs file and ended up writing all my
configurations in an `org` file, that holds both the source code and
documentation for my configurations. I then also went into a brief overview
about how I configure Emacs and `org` mode specifically.

Emacs is endlessly configureable. There are insanely powerful plugins such as
MaGit (git plugin), Projectile (session/project management), Helm (completion
framework), TRAMP (SSH remote file support), autocomplete and language servers,
and more. I am still figuring out all these other tools, which goes to show how
awesome Emacs really is.

I should have a blog post up soon detailing how I organize my blog posts using
`ox-hugo`. Stay tuned for that!
