subtree-merge-blosc.sh
======================

Description
-----------

This is the ``subtree-merge-blosc.sh`` script. It is a script to automatically
subtree merge a specific version of Blosc.

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

Used in ...
===========

* `python-blosc <http://github.com/Blosc/python-blosc>`_
* `PyTables <http://github.com/PyTables/PyTables>`_
* `bcolz <http://github.com/Blosc/bcolz>`_

If your project repository includes this script, please send a pull-request to include
it in the above list so that we can notify you of any new releases.

Reasoning
---------

The question arises, as to why it was chosen to include the Blosc sources
instead of using Git's submodules. Essentially, at the time of writing this
script, git submodules were fairly difficult to use and in addition the Blosc
sources themselves were fairly small. Additionally, for python-blosc, it was
important to include the Blosc sources and a way to build them into the C
extension via the ``setup.py`` file to ease installation for users. Shipping
them with python-blosc via PyPi means a user could easily install a fully
functional python-blosc by simply doing ``pip install blosc`` and there is no
need to build Blosc separately.

Therefore, directly including the sources was and still is  a good trade-off to
make. Now (January 2015) Blosc has advanced and itself includes the sources for
the codecs that it can handle and including sources has become somewhat of a
tradition. Thus we continue, and include Blosc and all of the codecs it
supports directly in the sources of python-blosc. This technique has been
painless of the years and proved itself useful so that it was adopted in other
projects that use Blosc too.

A note to packagers: we do realize, that for packaging it doesn't make sense to
compile Blosc into a C extension for multiple projects thereby duplicating the
amount of symbols and being wasteful of space on a target system. Since we are
aware of this need it is (and always should be) possible to build Blosc as a
shared library and dynamically link any projects that use it. See the
documentation of the corresponding projects for more information.


Checking that it worked
-----------------------

You can place a clone of the Blosc repository next to your own project,
checkout the tag that you are intending to include and then::

    $ diff -r c-blosc ../c-blosc
    Only in ../c-blosc: .git


Merging Blosc sources from upstream
-----------------------------------

As stated abpove, we use the `subtree merge technique
<http://git-scm.com/book/en/Git-Tools-Subtree-Merging>`_ to maintain the
upstream Blosc sources. However, we do not use the technique exactly as listed
in the Pro-Git book.

The reason is quite technical: adding the Blosc Git repository as a remote will
also include the Blosc tags in your repository.  Since the Blosc and (for
ecxample) python-blosc repositories share the same tagging scheme, i.e.
``v.X.Y.Z``, we may have potentially conflicting tags. For example, one might
want to tag python-blosc ``v1.2.3``, however, since Blosc already has a tag of
this name, Git will deny you creating this. One could use the ``--no-tags``
option for ``git fetch`` when fetching Blosc -- but alas, this would defeat the
purpose.  The tagged versions of Blosc are exactly the ones we are interested
in for the subtree merge!  So, as a compromise there is a shell script
``subtree-merge-blosc.sh``.  This accepts a single tag as argument and does a
plain ``git fetch``. This has the effect of fetching the commit that the
requested tag points to and storing it in ``FETCH_HEAD``, but not actually
fetching that tag or any of the other tags.

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

Author
------

Valentin Haenel <valentin@haenel.co>

License
-------

WTFPL (Do what the fuck you want to public license)
