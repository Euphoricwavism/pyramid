.. Much Ado About Traversal

.. _much_ado_about_traversal_chapter:

========================
トラバーサルの空騒ぎ
========================

.. (Or, why you should care about it)

(あるいは、なぜあなたはそれに関心を持たなければならないか)


.. note::

   .. This chapter was adapted, with permission, from a blog post by `Rob
   .. Miller <http://blog.nonsequitarian.org/>`_, originally published at
   .. http://blog.nonsequitarian.org/2010/much-ado-about-traversal/ .

   この章は、元々は `Rob Miller <http://blog.nonsequitarian.org/>`_ による
   ブログポストとして
   http://blog.nonsequitarian.org/2010/much-ado-about-traversal/ で公開
   されていたもので、許可を得て追加されました。


.. Traversal is an alternative to :term:`URL dispatch` which allows
.. :app:`Pyramid` applications to map URLs to code.

トラバーサルは URL ディスパッチの代替手段で、 :app:`Pyramid`
アプリケーションが URL をコードに写像する際に用いられます。


.. note::
   
   .. Ex-Zope users who are already familiar with traversal and view lookup
   .. conceptually may want to skip directly to the :ref:`traversal_chapter`
   .. chapter, which discusses technical details.  This chapter is mostly aimed
   .. at people who have previous :term:`Pylons` experience or experience in
   .. another framework which does not provide traversal, and need an
   .. introduction to the "why" of traversal.

   すでにトラバーサルとビュー検索の概念に精通している元 Zope ユーザは
   :ref:`traversal_chapter` 章に直接スキップすると良いでしょう。
   そこでは技術的詳細について議論します。本章は、主に元 :term:`Pylons`
   経験者や、トラバーサルを提供しない他のフレームワークの経験者で、
   トラバーサルの「なぜ」に関するイントロダクションを必要とする読者を
   対象としています。


.. Some folks who have been using Pylons and its Routes-based URL matching for a
.. long time are being exposed for the first time, via :app:`Pyramid`, to new
.. ideas such as ":term:`traversal`" and ":term:`view lookup`" as a way to route
.. incoming HTTP requests to callable code.  Some of the same folks believe that
.. traversal is hard to understand.  Others question its usefulness; URL
.. matching has worked for them so far, why should they even consider dealing
.. with another approach, one which doesn't fit their brain and which doesn't
.. provide any immediately obvious value?

長年にわたり Pylons およびその Routes ベースの URL マッチングを使用してきた
人々は、やって来た HTTP リクエストを callable コードにルーティングする方法
として ":term:`traversal`" や ":term:`view lookup`" のような新しい考えに、
:app:`Pyramid` によって初めて出会います。彼らの一部は、トラバーサルは
理解するのが難しいと感じます。他の人々はその有用性について尋ねます; URL
マッチングは、これまで問題なく動いていました。なぜ、彼らの脳に適合しない、
即座に明白な価値を提供しないような、別のアプローチを扱うことを考慮
しなければならないのでしょうか。


.. You can be assured that if you don't want to understand traversal, you don't
.. have to.  You can happily build :app:`Pyramid` applications with only
.. :term:`URL dispatch`.  However, there are some straightforward, real-world
.. use cases that are much more easily served by a traversal-based approach than
.. by a pattern-matching mechanism.  Even if you haven't yet hit one of these
.. use cases yourself, understanding these new ideas is worth the effort for any
.. web developer so you know when you might want to use them.  :term:`Traversal`
.. is actually a straightforward metaphor easily comprehended by anyone who's
.. ever used a run-of-the-mill file system with folders and files.

トラバーサルを理解したくなければ、その必要はないことを保証します。
:term:`URL dispatch` だけを用いた :app:`Pyramid` アプリケーションを
好きなだけ構築することができます。しかし、パターンマッチングメカニズム
よりもトラバーサルに基づくアプローチの方がはるかに簡単に扱うことのできる、
いくつかの直裁的 (straightforward) な現実世界でのユースケースがあります。
あなた自身がまだこれらのユースケースに出会ったことがなくても、いつそれを
使うのかを知るために、これらの新しい考え方を理解することはあらゆるウェブ
開発者にとって努力の価値があります。 :term:`Traversal` は、実際にはフォルダ
とファイルを持つありふれたファイルシステムを使用した人なら誰でも簡単に理解
できる直裁的なメタファーです。


