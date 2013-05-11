.. _installing_chapter: 

:app:`Pyramid` のインストール
==============================

.. index::
   single: install preparation

.. Before You Install

インストールの前に
------------------

.. You will need `Python <http://python.org>`_ version 2.6 or better to
.. run :app:`Pyramid`.  

:app:`Pyramid` を利用するためにはバージョン2.6以上の 
`Python <http://python.org>`_ が必要です。

.. .. sidebar:: Python Versions

..  As of this writing, :app:`Pyramid` has been tested under Python 2.6.8,
..  Python 2.7.3, Python 3.2.3, and Python 3.3b1.  :app:`Pyramid` does not
..  run under any version of Python before 2.6.

.. sidebar:: Python のバージョン

   このページが書かれている時点では、:app:`Pyramid` は Python 2.6.8 , 
   Python 2.7.3, Python 3.2.3 および Python 3.3b1 の環境でテストされて
   います。 :app:`Pyramid` は Python 2.6 以前では動作しません。

.. :app:`Pyramid` is known to run on all popular UNIX-like systems such as
.. Linux, MacOS X, and FreeBSD as well as on Windows platforms.  It is also
.. known to run on :term:`PyPy` (1.9+).

:app:`Pyramid` は Linux, MacOS X, FreeBSD といったポピュラーな UNIX 系
システム上で Windows 上で動作するのと同様に動作します。`PyPy` (1.9+) 上
で動作するシステムとしても知られています。

.. :app:`Pyramid` installation does not require the compilation of any
.. C code, so you need only a Python interpreter that meets the
.. requirements mentioned.

:app:`Pyramid` のインストールは C 言語のコンパイルを必要とせず、
先述した条件を満たす Python のインタプリタだけで十分なのです。

.. If You Don't Yet Have A Python Interpreter (UNIX)

Python のインタプリタが用意されていない場合 (UNIX)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. If your system doesn't have a Python interpreter, and you're on UNIX,
.. you can either install Python using your operating system's package
.. manager *or* you can install Python from source fairly easily on any
.. UNIX system that has development tools.

もしもあなたが利用するシステムに Python のインタプリタが存在せず、
UNIX を利用していた場合、利用している OS のパッケージ・マネージャー
*もしくは* 開発ツールを内蔵した各種UNIXに対しては簡単に
ソースコードから Python をインストールできます。

.. index::
   pair: install; Python (from package, UNIX)


.. Package Manager Method

パッケージ・マネージャーを利用する方法
++++++++++++++++++++++++++++++++++++++

.. You can use your system's "package manager" to install Python. Every
.. system's package manager is slightly different, but the "flavor" of
.. them is usually the same.

Python のインストールには各自のシステムの "パッケージ・マネージャー" を
利用できます。各システムのパッケージ・マネージャーは若干異なっていますが、
それらの "雰囲気" はほぼ一緒です。

.. For example, on an Ubuntu Linux system, to use the system package
.. manager to install a Python 2.7 interpreter, use the following
.. command:

例えば、Ubuntu Linux のシステムでは、Python 2.7 のインタプリタを
インストールするパッケージ・マネージャーを利用するには、以下の
コマンドを利用します。

.. code-block:: text

   $ sudo apt-get install python2.7-dev

.. This command will install both the Python interpreter and its development
.. header files.  Note that the headers are required by some (optional) C
.. extensions in software depended upon by Pyramid, not by Pyramid itself.

このコマンドを入力することで、Python のインタプリタと開発ヘッダファイルが
インストールされます。それらのヘッダは Pyramid 自体でなく、Pyramid に
依存した一部の C 言語拡張で利用されます。

.. Once these steps are performed, the Python interpreter will usually be
.. invokable via ``python2.7`` from a shell prompt.

一度この作業を行えば Python のインタプリタはシェルプロンプト
で ``python2.7`` と入力することで利用可能になります。

.. index::
   pair: install; Python (from source, UNIX)


.. Source Compile Method

ソースコードからコンパイルする方法
++++++++++++++++++++++++++++++++++

