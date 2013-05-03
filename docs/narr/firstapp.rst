.. index::
   single: hello world program

.. _firstapp_chapter:

はじめての :app:`Pyramid` アプリケーションを作る
==================================================

.. In this chapter, we will walk through the creation of a tiny :app:`Pyramid`
.. application.  After we're finished creating the application, we'll explain in
.. more detail how it works. It assumes you already have :app:`Pyramid` installed.
.. If you do not, head over to the :ref:`installing_chapter` section.

この章では、簡単な :app:`Pyramid` アプリケーション作成を経験してみます。
アプリケーション作成を経て、どのように動作するのかについて詳しく解説して
いきます。すでに :app:`Pyramid` のインストールは完了している事を前提
として進行します。もしまだインストールが完了していない場合は 
:ref:`installing_chapter` セクションを参照してください。

.. _helloworld_imperative:

Hello World
-----------

.. Here's one of the very simplest :app:`Pyramid` applications:

以下に最もシンプルな :app:`Pyramid` のアプリケーションの一つを示します。

.. literalinclude:: helloworld.py
   :linenos:

.. When this code is inserted into a Python script named ``helloworld.py`` and
.. executed by a Python interpreter which has the :app:`Pyramid` software
.. installed, an HTTP server is started on TCP port 8080.

上記コードを ``helloworld.py`` として保存し、 :app:`Pyramid` がインストール
された環境の Python インタプリタで実行すると TCP ポートの 8080 番で
HTTP サーバーが起動します。

.. On UNIX:

UNIX の場合:

.. code-block:: text

   $ /path/to/your/virtualenv/bin/python helloworld.py

.. On Windows:

Windows の場合:

.. code-block:: text

   C:\> \path\to\your\virtualenv\Scripts\python.exe helloworld.py

.. This command will not return and nothing will be printed to the console.
.. When port 8080 is visited by a browser on the URL ``/hello/world``, the
.. server will simply serve up the text "Hello world!".  If your application is
.. running on your local system, using ``http://localhost:8080/hello/world``
.. in a browser will show this result.

このコマンドはコンソールに出力を返しません。ブラウザによって 8080 番
ポートは ``/hello/world`` という URL でアクセスされた場合、サーバーは 
"Hello world!" と返すだけです。もしこのアプリケーションをローカルで
動作させている場合、 ``http://localhost:8080/hello/world`` という URL 
をブラウザに対して利用する事でその結果を見ることができます。

.. Each time you visit a URL served by the application in a browser, a logging
.. line will be emitted to the console displaying the hostname, the date, the
.. request method and path, and some additional information.  This output is
.. done by the wsgiref server we've used to serve this application.  It logs an
.. "access log" in Apache combined logging format to the console.

ブラウザでこのアプリケーションによって提供されている URL にアクセスする
度にホスト名、日時、リクエストメソッドおよびパスその他の付加情報が
コンソール上にログとして出力されます。この出力は、我々がアプリケーションを
提供するために利用している wsgiref サーバーによって返されます。
これはコンソール上に Apache と同型の "アクセスログ" をログ出力します。

.. Press ``Ctrl-C`` (or ``Ctrl-Break`` on Windows) to stop the application.

``Ctrl-C`` ( Windows の場合は ``Ctrl-Break`` ) を押すことで
アプリケーションを停止させる事ができます。

.. Now that we have a rudimentary understanding of what the application does,
.. let's examine it piece-by-piece.

アプリケーションが何を行うかを簡単に理解した所で、部分部分を検証します。

インポート
~~~~~~~~~~~

.. The above ``helloworld.py`` script uses the following set of import
.. statements:

先に示した ``helloworld.py`` スクリプトは下記のインポート宣言を利用して
います。

.. literalinclude:: helloworld.py
   :linenos:
   :lines: 1-3