.. URL Dispatch

.. index::
   single: URL dispatch

URL ディスパッチ
----------------

.. Let's step back and consider the problem we're trying to solve.  An
.. HTTP request for a particular path has been routed to our web
.. application.  The requested path will possibly invoke a specific
.. :term:`view callable` function defined somewhere in our app.  We're
.. trying to determine *which* callable function, if any, should be
.. invoked for a given requested URL.

一歩下がって、解決しようとしている問題について考察してみましょう。
特定のパスに対する HTTP リクエストがウェブアプリケーションにルーティング
されています。リクエストされたパスは、おそらくアプリのどこかで定義された
特定の :term:`view callable` 関数を起動するでしょう。私たちは、与えられた
リクエスト URL に対して、もし存在するなら *どの* callable 関数を起動
すべきかを決定しようとしています。


.. Many systems, including Pyramid, offer a simple solution.  They offer the
.. concept of "URL matching".  URL matching approaches this problem by parsing
.. the URL path and comparing the results to a set of registered "patterns",
.. defined by a set of regular expressions, or some other URL path templating
.. syntax.  Each pattern is mapped to a callable function somewhere; if the
.. request path matches a specific pattern, the associated function is called.
.. If the request path matches more than one pattern, some conflict resolution
.. scheme is used, usually a simple order precedence so that the first match
.. will take priority over any subsequent matches.  If a request path doesn't
.. match any of the defined patterns, a "404 Not Found" response is returned.

Pyramid を含む多くのシステムは単純な解決策を提供しています。それらは、
「URL マッチング」の概念を提供します。 URL マッチングは、URL パスを
解析して、その結果を正規表現あるいは他の何らかの URL パス・テンプレート
構文によって定義された複数の登録済みの「パターン」と比較することによって
この問題に取り組みます。それぞれのパターンは、どこかにある callable 関数
に写像されます;リクエストパスが特定のパターンとマッチした場合、関連する
関数が呼ばれます。リクエストパスが複数のパターンとマッチする場合、
何らかの競合解消スキームが使用されます。通常これは単純な先着順で、
その結果最初のマッチは後の任意のマッチより高い優先度を持つでしょう。
リクエストパスが定義されたパターンのうちのどれともマッチしない場合、
"404 Not Found" レスポンスが返されます。


.. In Pyramid, we offer an implementation of URL matching which we call
.. :term:`URL dispatch`.  Using :app:`Pyramid` syntax, we might have a match
.. pattern such as ``/{userid}/photos/{photoid}``, mapped to a ``photo_view()``
.. function defined somewhere in our code.  Then a request for a path such as
.. ``/joeschmoe/photos/photo1`` would be a match, and the ``photo_view()``
.. function would be invoked to handle the request.  Similarly,
.. ``/{userid}/blog/{year}/{month}/{postid}`` might map to a
.. ``blog_post_view()`` function, so ``/joeschmoe/blog/2010/12/urlmatching``
.. would trigger the function, which presumably would know how to find and
.. render the ``urlmatching`` blog post.

Pyramid は :term:`URL dispatch` と呼ばれる URL マッチングの実装を提供
しています。 :app:`Pyramid` シンタックスを用いて
``/{userid}/photos/{photoid}`` のようなマッチパターンがあり、コードの
どこかに定義された ``photo_view()`` 関数に写像されるとします。このとき、
``/joeschmoe/photos/photo1`` のようなパスに対するリクエストがマッチして、
``photo_view()`` 関数がそのリクエストを扱うために起動されます。同様に、
``/{userid}/blog/{year}/{month}/{postid}`` が ``blog_post_view()``
関数に写像するとします。すると、 ``/joeschmoe/blog/2010/12/urlmatching``
はその関数を起動します。この関数は、おそらく ``urlmatching`` ブログポストを
見つけてレンダリングする方法を知っているでしょう。


.. Historical Refresher

歴史の復習
--------------------

