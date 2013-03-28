.. index::
   single: ZCA
   single: Zope Component Architecture
   single: zope.component
   single: application registry
   single: getSiteManager
   single: getUtility

.. Using the Zope Component Architecture in :app:`Pyramid`

.. _zca_chapter:

:app:`Pyramid` で Zope コンポーネントアーキテクチャを使う
==========================================================

.. Under the hood, :app:`Pyramid` uses a :term:`Zope Component
.. Architecture` component registry as its :term:`application registry`.
.. The Zope Component Architecture is referred to colloquially as the
.. "ZCA."

内部的に、 :app:`Pyramid` は :term:`Zope Component Architecture` の
コンポーネントレジストリを :term:`application registry` として使用します。
Zope コンポーネントアーキテクチャーは会話では "ZCA" と呼ばれます。


.. The ``zope.component`` API used to access data in a traditional Zope
.. application can be opaque.  For example, here is a typical "unnamed
.. utility" lookup using the :func:`zope.component.getUtility` global API
.. as it might appear in a traditional Zope application:

従来の Zope アプリケーションでデータにアクセスするために使用される
``zope_component`` API は不明瞭なところがありました。
例えば、これは従来の Zope アプリケーションに現われるような、
:func:`zope.component.getUtility` グローバル API を使用した
典型的な「無名ユーティリティ」の検索です:


.. ignore-next-block
.. code-block:: python
   :linenos:

   from pyramid.interfaces import ISettings
   from zope.component import getUtility
   settings = getUtility(ISettings)


.. After this code runs, ``settings`` will be a Python dictionary.  But
.. it's unlikely that any "civilian" will be able to figure this out just
.. by reading the code casually.  When the ``zope.component.getUtility``
.. API is used by a developer, the conceptual load on a casual reader of
.. code is high.

このコードが実行された後で、 ``settings`` は Python 辞書になります。
しかし、「一般人」がコードをざっと読むだけでこれを理解することは困難です。
開発者によって ``zope.component.getUtility`` API が使用されている場合、
コードのカジュアルな読者にとって概念的な負荷は高いです。


.. While the ZCA is an excellent tool with which to build a *framework*
.. such as :app:`Pyramid`, it is not always the best tool with which
.. to build an *application* due to the opacity of the ``zope.component``
.. APIs.  Accordingly, :app:`Pyramid` tends to hide the presence of the
.. ZCA from application developers.  You needn't understand the ZCA to
.. create a :app:`Pyramid` application; its use is effectively only a
.. framework implementation detail.

ZCA は :app:`Pyramid` のような *フレームワーク* を構築するには優れた
ツールですが、 ``zope.component`` API の不透明さにより、それは必ずしも
アプリケーションを構築するための最良のツールとは限りません。そのため、
:app:`Pyramid` はアプリケーション開発者に対して ZCA の存在を見せない
ようにする傾向にあります。 :app:`Pyramid` アプリケーションを作成するために
ZCA を理解する必要はありません; その使用は事実上フレームワークの単なる
実装詳細です。


.. However, developers who are already used to writing :term:`Zope`
.. applications often still wish to use the ZCA while building a
.. :app:`Pyramid` application; :mod:`pyramid` makes this possible.

しかし、既に :term:`Zope` アプリケーションを書くことに慣れている開発者
は、今もしばしば :app:`Pyramid` アプリケーションを構築する際に ZCA を
使用することを望みます; :mod:`pyramid` はこれを可能にします。


.. Using the ZCA Global API in a :app:`Pyramid` Application

.. index::
   single: get_current_registry
   single: getUtility
   single: getSiteManager
   single: ZCA global API

:app:`Pyramid` アプリケーションで ZCA グローバル API を使う
-----------------------------------------------------------

.. :term:`Zope` uses a single ZCA registry -- the "global" ZCA registry
.. -- for all Zope applications that run in the same Python process,
.. effectively making it impossible to run more than one Zope application
.. in a single process.

