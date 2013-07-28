.. index::
   single: event
   single: subscriber
   single: INewRequest
   single: INewResponse
   single: NewRequest
   single: NewResponse

.. Using Events

.. _events_chapter:

イベントの使用
==============

.. An *event* is an object broadcast by the :app:`Pyramid` framework
.. at interesting points during the lifetime of an application.  You
.. don't need to use events in order to create most :app:`Pyramid`
.. applications, but they can be useful when you want to perform slightly
.. advanced operations.  For example, subscribing to an event can allow
.. you to run some code as the result of every new request.

*イベント* は、アプリケーションのライフタイムにおける興味深いポイントで
:app:`Pyramid` フレームワークによって通知されるオブジェクトです。
ほとんどの Pyramid アプリケーションでは、その作成においてイベントを使う
必要はありませんが、多少なりとも高度な操作を行ないたい場合、それらは
有用です。例えば、イベントの通知を受け取ることによって、すべての新しい
リクエストの結果としてあるコードを実行したりすることができます。


.. Events in :app:`Pyramid` are always broadcast by the framework.
.. However, they only become useful when you register a *subscriber*.  A
.. subscriber is a function that accepts a single argument named `event`:

:app:`Pyramid` において、イベントはフレームワークによって常に通知されます。
しかし、それらは *subscriber* を登録する場合にのみ有用になります。
subscriber は `event` という名前の単一の引数を受け取る関数です:


.. code-block:: python
   :linenos:

   def mysubscriber(event):
       print event


.. The above is a subscriber that simply prints the event to the console
.. when it's called.

上記は、呼び出されると単にコンソールにイベントを表示する subscriber です。


.. The mere existence of a subscriber function, however, is not sufficient to
.. arrange for it to be called.  To arrange for the subscriber to be called,
.. you'll need to use the
.. :meth:`pyramid.config.Configurator.add_subscriber` method or you'll
.. need to use the :func:`pyramid.events.subscriber` decorator to decorate a
.. function found via a :term:`scan`.

しかし、 subscriber 関数が単に存在するだけでは、それが呼び出されるように
なるのに十分ではありません。subscriber が呼び出されるようにするために、
:meth:`pyramid.config.Configurator.add_subscriber` メソッドを使用するか、
あるいは :func:`pyramid.events.subscriber` デコレータを使用して
:term:`scan` によって検索される関数をデコレートする必要があります。


.. Configuring an Event Listener Imperatively

イベントリスナーを命令的に設定する
------------------------------------------

.. You can imperatively configure a subscriber function to be called
.. for some event type via the
.. :meth:`~pyramid.config.Configurator.add_subscriber`
.. method (see also :term:`Configurator`):

あるイベントタイプに対して呼ばれる subscriber 関数を
:meth:`~pyramid.config.Configurator.add_subscriber` メソッドによって
命令的に設定することができます (:term:`Configurator` も参照):


.. code-block:: python
  :linenos:

  from pyramid.events import NewRequest

  from subscribers import mysubscriber

  # "config" below is assumed to be an instance of a 
  # pyramid.config.Configurator object

  config.add_subscriber(mysubscriber, NewRequest)


.. The first argument to
.. :meth:`~pyramid.config.Configurator.add_subscriber` is the
.. subscriber function (or a :term:`dotted Python name` which refers
.. to a subscriber callable); the second argument is the event type.

:meth:`~pyramid.config.Configurator.add_subscriber` に対する第1引数は
subscriber 関数 (あるいは subscriber callable を指す :term:`dotted
Python name`) です; 第2引数はイベントタイプです。


.. Configuring an Event Listener Using a Decorator

デコレータを使用してイベントリスナーを設定する
-----------------------------------------------

.. You can configure a subscriber function to be called for some event
.. type via the :func:`pyramid.events.subscriber` function.

あるイベントタイプに対して呼ばれる subscriber 関数を
:func:`pyramid.events.subscriber` 関数によって設定することができます。


.. code-block:: python
  :linenos:

  from pyramid.events import NewRequest
  from pyramid.events import subscriber

  @subscriber(NewRequest)
  def mysubscriber(event):
	  event.request.foo = 1


.. When the :func:`~pyramid.events.subscriber` decorator is used a
.. :term:`scan` must be performed against the package containing the
.. decorated function for the decorator to have any effect.

:func:`~pyramid.events.subscriber` デコレータが使用される場合、
デコレータが効果も発揮するために、デコレートされた関数を含むパッケージに
対して :term:`scan` が行なわれなければなりません。


.. Either of the above registration examples implies that every time the
.. :app:`Pyramid` framework emits an event object that supplies an
.. :class:`pyramid.events.NewRequest` interface, the ``mysubscriber`` function
.. will be called with an *event* object.