.. The script imports the :class:`~pyramid.config.Configurator` class from the
.. :mod:`pyramid.config` module.  An instance of the
.. :class:`~pyramid.config.Configurator` class is later used to configure your
.. :app:`Pyramid` application.

このスクリプトは :class:`~pyramid.config.Configurator` クラスを 
:mod:`pyramid.config` モジュールからインポートします。 
:class:`~pyramid.config.Configurator` のインスタンスは、:app:`Pyramid` 
アプリケーションを定義する際に利用します。

.. Like many other Python web frameworks, :app:`Pyramid` uses the :term:`WSGI`
.. protocol to connect an application and a web server together.  The
.. :mod:`wsgiref` server is used in this example as a WSGI server for
.. convenience, as it is shipped within the Python standard library.

他の多くの Python 製 Web フレームワーク同様、 :app:`Pyramid` は Web 
サーバーとアプリケーションとの互いの接続のために :term:`WSGI` プロトコル
を利用しています。 :mod:`wsgiref` サーバーは簡単のためにこの例では 
WSGI サーバーとして使用され、Python の標準ライブラリに含まれています。

.. The script also imports the :class:`pyramid.response.Response` class for
.. later use.  An instance of this class will be used to create a web response.

このスクリプトはまた :class:`pyramid.response.Response` クラスを後で
利用するためインポートしています。このクラスのインスタンスは Web の
レスポンスを作成するために使われています。

view callable （ビュー呼び出し可能オブジェクト）の定義
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. The above script, beneath its set of imports, defines a function
.. named ``hello_world``.

先に示したスクリプトで、インポート分に続くのは ``hello_world`` 
と名付けられた関数の定義です。

.. literalinclude:: helloworld.py
   :linenos:
   :pyobject: hello_world

.. The function accepts a single argument (``request``) and it returns an
.. instance of the :class:`pyramid.response.Response` class.  The single
.. argument to the class' constructor is a string computed from parameters
.. matched from the URL.  This value becomes the body of the response.

この関数は引数を1つ ( ``request`` ) 取り、 
:class:`pyramid.response.Response` クラスのインスタンスを返します。
このクラスのコンストラクタに対する1つの引数は URL にマッチしたパラメータ
から算出された文字列です。返す値はレスポンスのボディになります。

.. This function is known as a :term:`view callable`.  A view callable
.. accepts a single argument, ``request``.  It is expected to return a
.. :term:`response` object.  A view callable doesn't need to be a function; it
.. can be represented via another type of object, like a class or an instance,
.. but for our purposes here, a function serves us well.

この関数は :term:`view callable` （ビュー呼び出し可能オブジェクト）とも
呼ばれています。1つの view callable は1つの引数 ``request`` 
を受け取ります。この関数は :term:`response` オブジェクトを返すことを
期待されています。 view callable は関数である必要が無く、別の
オブジェクトの型、たとえばクラスあるいはインスタンス
でも意味をなします。しかしここでは関数として利用することにします。

.. A view callable is always called with a :term:`request` object.  A request
.. object is a representation of an HTTP request sent to :app:`Pyramid` via the
.. active :term:`WSGI` server.

view callable は通常 :term:`request` オブジェクトと共にコールされます。
1つの request オブジェクトは :term:`WSGI` サーバーを通して 
:app:`Pyramid` に送信された HTTP リクエストを意味しています。

.. A view callable is required to return a :term:`response` object because a
.. response object has all the information necessary to formulate an actual HTTP
.. response; this object is then converted to text by the :term:`WSGI` server
.. which called Pyramid and it is sent back to the requesting browser.  To
.. return a response, each view callable creates an instance of the
.. :class:`~pyramid.response.Response` class.  In the ``hello_world`` function,
.. a string is passed as the body to the response.