:term:`Zope` は、同一の Python プロセスで動作するすべての Zope アプリケーション
に対して単一の ZCA レジストリ (「グローバル」 ZCA レジストリ) を使用します。
そのため、単一のプロセスで複数の Zope アプリケーションを起動することは事実上
不可能です。


.. However, for ease of deployment, it's often useful to be able to run more
.. than a single application per process.  For example, use of a
.. :term:`PasteDeploy` "composite" allows you to run separate individual WSGI
.. applications in the same process, each answering requests for some URL
.. prefix.  This makes it possible to run, for example, a TurboGears application
.. at ``/turbogears`` and a :app:`Pyramid` application at ``/pyramid``, both
.. served up using the same :term:`WSGI` server within a single Python process.

しかし、デプロイの容易さのためには、1プロセスで複数のアプリケーションが
実行できることは多くの場合に有用です。例えば、 :term:`PasteDeploy`
"composite" を使用することによって、同じプロセスで独立した個別の WSGI
アプリケーションを起動することができます。それぞれのアプリケーションは
特定の URL プリフィックスのリクエストに応答します。これによって、例えば
``/turbogears`` で TurboGears アプリケーションを、 ``/pyramid`` で
:app:`Pyramid` アプリケーションを実行することが可能になります。
両方のアプリケーションは、単一の Python プロセス内の同じ :term:`WSGI`
サーバを使用して実行されます。


.. Most production Zope applications are relatively large, making it
.. impractical due to memory constraints to run more than one Zope
.. application per Python process.  However, a :app:`Pyramid` application
.. may be very small and consume very little memory, so it's a reasonable
.. goal to be able to run more than one :app:`Pyramid` application per
.. process.

ほとんどのプロダクション Zope アプリケーションは比較的大規模で、メモリの
制約によって Python プロセスあたり複数の Zope アプリケーションを実行する
ことは実用的ではありません。しかし、 :app:`Pyramid` アプリケーションは
非常に小さくメモリをほとんど消費しないかもしれません。したがって、1プロセス
あたり複数の :app:`Pyramid` アプリケーションが実行できることは合理的な
ゴールです。


.. In order to make it possible to run more than one :app:`Pyramid`
.. application in a single process, :app:`Pyramid` defaults to using a
.. separate ZCA registry *per application*.

単一のプロセスで複数の :app:`Pyramid` アプリケーションを起動できるように
するために、 :app:`Pyramid` はデフォルトで *アプリケーションごとに*
個別の ZCA レジストリを使用します。


.. While this services a reasonable goal, it causes some issues when
.. trying to use patterns which you might use to build a typical
.. :term:`Zope` application to build a :app:`Pyramid` application.
.. Without special help, ZCA "global" APIs such as
.. ``zope.component.getUtility`` and ``zope.component.getSiteManager``
.. will use the ZCA "global" registry.  Therefore, these APIs
.. will appear to fail when used in a :app:`Pyramid` application,
.. because they'll be consulting the ZCA global registry rather than the
.. component registry associated with your :app:`Pyramid` application.

これは合理的なゴールを提供していますが、 :app:`Pyramid` アプリケーション
を構築するために典型的な :term:`Zope` アプリケーションを構築するのに
用いられる利用パターンを適用しようとすると、いくつかの問題を引き起こします。
特別なことをしなければ、 ``zope.component.getUtility`` や
``zope.component.getSiteManager`` のような ZCA 「グローバル」 API は
ZCA 「グローバル」レジストリを使用します。そのため、これらの API は
:app:`Pyramid` アプリケーションの中で使用された場合にうまく動かないように
見えるでしょう。なぜなら、それらが :app:`Pyramid` アプリケーションに
関連付けられたコンポーネントレジストリではなく、 ZCA グローバルレジストリを
参照するからです。


