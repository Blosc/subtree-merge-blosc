subtree-merge-blosc.sh
======================

Description
-----------

This is the ``subtree-merge-blosc.sh`` script.

History
-------

This script was originally written by Valentin Haenel <valentin@haenel.co> for
the `python-blosc <http://github.com/blosc/python-blosc>`_ project in Apr 2013.

After that, it was also included directly in other repositories and served to
synchronize the Blosc sources, most notably for the PyTables and bcolz
projects. While the practice of copying and pasting code across repositories is
in general frowned upon, since it becomes very difficult to propagate bugfixes,
in this case there isn't really an alternative and since the scope of the
script is so minimal this practice is feasible.  Luckily, in this case, the
script didn't have to change during its lifecycle so all versions were (almost
exactly) identical.

In January 2015 it became apparent that the script may fail under certain
circumstances, e.g. when not invoked from the root directory of a Git
repository. Also when trying to merge Blosc ``v1.5.2`` into the python-blosc
project, the script failed due to an unknown cause, potentially attributable to
a bug in the implementation or the algorithmic specification of Git's *subtree*
merge strategy. As such, it became apparent that it may be necessary to enhance
the script. So, the script was extracted from its original home to ease
integration and dissemination of improvements by providing a single,
authoritative source which can hence forth be included in other repositories.

Reasoning
---------

TODO: Write

Used in ...
===========

* python-blosc
* PyTables
* bcolz

If your repository includes this script, please send a pull-request to include
it in the above list so that we can notify you of any new releases.

Checking that it worked
=======================

TODO:

Merging Blosc sources from upstream
===================================

TODO: overhaul

We use the `subtree merge technique
<http://git-scm.com/book/en/Git-Tools-Subtree-Merging>`_ to maintain the
upstream Blosc sources. However, we do not use the technique exactly as
listed in the Pro-Git book.

The reason is quite technical: adding the Blosc Git repository as a
remote will also include the Blosc tags in your repository.  Since the
Blosc and python-blosc repositories share the same tagging scheme,
i.e. ``v.X.Y.Z``, we may have potentially conflicting tags. For example,
one might want to tag python-blosc ``v1.2.3``, however, since Blosc
already has a tag of this name, Git will deny you creating this. One
could use the ``--no-tags`` option for ``git fetch`` when fetching Blosc
-- but alas, this would defeat the purpose.  The tagged versions of
Blosc are exactly the ones we are interested in for the subtree merge!
So, as a compromise there is a shell script ``subtree-merge-blosc.sh``.
This accepts a single tag as argument and does a plain ``git
fetch``. This has the effect of fetching the commit that the requested
tag points to, but not actually fetching that tag or any of the other
tags.

It is not perfect and can probably be improved upon, but it does have
some comments in the source, checks for some common errors and tries to
abort as early as possible in case things go wrong. A sample invocation
is shown below:

.. code-block:: console

    $ ./subtree-merge-blosc.sh v1.2.3
    found remote tag: '4eda92c4dcba18849d482f5014b374d8b4b4cdfc refs/tags/v1.2.3'
    warning: no common commits
    remote: Counting objects: 1558, done.
    remote: Compressing objects: 100% (606/606), done.
    remote: Total 1558 (delta 958), reused 1528 (delta 932)
    Receiving objects: 100% (1558/1558), 468.67 KiB | 304 KiB/s, done.
    Resolving deltas: 100% (958/958), done.
    From git://github.com/Blosc/c-blosc
     + tag               v1.2.3     -> FETCH_HEAD
    Squash commit -- not updating HEAD
    Automatic merge went well; stopped before committing as requested
    [subtree-merge-blosc.sh b7a7378] subtree merge blosc v1.2.3
     16 files changed, 60 insertions(+), 43 deletions(-)