.. Now that we've refreshed our understanding of :term:`URL dispatch`, we'll dig
.. in to the idea of traversal.  Before we do, though, let's take a trip down
.. memory lane.  If you've been doing web work for a while, you may remember a
.. time when we didn't have fancy web frameworks like :term:`Pylons` and
.. :app:`Pyramid`.  Instead, we had general purpose HTTP servers that primarily
.. served files off of a file system.  The "root" of a given site mapped to a
.. particular folder somewhere on the file system.  Each segment of the request
.. URL path represented a subdirectory.  The final path segment would be either
.. a directory or a file, and once the server found the right file it would
.. package it up in an HTTP response and send it back to the client.  So serving
.. up a request for ``/joeschmoe/photos/photo1`` literally meant that there was
.. a ``joeschmoe`` folder somewhere, which contained a ``photos`` folder, which
.. in turn contained a ``photo1`` file.  If at any point along the way we find
.. that there is not a folder or file matching the requested path, we return a
.. 404 response.

いま :term:`URL dispatch` についての理解を復習したので、トラバーサル
に関するアイデアを深追いします。しかし、その前に思い出をたどる旅に
出かけましょう。あなたが長い間ウェブの仕事をしているなら、
:term:`Pylons` や :app:`Pyramid` のような高機能な (fancy) ウェブ
フレームワークがなかった時代を思い出せるかもしれません。代わりに、
主としてファイルシステムからファイルを返す多目的 HTTP サーバーがありました。
あるサイトの "root" は、ファイルシステムのどこかにある特定のフォルダに
写像されます。リクエスト URL パスのセグメントは、それぞれサブディレクトリ
を表わします。最後のパスセグメントは、ディレクトリかファイルのいずれか
になります。そして、適切なファイルを見つけたら、サーバはそれを HTTP
レスポンスで包んでクライアントに送ります。したがって、
``/joeschmoe/photos/photo1`` に対するリクエストに serve up することは、
文字通りどこかに ``joeschmoe`` フォルダがあり、それは ``photos`` フォルダ
を含み、そしてそれは ``photo1`` ファイルを含んでいることを意味しました。
それを見つける途中のどの時点でも、フォルダまたはファイルがリクエスト
されたパスとマッチしない場合、 404 レスポンスを返します。


.. As the web grew more dynamic, however, a little bit of extra complexity was
.. added.  Technologies such as CGI and HTTP server modules were developed.
.. Files were still looked up on the file system, but if the file ended with
.. (for example) ``.cgi`` or ``.php``, or if it lived in a special folder,
.. instead of simply sending the file to the client the server would read the
.. file, execute it using an interpreter of some sort, and then send the output
.. from this process to the client as the final result.  The server
.. configuration specified which files would trigger some dynamic code, with the
.. default case being to just serve the static file.

しかし、ウェブがより動的になるとともに、少しだけ余分な複雑さが追加され
ました。 CGI や HTTP サーバモジュールのような技術が開発されました。
ファイルはまだファイルシステム上で検索されましたが、ファイルが (例えば)
``.cgi`` や ``.php`` で終わっている場合、あるいは単にファイルが特定の
フォルダの中にある場合、クライアントにファイルを送る代わりに、サーバは
ファイルを読み込み、ある種類のインタープリタを使用してそれを実行し、その後
最終結果としてこのプロセスからの出力をクライアントに送ります。サーバ
設定には、どのファイルが動的なコードを起動するか、そしてデフォルトケース
では静的ファイルを返すことが指定されました。


.. Traversal (aka Resource Location)

.. index::
   single: traversal

トラバーサル (別名リソース location)
------------------------------------

.. Believe it or not, if you understand how serving files from a file system
.. works, you understand traversal.  And if you understand that a server might do
.. something different based on what type of file a given request specifies,
.. then you understand view lookup.

信じられないかもしれませんが、ファイルシステムからファイルを返す仕組みが
どのように働くかを理解すれば、トラバーサルを理解したことになります。
また、与えられたリクエストによって指定されるファイルの種類に基づいて
サーバが異なる動作をすることを理解すれば、ビュー検索を理解したことになります。