.. There are three ways to fix this: by disusing the ZCA global API
.. entirely, by using
.. :meth:`pyramid.config.Configurator.hook_zca` or by passing
.. the ZCA global registry to the :term:`Configurator` constructor at
.. startup time.  We'll describe all three methods in this section.

これを修正するためには3つの方法があります: ZCA グローバル API を
まったく使わないか、 :meth:`pyramid.config.Configurator.hook_zca` を
使用するか、あるいはスタートアップ時点で ZCA グローバルレジストリを
:term:`Configurator` コンストラクタに渡すか。このセクションでは
これら3つの方法についてすべて記述します。


.. index::
   single: request.registry

.. Disusing the Global ZCA API

.. _disusing_the_global_zca_api:

グローバル ZCA API を使わない
+++++++++++++++++++++++++++++

.. ZCA "global" API functions such as ``zope.component.getSiteManager``,
.. ``zope.component.getUtility``, ``zope.component.getAdapter``, and
.. ``zope.component.getMultiAdapter`` aren't strictly necessary.  Every
.. component registry has a method API that offers the same
.. functionality; it can be used instead.  For example, presuming the
.. ``registry`` value below is a Zope Component Architecture component
.. registry, the following bit of code is equivalent to
.. ``zope.component.getUtility(IFoo)``:

``zope.component.getSiteManager``, ``zope.component.getUtility``,
``zope.component.getAdapter``, ``zope.component.getMultiAdapter``
のような ZCA 「グローバル」 API 関数は、厳密に言えば必須ではありません。
すべてのコンポーネントレジストリには、同じ機能を持つメソッド API があります;
それを代わりに使うことができます。例えば、下記の ``registry`` 値が
Zope コンポーネントアーキテクチャーのコンポーネントレジストリであると
仮定すると、次のコード片は ``zope.component.getUtility(IFoo)`` と等価です:


.. code-block:: python
   :linenos:

   registry.getUtility(IFoo)


.. The full method API is documented in the ``zope.component`` package,
.. but it largely mirrors the "global" API almost exactly.

完全なメソッド API は ``zope.component`` パッケージの中で文書化されます。
しかし、大部分は「グローバル」 API のほぼ忠実なミラーです。


.. If you are willing to disuse the "global" ZCA APIs and use the method
.. interface of a registry instead, you need only know how to obtain the
.. :app:`Pyramid` component registry.

「グローバル」 ZCA API の使用をやめて、代わりにレジストリのメソッド
インタフェースを使用すれば、あとは :app:`Pyramid` コンポーネントレジストリ
を取得する方法を知る必要があるだけです。


.. There are two ways of doing so:

それをするのに 2 つの方法があります:


.. - use the :func:`pyramid.threadlocal.get_current_registry`
..   function within :app:`Pyramid` view or resource code.  This will
..   always return the "current" :app:`Pyramid` application registry.

- :app:`Pyramid` ビューかリソースコードの中で
  :func:`pyramid.threadlocal.get_current_registry` 関数を使用する。
  これは常に「現在の」 :app:`Pyramid` アプリケーションレジストリを返します。


.. - use the attribute of the :term:`request` object named ``registry``
..   in your :app:`Pyramid` view code, eg. ``request.registry``.  This
..   is the ZCA component registry related to the running
..   :app:`Pyramid` application.

- :app:`Pyramid` ビューコードの中で :term:`request` オブジェクトの
  ``registry`` という名前の属性を使用する (例えば ``request.registry``)。
  これは実行中の :app:`Pyramid` アプリケーションに関係付けられた
  ZCA コンポーネントレジストリです。


.. See :ref:`threadlocals_chapter` for more information about
.. :func:`pyramid.threadlocal.get_current_registry`.

:func:`pyramid.threadlocal.get_current_registry` についての
詳細は :ref:`threadlocals_chapter` を参照してください。


.. index::
   single: hook_zca (configurator method)

.. Enabling the ZCA Global API by Using ``hook_zca``

