.. index::
   single: session

.. _sessions_chapter:

.. Sessions

セッション
==========

.. A :term:`session` is a namespace which is valid for some period of
.. continual activity that can be used to represent a user's interaction
.. with a web application.

:term:`session` は、ユーザとウェブアプリケーションとの相互作用を表現
するために使用できる、連続的な活動の特定の期間で有効な名前空間です。


.. This chapter describes how to configure sessions, what session
.. implementations :app:`Pyramid` provides out of the box, how to store and
.. retrieve data from sessions, and two session-specific features: flash
.. messages, and cross-site request forgery attack prevention.

この章では、セッションをどのように設定するか、 :app:`Pyramid` が
最初から提供しているセッション実装にはどんなものがあるか、セッションに
データを格納したり検索したりする方法、そしてセッションに特有の2つの機能、
フラッシュメッセージとクロスサイトリクエストフォージェリ攻撃防御について
記述します。


.. index::
   single: session factory (default)

.. Using The Default Session Factory

.. _using_the_default_session_factory:

デフォルトセッションファクトリを使う
------------------------------------

.. In order to use sessions, you must set up a :term:`session factory`
.. during your :app:`Pyramid` configuration.

セッションを使用するためには、 :app:`Pyramid` 設定で
:term:`session factory` をセットアップする必要があります。


.. A very basic, insecure sample session factory implementation is
.. provided in the :app:`Pyramid` core.  It uses a cookie to store
.. session information.  This implementation has the following
.. limitation:

非常に基本的な、セキュアでないセッションファクトリの実装のサンプルは
:app:`Pyramid` コアの中で提供されます。この実装は、セッション情報を
格納するために cookie を使用します。この実装には次の制限があります:


.. - The session information in the cookies used by this implementation
..   is *not* encrypted, so it can be viewed by anyone with access to the
..   cookie storage of the user's browser or anyone with access to the
..   network along which the cookie travels.

- この実装によって使用される cookie 中のセッション情報は暗号化
  *されません* 。したがって、ユーザのブラウザの cookie ストレージに
  アクセスできる人なら誰でも、あるいは cookie が流れるネットワークに
  アクセスできる人なら誰でも、その情報を見ることができます。


.. - The maximum number of bytes that are storable in a serialized
..   representation of the session is fewer than 4000.  This is
..   suitable only for very small data sets.

- 保存可能な最大バイト数は、セッションのシリアライズ表現で 4000 未満です。
  これは、非常に小さなデータセットにのみ適しています。


.. It is digitally signed, however, and thus its data cannot easily be
.. tampered with.

しかし、それはデジタル署名されており、したがってそのデータを簡単に
改竄することはできません。


.. You can configure this session factory in your :app:`Pyramid`
.. application by using the ``session_factory`` argument to the
.. :class:`~pyramid.config.Configurator` class:

:class:`~pyramid.config.Configurator` クラスに ``session_factory`` 引数
を渡すことで、 :app:`Pyramid` アプリケーション内でこのセッションファクトリ
を設定することができます:


.. code-block:: python
   :linenos:

   from pyramid.session import UnencryptedCookieSessionFactoryConfig
   my_session_factory = UnencryptedCookieSessionFactoryConfig('itsaseekreet')
   
   from pyramid.config import Configurator
   config = Configurator(session_factory = my_session_factory)