.. The major difference between file system lookup and traversal is that a file
.. system lookup steps through nested directories and files in a file system
.. tree, while traversal steps through nested dictionary-type objects in a
.. :term:`resource tree`.  Let's take a detailed look at one of our example
.. paths, so we can see what I mean:

ファイルシステム検索とトラバーサルの主な違いは、ファイルシステム検索は
ファイルシステムツリーの入れ子のディレクトリとファイルを検索するのに対して、
トラバーサルは :term:`resource tree` の中の入れ子の辞書形式のオブジェクトを
検索することです。例のパスのうちの1つに注目してみましょう。そうすれば、
私が何を言いたいか分かるでしょう:


.. The path ``/joeschmoe/photos/photo1``, has four segments: ``/``,
.. ``joeschmoe``, ``photos`` and ``photo1``.  With file system lookup we might
.. have a root folder (``/``) containing a nested folder (``joeschmoe``), which
.. contains another nested folder (``photos``), which finally contains a JPG
.. file (``photo1``).  With traversal, we instead have a dictionary-like root
.. object.  Asking for the ``joeschmoe`` key gives us another dictionary-like
.. object.  Asking this in turn for the ``photos`` key gives us yet another
.. mapping object, which finally (hopefully) contains the resource that we're
.. looking for within its values, referenced by the ``photo1`` key.

パス ``/joeschmoe/photos/photo1`` には、 ``/``, ``joeschmoe``,
``photos``, ``photo1`` という4つのセグメントがあります。
ファイルシステム検索では、ルートフォルダ (``/``) が入れ子のフォルダ
(``joeschmoe``) を含み、それが別の入れ子のフォルダ (``photos``) を含み、
それが最後に JPG ファイル (``photo1``) を含んでいるでしょう。
トラバーサルでは、その代りに、辞書形式の root オブジェクトを持っています。
``joeschmoe`` キーを求めることは別の辞書形式のオブジェクトを与えます。
次にこのオブジェクトに ``photos`` キーを求めることは別の写像オブジェク
トを与えます。それは最終的に (おそらく) ``photo1`` キーによって参照される
値の中に探しているリソースを含んでいます。


.. In pure Python terms, then, the traversal or "resource location"
.. portion of satisfying the ``/joeschmoe/photos/photo1`` request
.. will look something like this pseudocode:

pure Python の用語では、 ``/joeschmoe/photos/photo1`` に対するリクエストを
満たすトラバーサルあるいは「リソース location」部分は、この擬似コードの
ように見えるでしょう:


::

    get_root()['joeschmoe']['photos']['photo1']


.. ``get_root()`` is some function that returns a root traversal
.. :term:`resource`.  If all of the specified keys exist, then the returned
.. object will be the resource that is being requested, analogous to the JPG
.. file that was retrieved in the file system example.  If a :exc:`KeyError` is
.. generated anywhere along the way, :app:`Pyramid` will return 404.  (This
.. isn't precisely true, as you'll see when we learn about view lookup below,
.. but the basic idea holds.)

``get_root()`` は root トラバーサル :term:`resource` を返す何らかの関数
です。指定されたキーがすべて存在すれば、返されたオブジェクトはファイル
システムの例において検索された JPG ファイルと類似して、リクエストされて
いるリソースになるでしょう。途中のどこかの場所で :exc:`KeyError` が発生
した場合 :app:`Pyramid` は 404 を返します。 (これは正確には真実ではあり
ません。そのことは後でビュー検索に関して学んだときに分かるでしょう。
しかし基本概念は押さえています。)


.. What Is a "Resource"?

.. index::
   single: resource

「リソース」とは何か?
---------------------

.. "Files on a file system I understand", you might say.  "But what are these
.. nested dictionary things?  Where do these objects, these 'resources', live?
.. What *are* they?"

「ファイルシステム上のファイルなら理解している」とあなたは言うかもしれません。
「でも、この入れ子の辞書とは何?  これらのオブジェクト、これらの『リソース』は
どこにある?  それは一体 *何* なの?」