上記どちらの登録例でも、 :app:`Pyramid` フレームワークが
:class:`pyramid.events.NewRequest` インターフェースを提供するイベント
オブジェクトを emit するときは常に ``mysubscriber`` 関数が *event*
オブジェクトを伴って呼ばれるようになります。


.. As you can see, a subscription is made in terms of a *class* (such as
.. :class:`pyramid.events.NewResponse`).  The event object sent to a subscriber
.. will always be an object that possesses an :term:`interface`.  For
.. :class:`pyramid.events.NewResponse`, that interface is
.. :class:`pyramid.interfaces.INewResponse`. The interface documentation
.. provides information about available attributes and methods of the event
.. objects.

見て分かるように、 subscriber の登録は (:class:`pyramid.events.NewResponse`
のような) *クラス* の観点からなされます。 subscriber のもとへ送られた
イベントオブジェクトは常に :term:`interface` を所有するオブジェクトに
なります。 :class:`pyramid.events.NewResponse` については、その
インタフェースは :class:`pyramid.interfaces.INewResponse` です。
インタフェースのドキュメントは、イベントオブジェクトの利用可能な属性と
メソッドに関する情報を提供しています。


.. The return value of a subscriber function is ignored.  Subscribers to
.. the same event type are not guaranteed to be called in any particular
.. order relative to each other.

subscriber 関数の戻り値は無視されます。同じイベントタイプに対する複数の
subscriber が、お互いに対して相対的になんらかの特定の順番で呼ばれること
は保証されません。


.. All the concrete :app:`Pyramid` event types are documented in the
.. :ref:`events_module` API documentation.

すべての具体的な :app:`Pyramid` イベントタイプは、
:ref:`events_module` API ドキュメンテーションの中で文書化されます。


.. An Example

例
----------

.. If you create event listener functions in a ``subscribers.py`` file in
.. your application like so:

アプリケーション内の ``subscribers.py`` ファイルに、次のように
イベントリスナー関数を作成する場合:


.. code-block:: python
   :linenos:

   def handle_new_request(event):
       print 'request', event.request   

   def handle_new_response(event):
       print 'response', event.response


.. You may configure these functions to be called at the appropriate
.. times by adding the following code to your application's
.. configuration startup:

アプリケーションの初期設定部に次のコードを加えることにより、
これらの関数が適切なタイミングで呼ばれるように設定することができます:


.. code-block:: python
   :linenos:

   # config is an instance of pyramid.config.Configurator

   config.add_subscriber('myproject.subscribers.handle_new_request',
                         'pyramid.events.NewRequest')
   config.add_subscriber('myproject.subscribers.handle_new_response',
                         'pyramid.events.NewResponse')


.. Either mechanism causes the functions in ``subscribers.py`` to be
.. registered as event subscribers.  Under this configuration, when the
.. application is run, each time a new request or response is detected, a
.. message will be printed to the console.

どちらのメカニズムも ``subscribers.py`` の中の関数をイベント
subscriber として登録します。この設定の下でアプリケーションが実行された
場合、新しいリクエストまたはレスポンスが検知されるごとにコンソールに
メッセージが表示されるようになります。


.. Each of our subscriber functions accepts an ``event`` object and
.. prints an attribute of the event object.  This begs the question: how
.. can we know which attributes a particular event has?

これらの subscriber 関数は、 ``event`` オブジェクトを受け取ってイベント
オブジェクトの属性を表示します。これは次の質問を避けています: 特定の
イベントがどんな属性を持っているか、どのようにして知ることができますか。


.. We know that :class:`pyramid.events.NewRequest` event objects have a
.. ``request`` attribute, which is a :term:`request` object, because the
.. interface defined at :class:`pyramid.interfaces.INewRequest` says it must.
.. Likewise, we know that :class:`pyramid.interfaces.NewResponse` events have a
.. ``response`` attribute, which is a response object constructed by your
.. application, because the interface defined at
.. :class:`pyramid.interfaces.INewResponse` says it must
.. (:class:`pyramid.events.NewResponse` objects also have a ``request``).

:class:`pyramid.events.NewRequest` イベントオブジェクトが ``request``
属性を持っていることを私たちは知っています。それは :term:`request`
オブジェクトです。なぜなら :class:`pyramid.interfaces.INewRequest` で
インタフェースがそのように定義されているからです。同様に、
:class:`pyramid.interfaces.NewResponse` のイベントが ``response`` 属性を
持っていることを私たちは知っています。それはアプリケーションによって
構築されたレスポンスオブジェクトです。なぜなら
:class:`pyramid.interfaces.INewResponse` でインタフェースがそのように
定義されているからです (:class:`pyramid.events.NewResponse` オブジェクトは
``request`` も持っています)。