.. It's useful to use a Python interpreter that *isn't* the "system"
.. Python interpreter to develop your software.  The authors of
.. :app:`Pyramid` tend not to use the system Python for development
.. purposes; always a self-compiled one.  Compiling Python is usually
.. easy, and often the "system" Python is compiled with options that
.. aren't optimal for web development.

ソフトウェア開発のためには "システムの" Python インタプリタ *でない* 
Python インタプリタを利用するほうが便利です。:app:`Pyramid` の
開発者は開発目的でシステム上の Python を利用することほぼ無く、
自身でコンパイルしたインタプリタを利用します。Python のコンパイルは
通常簡単な物である一方、"システムの" Python はしばしば Web 開発に
適しているとは言えないコンパイルオプションが適用されています。

.. To compile software on your UNIX system, typically you need
.. development tools.  Often these can be installed via the package
.. manager.  For example, this works to do so on an Ubuntu Linux system:

UNIX システム上でソフトウェアをコンパイルするには、一般的には
開発ツール類を必要とします。これらは大抵の場合パッケージ・マネージャー
を利用してインストールできます。例えば、 Ubuntu Linux システムでは
次のように入力することでインストールが行われます。

.. code-block:: text

   $ sudo apt-get install build-essential

.. On Mac OS X, installing `XCode
.. <http://developer.apple.com/tools/xcode/>`_ has much the same effect.

Mac OS X では、`XCode<http://developer.apple.com/tools/xcode/>`_ 
をインストールする事が同様の効果を持ちます。

.. Once you've got development tools installed on your system, you can
.. install a Python 2.7 interpreter from *source*, on the same system,
.. using the following commands:

一度システムに開発ツールをインストールすれば、先のシステムに対しては
下記のように入力することで Python 2.7 を *ソースコードから* 
インストールできます。

.. code-block:: text

   [chrism@vitaminf ~]$ cd ~
   [chrism@vitaminf ~]$ mkdir tmp
   [chrism@vitaminf ~]$ mkdir opt
   [chrism@vitaminf ~]$ cd tmp
   [chrism@vitaminf tmp]$ wget \
          http://www.python.org/ftp/python/2.7.3/Python-2.7.3.tgz
   [chrism@vitaminf tmp]$ tar xvzf Python-2.7.3.tgz
   [chrism@vitaminf tmp]$ cd Python-2.7.3
   [chrism@vitaminf Python-2.7.3]$ ./configure \
           --prefix=$HOME/opt/Python-2.7.3
   [chrism@vitaminf Python-2.7.3]$ make; make install

.. Once these steps are performed, the Python interpreter will be
.. invokable via ``$HOME/opt/Python-2.7.3/bin/python`` from a shell
.. prompt.

これらのステップを踏めば、Python のインタプリタはシェルプロンプトで 
``$HOME/opt/Python-2.7.3/bin/python`` と入力することで利用可能に
なります。

.. index::
   pair: install; Python (from package, Windows)


.. If You Don't Yet Have A Python Interpreter (Windows)