.. Since :app:`Pyramid` is not a highly opinionated framework, it makes no
.. restriction on how a :term:`resource` is implemented; a developer can
.. implement them as he wishes.  One common pattern used is to persist all of
.. the resources, including the root, in a database as a graph.  The root object
.. is a dictionary-like object.  Dictionary-like objects in Python supply a
.. ``__getitem__`` method which is called when key lookup is done.  Under the
.. hood, when ``adict`` is a dictionary-like object, Python translates
.. ``adict['a']`` to ``adict.__getitem__('a')``.  Try doing this in a Python
.. interpreter prompt if you don't believe us:

:app:`Pyramid` はあまり主張の強いフレームワークではないので、
:term:`resource` がどのように実装されるかに対する制限を設けません;
開発者は望むようにそれを実装することができます。使用される1つの一般的な
パターンは、 root を含むリソースのすべてをグラフとしてデータベース中に永続化
することです。 root オブジェクトは辞書風オブジェクトです。 Python において
辞書風オブジェクトは、キー検索が行われるときに呼ばれる ``__getitem__``
メソッドを提供します。内部では、 ``adict`` が辞書風オブジェクトであるときに、
Python は ``adict['a']`` を ``adict.__getitem__('a')`` に翻訳します。
信じてもらえなければ、 Python インタープリタプロンプトの中でこれを
してみてください:


.. code-block:: text
   :linenos:

   Python 2.4.6 (#2, Apr 29 2010, 00:31:48) 
   [GCC 4.4.3] on linux2
   Type "help", "copyright", "credits" or "license" for more information.
   >>> adict = {}
   >>> adict['a'] = 1
   >>> adict['a']
   1
   >>> adict.__getitem__('a')
   1


.. The dictionary-like root object stores the ids of all of its subresources as
.. keys, and provides a ``__getitem__`` implementation that fetches them.  So
.. ``get_root()`` fetches the unique root object, while
.. ``get_root()['joeschmoe']`` returns a different object, also stored in the
.. database, which in turn has its own subresources and ``__getitem__``
.. implementation, etc.  These resources might be persisted in a relational
.. database, one of the many "NoSQL" solutions that are becoming popular these
.. days, or anywhere else, it doesn't matter.  As long as the returned objects
.. provide the dictionary-like API (i.e. as long as they have an appropriately
.. implemented ``__getitem__`` method) then traversal will work.

辞書風の root オブジェクトは、そのサブリソースのすべての id をキーとして
格納し、それを取得する ``__getitem__`` 実装を提供します。したがって、
``get_root()`` はユニークな root オブジェクトを取得します。その一方で
``get_root()['joeschmoe']`` は、データベースなどに格納されている異なる
オブジェクトを返します。そのオブジェクトは、それ自身のサブリソースと
``__getitem__`` 実装を持ちます。これらのリソースは、リレーショナル
データベースや、最近ポピュラーになっている多くの "NoSQL" ソリューションの
うちの1つ、あるいはそれ以外に永続化されるかもしれませんが、それは重要では
ありません。返されたオブジェクトが辞書風 API を提供する限り (つまり、
それらに ``__getitem__`` メソッドが適切に実装されている限り) トラバーサル
は動作します。


.. In fact, you don't need a "database" at all.  You could use plain
.. dictionaries, with your site's URL structure hard-coded directly in
.. the Python source.  Or you could trivially implement a set of objects
.. with ``__getitem__`` methods that search for files in specific
.. directories, and thus precisely recreate the traditional mechanism of
.. having the URL path mapped directly to a folder structure on the file
.. system.  Traversal is in fact a superset of file system lookup.

実際、「データベース」は全く必要ありません。プレーンな辞書とともに
Python ソース内に直接ハードコーディングされたサイトの URL 構造を
使用することができます。あるいは、特定のディレクトリの中でファイルを
検索して、 URL パスをファイルシステム上のフォルダ構造に直接写像する
従来のメカニズムを正確に模倣する ``__getitem__`` メソッドを持つ
オブジェクトを自明に実装することができます。トラバーサルは
実際のところファイルシステム検索のスーパーセットです。


.. .. note:: See the chapter entitled :ref:`resources_chapter` for a more
..    technical overview of resources.

.. note::

   より技術的なリソースの概観については、 :ref:`resources_chapter`
   の章を参照してください。


.. View Lookup

.. index::
   single: view lookup

ビュー検索
-----------

.. At this point we're nearly there.  We've covered traversal, which is the
.. process by which a specific resource is retrieved according to a specific URL
.. path.  But what is "view lookup"?

この時点で、ほとんどそこまで来ています。これまでにトラバーサルをカバーしました。
それは特定のリソースが特定の URL パスによって検索されるプロセスです。
しかし「ビュー検索」とは何でしょうか。


.. The need for view lookup is simple: there is more than one possible action
.. that you might want to take after finding a :term:`resource`.  With our photo
.. example, for instance, you might want to view the photo in a page, but you
.. might also want to provide a way for the user to edit the photo and any
.. associated metadata.  We'll call the former the ``view`` view, and the latter
.. will be the ``edit`` view.  (Original, I know.)  :app:`Pyramid` has a
.. centralized view :term:`application registry` where named views can be
.. associated with specific resource types.  So in our example, we'll assume
.. that we've registered ``view`` and ``edit`` views for photo objects, and that
.. we've specified the ``view`` view as the default, so that
.. ``/joeschmoe/photos/photo1/view`` and ``/joeschmoe/photos/photo1`` are
.. equivalent.  The edit view would sensibly be provided by a request for
.. ``/joeschmoe/photos/photo1/edit``.

ビュー検索の必要性は単純です: :term:`resource` を見つけた後で、取りうる
可能なアクションが複数あります。これまでの写真の例で、例えば、ページの
中で写真を見たいと思うかもしれません。しかし、さらにユーザが写真や関連
する任意のメタデータを編集する方法も提供したいと思うかもしれません。
前者を ``view`` ビューと呼ぶことにして、後者を ``edit`` ビューと
呼ぶことにします。 (独創的ですね。) :app:`Pyramid` には中央集権的な
ビュー :term:`application registry` があり、名前付きのビューを特定の
リソースタイプに関連付けることができます。そこで、この例において
photo オブジェクトに対する ``view`` ビューと ``edit`` ビューを登録して、
デフォルトとして ``view`` ビューを指定したと仮定しましょう。その結果、
``/joeschmoe/photos/photo1/view`` と ``/joeschmoe/photos/photo1`` は
等価になります。 edit ビューは ``/joeschmoe/photos/photo1/edit`` に
対するリクエストによって sensibly 提供されるでしょう。


.. Hopefully it's clear that the first portion of the edit view's URL path is
.. going to resolve to the same resource as the non-edit version, specifically
.. the resource returned by ``get_root()['joeschmoe']['photos']['photo1']``.
.. But traveral ends there; the ``photo1`` resource doesn't have an ``edit``
.. key.  In fact, it might not even be a dictionary-like object, in which case
.. ``photo1['edit']`` would be meaningless.  When the :app:`Pyramid` resource
.. location has been resolved to a *leaf* resource, but the entire request path
.. has not yet been expended, the *very next* path segment is treated as a
.. :term:`view name`.  The registry is then checked to see if a view of the
.. given name has been specified for a resource of the given type.  If so, the
.. view callable is invoked, with the resource passed in as the related
.. ``context`` object (also available as ``request.context``).  If a view
.. callable could not be found, :app:`Pyramid` will return a "404 Not Found"
.. response.

おそらく、 edit ビューの URL パスの最初の部分は非 edit バージョンと同じ
リソース (特に ``get_root()['joeschmoe']['photos']['photo1']`` によって
返されたリソース) になることは明らかです。しかし、トラバーサルはそこで
終了します; ``photo1`` リソースには ``edit`` キーがありません。実際、
それは辞書風オブジェクトではないかもしれません。その場合には
``photo1['edit']`` は無意味でしょう。 :app:`Pyramid` リソース location
が *末端の* リソースに解決され、しかしリクエストパス全体がまだ消費され
ていない場合、 *直後の* パスセグメントが :term:`view name` として扱われます。
そして、与えられた名前のビューが与えられたタイプのリソースに対して指定
されているかどうかを確かめるためレジストリがチェックされます。もしそうなら、
渡されたリソースを関連する ``context`` オブジェクトとして (さらに
``request.context`` としても利用可能です) ビュー callable が起動されます。
ビュー callable が見つけられなければ、 :app:`Pyramid` は "404 Not Found"
レスポンスを返します。


.. You might conceptualize a request for ``/joeschmoe/photos/photo1/edit`` as
.. ultimately converted into the following piece of Pythonic pseudocode:

``/joeschmoe/photos/photo1/edit`` に対するリクエストは、最終的に次のような
Python 風の擬似コードに変換されると概念化することができます:


::

  context = get_root()['joeschmoe']['photos']['photo1']
  view_callable = get_view(context, 'edit')
  request.context = context
  view_callable(request)


.. The ``get_root`` and ``get_view`` functions don't really exist.  Internally,
.. :app:`Pyramid` does something more complicated.  But the example above
.. is a reasonable approximation of the view lookup algorithm in pseudocode.

``get_root`` 関数と ``get_view`` 関数は、実際には存在しません。内部的に、
:app:`Pyramid` はより複雑なことをしています。しかし、上記の例は擬似コードに
よるビュー検索アルゴリズムの合理的な近似です。


.. Use Cases

ユースケース
------------

.. Why should we care about traversal?  URL matching is easier to explain, and
.. it's good enough, right?

なぜトラバーサルに関心を持たなければならないのでしょうか。 URL マッチの
方が説明が簡単だし、それで十分ではないでしょうか?


.. In some cases, yes, but certainly not in all cases.  So far we've had very
.. structured URLs, where our paths have had a specific, small number of pieces,
.. like this:

いくつかの場合は yes です。しかし、もちろんすべての場合にではありません。
これまで、非常に構造化された URL を扱ってきました。パスはこのように、
少数の特定の部分を持っていました:


::

  /{userid}/{typename}/{objectid}[/{view_name}]


.. In all of the examples thus far, we've hard coded the typename value,
.. assuming that we'd know at development time what names were going to be used
.. ("photos", "blog", etc.).  But what if we don't know what these names will
.. be?  Or, worse yet, what if we don't know *anything* about the structure of
.. the URLs inside a user's folder?  We could be writing a CMS where we want the
.. end user to be able to arbitrarily add content and other folders inside his
.. folder.  He might decide to nest folders dozens of layers deep.  How will you
.. construct matching patterns that could account for every possible combination
.. of paths that might develop?

これまでのすべての例において、どの名前 ("photos", "blog" など) が
使われるか開発時に知っているという前提で、 typename 値をハードコーディング
していました。しかし、もしこれらの名前がどうなるか知らなかったら、
どうしますか。あるいは、さらに悪いことに、ユーザのフォルダ内部の URL
構造について *何も* 知らなかったら、どうしますか。私たちは CMS を書く
こともあるでしょう。その場合、エンドユーザがフォルダ内部にコンテンツや
他のフォルダを任意に追加できることが望まれます。エンドユーザは、フォルダ
を何層も深く入れ子にすることに決めるかもしれません。発展する可能性のある
パスのあらゆる組み合わせを考慮できるマッチパターンを、どのように構築
するのでしょうか。


.. It might be possible, but it certainly won't be easy.  The matching
.. patterns are going to become complex quickly as you try to handle all
.. of the edge cases.

それは可能かもしれませんが、確実に容易ではないでしょう。すべてのエッジ
ケースを扱おうとすると、マッチパターンは急速に複雑になるでしょう。


.. With traversal, however, it's straightforward.  Twenty layers of nesting
.. would be no problem.  :app:`Pyramid` will happily call ``__getitem__`` as
.. many times as it needs to, until it runs out of path segments or until a
.. resource raises a :exc:`KeyError`.  Each resource only needs to know how to
.. fetch its immediate children, the traversal algorithm takes care of the rest.
.. Also, since the structure of the resource tree can live in the database and
.. not in the code, it's simple to let users modify the tree at runtime to set
.. up their own personalized "directory" structures.

しかし、トラバーサルを使えば直裁的です。20 層のネストも問題になりません。
パスセグメントを使い果たすかリソースが :exc:`KeyError` を上げるまで、
:app:`Pyramid` は適切に ``__getitem__`` を必要な回数だけ呼び出すでしょう。
それぞれのリソースは、単にその直接の子を取得する方法を知っている必要が
あります。トラバーサルアルゴリズムが残りの面倒を見ます。さらに、リソース木
の構造はコード中ではなくデータベースに入れることができるので、ユーザ自身に
個人化された「ディレクトリ」構造を構築するために、実行時にユーザに
リソース木を修正させることも簡単にできます。


.. Another use case in which traversal shines is when there is a need to support
.. a context-dependent security policy.  One example might be a document
.. management infrastructure for a large corporation, where members of different
.. departments have varying access levels to the various other departments'
.. files.  Reasonably, even specific files might need to be made available to
.. specific individuals.  Traversal does well here if your resources actually
.. represent the data objects related to your documents, because the idea of a
.. resource authorization is baked right into the code resolution and calling
.. process.  Resource objects can store ACLs, which can be inherited and/or
.. overridden by the subresources.

トラバーサルが活躍する別のユースケースは、コンテキスト依存のセキュリティ
ポリシーをサポートする必要があるときです。一例を挙げると、大企業のため
のドキュメント管理インフラです。そこでは、異なる部門のメンバーは他の様々
な部門のファイルに対して異なるアクセスレベルを持ちます。当然、特定の
ファイルは特定の個人だけが利用可能である必要もあります。リソースが実際に
ドキュメントと関連付けられたデータオブジェクトを表現している場合、
トラバーサルはここでうまく働きます。なぜなら、コード解決と呼び出しプロセスに、
リソース認可という考え方が埋め込まれているからです。リソースオブジェクトは
ACL を格納することができます。それはサブリソースによって継承や
オーバーライドをすることができます。


.. If each resource can thus generate a context-based ACL, then whenever view
.. code is attempting to perform a sensitive action, it can check against that
.. ACL to see whether the current user should be allowed to perform the action.
.. In this way you achieve so called "instance based" or "row level" security
.. which is considerably harder to model using a traditional tabular approach.
.. :app:`Pyramid` actively supports such a scheme, and in fact if you register
.. your views with guard permissions and use an authorization policy,
.. :app:`Pyramid` can check against a resource's ACL when deciding whether or
.. not the view itself is available to the current user.

もしそれぞれのリソースがこのようにコンテキストベースの ACL を生成できるなら、
ビューコードが機密なアクションを実行しようとする場合は常に、カレント
ユーザーがそのアクションを行なうことが認められるかどうかを確かめるために、
ACL に対してチェックすることができます。このようにして、従来の表形式
アプローチを使用してモデル化することが極めて難しい、いわゆる
「インスタンスベース」あるいは「列レベル」のセキュリティを達成します。
:app:`Pyramid` はそのようなスキームを積極的にサポートします。そして実際、
ガードパーミッションとともにビューを登録して、認可ポリシーを使用すれば、
:app:`Pyramid` はビューそれ自身がカレントユーザーに対して利用可能かどうかを
決定する際にリソースの ACL に対してチェックすることができます。


.. In summary, there are entire classes of problems that are more easily served
.. by traversal and view lookup than by :term:`URL dispatch`.  If your problems
.. don't require it, great: stick with :term:`URL dispatch`.  But if you're
.. using :app:`Pyramid` and you ever find that you *do* need to support one of
.. these use cases, you'll be glad you have traversal in your toolkit.

要約すると、 :term:`URL dispatch` を使うよりもトラバーサルとビュー検索
を使った方が容易に扱える問題の大きなクラスがあります。あなたの問題が
それを要求しない場合、素晴らしい: ずっと :term:`URL dispatch` を
使い続けてください。しかし、 :app:`Pyramid` を使用していて、いつかこれらの
ユースケースのうちのいずれかをサポートする *必要がある* と分かれば、
ツールキットにトラバーサルを持っていることに感謝することでしょう。


.. note::

   .. It is even possible to mix and match :term:`traversal` with
   .. :term:`URL dispatch` in the same :app:`Pyramid` application. See the
   .. :ref:`hybrid_chapter` chapter for details.

   同じ :app:`Pyramid` アプリケーションの中で :term:`traversal` と
   :term:`URL dispatch` を組み合わせることは可能です。詳細については、
   :ref:`hybrid_chapter` 章を参照してください。