1つの view callable は1つの :term:`response` オブジェクトを
返す必要が有ります。それは1つの response オブジェクトは実際の HTTP 
レスポンスを決定するのに必要な全ての情報が含まれているからで、この
オブジェクトは Pyramid をコールした :term:`WSGI` サーバーによってテキストに
変換されて、リクエストを送信したブラウザに返されます。レスポンスを返すため
には、各 view callable が :class:`~pyramid.response.Response` クラスの
インスタンスを一つ一つ作成できる必要が有ります。上記の ``hello_world`` 
関数では一つの文字列がレスポンスのボディとして処理されます。

.. index::
   single: imperative configuration
   single: Configurator
   single: helloworld (imperative)

.. _helloworld_imperative_appconfig:

アプリケーションの定義
~~~~~~~~~~~~~~~~~~~~~~~~

.. In the above script, the following code represents the *configuration* of
.. this simple application. The application is configured using the previously
.. defined imports and function definitions, placed within the confines of an
.. ``if`` statement:

上記のスクリプトでは、下記の部分がこのシンプルなアプリケーションを 
*定義している箇所* になります。このアプリケーションは直前で定めた
インポートと関数の設定によって定義付けられており、``if`` 文の中に
記述されています。

.. literalinclude:: helloworld.py
   :linenos:
   :lines: 9-15

.. Let's break this down piece-by-piece.

それではもっと細かく見て行きましょう。

Configurator Construction
~~~~~~~~~~~~~~~~~~~~~~~~~

.. literalinclude:: helloworld.py
   :linenos:
   :lines: 9-10

.. The ``if __name__ == '__main__':`` line in the code sample above represents a
.. Python idiom: the code inside this if clause is not invoked unless the script
.. containing this code is run directly from the operating system command
.. line. For example, if the file named ``helloworld.py`` contains the entire
.. script body, the code within the ``if`` statement will only be invoked when
.. ``python helloworld.py`` is executed from the command line.

上記の ``if __name__ == '__main__':`` というサンプルコードの行は Python で
頻繁に用いられます。このコードの if 文の中は OS のコマンドラインから直接
上記コードを含むスクリプトが呼び出された時に限って実行されます。例えば、
この ``helloworld.py`` と命名されたファイルが上記スクリプトを完全に内部に
保持していた場合、 ``if`` 文の中のコードはコマンドラインから 
``python helloworld.py`` が実行された時に限って処理されます。

.. Using the ``if`` clause is necessary -- or at least best practice -- because
.. code in a Python ``.py`` file may be eventually imported via the Python
.. ``import`` statement by another ``.py`` file.  ``.py`` files that are
.. imported by other ``.py`` files are referred to as *modules*.  By using the
.. ``if __name__ == '__main__':`` idiom, the script above is indicating that it does
.. not want the code within the ``if`` statement to execute if this module is
.. imported from another; the code within the ``if`` block should only be run
.. during a direct script execution.

上記 ``if`` 文の利用は必須、少なくとも最も合理的であると言えます。
なぜならば Python の ``.py`` ファイル内のコードは、 Python の ``import`` 
文を用いる事で他の ``.py`` ファイルにインポートされる場合が有るためです。
ある ``.py`` ファイルにインポートされた ``.py`` ファイルは *module* 
（モジュール）と呼ばれます。 ``if __name__ == '__main__':`` を利用する事で、
上記のスクリプトはコード中の ``if`` 文内に記述された処理を、このモジュールが
他のスクリプトにインポートされた際に実行を期待されない事、直接スクリプトを
実行された時のみ ``if`` 文中のコードの実行が期待される事を示しています。

.. The ``config = Configurator()`` line above creates an instance of the
.. :class:`~pyramid.config.Configurator` class.  The resulting ``config`` object
.. represents an API which the script uses to configure this particular
.. :app:`Pyramid` application.  Methods called on the Configurator will cause
.. registrations to be made in an :term:`application registry` associated with
.. the application.

