This document is to record my development of this site, as a learning journal as well as a way to replicate the process if I am to switch a machine. 🏃 🏃 :running: :running:

# Site information

[GitHub Repository](https://github.com/foxai-io/foxai.io)  
[Site URL](https://foxai.io)


## Additional information

Site is running on `main` branch,  
Site's **publishing source** is [docs](./docs/) folder,  
**404 Page** location: [404.html](./docs/404.html)

# Environment

**GitHub Pages + Jekyll**

`Jekyll` is a `Ruby Gem` that can be installed on most systems. [ref](https://jekyllrb.com/docs/installation/)

Requirements for installation of `Jekyll`:  
- Ruby version 2.5.0 or higher, including all development headers (check your Ruby version using ruby -v)
- RubyGems (check your Gems version using gem -v)
- GCC and Make (check versions using gcc -v,g++ -v, and make -v)


## Ruby

MacOS comes with a default `Ruby`, which is not recommended for dev work.  
Default `Ruby` version:
```
2.6.10p210 (2022-04-12 revision 67958)
```

To develop `Jekyll` site, we can use `chruby` to install and manage a new `Ruby` on dev machine.

My command to install `chruby`:
```shell
$ brew update
$ brew install chruby ruby-install xz
$ ruby-install --latest ruby
```

Add those 3 lines to `~/.zshrc` file:
```
source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh
source $(brew --prefix)/opt/chruby/share/chruby/auto.sh
chruby ruby-3.1.3
```

The current `Ruby` version:

```
$ ruby --version
ruby 3.1.3p185 (2022-11-24 revision 1a6b16756e) [arm64-darwin22]
```


## Gem, or RubyGems

`RubyGems` is a package manager for Ruby. 


## bundle

`bundle` is a Ruby Dependency Management.

# Learning Resources

[GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages)  
[Jekyll](https://jekyllrb.com/docs/github-pages/)

[Markdown syntax](https://www.markdownguide.org/basic-syntax/)  
[Markdown extended syntax](https://www.markdownguide.org/extended-syntax)  
[Markdown syntax 2](https://www.jetbrains.com/help/hub/Markdown-Syntax.html)

[emojis in markdown](https://gist.github.com/rxaviers/7360908)

# Useful commands

**Run site locally**

```shell
bundle exec jekyll serve
```

# FAQ

1. Do I have to learn Ruby language to use Jekyll ?

    No, you don't have to learn Ruby to use Jekyll effectively. Jekyll is built with Ruby, but as an end user, you can use Jekyll without deep knowledge of the Ruby language.

    Jekyll uses a combination of Markdown, HTML, CSS, and Liquid (a simple templating language) to generate static websites. To work with Jekyll, you will mainly need to understand the following:

    Markdown: Markdown is a lightweight markup language that allows you to write content using simple text formatting. Jekyll uses Markdown to convert your content into HTML for your site.

    HTML and CSS: Basic knowledge of HTML and CSS will help you customize the appearance and layout of your site.

    Liquid: Liquid is a templating language used by Jekyll for processing and rendering templates. You'll use Liquid tags and filters to inject dynamic content, like posts and pages, into your static HTML templates.

    Jekyll directory structure: Understanding the Jekyll directory structure, such as the roles of the _config.yml, _layouts, _includes, _posts, and _pages directories, will help you manage your site efficiently.

    Front matter: Front matter is a YAML-formatted block of metadata at the beginning of a file in Jekyll. You'll use front matter to define custom variables, layouts, and other settings for your content.

    While learning Ruby can give you a deeper understanding of Jekyll's inner workings and allow you to create custom plugins, it's not necessary for most users to build and manage a Jekyll-based site. By focusing on the aspects mentioned above, you can effectively create, customize, and maintain your Jekyll site without having to learn Ruby.

2. 