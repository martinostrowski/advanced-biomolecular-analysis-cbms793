---
title: Setup and Independent Work
---

Over the last few years the markdown-formatted tutorials for this course converged on the format and philosophy of the [Carpentries](https://software-carpentry.org). As of 2018 we have merged our Advanced Biomolecular Analysis tutorials into the Carpentries lesson templates and we acknowledge the many contributors that have refined and curated the  templates that render our markdown tutorials as lesson web pages. In recognition of the influence that Software Carpentry has contributed to improving the **core skills** component of this course a introduction to GitHub has been incorporated into the practical for week 8.


The format of these lesson templates should become familiar enough that you will be comfortable, and capable, of working through some of the material outside of class.

Several SWCarpentry episodes covering the basics of bash and Github are embedded in the class lessons and a very small number of episodes are set as homework.

Insert table of homework recommended episodes

##
##
##




Requirements:
* A GitHub account
* A working Python 3.4+ environment to run the lesson initialization script
* (Optional) A local install of [Jekyll](https://jekyllrb.com/) (version 3.2 or higher) which will require the Ruby language to be installed.

## logging into your account on the server

We will assume that your user ID is `tyrion`

1.  We'll use the [GitHub's importer][importer] to make a copy of this repo in your own GitHub account.
(Note: This is like a GitHub Fork, but not connected to the upstream changes)

2.  **Put the URL of [the styles repository][styles]** (https://github.com/carpentries/styles) in the "Your
    old repository’s clone URL" box.
    Do *not* use the URL of this repository,
    as that will bring in a lot of example files you don't actually want.

3.  Select the owner for your new repository.
    In our example this is `timtomch`,
    but it may instead be an organization you belong to.

4.  Choose a name for your lesson repository.
    In our example, this is `data-cleanup`.

5.  Make sure the repository is public.

6.  At this point, you should have a page like this:

    ![]({{ page.root }}/fig/using-github-import.png)

    You can now click "Begin Import".
    When the process is done,
    you can click "Continue to repository" to visit your newly-created repository.

    Through the Github interface you can begin to edit and

7.  If you want to work on the lesson from your local machine, you can
    now clone your newly-created repository to your computer:

    ~~~
    $ git clone -b gh-pages https://github.com/timtomch/data-cleanup.git
    ~~~
    {: .language-bash}

    Note that the URL for your lesson will have your username and chosen repository name.


14. Commit your changes *and the HTML pages in the root directory of
    your lesson repository* and push to the `gh-pages` branch of your
    repository:

    ~~~
    $ cd data-cleanup
    $ git add changed-file.md changed-file.html
    $ git commit -m "Explanatory message"
    $ git push origin gh-pages
    ~~~
    {: .language-bash}

15. [Tell us][email] where your lesson is so that we can add it to
    the appropriate index page(s).

**Note:**

1.  SSH cloning (rather than the HTTPS cloning used above)
    will also work for those who have set up SSH keys with GitHub.

2.  Once a lesson has been created, please submit changes
    for review as pull requests that contain *only the modified Markdown files*.
    Do *not* submit generated HTML.

3.  Some people have had intermittent errors during the import process,
    possibly because of the network timing out.
    If you experience a problem, please re-try;
    if the problem persists,
    please [get in touch][email].


## Setup Instructions for a specific existing lesson

1.  Installation instructions for core lessons are included in
    the [workshop template's home page][workshop-repo],
    so that they are all in one place.
    The `setup.md` files of core lessons link to
    the appropriate sections of the [workshop template page][workshop-repo].

2.  Other lessons' `setup.md` include full installation instructions organized by OS
    (following the model of the workshop template home page).

## (Optional) Jekyll Setup for Lesson Development

If you want to set up Jekyll
so that you can preview changes on your own machine before pushing them to GitHub,
you must install the software described below.
(Note: Julian Thilo has written instructions for
[installing Jekyll on Windows][jekyll-windows].)

1.  **Ruby**.
    This is included with Linux and Mac OS X;
    the simplest option on Windows is to use [RubyInstaller][ruby-installer].
    Make sure Ruby is upto date otherwise jekyll may fail.
    You can test your installation by running `ruby --version`.
    For more information,
    see [the Ruby installation guidelines][ruby-install-guide].

2.  **[RubyGems][rubygems]**
    (the package manager for Ruby).
    You can test your installation by running `gem --version`.

3.  **[Jekyll][jekyll]**.
    You can install this by running `gem install jekyll`.
    On macOS, a user does not have a permission to write to `/Library/Ruby/Gems/`.
    Run `gem install jekyll --user-install` instead.

4.  **R Packages**.
    We use [knitr][cran-knitr], [stringr][cran-stringr], and [checkpoint][cran-checkpoint]
    to format lessons written in R Markdown,
    so you will need to install these to build R lessons
    (and this example lesson). The best way to install these packages is to open an R terminal and type:

    ```
    > install.packages('knitr', repos = 'https://', dependencies = TRUE)
    > install.packages('stringr', repos = 'https://cran.rstudio.com', dependencies = TRUE)
    > install.packages('checkpoint', repos = 'https://cran.rstudio.com', dependencies = TRUE)
    > install.packages('ggplot2', repos = 'https://cran.rstudio.com', dependencies = TRUE)
    ```

If you want to run `bin/lesson_check.py` (which is invoked by `make lesson-check`)
you will need Jekyll (so that you have its Markdown parser, which is called Kramdown)
and the [PyYAML][pyyaml] module for Python 3.

{% include links.md %}