``config = Configurator()`` の行では :class:`~pyramid.config.Configurator` 
クラスのインスタンスを生成しています。結果として生成される ``config`` 
オブジェクトはこの特別な :app:`Pyramid` アプリケーションを定義するために
スクリプトが利用する API として機能します。この Configurator オブジェクト
上でコールされたメソッドは、アプリケーションに関連付けられた 
:term:`application registry` （アプリケーション・レジストリ）に対して
登録を行います。

.. _adding_configuration:

Adding Configuration （補足設定）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. ignore-next-block
.. literalinclude:: helloworld.py
   :linenos:
   :lines: 11-12

.. First line above calls the :meth:`pyramid.config.Configurator.add_route`
.. method, which registers a :term:`route` to match any URL path that begins
.. with ``/hello/`` followed by a string.

上記のコードの1行目は、その後ろに文字列が続く ``/hello/`` から始まる
あらゆる URL のパスにマッチする :term:`route` （経路）を登録する 
:meth:`pyramid.config.Configurator.add_route` メソッドを呼び出しています。

.. The second line, ``config.add_view(hello_world, route_name='hello')``,
.. registers the ``hello_world`` function as a :term:`view callable` and makes
.. sure that it will be called when the ``hello`` route is matched.

続く行では ``config.add_view(hello_world, route_name='hello')`` という
記述で ``hello_world`` 関数を :term:`view callable` として登録し、
経路 ``hello`` にマッチした場合に呼び出されるように設定しています。

.. index::
   single: make_wsgi_app
   single: WSGI application

WSGI アプリケーション作成
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. ignore-next-block
.. literalinclude:: helloworld.py
   :linenos:
   :lines: 13

.. After configuring views and ending configuration, the script creates a WSGI
.. *application* via the :meth:`pyramid.config.Configurator.make_wsgi_app`
.. method.  A call to ``make_wsgi_app`` implies that all configuration is
.. finished (meaning all method calls to the configurator which set up views,
.. and various other configuration settings have been performed).  The
.. ``make_wsgi_app`` method returns a :term:`WSGI` application object that can
.. be used by any WSGI server to present an application to a requestor.
.. :term:`WSGI` is a protocol that allows servers to talk to Python
.. applications.  We don't discuss :term:`WSGI` in any depth within this book,
.. however, you can learn more about it by visiting `wsgi.org
.. <http://wsgi.org>`_.

ビューの設定が終わると、このスクリプトは 
:meth:`pyramid.config.Configurator.make_wsgi_app` メソッドを利用して
1つの WSGI *アプリケーション* を作成します。 ``make_wsgi_app`` に対する
コールは全ての設定が完了した（ビューを定義する configurator に対して必要な
メソッドが全てコールされ、追加の設定も処理されている）状態である事を
意味します。 ``make_wsgi_app`` メソッドはリクエスト送信者に対して
アプリケーションを提供するあらゆる WSGI サーバーに利用される 
:term:`WSGI` アプリケーションのオブジェクトを返します。 :term:`WSGI` は
サーバーが Python アプリケーションとやり取りを行う1つの手段です。
このガイドブックでは :term:`WSGI` に関してこれ以上深く解説を行いませんが、 
`wsgi.org <http://wsgi.org>`_ を訪れることでより詳しく学ぶことができます。

.. The :app:`Pyramid` application object, in particular, is an instance of a
.. class representing a :app:`Pyramid` :term:`router`.  It has a reference to
.. the :term:`application registry` which resulted from method calls to the
.. configurator used to configure it.  The :term:`router` consults the registry
.. to obey the policy choices made by a single application.  These policy
.. choices were informed by method calls to the :term:`Configurator` made
.. earlier; in our case, the only policy choices made were implied by calls
.. to its ``add_view`` and ``add_route`` methods.