.. warning:: 

   .. Note the very long, very explicit name for
   .. ``UnencryptedCookieSessionFactoryConfig``.  It's trying to tell you that
   .. this implementation is, by default, *unencrypted*.  You should not use it
   .. when you keep sensitive information in the session object, as the
   .. information can be easily read by both users of your application and third
   .. parties who have access to your users' network traffic.  And if you use this
   .. sessioning implementation, and you inadvertently create a cross-site
   .. scripting vulnerability in your application, because the session data is
   .. stored unencrypted in a cookie, it will also be easier for evildoers to
   .. obtain the current user's cross-site scripting token.  In short, use a
   .. different session factory implementation (preferably one which keeps session
   .. data on the server) for anything but the most basic of applications where
   .. "session security doesn't matter", and you are sure your application has no
   .. cross-site scripting vulnerabilities.

   ``UnencryptedCookieSessionFactoryConfig`` という、非常に長くて明示的
   な名前に注意してください。これは、この実装がデフォルトでは
   *暗号化されない* ということを伝えようとしています。セッションオブジェクト
   に機密情報を保存する場合、この実装を使用するべきではありません。
   なぜなら、アプリケーションのユーザとユーザのネットワークトラフィック
   にアクセスできる第三者の両方が、情報を容易に読み出すことができるからです。
   また、このセッション実装を使用していて、不注意にもアプリケーションで
   クロスサイトスクリプティング脆弱性を生み出せば、セッションデータは
   cookie の中に暗号化されずに保存されているので、悪意を持った攻撃者が現在
   のユーザーのクロスサイトスクリプティングトークンを得ることも簡単でしょう。
   端的に言えば、アプリケーションの中で「セッションセキュリティは気にしない」
   最も基本的な部分以外では、異なるセッションファクトリ実装 (できれば
   サーバー上にセッションデータを保存するもの) を使用してください。
   そうすれば、アプリケーションがクロスサイトスクリプティング脆弱性を
   持っていないと確信できます。


.. Using a Session Object

.. index::
   single: session object


セッションオブジェクトを使う
----------------------------

.. Once a session factory has been configured for your application, you
.. can access session objects provided by the session factory via
.. the ``session`` attribute of any :term:`request` object.  For
.. example:

アプリケーションのために一旦セッションファクトリが設定されたら、
セッションファクトリによって提供されるセッションオブジェクトに
任意の :term:`request` オブジェクトの ``session`` 属性によって
アクセスすることができます。例えば:


.. code-block:: python
   :linenos:

   from pyramid.response import Response

   def myview(request):
       session = request.session
       if 'abc' in session:
           session['fred'] = 'yes'
       session['abc'] = '123'
       if 'fred' in session:
           return Response('Fred was in the session')
       else:
           return Response('Fred was not in the session')


.. You can use a session much like a Python dictionary.  It supports all
.. dictionary methods, along with some extra attributes, and methods.

セッションは Python 辞書とほとんど同じように使うことができます。
それはすべての辞書メソッドに加えて、いくつかの追加の属性とメソッドを
サポートします。


.. Extra attributes:

追加の属性:


.. ``created``
..   An integer timestamp indicating the time that this session was created.

``created``
  このセッションが作られた時刻を示す整数タイムスタンプ。


.. ``new``
..   A boolean.  If ``new`` is True, this session is new.  Otherwise, it has 
..   been constituted from data that was already serialized.

``new``
  真偽値。 ``new`` が True の場合、このセッションは新規作成されました。
  そうでなければ、シリアライズ済みのデータから復元されました。


.. Extra methods:

追加のメソッド:


.. ``changed()``
..   Call this when you mutate a mutable value in the session namespace.
..   See the gotchas below for details on when, and why you should
..   call this.

``changed()``
  セッション名前空間の中の mutable な値を変更する場合、このメソッド
  を呼んでください。このメソッドをいつ、そしてなぜ呼ばなければならないか
  についての詳細は、下記の gotchas を参照してください。


.. ``invalidate()``
..   Call this when you want to invalidate the session (dump all data,
..   and -- perhaps -- set a clearing cookie).

``invalidate()``
  セッションを無効にしたい場合 (すべてのデータをダンプして、 -- 恐らく --
  cleaning cookie をセットする)、 このメソッドを呼んでください。


.. The formal definition of the methods and attributes supported by the
.. session object are in the :class:`pyramid.interfaces.ISession`
.. documentation.

セッションオブジェクトがサポートするメソッドと属性の正式な定義は、
:class:`pyramid.interfaces.ISession` ドキュメンテーションにあります。


Some gotchas:


.. - Keys and values of session data must be *pickleable*.  This means,
..   typically, that they are instances of basic types of objects,
..   such as strings, lists, dictionaries, tuples, integers, etc.  If you
..   place an object in a session data key or value that is not
..   pickleable, an error will be raised when the session is serialized.

- セッションデータのキーおよび値は pickle 可能でなければなりません。
  これは、典型的には、文字列、リスト、辞書、タプル、整数などのような
  基本型のオブジェクトのインスタンスを意味します。pickle 可能でない
  オブジェクトをセッションデータのキーまたは値に入れると、セッションが
  シリアライズされる時にエラーが上げられるでしょう。


.. - If you place a mutable value (for example, a list or a dictionary)
..   in a session object, and you subsequently mutate that value, you must
..   call the ``changed()`` method of the session object. In this case, the
..   session has no way to know that is was modified. However, when you
..   modify a session object directly, such as setting a value (i.e.,
..   ``__setitem__``), or removing a key (e.g., ``del`` or ``pop``), the
..   session will automatically know that it needs to re-serialize its
..   data, thus calling ``changed()`` is unnecessary. There is no harm in
..   calling ``changed()`` in either case, so when in doubt, call it after
..   you've changed sessioning data.

- セッションオブジェクトに mutable な値 (例えばリストまたは辞書) を入れて、
  続いてその値を変更した場合、セッションオブジェクトの ``changed()``
  メソッドを呼ばなければなりません。この場合、セッションにはそれが変更
  されたことを知る方法がありません。しかし、セッションオブジェクトを
  直接修正した場合、例えば値をセットしたり (つまり ``__setitem__``) キー
  を削除したり (例えば ``del`` や ``pop``) した場合は、セッションがその
  データを再度シリアライズする必要があることを自動的に知るので、
  ``changed()`` を呼ぶ必要はありません。どちらの場合でも ``changed()``
  を呼ぶことに害はないので、不確かなときはセッションデータを変更した後で
  そのメソッドを呼んでください。


.. index::
   single: pyramid_beaker
   single: Beaker
   single: session factory (alternates)

.. Using Alternate Session Factories

.. _using_alternate_session_factories:

代替セッションファクトリを使う
---------------------------------

.. At the time of this writing, exactly one alternate session factory
.. implementation exists, named ``pyramid_beaker``. This is a session factory
.. that uses the `Beaker <http://beaker.groovie.org/>`_ library as a backend.
.. Beaker has support for file-based sessions, database based sessions, and
.. encrypted cookie-based sessions.  See `the pyramid_beaker documentation
.. <http://docs.pylonsproject.org/projects/pyramid_beaker/en/latest/>`_ for more
.. information about ``pyramid_beaker``.

これを書いている時点では、 ``pyramid_beaker`` と呼ばれる唯一の代替
セッションファクトリ実装が存在します。これは、バックエンドとして
`Beaker <http://beaker.groovie.org/>`_ ライブラリを使用するセッションファクトリ
です。 Beaker は、ファイルに基づいたセッション、データベースに基づいた
セッション、暗号化された cookie に基づいたセッションをサポートしています。
``pyramid_beaker`` についての詳細は、 `pyramid_beaker ドキュメンテーション
<http://docs.pylonsproject.org/projects/pyramid_beaker/en/latest/>`_ を
参照してください。


.. index::
   single: session factory (custom)

.. Creating Your Own Session Factory

独自のセッションファクトリを作る
---------------------------------

.. If none of the default or otherwise available sessioning
.. implementations for :app:`Pyramid` suit you, you may create your own
.. session object by implementing a :term:`session factory`.  Your
.. session factory should return a :term:`session`.  The interfaces for
.. both types are available in
.. :class:`pyramid.interfaces.ISessionFactory` and
.. :class:`pyramid.interfaces.ISession`. You might use the cookie
.. implementation in the :mod:`pyramid.session` module as inspiration.