Python のインタプリタが用意されていない場合 (Windows)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. If your Windows system doesn't have a Python interpreter, you'll need
.. to install it by downloading a Python 2.7-series interpreter
.. executable from `python.org's download section
.. <http://python.org/download/>`_ (the files labeled "Windows
.. Installer").  Once you've downloaded it, double click on the
.. executable and accept the defaults during the installation process.
.. You may also need to download and install the `Python for Windows
.. extensions <http://sourceforge.net/projects/pywin32/files/>`_.

Windows システムで Python のインタプリタがインストールされていない場合、 
`python.org's download section <http://python.org/download/>`_ から
実行可能な Python 2.7 系のインタプリタをダウンロードしてインストールする
必要があります(リンク先で "Windows Installer" と表示されているファイル)。
そのファイルをダウンロードした後、実行ファイルをダブルクリックし、
インストールのプロセスを進めます。 `Python for Windows extensions
<http://sourceforge.net/projects/pywin32/files/>`_ をインストールする
必要があるかもしれません。

.. .. warning::
.. 
..    After you install Python on Windows, you may need to add the
..    ``C:\Python27`` directory to your environment's ``Path`` in order
..    to make it possible to invoke Python from a command prompt by
..    typing ``python``.  To do so, right click ``My Computer``, select
..    ``Properties`` --> ``Advanced Tab`` --> ``Environment Variables``
..    and add that directory to the end of the ``Path`` environment
..    variable.

.. warning::

   Windows 上に Python をインストールした後は、コマンド・プロンプトで
   ``python`` と入力して Python を呼び出すために、 ``C:¥Python27`` 
   ディレクトリを環境変数の ``Path`` に追加する必要があるかもしれません。
   それを行うには ``マイコンピュータ`` を右クリックし、 ``プロパティ`` 
   --> ``システムの詳細設定`` --> ``詳細設定`` --> ``環境変数`` と
   選択していき、環境変数 ``Path`` の末尾に ``;C:¥Python27;`` を追加
   してください。

.. index::
   single: installing on UNIX

.. _installing_unix:


.. Installing :app:`Pyramid` on a UNIX System

UNIX システムに :app:`Pyramid` をインストールする
-------------------------------------------------

.. It is best practice to install :app:`Pyramid` into a "virtual"
.. Python environment in order to obtain isolation from any "system"
.. packages you've got installed in your Python version.  This can be
.. done by using the :term:`virtualenv` package.  Using a virtualenv will
.. also prevent :app:`Pyramid` from globally installing versions of
.. packages that are not compatible with your system Python.
.. :app:`Pyramid` を "システムの" Python 環境から独立した状態で利用するには 

"仮想の" Python 環境にインストールするのが最上の解決策です。この方法は
:term:`virtualenv` パッケージを利用する事で可能になります。virtualenv の
利用はあなたの環境の Python と互換性のないパッケージを :app:`Pyramid` 
がインストールしてしまう事態を防ぐ一助にもなります。

.. To set up a virtualenv in which to install :app:`Pyramid`, first ensure that
.. :term:`setuptools` or :term:`distribute` is installed.  To do so, invoke
.. ``import setuptools`` within the Python interpreter you'd like to run
.. :app:`Pyramid` under.

:app:`Pyramid` をインストールする virtualenv をセットアップするには、
最初に :term:`setuptools` もしくは :term:`distribute` がインストールされて
居ることを確認する必要が有ります。そのためには、 :app:`Pyramid` を動作させる
Python 環境下で ``import setuptools`` と入力してください。

.. Here's the output you'll expect if setuptools or distribute is already
.. installed:

setuptools もしくは distribute が正しくインストールされている場合、
以下のように出力されます。

.. code-block:: text

   [chrism@thinko docs]$ python2.7
   Python 2.7.3 (default, Aug  1 2012, 05:14:39) 
   [GCC 4.6.3] on linux2
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import setuptools
   >>> 

.. Here's the output you can expect if setuptools or distribute is not already
.. installed:

setuptools もしくは distribute が正しくインストールされていない場合、
以下のように出力されます。

.. code-block:: text

   [chrism@thinko docs]$ python2.7
   Python 2.7.3 (default, Aug  1 2012, 05:14:39) 
   [GCC 4.6.3] on linux2
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import setuptools
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   ImportError: No module named setuptools
   >>>

.. If ``import setuptools`` raises an :exc:`ImportError` as it does above, you
.. will need to install setuptools or distribute manually.  Note that above
.. we're using a Python 2.7-series interpreter on Mac OS X; your output may
.. differ if you're using a later Python version or a different platform.

``import setuptools`` によって上記の ``import setuptools`` が発生した場合、
setuptools あるいは distribute を手動でインストールする必要が有ります。
上記のエラーは Mac OS X 上で Python 2.7 系のインタプリタを利用した際に
出力されるエラーであることに注意して下さい。より新しい Python や異なる
プラットフォームを利用している場合は異なる出力となる場合が有ります。

.. If you are using a "system" Python (one installed by your OS distributor or a
.. 3rd-party packager such as Fink or MacPorts), you can usually install the
.. setuptools or distribute package by using your system's package manager.  If
.. you cannot do this, or if you're using a self-installed version of Python,
.. you will need to install setuptools or distribute "by hand".  Installing
.. setuptools or distribute "by hand" is always a reasonable thing to do, even
.. if your package manager already has a pre-chewed version of setuptools for
.. installation.

もし "システムの" Python ( OS の配布元、 Fink もしくは MacPorts のような
サード・パーティ製パッケージ管理システムによってインストールされている )
を利用する場合、setuptools もしくは distribute のパッケージをシステムの
パッケージマネージャーを使用してインストールできます。もしそれが不可能、
あるいは標準でインストールされているバージョンの Python を利用する場合は 
"手動で" setuptools や distribute をインストールする必要が有ります。
setuptools や distribute を "手動で" インストールするのは
パッケージマネージャーがお誂えむきに setuptools を内蔵していたとしても、
行うに値します。

.. If you're using Python 2, you'll want to install ``setuptools``.  If you're
.. using Python 3, you'll want to install ``distribute``.  Below we tell you how
.. to do both.

Python 2 を利用している場合は、 ``setuptools`` をインストールします。
Python 3 を利用している場合は、 ``distribute`` をインストールします。
以下にそのそれぞれ両方の方法を示します。

.. Installing Setuptools On Python 2

Python 2 に対応した setuptools をインストールする
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. To install setuptools by hand under Python 2, first download `ez_setup.py
.. <http://peak.telecommunity.com/dist/ez_setup.py>`_ then invoke it using the
.. Python interpreter into which you want to install setuptools.

Python 2 の環境に setuptools を手動でインストールするには、
はじめに `ez_setup.py <http://peak.telecommunity.com/dist/ez_setup.py>`_ を
ダウンロードします。続いて setuptools をインストールしたい Python 
環境下でそのファイルを Python インタプリタで呼び出します。

.. code-block:: text

   $ python ez_setup.py

.. Once this command is invoked, setuptools should be installed on your
.. system.  If the command fails due to permission errors, you may need
.. to be the administrative user on your system to successfully invoke
.. the script.  To remediate this, you may need to do:

このコマンドが一度呼ばれると、setuptools がインストールされます。
ファイル権限に関係するエラーでコマンドが異常終了する場合は、
スクリプトを呼べるように管理者権限での実行が必要になるかもしれません。
それを行うためには、以下を実行して下さい。

.. code-block:: text

   $ sudo python ez_setup.py


.. Installing Distribute On Python 3

Python 3 に対応した distribute をインストールする
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. ``setuptools`` doesn't work under Python 3.  Instead, you can use
.. ``distribute``, which is a fork of setuptools that does work on Python 3.  To
.. install it, first download `distribute_setup.py
.. <http://python-distribute.org/distribute_setup.py>`_ then invoke it using the
.. Python interpreter into which you want to install setuptools.

Python 3 環境下では ``setuptools`` が動作しません。その代わりに setuptools の
フォークである ``distribute`` を利用できます。インストールするためには、
`distribute_setup.py <http://python-distribute.org/distribute_setup.py>`_ を
ダウンロードし、distribute をインストールしたい Python 環境下でその
ファイルを Python インタプリタで呼び出します。

.. code-block:: text

   $ python3 distribute_setup.py

.. Once this command is invoked, distribute should be installed on your system.
.. If the command fails due to permission errors, you may need to be the
.. administrative user on your system to successfully invoke the script.  To
.. remediate this, you may need to do:

このコマンドが一度呼ばれると、distribute がインストールされます。
ファイル権限に関係するエラーでコマンドが異常終了する場合は、
スクリプトを呼べるように管理者権限での実行が必要になるかもしれません。
それを行うためには、以下を実行して下さい。

.. code-block:: text

   $ sudo python3 distribute_setup.py

.. index::
   pair: install; virtualenv


.. Installing the ``virtualenv`` Package

``virtualenv`` パッケージのインストール
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. Once you've got setuptools or distribute installed, you should install the
.. :term:`virtualenv` package.  To install the :term:`virtualenv` package into
.. your setuptools-enabled Python interpreter, use the ``easy_install`` command.

setuptools あるいは distribute がインストールされれば、 :term:`virtualenv` 
パッケージをインストールできるようになります。 :term:`virtualenv` を
setuptools が利用できる環境にインストールするためには、 ``easy_install`` 
コマンドを利用して下さい。

.. .. warning::

..    Python 3.3 includes ``pyvenv`` out of the box, which provides similar
..    functionality to ``virtualenv``.  We however suggest using ``virtualenv``
..    instead, which works well with Python 3.3.  This isn't a recommendation made
..    for technical reasons; it's made because it's not feasible for the authors
..    of this guide to explain setup using multiple virtual environment systems.
..    We are aiming to not need to make the installation documentation
..    Turing-complete.

..   If you insist on using ``pyvenv``, you'll need to understand how to install
..   software such as ``distribute`` into the virtual environment manually,
..   which this guide does not cover.

.. warning::

   Python 3.3 は従来の枠を超え、機能的に ``virtualenv`` と似た ``pyvenv`` 
   を内蔵しています。しかし、私達は ``virtualenv`` の利用を推奨します。
   それは技術的な根拠による推薦ではなく、このガイドの著者が複数の仮想環境
   を利用したセットアップについて満足の行く説明ができないためになります。
   我々はこのインストールの手引きをチューリング完全なものにしたいのです。

   もしあなたが ``pyvenv`` を利用したい場合、手動で仮想環境に ``distribute``
   のようなソフトウェアを手動でインストールする方法について理解している
   必要があります。この手引きではその領域については取り扱いません。

.. code-block:: text

   $ easy_install virtualenv

.. This command should succeed, and tell you that the virtualenv package is now
.. installed.  If it fails due to permission errors, you may need to install it
.. as your system's administrative user.  For example:

このコマンドが完了すると、たった今インストールされた virtualenv パッケージの
バージョンを出力してくれます。コマンドが権限によるエラーで終了する場合は、
以下のようにシステムの管理者権限でインストールする必要が有ります。

.. code-block:: text

   $ sudo easy_install virtualenv

.. index::
   single: virtualenv
   pair: Python; virtual environment


.. Creating the Virtual Python Environment

仮想の Python 環境を構築する
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. Once the :term:`virtualenv` package is installed in your Python, you
.. can then create a virtual environment.  To do so, invoke the
.. following:

あなたの Python 環境に :term:`virtualenv` がインストールされると、
仮想環境を作ることができるようになります。環境を利用するためには、
以下のように宣言して下さい。

.. code-block:: text

   $ virtualenv --no-site-packages env
   New python executable in env/bin/python
   Installing setuptools.............done.

.. .. warning::

..    Using ``--no-site-packages`` when generating your
..    virtualenv is *very important*. This flag provides the necessary
..    isolation for running the set of packages required by
..    :app:`Pyramid`.  If you do not specify ``--no-site-packages``,
..    it's possible that :app:`Pyramid` will not install properly into
..    the virtualenv, or, even if it does, may not run properly,
..    depending on the packages you've already got installed into your
..    Python's "main" site-packages dir.

.. warning::

   virtualenv の環境が *非常に重要な場合に* ``--no-site-packages`` 
   オプションを利用します。このフラグは :app:`Pyramid` が必要とする
   パッケージの依存情報を独立したものにするために利用します。
   ``--no-site-packages`` を指定しなかった場合 :app:`Pyramid` が
   virtualenv 環境に正しくインストールされない可能性があり、
   インストールされたとしても既にインストールされている 
   "メインの" Python のサイト・パッケージに依存してしまうため
   正常に動作しない場合があります。

.. .. warning:: *do not* use ``sudo`` to run the
..    ``virtualenv`` script.  It's perfectly acceptable (and desirable)
..    to create a virtualenv as a normal user.

.. warning:: ``virtualenv`` スクリプトを ``sudo`` 権限で実行
   *しないで* ください。通常ユーザーで十分問題がなく（むしろ
   望ましい） virtualenv 環境を作る事が可能です。

.. You should perform any following commands that mention a "bin"
.. directory from within the ``env`` virtualenv dir.

以下のコマンドは virtualenv 環境内の ``env`` ディレクトリ内で
"bin" ディレクトリを呼び出して実行する必要が有ります。


.. Installing :app:`Pyramid` Into the Virtual Python Environment

:app:`Pyramid` を仮想 Python 環境にインストールする
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. After you've got your ``env`` virtualenv installed, you may install
.. :app:`Pyramid` itself using the following commands from within the
.. virtualenv (``env``) directory you created in the last step.

virtualenv による ``env`` 環境を構築した後は、以下のコマンドを
ここまでの過程で作成したvirtualenv (``env``) ディレクトリ内で
入力する事で :app:`Pyramid` をインストールできます。

.. code-block:: text

   $ cd env
   $ bin/easy_install pyramid

.. The ``easy_install`` command will take longer than the previous ones to
.. complete, as it downloads and installs a number of dependencies.

``easy_install`` コマンドは依存関係にある数多くのパッケージをインストール
するため、完了するには前回入力した時よりも長い時間を要します。

.. index::
   single: installing on Windows

.. _installing_windows:


.. Installing :app:`Pyramid` on a Windows System

Windows システムに :app:`Pyramid` をインストールする
----------------------------------------------------

.. You can use Pyramid on Windows under Python 2 or under Python 3.  Directions
.. for both versions are included below.

Python 2 もしくは Python 3 がインストールされた Windows 環境で Pyramid を
利用できます。両方のバージョンに関して次に示します。


.. Windows Using Python 2

Python 2 を利用する Windows の場合
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. #. Install, or find `Python 2.7
..   <http://www.python.org/download/releases/2.7.3/>`_ for your system.

#. `Python 2.7
   <http://www.python.org/download/releases/2.7.3/>`_ がインストール
   されていることを確認して下さい。

.. #. Install the `Python for Windows extensions
..    <http://sourceforge.net/projects/pywin32/files/>`_.  Make sure to
..    pick the right download for Python 2.7 and install it using the
..    same Python installation from the previous step.

#. `Python for Windows extensions
   <http://sourceforge.net/projects/pywin32/files/>`_ をインストール
   して下さい。Python 2.7 に対応したバージョンをダウンロードし、
   直前のステップで確認した Python を利用する環境下にインストール
   してください。

.. #. Install latest :term:`setuptools` distribution into the Python you
..    obtained/installed/found in the step above: download `ez_setup.py
..    <http://peak.telecommunity.com/dist/ez_setup.py>`_ and run it using
..    the ``python`` interpreter of your Python 2.7 installation using a
..    command prompt:

#. ここまでの過程で発見・インストール・取得した Python 環境に 
   :term:`setuptools` の最新版をインストールします。
   `ez_setup.py <http://peak.telecommunity.com/dist/ez_setup.py>`_ を
   ダウンロードしてコマンドプロンプトで Python 2.7 の ``python`` 
   インタプリタに渡して動作させて下さい。

   .. code-block:: text

      c:\> c:\Python27\python ez_setup.py

.. #. Use that Python's `bin/easy_install` to install `virtualenv`:

#. `virtualenv` をインストールするために Python 環境の 
   `bin/easy_install` を利用してください。

   .. code-block:: text

      c:\> c:\Python27\Scripts\easy_install virtualenv

.. #. Use that Python's virtualenv to make a workspace:

#. ワークスペースを作成するために Python の virtualenv を利用して
   下さい。

   .. code-block:: text

      c:\> c:\Python27\Scripts\virtualenv --no-site-packages env

.. #. Switch to the ``env`` directory:

#. ``env`` ディレクトリに移動します。

   .. code-block:: text

      c:\> cd env

.. #. (Optional) Consider using ``Scripts\activate.bat`` to make your shell
..    environment wired to use the virtualenv.

#. (オプション) シェル環境と virtualenv 環境とを関連付ける
   ために ``Scripts¥activate.bat`` を利用する事を検討して下さい。

.. #. Use ``easy_install`` to get :app:`Pyramid` and its direct dependencies
..    installed:

#. :app:`Pyramid` を取得するために ``easy_install`` を利用すると、依存する
   パッケージもインストールされます。

   .. code-block:: text

      c:\env> Scripts\easy_install pyramid

.. Windows Using Python 3

Python 3 を利用する Windows の場合
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. #. Install, or find `Python 3.2
..    <http://www.python.org/download/releases/3.2.3/>`_ for your system.

#. `Python 3.2
   <http://www.python.org/download/releases/3.2.3/>`_ がシステムに
   インストールされている事を確認して下さい。

.. #. Install the `Python for Windows extensions
..    <http://sourceforge.net/projects/pywin32/files/>`_.  Make sure to
..    pick the right download for Python 3.2 and install it using the
..    same Python installation from the previous step.

#. `Python for Windows extensions
   <http://sourceforge.net/projects/pywin32/files/>`_ をインストール
   して下さい。Python 3.2 に対応した物をダウンロードし、直前の
   ステップまででインストールした

.. #. Install latest :term:`distribute` distribution into the Python you
..    obtained/installed/found in the step above: download `distribute_setup.py
..    <http://python-distribute.org/distribute_setup.py>`_ and run it using the
..    ``python`` interpreter of your Python 3.2 installation using a command
..    prompt:

#. ここまでの過程で取得・インストール・検出された Python 環境に
   最新の :term:`distribute` をインストールします。
   `distribute_setup.py
   <http://python-distribute.org/distribute_setup.py>`_ 
   をダウンロードし、利用している Python 3.2 の ``Python`` 
   インタプリタにコマンドプロンプトで渡して動作させて下さい。

   .. code-block:: text

      c:\> c:\Python32\python distribute_setup.py

.. #. Use that Python's `bin/easy_install` to install `virtualenv`:

#. `virtualenv` のインストールのために Python の `bin/easy_install` 
   を利用して下さい。

   .. code-block:: text

      c:\> c:\Python32\Scripts\easy_install virtualenv

.. #. Use that Python's virtualenv to make a workspace:

#. Python の virtualenv を利用してワーク・スペースを作成します。

   .. code-block:: text

      c:\> c:\Python32\Scripts\virtualenv --no-site-packages env

.. #. Switch to the ``env`` directory:

#. ``env`` ディレクトリに移動します。

   .. code-block:: text

      c:\> cd env

.. #. (Optional) Consider using ``Scripts\activate.bat`` to make your shell
..    environment wired to use the virtualenv.

#. (オプション) シェル環境と virtualenv 環境とを関連付ける
   ために ``Scripts¥activate.bat`` を利用する事を検討して下さい。

.. #. Use ``easy_install`` to get :app:`Pyramid` and its direct dependencies
..    installed:

#. :app:`Pyramid` を取得するために ``easy_install`` を利用すると、依存する
   パッケージもインストールされます。

   .. code-block:: text

      c:\env> Scripts\easy_install pyramid

.. What Gets Installed

インストールされた物
---------------------

.. When you ``easy_install`` :app:`Pyramid`, various other libraries such as
.. WebOb, PasteDeploy, and others are installed.

:app:`Pyramid` を ``easy_install`` を利用してインストールすると、WebOb、
PasteDeploy 等ののライブラリがインストールされます。

.. Additionally, as chronicled in :ref:`project_narr`, scaffolds will be
.. registered, which make it easy to start a new :app:`Pyramid` project.

加えて、 :ref:`project_narr` に記述されている、新たに Pyramid の
プロジェクトを簡単に作る事ができるライブラリが登録されます。