.. _hook_zca:

``hook_zca`` を使って ZCA グローバル API を有効にする
+++++++++++++++++++++++++++++++++++++++++++++++++++++

.. Consider the following bit of idiomatic :app:`Pyramid` startup code:

次の慣用句的な :app:`Pyramid` スタートアップコードの断片を考えてください:


.. code-block:: python
   :linenos:

   from zope.component import getGlobalSiteManager
   from pyramid.config import Configurator

   def app(global_settings, **settings):
       config = Configurator(settings=settings)
       config.include('some.other.package')
       return config.make_wsgi_app()


.. When the ``app`` function above is run, a :term:`Configurator` is
.. constructed.  When the configurator is created, it creates a *new*
.. :term:`application registry` (a ZCA component registry).  A new
.. registry is constructed whenever the ``registry`` argument is omitted
.. when a :term:`Configurator` constructor is called, or when a
.. ``registry`` argument with a value of ``None`` is passed to a
.. :term:`Configurator` constructor.

上記の ``app`` 関数が実行される時に :term:`Configurator` が構築されます。
configurator が作成される場合、それは *新しい* :term:`application
registry` (ZCA コンポーネントレジストリ) を作成します。 :term:`Configurator`
コンストラクタが呼ばれる時に ``registry`` 引数が省略された場合、
または :term:`Configurator` コンストラクタに ``registry`` 引数として
``None`` 値が渡された場合、新しいレジストリが構築されます。


.. During a request, the application registry created by the Configurator
.. is "made current".  This means calls to
.. :func:`~pyramid.threadlocal.get_current_registry` in the thread
.. handling the request will return the component registry associated
.. with the application.

リクエスト中は、 Configurator によって作成されたアプリケーションレジストリ
が「現在の値」になります。これは、リクエストを扱うスレッドの中で
:func:`~pyramid.threadlocal.get_current_registry` を呼び出すと、
アプリケーションに関連付けられたコンポーネントレジストリが返ることを
意味します。


.. As a result, application developers can use ``get_current_registry``
.. to get the registry and thus get access to utilities and such, as per
.. :ref:`disusing_the_global_zca_api`.  But they still cannot use the
.. global ZCA API.  Without special treatment, the ZCA global APIs will
.. always return the global ZCA registry (the one in
.. ``zope.component.globalregistry.base``).

その結果、アプリケーション開発者は ``get_current_registry`` を使用して
レジストリを取得して、 :ref:`disusing_the_global_zca_api` と同じように
ユーティリティその他にアクセスすることができます。
しかし、依然としてグローバル ZCA API は使用することができません。
特別な処置をしなければ、 ZCA グローバル API は常に ZCA グローバル
レジストリ (``zope.component.globalregistry.base`` の中のもの) を返します。


.. To "fix" this and make the ZCA global APIs use the "current"
.. :app:`Pyramid` registry, you need to call
.. :meth:`~pyramid.config.Configurator.hook_zca` within your setup code.
.. For example:

これを「修正」して、 ZCA グローバル API に「現在の」 :app:`Pyramid`
レジストリを使用させるためには、セットアップコード内で
:meth:`~pyramid.config.Configurator.hook_zca` を呼ぶ必要があります。
例えば:


.. code-block:: python
   :linenos:

   from zope.component import getGlobalSiteManager
   from pyramid.config import Configurator

   def app(global_settings, **settings):
       config = Configurator(settings=settings)
       config.hook_zca()
       config.include('some.other.application')
       return config.make_wsgi_app()


.. We've added a line to our original startup code, line number 6, which
.. calls ``config.hook_zca()``.  The effect of this line under the hood
.. is that an analogue of the following code is executed:

オリジナルのスタートアップコードに ``config.hook_zca()`` を呼び出す
1行を追加しました (6行目)。この行による内部的な影響は、次のコードに類似
したことが行われるというものです:


.. code-block:: python
   :linenos:

   from zope.component import getSiteManager
   from pyramid.threadlocal import get_current_registry
   getSiteManager.sethook(get_current_registry)


.. This causes the ZCA global API to start using the :app:`Pyramid`
.. application registry in threads which are running a :app:`Pyramid`
.. request.

これは、 :app:`Pyramid` リクエストを実行しているスレッドの中で
ZCA グローバル API が :app:`Pyramid` アプリケーションレジストリを使用
するようにします。


.. Calling ``hook_zca`` is usually sufficient to "fix" the problem of
.. being able to use the global ZCA API within a :app:`Pyramid`
.. application.  However, it also means that a Zope application that is
.. running in the same process may start using the :app:`Pyramid`
.. global registry instead of the Zope global registry, effectively
.. inverting the original problem.  In such a case, follow the steps in
.. the next section, :ref:`using_the_zca_global_registry`.

``hook_zca`` を呼ぶことは、通常 :app:`Pyramid` アプリケーションの中で
グローバル ZCA API を使用できるようにする問題を「修正」するには十分です。
しかし、それはまた、同じプロセスで動作している Zope アプリケーションが
Zope グローバルレジストリの代わりに :app:`Pyramid` のグローバルレジストリ
を使用するようになることを意味します。事実上、オリジナルの問題の逆です。
そのような場合には、次のセクション :ref:`using_the_zca_global_registry` の
ステップに従ってください。


.. index::
   single: get_current_registry
   single: getGlobalSiteManager
   single: ZCA global registry

.. Enabling the ZCA Global API by Using The ZCA Global Registry

.. _using_the_zca_global_registry:

ZCA グローバルレジストリを使って ZCA グローバル API を有効にする
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

.. You can tell your :app:`Pyramid` application to use the ZCA global
.. registry at startup time instead of constructing a new one:

スタートアップ時に、 :app:`Pyramid` アプリケーションに対して、
新しいレジストリを構築する代わりに ZCA グローバルレジストリを使用するように
指定することができます:


.. code-block:: python
   :linenos:

   from zope.component import getGlobalSiteManager
   from pyramid.config import Configurator

   def app(global_settings, **settings):
       globalreg = getGlobalSiteManager()
       config = Configurator(registry=globalreg)
       config.setup_registry(settings=settings)
       config.include('some.other.application')
       return config.make_wsgi_app()


.. Lines 5, 6, and 7 above are the interesting ones.  Line 5 retrieves
.. the global ZCA component registry.  Line 6 creates a
.. :term:`Configurator`, passing the global ZCA registry into its
.. constructor as the ``registry`` argument.  Line 7 "sets up" the global
.. registry with Pyramid-specific registrations; this is code that is
.. normally executed when a registry is constructed rather than created,
.. but we must call it "by hand" when we pass an explicit registry.

上記の 5, 6, 7 行目が興味のある行です。5行目は、グローバル ZCA
コンポーネントレジストリを検索しています。6行目は、コンストラクタに
``registry`` 引数としてグローバル ZCA レジストリを渡して、
:term:`Configurator` を作成しています。7行目は、グローバルレジストリに
Pyramid 特有の設定を「セットアップ」します; これは通常レジストリが
作成されるときというよりは構築される時に実行されるコードですが、明示的に
レジストリを渡す場合はそれを「手動で」呼ばなければなりません。


.. At this point, :app:`Pyramid` will use the ZCA global registry
.. rather than creating a new application-specific registry; since by
.. default the ZCA global API will use this registry, things will work as
.. you might expect a Zope app to when you use the global ZCA API.

この段階では、 :app:`Pyramid` は新しいアプリケーション固有のレジストリを
作成するのではなく ZCA グローバルレジストリを使用します; デフォルトで
ZCA グローバル API がこのレジストリを使用するので、グローバル ZCA API を
使用するときに Zope アプリに期待されるように物事はうまく機能するでしょう。