:app:`Pyramid` のためのデフォルトの、あるいは他の利用可能なセッション
実装のどれも適合しない場合、 :term:`session factory` を実装することで自分
のセッションオブジェクトを作成することができます。セッションファクトリは
:term:`session` を返す必要があります。それぞれの型に対するインタフェースは
:class:`pyramid.interfaces.ISessionFactory` と
:class:`pyramid.interfaces.ISession` で利用可能です。
:mod:`pyramid.session` モジュールの cookie 実装をインスピレーションとして
使用すると良いかもしれません。


.. index::
   single: flash messages

.. Flash Messages

フラッシュメッセージ
--------------------

.. "Flash messages" are simply a queue of message strings stored in the
.. :term:`session`.  To use flash messaging, you must enable a :term:`session
.. factory` as described in :ref:`using_the_default_session_factory` or
.. :ref:`using_alternate_session_factories`.

「フラッシュメッセージ」は :term:`session` に格納されるメッセージ文字列
の単なるキューです。フラッシュメッセージを使用するために、
:ref:`using_the_default_session_factory` または
:ref:`using_alternate_session_factories` で述べられているように
:term:`session factory` を有効にしなければなりません。


.. Flash messaging has two main uses: to display a status message only once to
.. the user after performing an internal redirect, and to allow generic code to
.. log messages for single-time display without having direct access to an HTML
.. template. The user interface consists of a number of methods of the
.. :term:`session` object.

フラッシュメッセージには2つの主な用途があります: 内部リダイレクトを行なった
後でユーザにステータスメッセージを一度だけ表示することと、単発の
ログメッセージを表示する汎用的なコードを HTML テンプレートに直接
アクセスすることなく可能にすることです。そのユーザインタフェースは
:term:`session` オブジェクトの多くのメソッドから構成されます。


.. index::
   single: session.flash

.. Using the ``session.flash`` Method

``session.flash`` メソッドを使う
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. To add a message to a flash message queue, use a session object's ``flash()``
.. method:

フラッシュメッセージ・キューにメッセージを追加するためには、
セッションオブジェクトの ``flash()`` メソッドを使用してください:


.. code-block:: python

   request.session.flash('mymessage')


.. The ``flash()`` method appends a message to a flash queue, creating the queue
.. if necessary. 

``flash()`` メソッドは、必要ならキューを作成して、フラッシュキューに
メッセージを追加します。


.. ``flash()`` accepts three arguments:

``flash()`` は3つの引数を受け取ります:


.. method:: flash(message, queue='', allow_duplicate=True)


.. The ``message`` argument is required.  It represents a message you wish to
.. later display to a user.  It is usually a string but the ``message`` you
.. provide is not modified in any way.

``message`` 引数は必須です。後でユーザに表示してほしいメッセージを表わします。
通常これは文字列ですが、提供された ``message`` は決して変更されません。


.. The ``queue`` argument allows you to choose a queue to which to append
.. the message you provide.  This can be used to push different kinds of
.. messages into flash storage for later display in different places on a
.. page.  You can pass any name for your queue, but it must be a string.
.. Each queue is independent, and can be popped by ``pop_flash()`` or
.. examined via ``peek_flash()`` separately.  ``queue`` defaults to the
.. empty string.  The empty string represents the default flash message
.. queue.

``queue`` 引数は、提供されたメッセージをどのキューに追加するか選択
できるようにします。これは後でページ上の異なる場所に表示するため異なる
種類のメッセージをフラッシュストレージに push するのに使えます。
キューにはどんな名前も渡せますが、文字列でなければなりません。
キューはそれぞれ独立していて、 ``pop_flash()`` によってポップしたり、
``peek_flash()`` によって別々に検査したりすることができます。
``queue`` のデフォルトは空の文字列です。空の文字列はデフォルトの
フラッシュメッセージ・キューを表わします。


.. code-block:: python

   request.session.flash(msg, 'myappsqueue')


.. The ``allow_duplicate`` argument defaults to ``True``.  If this is
.. ``False``, and you attempt to add a message value which is already
.. present in the queue, it will not be added.

