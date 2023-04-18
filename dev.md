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

2. What's the difference when running locally `jekyll serve` vs `bundle exec jekyll serve` ?

    When you run `jekyll serve` and `bundle exec jekyll serve`, you might get different results due to differences in the environment and gem versions being used. Here's an explanation of the two commands:

    `jekyll serve`: This command runs the Jekyll site using the global installation of Jekyll and its dependencies on your system. It uses the versions of Jekyll and other gems installed in your global Ruby environment. If you have multiple versions of Jekyll or its dependencies installed globally, this command might not use the exact versions specified in your Gemfile.lock.

    `bundle exec jekyll serve`: This command runs Jekyll within the context of your project's Gemfile. It ensures that the specific versions of Jekyll and its dependencies specified in your Gemfile.lock are used. This is the recommended way to run Jekyll, as it guarantees that your project runs with the same gem versions across different environments.

    If you're experiencing different results when using the two commands, it's likely due to differences in the gem versions being used. To ensure consistency across different environments and setups, it's recommended to use `bundle exec jekyll serve` to run your Jekyll site locally.


3. does every blog file must follow naming convention: `year-month-day-title.markdown` ?

    While using Jekyll, it is strongly recommended to follow the naming convention year-month-day-title.markdown (or .md instead of .markdown) for your blog post files. This convention helps Jekyll to automatically recognize and parse the date and title of the blog post.

    However, you can bypass this convention by providing the necessary front matter (metadata) in the file itself. If you choose not to follow the convention, you will need to provide the title and date in the front matter of your blog post file, like this:

    ```
    ---
    layout: post
    title: "Your Blog Post Title"
    date: 2023-03-14
    ---
    ```

    By providing the title and date in the front matter, Jekyll can still process the file correctly. However, following the naming convention makes it easier to manage and organize your blog posts, especially when you have a large number of posts.


4. Differences between code snippets in jekyll and markdown

    You can definitely use the standard Markdown code snippets with Jekyll using the triple backticks (```) syntax. The {% highlight %} syntax is provided by Jekyll for additional customization and functionality, like adding line numbers or using different styles.

    The primary difference between the two is that the {% highlight %} syntax uses Jekyll's built-in syntax highlighting library, called Rouge, which provides more advanced features and customization options. The standard Markdown code block with triple backticks (```) will use the default syntax highlighting provided by the Markdown parser.

    If you don't need the extra features provided by Rouge, you can stick to the standard Markdown code blocks using triple backticks (```). However, if you need advanced highlighting features, like line numbers, specific language support, or custom styles, you can use the {% highlight %} syntax.

    For most use cases, the standard Markdown code blocks are sufficient, and you can use the triple backticks (```) syntax for code snippets in your Jekyll blog.

5. does `Minimal Mistakes theme` supports navigating through tags and categories?
   
    Yes, the Minimal Mistakes theme supports navigating through tags and categories. You can create tag and category archive pages that display a list of posts with the corresponding tag or category.

    To create tag and category archive pages, follow these steps:

    1. In your Jekyll site's root directory, create two new folders: `_pages` (if it doesn't already exist) and _data.

    2. Inside the _data folder, create a file called navigation.yml. This file will define your site's navigation menu. Add the following content to create menu items for the tag and category archives:

        ```yaml
        main:
        - title: "Categories"
        url: /categories/
        - title: "Tags"
        url: /tags/
        ```

    3. Inside the `_pages` folder, create two new files: `categories.md` and `tags.md`

    4. Add the following content to the `categories.md` file:

        ```
        ---
        title: Categories
        layout: archive-taxonomies # categories
        type: categories
        # permalink: /categories/
        menu:
            main:
                weight: 4
        ---
        ```

    5. Add the following content to the `tags.md` file:

        ```
        ---
        title: Tags
        layout: archive-taxonomies # tags
        type: tags
        # permalink: /tags/
        menu:
            main:
                weight: 3
        ---
        ```

    6. In your site's `_config.yml` file, make sure you have the following lines:

        ```
        # Tags and categories
        category_archive_path: "categories"
        tag_archive_path: "tags"
        ```

    7. Save your changes and restart your Jekyll server using `bundle exec jekyll serve`. You should now have navigation menu items for tags and categories, and clicking on them will take you to the corresponding archive pages.