特に :app:`Pyramid` アプリケーションオブジェクトは :app:`Pyramid` の
ルーター ( :term:`router` ) を表現するクラスのインスタンスです。
このオブジェクトは 自身を定義するのに利用されたコンフィギュレーターに
対するメソッド呼び出しの結果とである :term:`application registry` を
参照しています。ルーター ( :term:`router` ) は1つのアプリケーションで
選択された方針に従うためにレジストリを使用します。これらの方針の選択は
前もって行われる :term:`Configurator` に対するメソッド呼び出しによって
設定されます。このチュートリアルでは、 ``add_view`` および ``add_route`` 
メソッドに対する呼び出しによって方針が設定されます。

WSGI アプリケーションとして起動
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. ignore-next-block
.. literalinclude:: helloworld.py
   :linenos:
   :lines: 14-15

.. Finally, we actually serve the application to requestors by starting up a
.. WSGI server.  We happen to use the :mod:`wsgiref` ``make_server`` server
.. maker for this purpose.  We pass in as the first argument ``'0.0.0.0'``,
.. which means "listen on all TCP interfaces."  By default, the HTTP server
.. listens only on the ``127.0.0.1`` interface, which is problematic if you're
.. running the server on a remote system and you wish to access it with a web
.. browser from a local system.  We also specify a TCP port number to listen on,
.. which is 8080, passing it as the second argument.  The final argument is the
.. ``app`` object (a :term:`router`), which is the application we wish to
.. serve.  Finally, we call the server's ``serve_forever`` method, which starts
.. the main loop in which it will wait for requests from the outside world.

最後に WSGI サーバーを起動することでリクエスト送信者にアプリケーションを
提供します。そのためにここでは :mod:`wsgiref` インタフェースの 
``make_server`` を利用します。最初の引数 ``'0.0.0.0'`` は " 全ての TCP 
インタフェースを接続待ち状態にする " 事を意味します。標準ではこの HTTP 
サーバーは ``127.0.0.1`` インタフェースのみで接続待ち状態になり、これは
あなたがこのサーバーをリモートで動作させており、ローカルから
ウェブブラウザーを利用してサーバーにアクセスしようとする際に問題になる
場合が有ります。加えて接続待ち状態にする TCP ポートも第2引数で 8080 番に
設定します。最後の引数では ``app`` オブジェクト ( 1つの:term:`router` ) 
を指定し、動作させたいアプリケーションを指定します。最後に、外部からの
リクエストを待機し続けるメインループをスタートさせるため、サーバーの 
``serve_forever`` メソッドを呼び出します。

.. When this line is invoked, it causes the server to start listening on TCP
.. port 8080.  The server will serve requests forever, or at least until we stop
.. it by killing the process which runs it (usually by pressing ``Ctrl-C``
.. or ``Ctrl-Break`` in the terminal we used to start it).

この行が実行されると、 TCP ポートの 8080 番で接続待ち状態がサーバーに
よって開始されます。このサーバーは、私達が動作しているプロセスを殺す 
( 通常は ``Ctrl-C`` もしくは ``Ctrl-Break`` のショートカットをターミナル
上で利用する ) までリクエストに対して応答し続けます。

総括
~~~~

.. Our hello world application is one of the simplest possible :app:`Pyramid`
.. applications, configured "imperatively".  We can see that it's configured
.. imperatively because the full power of Python is available to us as we
.. perform configuration tasks.

私達がここで取り組んだ hello world アプリケーションは可能な限り最も
シンプルな :app:`Pyramid` アプリケーションの1つであることは 
"間違いありません" 。そう断言できるのは、私達がコンフィギュレーションを
設定する際に Python の全ての力を利用する事ができるためです。

参照
~~~~

.. For more information about the API of a :term:`Configurator` object,
.. see :class:`~pyramid.config.Configurator` .

:term:`Configurator` オブジェクトの API に関するさらなる情報は、 
:class:`~pyramid.config.Configurator` を参照して下さい。

.. For more information about :term:`view configuration`, see
.. :ref:`view_config_chapter`.

:term:`view configuration` に関するさらなる情報は
:ref:`view_config_chapter` を参照して下さい。