``allow_duplicate`` 引数のデフォルトは ``True`` です。これが ``False`` で、
既にキューの中にあるメッセージ値を追加しようとした場合、そのメッセージは
追加されません。


.. index::
   single: session.pop_flash

.. Using the ``session.pop_flash`` Method

``session.pop_flash`` メソッドを使う
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. Once one or more messages have been added to a flash queue by the
.. ``session.flash()`` API, the ``session.pop_flash()`` API can be used to
.. pop an entire queue and return it for use.

``session.flash()`` API によって1つ以上のメッセージが一旦フラッシュキューに
追加されたら、それを使うために全キューをポップして返すのに
``session.pop_flash()`` API を使うことができます。


.. To pop a particular queue of messages from the flash object, use the session
.. object's ``pop_flash()`` method. This returns a list of the messages
.. that were added to the flash queue, and empties the queue.

フラッシュオブジェクトから特定のメッセージキューをポップするには、
セッションオブジェクトの ``pop_flash`` メソッドを使用してください。
これは、フラッシュキューに追加されたメッセージのリストを返し、
キューを空にします。


.. method:: pop_flash(queue='')

.. code-block:: python
   :linenos:

   >>> request.session.flash('info message')
   >>> request.session.pop_flash()
   ['info message']


.. Calling ``session.pop_flash()`` again like above without a corresponding call
.. to ``session.flash()`` will return an empty list, because the queue has already
.. been popped.

上記のように ``session.pop_flash`` をそれに対応する
``session.flash`` への呼び出しなしで再び呼び出すと、空のリストが返ります。
これはキューが既にポップされているからです。


.. code-block:: python
   :linenos:

   >>> request.session.flash('info message')
   >>> request.session.pop_flash()
   ['info message']
   >>> request.session.pop_flash()
   []

.. index::
   single: session.peek_flash

.. Using the ``session.peek_flash`` Method

``session.peek_flash`` メソッドを使う
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. Once one or more messages has been added to a flash queue by the
.. ``session.flash()`` API, the ``session.peek_flash()`` API can be used to
.. "peek" at that queue.  Unlike ``session.pop_flash()``, the queue is not
.. popped from flash storage.

``session.flash`` APIによって1つ以上のメッセージが一度フラッシュキューに
追加されたら、そのキューを「盗み見る」ために ``session.peek_flash`` API
を使うことができます。 ``session.pop_flash`` と異なり、キューは
フラッシュストレージからポップされません。


.. method:: peek_flash(queue='')

.. code-block:: python
   :linenos:

   >>> request.session.flash('info message')
   >>> request.session.peek_flash()
   ['info message']
   >>> request.session.peek_flash()
   ['info message']
   >>> request.session.pop_flash()
   ['info message']
   >>> request.session.peek_flash()
   []


.. index::
   single: preventing cross-site request forgery attacks
   single: cross-site request forgery attacks, prevention

.. Preventing Cross-Site Request Forgery Attacks

クロスサイトリクエストフォージェリ攻撃防御
---------------------------------------------

.. `Cross-site request forgery
.. <http://en.wikipedia.org/wiki/Cross-site_request_forgery>`_ attacks are a
.. phenomenon whereby a user with an identity on your website might click on a
.. URL or button on another website which secretly redirects the user to your
.. application to perform some command that requires elevated privileges.

`クロスサイトリクエストフォージェリ
<http://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B5%E3%82%A4%E3%83%88%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%82%B8%E3%82%A7%E3%83%AA>`_
攻撃は、あるウェブサイトの id を持つユーザが別のウェブサイト上の URL
またはボタンをクリックするときに起きる現象です。この攻撃は、高い特権が
必要な何らかのコマンドを実行するために、元のアプリケーションに対して
ユーザを秘密裡にリダイレクトさせます。


.. You can avoid most of these attacks by making sure that the correct *CSRF
.. token* has been set in an :app:`Pyramid` session object before performing any
.. actions in code which requires elevated privileges that is invoked via a form
.. post.  To use CSRF token support, you must enable a :term:`session factory`
.. as described in :ref:`using_the_default_session_factory` or
.. :ref:`using_alternate_session_factories`.

フォーム送信によって起動される高い特権を要求するコード中のあらゆる
アクションを行なう前に、正しい *CSRF トークン* が :app:`Pyramid`
セッションオブジェクトの中にセットされていることを確かめることで、
これらの攻撃のほとんどを回避することができます。
CSRF トークンサポートを使用するために、
:ref:`using_the_default_session_factory` または
:ref:`using_alternate_session_factories` で述べられているように
:term:`session factory` を有効にしなければなりません。


.. index::
   single: session.get_csrf_token

.. Using the ``session.get_csrf_token`` Method

``session.get_csrf_token`` メソッドを使う
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. To get the current CSRF token from the session, use the
.. ``session.get_csrf_token()`` method.

セッションから現在の CSRF トークンを得るには、
``session.get_csrf_token`` メソッドを使用してください。


.. code-block:: python

   token = request.session.get_csrf_token()


.. The ``session.get_csrf_token()`` method accepts no arguments.  It returns a
.. CSRF *token* string. If ``session.get_csrf_token()`` or
.. ``session.new_csrf_token()`` was invoked previously for this session, the
.. existing token will be returned.  If no CSRF token previously existed for
.. this session, a new token will be will be set into the session and returned.
.. The newly created token will be opaque and randomized.

``session.get_csrf_token`` メソッドは引数を取りません。このメソッドは
CSRF *トークン* 文字列を返します。もしこのセッション内でそれ以前に
``session.get_csrf_token`` または ``session.new_csrf_token`` が起動
されていれば、既存のトークンが返されます。もしこのセッション内でそれ以前に
CSRF トークンが存在しなければ、新しいトークンがセッションにセットされ
返されます。新しく作成されたトークンは不透明 (opaque) でランダム化 (randomized)
されています。


.. You can use the returned token as the value of a hidden field in a form that
.. posts to a method that requires elevated privileges.  The handler for the
.. form post should use ``session.get_csrf_token()`` *again* to obtain the
.. current CSRF token related to the user from the session, and compare it to
.. the value of the hidden form field.  For example, if your form rendering
.. included the CSRF token obtained via ``session.get_csrf_token()`` as a hidden
.. input field named ``csrf_token``:

返されたトークンは、高い特権を必要とするメソッドに POST するフォームの
中で hidden フィールドの値として使用することができます。フォームの
post ハンドラは、セッションからユーザに関連付けられた現在の CSRF トーク
ンを得るために *再度* ``session.get_csrf_token`` を使用して、それを
hidden フォームフィールドの値と比較するべきです。例えば、レンダリングされた
フォームが ``csrf_token`` という名前の hidden 入力フィールドとして
``session.get_csrf_token`` によって得られた CSRF トークンを含んでいた場合:


.. code-block:: python
   :linenos:

   token = request.session.get_csrf_token()
   if token != request.POST['csrf_token']:
       raise ValueError('CSRF token did not match')


.. index::
   single: session.new_csrf_token

.. Using the ``session.new_csrf_token`` Method

``session.new_csrf_token`` メソッドを使う
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. To explicitly add a new CSRF token to the session, use the
.. ``session.new_csrf_token()`` method.  This differs only from
.. ``session.get_csrf_token()`` inasmuch as it clears any existing CSRF token,
.. creates a new CSRF token, sets the token into the session, and returns the
.. token.

新しい CSRF トークンを明示的にセッションに追加するには
``session.new_csrf_token`` メソッドを使用してください。これは、既存の
CSRF トークンを消去して、新しい CSRF トークンを作成して、トークンを
セッションにセットして、トークンを返す点のみが ``session.get_csrf_token``
と異なります。


.. code-block:: python

   token = request.session.new_csrf_token()


