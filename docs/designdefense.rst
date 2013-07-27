.. Defending Pyramid's Design

.. _design_defense:

Pyramid の設計擁護
==========================

.. From time to time, challenges to various aspects of :app:`Pyramid` design are
.. lodged.  To give context to discussions that follow, we detail some of the
.. design decisions and trade-offs here.  In some cases, we acknowledge that the
.. framework can be made better and we describe future steps which will be taken
.. to improve it; in some cases we just file the challenge as noted, as
.. obviously you can't please everyone all of the time.

時々 :app:`Pyramid` の設計の様々な様相に対する挑戦が申し立てられます。
続く議論にコンテキストを与えるために、私たちは、ここで設計に関する意思
決定とトレードオフのうちのいくつかを詳述します。ある場合には、フレーム
ワークをより一層よくすることができ、それを改善するために取られる将来の
ステップについて記述することを認めます; ある場合には、注意として、挑戦
を単に記録します。明らかに、常に皆を喜ばせることができるとは限らないた
めです。


Pyramid Provides More Than One Way to Do It
-------------------------------------------

.. A canon of Python popular culture is "TIOOWTDI" ("there is only one way to do
.. it", a slighting, tongue-in-cheek reference to Perl's "TIMTOWTDI", which is
.. an acronym for "there is more than one way to do it").

Python のポピュラーな文化の基準は、 "TIOOWTDI" です (「やり方はひとつだけ」
-- これは Perl の「やり方は一つじゃない」の頭文字 "TIMTOWTDI" に対する
軽視的でからかい半分の言及です)


.. :app:`Pyramid` is, for better or worse, a "TIMTOWTDI" system.  For example,
.. it includes more than one way to resolve a URL to a :term:`view callable`:
.. via :term:`url dispatch` or :term:`traversal`.  Multiple methods of
.. configuration exist: :term:`imperative configuration`, :term:`configuration
.. decoration`, and :term:`ZCML` (optionally via :term:`pyramid_zcml`). It works
.. with multiple different kinds of persistence and templating systems.  And so
.. on.  However, the existence of most of these overlapping ways to do things
.. are not without reason and purpose: we have a number of audiences to serve,
.. and we believe that TIMTOWTI at the web framework level actually *prevents* a
.. much more insidious and harmful set of duplication at higher levels in the
.. Python web community.

:app:`Pyramid` は良くも悪くも "TIMTOWTDI" なシステムです。例えば、
:app:`Pyramid` は URL を :term:`view callable` に解決する方法を複数
含んでいます: :term:`url dispatch` と :term:`traversal` です。
configuration のための複数の方法があります:
:term:`imperative configuration`, :term:`configuration decoration`,
:term:`ZCML` (オプションで :term:`pyramid_zcml` 経由で)。
多数の異なる種類の永続化とテンプレートのシステムで作動します。などなど。
しかしながら、これらの物事を行うのに重複する方法のほとんどは、
存在する理由や目的がないわけではありません: 私たちには serve すべき多く
の聴衆がいます。そして私たちは、ウェブフレームワークレベルの TIMTOWTI が、
はるかに油断ならず有害な、 Python ウェブコミュニティー内での高レベルな
重複の数々を実際に *防ぐ* と信じています。


.. :app:`Pyramid` began its life as :mod:`repoze.bfg`, written by a team of
.. people with many years of prior :term:`Zope` experience.  The idea of
.. :term:`traversal` and the way :term:`view lookup` works was stolen entirely
.. from Zope.  The authorization subsystem provided by :app:`Pyramid` is a
.. derivative of Zope's.  The idea that an application can be *extended* without
.. forking is also a Zope derivative.

:app:`Pyramid` は、 :mod:`repoze.bfg` としてその生を開始しました。
それ以前の何年もの :term:`Zope` の経験を持つ人々のチームによって書かれています。
:term:`traversal` に関するアイデアと :term:`view lookup` が動作する方法
は、完全に Zope から取られました。 :app:`Pyramid` によって提供される認可
(authorization) サブシステムは Zope からの派生です。アプリケーションを
fork せずに *拡張する* ことができるというアイデアも Zope からの派生です。


.. Implementations of these features were *required* to allow the :app:`Pyramid`
.. authors to build the bread-and-butter CMS-type systems for customers in the
.. way they were accustomed to building them.  No other system save Zope itself
.. had such features.  And Zope itself was beginning to show signs of its age.
.. We were becoming hampered by consequences of its early design mistakes.
.. Zope's lack of documentation was also difficult to work around: it was hard
.. to hire smart people to work on Zope applications, because there was no
.. comprehensive documentation set to point them at which explained "it all" in
.. one consumble place, and it was too large and self-inconsistent to document
.. properly.  Before :mod:`repoze.bfg` went under development, its authors
.. obviously looked around for other frameworks that fit the bill.  But no
.. non-Zope framework did.  So we embarked on building :mod:`repoze.bfg`.

これらの特徴の実装は、 :app:`Pyramid` の作者達が仕事で顧客のための CMS
タイプのシステムを自分達が慣れたやり方で構築できるように *要求* されました。
そのような特徴を持つシステムは、 Zope 自身を除いて他にありませんでした。
また、 Zope 自身は、その寿命のサインを示し始めていました。私たちは、
初期の設計ミスの結果によって阻害されるようになっていました。
Zope のドキュメンテーションの不足もまた、 work around を難しくしていました:
一箇所でそれらを指す「それですべて」といえるような包括的なドキュメント集がなく、
適切に説明するには大きすぎて自己一貫性がないことから、
Zope アプリケーションで働くために賢い人々を雇うことが難しい状況でした。
:mod:`repoze.bfg` を開発する前に、作者達は当然条件を満たした他のフレー
ムワークを探しましたが、非 Zope フレームワークで条件を満たすものは
ありませんでした。そこで、私たちは :mod:`repoze.bfg` の構築に乗り出しました。


.. As the result of our research, however, it became apparent that, despite the
.. fact that no *one* framework had all the features we required, lots of
.. existing frameworks had good, and sometimes very compelling ideas.  In
.. particular, :term:`URL dispatch` is a more direct mechanism to map URLs to
.. code.

しかし、調査の結果どの *単一の* フレームワークも私たちが要求した特徴を
すべて持つものはないという事実にもかかわらず、多くの既存のフレームワーク
には優れた、時には非常に魅力的なアイデアがあることが明らかになりました。
特に、 :term:`URL dispatch` は URL をコードにマッピングするより直接的な
メカニズムです。


.. So although we couldn't find a framework save for Zope that fit our needs,
.. and while we incorporated a lot of Zope ideas into BFG, we also emulated the
.. features we found compelling in other frameworks (such as :term:`url
.. dispatch`).  After the initial public release of BFG, as time went on,
.. features were added to support people allergic to various Zope-isms in the
.. system, such as the ability to configure the application using
.. :term:`imperative configuration` and :term:`configuration decoration` rather
.. than solely using :term:`ZCML`, and the elimination of the required use of
.. :term:`interface` objects.  It soon became clear that we had a system that
.. was very generic, and was beginning to appeal to non-Zope users as well as
.. ex-Zope users.

そのため、 Zope を除いて私たちのニーズに適合したフレームワークを見つけ
ることはできませんでしたが (そして私たちは BFG に Zope の多くのアイデア
を組み込みましたが)、さらに他のフレームワークの中で見つけた魅力的な特徴
(例えば :term:`url dispatch`) をエミュレートしました。 BFG の最初の
パブリックリリースの後、時間が進むにつれて、システムに含まれる Zope 主義に
対してアレルギーを持つ人々をサポートするために様々な特徴が付け加えられました。
例えば :term:`ZCML` を単独で使用するのではなく
:term:`imperative configuration` と :term:`configuration decoration` を
使用してアプリケーションを設定する機能や、 :term:`interface` オブジェクトの
required な使用の除去などです。すぐに、このシステムが非常に一般的で、
元 Zope ユーザにも非 Zope ユーザにもアピールすることははっきりしました。


.. As the result of this generalization, it became obvious BFG shared 90% of its
.. featureset with the featureset of Pylons 1, and thus had a very similar
.. target market.  Because they were so similar, choosing between the two
.. systems was an exercise in frustration for an otherwise non-partisan
.. developer.  It was also strange for the Pylons and BFG development
.. communities to be in competition for the same set of users, given how similar
.. the two frameworks were.  So the Pylons and BFG teams began to work together
.. to form a plan to merge.  The features missing from BFG (notably :term:`view
.. handler` classes, flash messaging, and other minor missing bits), were added,
.. to provide familiarity to ex-Pylons users.  The result is :app:`Pyramid`.

この一般化の結果、 BFG は Pylons 1 の機能セットと 90% を共有し、
したがってターゲット市場が非常に類似していることが明らかになりました。
それらが非常に似ていたので、 2つのシステムのどちらかを選ぶことは、その
他の面では超党派の開発者にとってはフラストレーションの溜まる訓練でした。
さらに、 Pylons と BFG の開発者コミュニティが同じユーザセットに対して
競争しているのは、2つのフレームワークがどれくらい類似しているかを考えると
奇妙なことでした。そこで Pylons と BFG のチームはマージ計画を立てるため
の活動を共に始めました。 BFG に足りない特徴 (特に :term:`view handler`
クラス、フラッシュメッセージ、その他のマイナーな欠落) が、元 Pylons ユーザ
に親しさを供給するために加えられました。その結果が :app:`Pyramid` です。


.. The Python web framework space is currently notoriously balkanized.  We're
.. truly hoping that the amalgamation of components in :app:`Pyramid` will
.. appeal to at least two currently very distinct sets of users: Pylons and BFG
.. users.  By unifying the best concepts from Pylons and BFG into a single
.. codebase and leaving the bad concepts from their ancestors behind, we'll be
.. able to consolidate our efforts better, share more code, and promote our
.. efforts as a unit rather than competing pointlessly.  We hope to be able to
.. shortcut the pack mentality which results in a *much larger* duplication of
.. effort, represented by competing but incredibly similar applications and
.. libraries, each built upon a specific low level stack that is incompatible
.. with the other.  We'll also shrink the choice of credible Python web
.. frameworks down by at least one.  We're also hoping to attract users from
.. other communities (such as Zope's and TurboGears') by providing the features
.. they require, while allowing enough flexibility to do things in a familiar
.. fashion.  Some overlap of functionality to achieve these goals is expected
.. and unavoidable, at least if we aim to prevent pointless duplication at
.. higher levels.  If we've done our job well enough, the various audiences will
.. be able to coexist and cooperate rather than firing at each other across some
.. imaginary web framework DMZ.

Python ウェブフレームワークの世界は現在、分断された状態にあるという悪評
が立っています。私たちは、 :app:`Pyramid` におけるコンポーネントの融合
が少なくとも 2 つの、現在は非常に distinct なユーザセットに訴えること
を本当に望んでいます: Pylons と BFG のユーザです。 Pylons と BFG から
最良の概念を単一のコードベースの中へ統一し、それらの背後にある先祖からの
悪い概念を捨て去ることで、私たちの努力をより集約し、より多くのコードを
共有し、意味のない競争ではなく単一のユニットとして私たちの努力を促進する
ことができるでしょう。私たちは、努力の *非常に大きな* 重複に繋がる
pack mentality をショートカットできることを望みます。このような
mentality は、 競争的だが信じられないほど似たようなアプリケーションとラ
イブラリ(お互い互換性がない特定の低レベルスタックの上に構築された) によっ
て表わされます。さらに、信頼できる Python ウェブフレームワークの選択を
少なくとも1つ縮小するでしょう。また、 Zope や TurboGears のような他の
コミュニティーのユーザに対して、物事を慣れた方法で行う十分な柔軟性を
可能にしながら要求する特徴を提供することで、私たちはそのようなユーザを
引きつけることを望んでいます。これらのゴールを達成するためにある程度の
機能のオーバーラップは想定され、避けることができません。少なくとも私た
ちが高レベルの無意味な複製を防ぐつもりならば。もし私たちが仕事を十分に
果たしていれば、様々な聴衆は、ある種の想像上のウェブフレームワーク DMZ
で互いに発砲しあうのではなく、共存し協力することができるでしょう。


Pyramid Uses A Zope Component Architecture ("ZCA") Registry
-----------------------------------------------------------

.. :app:`Pyramid` uses a :term:`Zope Component Architecture` (ZCA) "component
.. registry" as its :term:`application registry` under the hood.  This is a
.. point of some contention.  :app:`Pyramid` is of a :term:`Zope` pedigree, so
.. it was natural for its developers to use a ZCA registry at its inception.
.. However, we understand that using a ZCA registry has issues and consequences,
.. which we've attempted to address as best we can.  Here's an introspection
.. about :app:`Pyramid` use of a ZCA registry, and the trade-offs its usage
.. involves.

:app:`Pyramid` はアプリケーションレジストリとして内部で Zope の
:term:`Zope Component Architecture` (ZCA) "コンポーネントレジストリ" を
使用します。これはいくらか議論のポイントです。 :app:`Pyramid` は
:term:`Zope` の系列です。したがって、開発者がその初期で ZCA レジストリ
を使用することは自然でした。しかしながら、私たちは ZCA レジストリの使用
には issues と consequences があることを理解しています。私たちはできる
限りそれに対処しようとしました。以下は :app:`Pyramid` で ZCA レジストリ
を使用すること、およびその使用によるトレードオフの内省です。


Problems
++++++++

.. The global API that may be used to access data in a ZCA component registry
.. is not particularly pretty or intuitive, and sometimes it's just plain
.. obtuse.  Likewise, the conceptual load on a casual source code reader of code
.. that uses the ZCA global API is somewhat high.  Consider a ZCA neophyte
.. reading the code that performs a typical "unnamed utility" lookup using the
.. :func:`zope.component.getUtility` global API:

ZCA コンポーネントレジストリの中のデータにアクセスするために使用される
グローバル API は、特に pretty でなく直感的でもありません。また、時々
それはまったく plain obtuse です。同様に ZCA のグローバル API を使用す
るコードのカジュアルソースコードリーディングをする人に対する概念的負荷
は多少高いです。 ZCA 新参者が :func:`zope.component.getUtility` の
グローバル API を使用した典型的な「無名のユーティリティ」の検索を行なう
コードを読むことを考慮してください:


.. ignore-next-block
.. code-block:: python
   :linenos:

   from pyramid.interfaces import ISettings
   from zope.component import getUtility
   settings = getUtility(ISettings)


.. After this code runs, ``settings`` will be a Python dictionary.  But it's
.. unlikely that any civilian would know that just by reading the code.  There
.. are a number of comprehension issues with the bit of code above that are
.. obvious.

このコードが実行された後、 ``settings`` は Python 辞書になります。しかし、
普通の人はコードを読むだけではそれは知りようもありません。上記のコード断片には
明らかに、理解しやすさに多くの問題があります。


.. First, what's a "utility"?  Well, for the purposes of this discussion, and
.. for the purpose of the code above, it's just not very important.  If you
.. really want to know, you can read `this
.. <http://www.muthukadan.net/docs/zca.html#utility>`_.  However, still, readers
.. of such code need to understand the concept in order to parse it.  This is
.. problem number one.

まず、 "utility" とは何でしょうか。もちろん、この議論のために、そして
上記のコードの目的にとって、それはそれほど重要ではありません。本当に知り
たければ、 `これ <http://www.muthukadan.net/docs/zca.html#utility>`_
を読めば良いでしょう。しかしながら、コードの読者はコードをパースするた
めに依然としてその概念を理解する必要があります。これは問題その1です。


.. Second, what's this ``ISettings`` thing?  It's an :term:`interface`.  Is that
.. important here?  Not really, we're just using it as a key for some lookup
.. based on its identity as a marker: it represents an object that has the
.. dictionary API, but that's not very important in this context.  That's
.. problem number two.

次に、この ``ISettings`` というのは一体何でしょう。それは
:term:`interface` です。それはここで重要ですか？ そうではありません。
単に同一性に基づく検索のためのキーにマーカーとして使用しているだけです:
それは辞書 API を持ったオブジェクトを表していますが、この文脈において
それはあまり重要ではありません。これが問題その2です。


.. Third of all, what does the ``getUtility`` function do?  It's performing a
.. lookup for the ``ISettings`` "utility" that should return.. well, a utility.
.. Note how we've already built up a dependency on the understanding of an
.. :term:`interface` and the concept of "utility" to answer this question: a bad
.. sign so far.  Note also that the answer is circular, a *really* bad sign.

第三に、 ``getUtility`` 関数は何を行いますか？ それは ``ISettings``
「ユーティリティ」の検索を行なって、それを返すはずです……さて、ユーティリティとは。
ここまでで :term:`interface` についての理解と、この質問に答えるために
「ユーティリティ」概念に対する依存性をどのように構築したか注意してください:
非常に悪いサインです。さらに答えが循環的であることに注目してください。
*本当に* 悪いサインです。


.. Fourth, where does ``getUtility`` look to get the data?  Well, the "component
.. registry" of course.  What's a component registry?  Problem number four.

４番目に、 ``getUtility`` はデータを得るためにどこを見るのでしょうか？
ええ、もちろん「コンポーネントレジストリ」です。
コンポーネントレジストリとは何でしょうか。問題その4。


.. Fifth, assuming you buy that there's some magical registry hanging around,
.. where *is* this registry?  *Homina homina*... "around"?  That's sort of the
.. best answer in this context (a more specific answer would require knowledge
.. of internals).  Can there be more than one registry?  Yes.  So *which*
.. registry does it find the registration in?  Well, the "current" registry of
.. course.  In terms of :app:`Pyramid`, the current registry is a thread local
.. variable.  Using an API that consults a thread local makes understanding how
.. it works non-local.

５番目に、何らかの魔法のレジストリが近くにある (hanging around) こと
を受け入れたとして、このレジストリはどこに *ある* のでしょうか？
はてさて... "あちこち (around)"？ それは、確かにこの文脈で最良の答えでしょう
(より具体的な答えは、内部についての知識を要求します)。複数のレジストリ
があり得ますか？ はい。そうすると、それは *どの* レジストリから検索しま
すか？ ええ、もちろん「カレント」レジストリです。 :app:`Pyramid` においては、
カレントレジストリはスレッドローカル変数です。スレッドローカル変数を参照する
API の使用は、それがどのように作動するか理解することを非局所的にします。


.. You've now bought in to the fact that there's a registry that is just hanging
.. around.  But how does the registry get populated?  Why, via code that calls
.. directives like ``config.add_view``.  In this particular case, however, the
.. registration of ``ISettings`` is made by the framework itself under the hood:
.. it's not present in any user configuration.  This is extremely hard to
.. comprehend.  Problem number six.

今、単にレジストリが hanging around しているという事実を buy in しました。
しかし、レジストリはどのようにして populate されるのでしょうか？
なぜ ``config.add_view`` のようなディレクティブを呼び出すコードによって？
しかしながら、特にこの場合 ``ISettings`` の登録はフレームワーク自体によって
内部で作られます: それはユーザ設定の中には全く存在していません。
これは理解するのが非常に難しいです。問題その6。


.. Clearly there's some amount of cognitive load here that needs to be borne by
.. a reader of code that extends the :app:`Pyramid` framework due to its use of
.. the ZCA, even if he or she is already an expert Python programmer and whom is
.. an expert in the domain of web applications.  This is suboptimal.

ここには明らかに、 ZCA を使用することで :app:`Pyramid` フレームワークを
拡張しようとするコードの読者が負担する必要のある一定の認知負荷があります。
仮に彼または彼女が既にエキスパート Python プログラマでも、
そしてウェブアプリケーション領域のエキスパートであってもです。
これは準最適です。


Ameliorations
+++++++++++++

.. First, the primary amelioration: :app:`Pyramid` *does not expect application
.. developers to understand ZCA concepts or any of its APIs*.  If an
.. *application* developer needs to understand a ZCA concept or API during the
.. creation of a :app:`Pyramid` application, we've failed on some axis.

最初に、主要な改善: :app:`Pyramid` は、 *アプリケーション開発者が ZCA
概念あるいはその API を理解することを期待しません* 。 *アプリケーション*
開発者が :app:`Pyramid` アプリケーションの生成中に ZCA 概念または API
を理解する必要があるとすれば、ある評価軸において失敗しています。


.. Instead, the framework hides the presence of the ZCA registry behind
.. special-purpose API functions that *do* use ZCA APIs.  Take for example the
.. ``pyramid.security.authenticated_userid`` function, which returns the userid
.. present in the current request or ``None`` if no userid is present in the
.. current request.  The application developer calls it like so:

代わりに、フレームワークは ZCA レジストリの存在を ZCA API を使用する特殊
目的 API 関数の背後に隠します。例えば
``pyramid.security.authenticated_userid`` 関数を例に取ります。この関数
は、現在のリクエスト中にある userid を返します。あるいは userid が 現在
のリクエストの中にない場合は ``None`` を返します。
アプリケーション開発者はそれを以下のように呼び出します:


.. ignore-next-block
.. code-block:: python
   :linenos:

   from pyramid.security import authenticated_userid
   userid = authenticated_userid(request)


.. He now has the current user id.

これでカレントユーザー id を得ることができます。


.. Under its hood however, the implementation of ``authenticated_userid``
.. is this:

しかしながら、内部では ``authenticated_userid`` の実装はこのように
なっています:


.. code-block:: python
   :linenos:

   def authenticated_userid(request):
       """ Return the userid of the currently authenticated user or
       ``None`` if there is no authentication policy in effect or there
       is no currently authenticated user. """

       registry = request.registry # the ZCA component registry
       policy = registry.queryUtility(IAuthenticationPolicy)
       if policy is None:
           return None
       return policy.authenticated_userid(request)


.. Using such wrappers, we strive to always hide the ZCA API from application
.. developers.  Application developers should just never know about the ZCA API:
.. they should call a Python function with some object germane to the domain as
.. an argument, and it should return a result.  A corollary that follows is
.. that any reader of an application that has been written using :app:`Pyramid`
.. needn't understand the ZCA API either.

このようなラッパーを使用して、アプリケーション開発者に ZCA API を見せな
いように、私たちは常に努力しています。アプリケーション開発者は ZCA API
のことを知るべきではありません: ドメインと密接に関係するいくつかの
オブジェクトを引数に取って結果を返す Python 関数を呼び出すべきです。
そこからの帰結は、 :app:`Pyramid` を使って書かれたアプリケーションの
読者は ZCA API を理解する必要もないということです。


.. Hiding the ZCA API from application developers and code readers is a form of
.. enhancing domain specificity.  No application developer wants to need to
.. understand the small, detailed mechanics of how a web framework does its
.. thing.  People want to deal in concepts that are closer to the domain they're
.. working in: for example, web developers want to know about *users*, not
.. *utilities*.  :app:`Pyramid` uses the ZCA as an implementation detail, not as
.. a feature which is exposed to end users.

ZCA API をアプリケーション開発者およびコードの読者に見せないようにして
おくことは、ドメイン固有性を増強する一つの形です。アプリケーション開発
者は、ウェブフレームワークがどのように物事を行うかの細かい詳細な仕組み
を理解したいとは思いません。人々は、自分が活動している領域に近い概念を
扱うことを望みます: 例えば、ウェブ開発者は *ユーティリティ* のことでは
なくて *ユーザ* のことを知りたいでしょう。 :app:`Pyramid` は、エンド
ユーザに対して露出された機能としてではなく、実装詳細として ZCA を
使用します。


.. However, unlike application developers, *framework developers*, including
.. people who want to override :app:`Pyramid` functionality via preordained
.. framework plugpoints like traversal or view lookup *must* understand the ZCA
.. registry API.

しかし、アプリケーション開発者とは異なり、 *フレームワーク開発者* は
ZCA レジストリ API を理解しなければなりません。これには traversal や
view lookup のようなあらかじめ用意されたフレームワークのプラグポイント
を通して :app:`Pyramid` の機能をオーバーライドしたい人々も含まれます。


.. :app:`Pyramid` framework developers were so concerned about conceptual load
.. issues of the ZCA registry API for framework developers that a `replacement
.. registry implementation <http://svn.repoze.org/repoze.component/trunk>`_
.. named :mod:`repoze.component` was actually developed.  Though this package
.. has a registry implementation which is fully functional and well-tested, and
.. its API is much nicer than the ZCA registry API, work on it was largely
.. abandoned and it is not used in :app:`Pyramid`.  We continued to use a ZCA
.. registry within :app:`Pyramid` because it ultimately proved a better fit.

:app:`Pyramid` フレームワークの開発者はフレームワーク開発者に対する
ZCA レジストリ API の概念負荷の問題について非常に関心を持っていたので、
`代替のレジストリ実装 <http://svn.repoze.org/repoze.component/trunk>`_
:mod:`repoze.component` が実際に開発されました。このパッケージは、完全に
機能し十分テストされたレジストリ実装を持ち、その API は ZCA レジストリ
API よりはるかに良いものでしたが、その作業の大部分は放棄されました。
また :app:`Pyramid` の中でも使用されていません。最終的に ZCA レジストリ
がより良い適合を示すことが証明されたので、私たちは :app:`Pyramid` の中で
それを使用し続けました。


.. note::

   .. We continued using ZCA registry rather than disusing it in
   .. favor of using the registry implementation in
   .. :mod:`repoze.component` largely because the ZCA concept of
   .. interfaces provides for use of an interface hierarchy, which is
   .. useful in a lot of scenarios (such as context type inheritance).
   .. Coming up with a marker type that was something like an interface
   .. that allowed for this functionality seemed like it was just
   .. reinventing the wheel.

   私たちは :mod:`repoze.component` に含まれるレジストリ実装を使用する
   ことを支持して ZCA レジストリを廃止するのではなく、それを使用し続ける
   ことにしましたが、その大きな理由は ZCA のインタフェース概念が
   インタフェース階層を使用するために必要だからです。それは (コンテキスト
   タイプ継承のような) 多くのシナリオで役に立ちます。この機能を可能にする
   インタフェースのような何らかのマーカー型を考え出すことは、単に車輪の
   再発明のように思われました。


.. Making framework developers and extenders understand the ZCA registry API is
.. a trade-off.  We (the :app:`Pyramid` developers) like the features that the
.. ZCA registry gives us, and we have long-ago borne the weight of understanding
.. what it does and how it works.  The authors of :app:`Pyramid` understand the
.. ZCA deeply and can read code that uses it as easily as any other code.

フレームワークの開発者および機能を拡張しようとする人に ZCA レジストリ
API を理解させることはトレードオフです。私たち (:app:`Pyramid` 開発者)
は、 ZCA レジストリによって得られる特徴が好きです。また、私たちはそれが
何を行うか、またそれがどのようにして作動するかを理解することの重みにずっと
耐えてきました。 :app:`Pyramid` の作者は ZCA を深く理解し、それを使用す
るコードを他のコードと同じくらい容易に読むことができます。


.. But we recognize that developers who might want to extend the framework are not
.. as comfortable with the ZCA registry API as the original developers are with
.. it.  So, for the purposes of being kind to third-party :app:`Pyramid`
.. framework developers in, we've drawn some lines in the sand.

しかし、潜在的にフレームワークを拡張したい開発者が ZCA レジストリ API
にオリジナルの開発者ほど満足していないことは認識しています。したがって、
サードパーティの :app:`Pyramid` フレームワーク開発者のことを考慮して、
私たちは妥協点を示しています (draw some lines in the sand)。


.. In all core code, We've made use of ZCA global API functions such as
.. ``zope.component.getUtility`` and ``zope.component.getAdapter`` the exception
.. instead of the rule.  So instead of:

私たちは、すべての中核コードの中で ``zope.component.getUtility`` や
``zope.component.getAdapter`` のような ZCA のグローバル API 関数を使用
することを、ルールではなく例外としました。したがって、次のようにする代わりに:


.. code-block:: python
   :linenos:

   from pyramid.interfaces import IAuthenticationPolicy
   from zope.component import getUtility
   policy = getUtility(IAuthenticationPolicy)


.. :app:`Pyramid` code will usually do:

:app:`Pyramid` コードは通常このようになります:


.. code-block:: python
   :linenos:

   from pyramid.interfaces import IAuthenticationPolicy
   from pyramid.threadlocal import get_current_registry
   registry = get_current_registry()
   policy = registry.getUtility(IAuthenticationPolicy)


.. While the latter is more verbose, it also arguably makes it more obvious
.. what's going on.  All of the :app:`Pyramid` core code uses this pattern
.. rather than the ZCA global API.

後者は冗長ですが、間違いなく何が起こっているかがより明確です。
:app:`Pyramid` 中核コードはすべて、 ZCA のグローバル API ではなく、この
パターンを使用します。


Rationale
+++++++++

.. Here are the main rationales involved in the :app:`Pyramid` decision to use
.. the ZCA registry:

これらは、 ZCA レジストリを使用するという :app:`Pyramid` の意思決定に
関係する主な根拠です:


.. - History.  A nontrivial part of the answer to this question is "history".
..   Much of the design of :app:`Pyramid` is stolen directly from :term:`Zope`.
..   Zope uses the ZCA registry to do a number of tricks.  :app:`Pyramid` mimics
..   these tricks, and, because the ZCA registry works well for that set of
..   tricks, :app:`Pyramid` uses it for the same purposes.  For example, the way
..   that :app:`Pyramid` maps a :term:`request` to a :term:`view callable` using
..   :term:`traversal` is lifted almost entirely from Zope.  The ZCA registry
..   plays an important role in the particulars of how this request to view
..   mapping is done.

- 歴史。この質問に対する答えの自明でない部分は「歴史的理由」です。
  :app:`Pyramid` の設計の多くは Zope から直接取られています。 Zopeは
  多くのトリックを行うために ZCA レジストリを使用します。
  :app:`Pyramid` はこれらのトリックを模倣します。そして ZCA レジストリ
  はそのようなトリックの数々についてうまく働くので、 :app:`Pyramid` でも
  同じ目的にそれを使用します。例えば :app:`Pyramid` が :term:`traversal`
  を使って :term:`request` を :term:`view callable` にマッピングする
  方法は、ほとんど完全に Zope から持ち込まれています。 ZCA レジストリは、
  このリクエストからビューへのマッピングがどのように行われるかの詳細に
  重要な役割を果たします。


.. - Features.  The ZCA component registry essentially provides what can be
..   considered something like a superdictionary, which allows for more complex
..   lookups than retrieving a value based on a single key.  Some of this lookup
..   capability is very useful for end users, such as being able to register a
..   view that is only found when the context is some class of object, or when
..   the context implements some :term:`interface`.

- 機能。ZCA コンポーネントレジストリは、本質的にスーパー辞書とでも呼べ
  るものを提供します。それは単一のキーに基づいて値を検索するより複雑な
  検索を可能にします。この検索能力のうちのいくらかはエンドユーザに非常
  に役立ちます。例えば、コンテキストがあるクラスのオブジェクトである場合
  だけ、あるいはコンテキストが何らかの :term:`interface` を実装してい
  る場合だけ検索されるビューを登録するといったことが可能になります。


.. - Singularity.  There's only one place where "application configuration"
..   lives in a :app:`Pyramid` application: in a component registry.  The
..   component registry answers questions made to it by the framework at runtime
..   based on the configuration of *an application*.  Note: "an application" is
..   not the same as "a process", multiple independently configured copies of
..   the same :app:`Pyramid` application are capable of running in the same
..   process space.

- 単一性 (singularity)。 :app:`Pyramid` アプリケーションの中に、
  「アプリケーション設定」はたった一箇所にしかありません: コンポーネント
  レジストリの中です。コンポーネントレジストリは、実行時にフレームワーク
  によってなされる質問に対して *アプリケーション* の設定に基づいて答えます。
  注:「アプリケーション」は「プロセス」と同じではありません。同じ
  :app:`Pyramid` アプリケーションの複数の独立して設定されたコピーが
  同じプロセス空間で走ることもあります。


.. - Composability.  A ZCA component registry can be populated imperatively, or
..   there's an existing mechanism to populate a registry via the use of a
..   configuration file (ZCML, via the optional :term:`pyramid_zcml` package).
..   We didn't need to write a frontend from scratch to make use of
..   configuration-file-driven registry population.

- 構成可能性 (composability)。 ZCA コンポーネントレジストリは命令的に
  populate されることができます。あるいは、設定ファイルを使って
  レジストリを populate する既存のメカニズムがあります (オプションの
  :term:`pyramid_zcml` パッケージによる ZCML)。設定ファイル駆動による
  レジストリ population を利用するためのフロントエンドをゼロから書く必要は
  ありませんでした。


.. - Pluggability.  Use of the ZCA registry allows for framework extensibility
..   via a well-defined and widely understood plugin architecture.  As long as
..   framework developers and extenders understand the ZCA registry, it's
..   possible to extend :app:`Pyramid` almost arbitrarily.  For example, it's
..   relatively easy to build a directive that registers several views all at
..   once, allowing app developers to use that directive as a "macro" in code
..   that they write.  This is somewhat of a differentiating feature from other
..   (non-Zope) frameworks.

- プラグ可能性 (pluggability)。 ZCA レジストリの使用は、十分に定義され
  広く理解されたプラグインアーキテクチャーによってフレームワークの拡張を
  可能にします。フレームワーク開発者および拡張者は ZCA レジストリを理解
  しさえすれば、 :app:`Pyramid` をほとんど任意に拡張することが可能です。
  例えば、アプリ開発者が、いくつかのビューを同時に登録するディレクティブ
  を構築して、コードの中で「マクロ」としてそのディレクティブを使用する
  ことは比較的簡単です。これは多少他の (非 Zope) フレームワークと異なる
  特徴です。


.. - Testability.  Judicious use of the ZCA registry in framework code makes
..   testing that code slightly easier.  Instead of using monkeypatching or
..   other facilities to register mock objects for testing, we inject
..   dependencies via ZCA registrations and then use lookups in the code find
..   our mock objects.

- テスト容易性。フレームワークコード中で ZCA レジストリを賢く使えば、
  コードのテストを行うのがより簡単になります。モンキーパッチやテストの
  ためにモックオブジェクトを登録する他の機能の代わりに、私たちは ZCA
  レジストリによって依存性を注入し、次にコードの中で検索を使用することで
  モックオブジェクトを見つけます。


.. - Speed.  The ZCA registry is very fast for a specific set of complex lookup
..   scenarios that :app:`Pyramid` uses, having been optimized through the years
..   for just these purposes.  The ZCA registry contains optional C code for
..   this purpose which demonstrably has no (or very few) bugs.

- スピード。 ZCA レジストリは、 :app:`Pyramid` が使用する複雑な検索
  シナリオの特定のセットに対しては非常に高速です。何年もの間この目的の
  ためだけに最適化されています。またこの目的のために、 ZCA レジストリには
  バグがない(あるいは非常に少ない)ことが実証済の、オプションの C コード
  が含まれています。


.. - Ecosystem.  Many existing Zope packages can be used in :app:`Pyramid` with
..   few (or no) changes due to our use of the ZCA registry.

- エコシステム。 ZCA レジストリを使っていることで、多くの既存の Zope
  パッケージをほとんど(あるいはまったく)変更することなく
  :app:`Pyramid` の中で使用できます。


Conclusion
++++++++++

.. If you only *develop applications* using :app:`Pyramid`, there's not much to
.. complain about here.  You just should never need to understand the ZCA
.. registry API: use documented :app:`Pyramid` APIs instead.  However, you may
.. be an application developer who doesn't read API documentation because it's
.. unmanly. Instead you read the raw source code, and because you haven't read
.. the documentation, you don't know what functions, classes, and methods even
.. *form* the :app:`Pyramid` API.  As a result, you've now written code that
.. uses internals and you've painted yourself into a conceptual corner as a
.. result of needing to wrestle with some ZCA-using implementation detail.  If
.. this is you, it's extremely hard to have a lot of sympathy for you.  You'll
.. either need to get familiar with how we're using the ZCA registry or you'll
.. need to use only the documented APIs; that's why we document them as APIs.

:app:`Pyramid` を使用して、単に *アプリケーションを開発* するだけなら、
ここで不満を言うことは多くありません。 ZCA レジストリ API を理解する
必要はまったくありません: 代わりに文書化された :app:`Pyramid` API を
使用してください。しかし、もしかしたら軟弱であるという理由で API
ドキュメントを読まないアプリケーション開発者がいるかもしれません。
その場合は代わりに生のソースコードを読むことになります。ドキュメント
を読んでいないので、どの関数、クラス、メソッドが :app:`Pyramid` API
を *構成* するかは依然として知りません。そのせいで内部コードを使用する
コードを書いたとしたら、 ZCA を使用する実装詳細と悪戦苦闘した末に自分自身を
概念の窮地に追い込むことになるでしょう。もしこれがあなたのことなら、
同情の余地はほとんどありません。私たちが ZCA レジストリを使用している
方法に精通するか、文書化された API だけを使用する必要があるでしょう;
そのために、私たちは API を文書化しています。


.. If you *extend* or *develop* :app:`Pyramid` (create new directives, use some
.. of the more obscure hooks as described in :ref:`hooks_chapter`, or work on
.. the :app:`Pyramid` core code), you will be faced with needing to understand
.. at least some ZCA concepts.  In some places it's used unabashedly, and will
.. be forever.  We know it's quirky, but it's also useful and fundamentally
.. understandable if you take the time to do some reading about it.

:app:`Pyramid` を *拡張* または *開発* する (新しいディレクティブを作る、
:ref:`hooks_chapter` で説明されているよりもっと不明瞭ないくつかのフック
を使う、 :app:`Pyramid` 中核コードを変更する) 場合は、少なくともいくつ
かの ZCA 概念を理解する必要性に直面するでしょう。いくつかの場所では、
それは臆面もなく使用され、今後もそれは変わらないでしょう。それが一種独特
であることは私たちも知っています。しかし同時に有用でもあり、時間を取って
少し読み込めば、基本的に理解することができます。


.. _zcml_encouragement:

Pyramid "Encourages Use of ZCML"
--------------------------------

.. :term:`ZCML` is a configuration language that can be used to configure the
.. :term:`Zope Component Architecture` registry that :app:`Pyramid` uses for
.. application configuration.  Often people claim that Pyramid "needs ZCML".

:term:`ZCML` は、 :app:`Pyramid` がアプリケーション設定に使用している
:term:`Zope Component Architecture` レジストリを設定するために使用する
ことができる設定言語です。よく、 Pyramid は「ZCML を必要としている」と
言われます。


.. It doesn't.  In :app:`Pyramid` 1.0, ZCML doesn't ship as part of the core;
.. instead it ships in the :term:`pyramid_zcml` add-on package, which is
.. completely optional.  No ZCML is required at all to use :app:`Pyramid`, nor
.. any other sort of frameworky declarative frontend to application
.. configuration.

そうではありません。 :app:`Pyramid` 1.0 では、ZCML は中核コードの一部と
しては含まれません; 代わりに :term:`pyramid_zcml` アドオンパッケージの
一部として提供されており、それは完全にオプションです。 :app:`Pyramid` を
使用するために　ZCML や他の種類のフレームワーク的なアプリケーション設定
に対する宣言的なフロント・エンドは全く必要ありません。


Pyramid Does Traversal, And I Don't Like Traversal
--------------------------------------------------

.. In :app:`Pyramid`, :term:`traversal` is the act of resolving a URL path to a
.. :term:`resource` object in a resource tree.  Some people are uncomfortable
.. with this notion, and believe it is wrong.  Thankfully, if you use
.. :app:`Pyramid`, and you don't want to model your application in terms of a
.. resource tree, you needn't use it at all.  Instead, use :term:`URL dispatch`
.. to map URL paths to views.

:app:`Pyramid` で :term:`traversal` とは、 URL パスをリソースツリーの中
の :term:`resource` オブジェクトへと解決する行為です。この概念を快く思
わない人もいて、彼らはそれが間違っていると信じています。幸いなことに、
:app:`Pyramid` を使用していて、リソースツリーの観点に基づいて
アプリケーションをモデル化したくなければ、それを使用する必要は全くあり
ません。代わりに、URL パスをビューへマップするために :term:`URL
dispatch` を使用してください。


.. The idea that some folks believe traversal is unilaterally wrong is
.. understandable.  The people who believe it is wrong almost invariably have
.. all of their data in a relational database.  Relational databases aren't
.. naturally hierarchical, so traversing one like a tree is not possible.

トラバーサルが一方的に間違っていると信じる一部の人がいるという考えは
理解できます。それがほとんど常に間違っていると信じる人々は、すべての
データをリレーショナルデータベースの中に持っています。リレーショナル
データベースはその性質上階層的ではないので、ツリーのようにトラバース
することができません。


.. However, folks who deem traversal unilaterally wrong are neglecting to take
.. into account that many persistence mechanisms *are* hierarchical.  Examples
.. include a filesystem, an LDAP database, a :term:`ZODB` (or another type of
.. graph) database, an XML document, and the Python module namespace.  It is
.. often convenient to model the frontend to a hierarchical data store as a
.. graph, using traversal to apply views to objects that either *are* the
.. resources in the tree being traversed (such as in the case of ZODB) or at
.. least ones which stand in for them (such as in the case of wrappers for files
.. from the filesystem).

しかしながら、トラバーサルが一方的に間違っていると考える人々は、多くの
永続化メカニズムが階層的 *である* ことを考慮に入れることを怠っています。
例として、ファイルシステム、 LDAP データベース、 :term:`ZODB` (あるいは
別の種類のグラフ) データベース、 XML ドキュメント、 Python モジュールの
ネームスペースなどがあります。フロントエンドを階層型データストアにグラフ
としてモデル化することは多くの場合に便利で、ビューをオブジェクトへ適用
するためにトラバーサルが使用されます。そのようなオブジェクトは、
トラバースされているツリーのリソースであるか (ZODB の場合のように) 、
あるいは少なくともいくつかのものがそれらの代わりを務めるものであるか
(ファイルシステムから読み出されたファイルに対するラッパーの場合のように)
のいずれかです。


.. Also, many website structures are naturally hierarchical, even if the data
.. which drives them isn't.  For example, newspaper websites are often extremely
.. hierarchical: sections within sections within sections, ad infinitum.  If you
.. want your URLs to indicate this structure, and the structure is indefinite
.. (the number of nested sections can be "N" instead of some fixed number), a
.. resource tree is an excellent way to model this, even if the backend is a
.. relational database.  In this situation, the resource tree a just a site
.. structure.

さらに、多くのウェブサイトの構造は性質上、たとえそれを駆動するためのデー
タが階層的でなかったとしても階層的です。例えば、新聞社のウェブサイトは
多くの場合非常に階層的です: セクションの中にセクションがあり、その中に
またセクションがあるという風に無制限に続きます。この構造を URL が示すよ
うにしようとして、かつ構造が不定 (入れ子のセクションの数がある定数の代
わりに "N" になる可能性がある) であれば、仮にバックエンドがリレーショナ
ルデータベースであっても、リソースツリーはこれをモデル化する優れた方法
です。この状況では、リソースツリーは単なるサイト構造です。


.. Traversal also offers better composability of applications than URL dispatch,
.. because it doesn't rely on a fixed ordering of URL matching.  You can compose
.. a set of disparate functionality (and add to it later) around a mapping of
.. view to resource more predictably than trying to get the right ordering of
.. URL pattern matching.

また、トラバーサルは URL マッチングの固定された順番に依存しないので、
URL ディスパッチよりアプリケーションの合成可能性が高まります。 URL パター
ンマッチングの正しい順番を維持することと比較すると、ビューのリソースへ
のマッピングの周辺に1セットの異種の機能性をより予想可能な形で構成する
(そして後でそれを増やす)ことができます。


.. But the point is ultimately moot.  If you don't want to use traversal, you
.. needn't.  Use URL dispatch instead.

しかし、このポイントは究極的には議論の余地があります。トラバーサルを使用
したくなければ、使用する必要はありません。代わりに URL ディスパッチを
使用してください。


Pyramid Does URL Dispatch, And I Don't Like URL Dispatch
--------------------------------------------------------

.. In :app:`Pyramid`, :term:`url dispatch` is the act of resolving a URL path to
.. a :term:`view` callable by performing pattern matching against some set of
.. ordered route definitions.  The route definitions are examined in order: the
.. first pattern which matches is used to associate the URL with a view
.. callable.

:app:`Pyramid` では、 :term:`url dispatch` とは 1セットの順序付けられた
ルーティング定義に対してパターンマッチングを行なうことで URL パスをビュー
callable に解決する行為です。ルーティング定義は順番に検査されます: 一致
する最初のパターンが URL をビュー callable に関連付けるために使用されま
す。


.. Some people are uncomfortable with this notion, and believe it is wrong.
.. These are usually people who are steeped deeply in :term:`Zope`.  Zope does
.. not provide any mechanism except :term:`traversal` to map code to URLs.  This
.. is mainly because Zope effectively requires use of :term:`ZODB`, which is a
.. hierarchical object store.  Zope also supports relational databases, but
.. typically the code that calls into the database lives somewhere in the ZODB
.. object graph (or at least is a :term:`view` related to a node in the object
.. graph), and traversal is required to reach this code.

一部の人々はこの概念で不快で、それが間違っていると信じています。彼らは通常
:term:`Zope` に深く没頭している人々です。 Zope は :term:`traversal`
以外にコードを URL にマップするメカニズムを提供していません。これは主として
Zope が事実上 ZODB の使用を要求するからです。 ZODB は階層的オブジェクト
ストアです。 Zope はリレーショナルデータベースもサポートしていますが、
典型的には、そのデータベースに対して問い合わを行うコードは ZODB オブジェクト
グラフのどこかに存在しています (あるいは、少なくともそれはオブジェクト
グラフ中のノードと関係する :term:`view` です)。また、このコードにたどり
着くためにトラバーサルが必要とされます。


.. I'll argue that URL dispatch is ultimately useful, even if you want to use
.. traversal as well.  You can actually *combine* URL dispatch and traversal in
.. :app:`Pyramid` (see :ref:`hybrid_chapter`).  One example of such a usage: if
.. you want to emulate something like Zope 2's "Zope Management Interface" UI on
.. top of your object graph (or any administrative interface), you can register
.. a route like ``config.add_route('manage', '/manage/*traverse')`` and then
.. associate "management" views in your code by using the ``route_name``
.. argument to a ``view`` configuration,
.. e.g. ``config.add_view('.some.callable', context=".some.Resource",
.. route_name='manage')``.  If you wire things up this way someone then walks up
.. to for example, ``/manage/ob1/ob2``, they might be presented with a
.. management interface, but walking up to ``/ob1/ob2`` would present them with
.. the default object view.  There are other tricks you can pull in these hybrid
.. configurations if you're clever (and maybe masochistic) too.

あなたがこれと同様にトラバーサルを使用したかったとしても、私は究極的に
は URLディスパッチが有用であると主張しましょう。実際に :app:`Pyramid`
の中で URL ディスパッチとトラバーサルを *組み合わせる* ことができます
(:ref:`hybrid_chapter` を参照)。そのような使用法の一例: オブジェクト
グラフ上に Zope 2 の "Zope Management Interface" UI のようなもの
(あるいは任意の管理インターフェース) をエミュレートしたければ、
``config.add_route('manage', '/manage/*traverse')`` のようにルーティング
を登録し、次に ``view`` 設定で ``route_name`` 引数を使用することにより
(例えば ``config.add_view('.some.callable', context=".some.Resource",
route_name='manage')``)、 "management" ビューをコードに関連付けることが
できます。この方法で物事を構成 (wire things up) して、その後で誰かが、
例えば ``/manage/ob1/ob2`` に walk up to した場合、管理インタフェースが
表示されるでしょう。しかし ``/ob1/ob2`` に walk up to to した場合は
デフォルトオブジェクトビューが表示されるでしょう。あなたが利口なら
(かつ、恐らくマゾヒストなら)、これらのハイブリッド設定に pull in
することのできる他のトリックもあります。


.. Also, if you are a URL dispatch hater, if you should ever be asked to write
.. an application that must use some legacy relational database structure, you
.. might find that using URL dispatch comes in handy for one-off associations
.. between views and URL paths.  Sometimes it's just pointless to add a node to
.. the object graph that effectively represents the entry point for some bit of
.. code.  You can just use a route and be done with it.  If a route matches, a
.. view associated with the route will be called; if no route matches,
.. :app:`Pyramid` falls back to using traversal.

さらに、もしあなたが URL ディスパッチ嫌いだったとして、いつの日かレガシー
リレーショナルデータベース構造を使用しなければならないアプリケーションを
書くように依頼されたら、ビューと URL パスの間の1回限りの関連性のために
URL ディスパッチを使用することが有用だと知るでしょう。オブジェクトグラフに
事実上ちょっとしたコードのためのエントリーポイントを表わすようなノードを
追加することは、時々まったく無意味なことです。その場合はルーティングを
使用すればそれで済みます。ルーティングが一致すれば、関連付けられたビューが
呼ばれます; 一致しない場合 :app:`Pyramid` はトラバーサルの使用に切り替え
ます。


.. But the point is ultimately moot.  If you use :app:`Pyramid`, and you really
.. don't want to use URL dispatch, you needn't use it at all.  Instead, use
.. :term:`traversal` exclusively to map URL paths to views, just like you do in
.. :term:`Zope`.

しかし、このポイントは究極的には議論の余地があります。 :app:`Pyramid`
を使用し、あなたが本当に URL ディスパッチを使用したくなければ、使用する
必要は全くありません。代わりに、ちょうど Zope の中で行うように、URL
パスをビューへとマッピングするためにトラバーサルを排他的に使用してください。


Pyramid Views Do Not Accept Arbitrary Keyword Arguments
-------------------------------------------------------

.. Many web frameworks (Zope, TurboGears, Pylons 1.X, Django) allow for their
.. variant of a :term:`view callable` to accept arbitrary keyword or positional
.. arguments, which are filled in using values present in the ``request.POST``
.. or ``request.GET`` dictionaries or by values present in the route match
.. dictionary.  For example, a Django view will accept positional arguments
.. which match information in an associated "urlconf" such as
.. ``r'^polls/(?P<poll_id>\d+)/$``:

多くのウェブフレームワーク (Zope, TurboGears, Pylons 1.X, Django) では、
:term:`view callable` の変種が任意のキーワード引数または位置引数を
受け取ることができるようになっています。それらは ``request.POST`` や
``request.GET`` 辞書に含まれる値や、ルートマッチ辞書に含まれる値によって
満たされます。例えば Django ビューは、 ``r'^polls/(?P<poll_id>\d+)/$``
のようなビューと関連付けられた "urlconf" の中の情報と一致する位置引数を
受け取ります:


.. code-block:: python
   :linenos:

   def aview(request, poll_id):
       return HttpResponse(poll_id)


.. Zope, likewise allows you to add arbitrary keyword and positional
.. arguments to any method of a resource object found via traversal:

Zope も同様に、トラバーサルによって見つかったリソースオブジェクトの任意
のメソッドに任意のキーワード引数および位置引数を加えることができます:


.. ignore-next-block
.. code-block:: python
   :linenos:

   from persistent import Persistent

   class MyZopeObject(Persistent):
        def aview(self, a, b, c=None):
            return '%s %s %c' % (a, b, c)


.. When this method is called as the result of being the published callable, the
.. Zope request object's GET and POST namespaces are searched for keys which
.. match the names of the positional and keyword arguments in the request, and
.. the method is called (if possible) with its argument list filled with values
.. mentioned therein.  TurboGears and Pylons 1.X operate similarly.

このメソッドが published callable であることの結果として呼び出される場合、
リクエストの中で位置引数およびキーワード引数の名前と一致するキーを求めて
Zope リクエストオブジェクトの GET および POST 名前空間が探索されます。
そして、メソッドは (可能なら)そこに mention された値で満たされた引数
リストで呼ばれます。 TurboGears および Pylons 1.X も同様に作動します。


.. Out of the box, :app:`Pyramid` is configured to have none of these features.
.. By default, :mod:`pyramid` view callables always accept only ``request`` and
.. no other arguments.  The rationale: this argument specification matching done
.. aggressively can be costly, and :app:`Pyramid` has performance as one of its
.. main goals, so we've decided to make people, by default, obtain information
.. by interrogating the request object within the view callable body instead of
.. providing magic to do unpacking into the view argument list.

初期状態 (out of the box; 箱から出した状態) では、 :app:`Pyramid` は
これらの特徴のどれも持たないように構成されます。デフォルトで、
:mod:`pyramid` ビュー callable は常に ``request`` だけを受け取り、他の
引数はありません。論理的根拠: この引数特定のマッチングを積極的に使うと
高コストになり得ます。また、 :app:`Pyramid` の主なゴールの1つとして
パフォーマンスがあります。したがって私たちは、ビューの引数リストに
unpack するためにマジックを提供する代わりに、デフォルトではビュー
callbale の本体内で request オブジェクトに問い合わせることにより情報を
得てもらうことに決めました。


.. However, as of :app:`Pyramid` 1.0a9, user code can influence the way view
.. callables are expected to be called, making it possible to compose a system
.. out of view callables which are called with arbitrary arguments.  See
.. :ref:`using_a_view_mapper`.

しかしながら、 :app:`Pyramid` 1.0a9 からは、ユーザコードが期待される
ビュー callable の呼ばれ方に影響を及ぼすことができるようになりました。
これにより、任意の引数で呼ばれるビュー callable からシステムを構成する
ことが可能になります。 :ref:`using_a_view_mapper` を参照してください。


Pyramid Provides Too Few "Rails"
--------------------------------

.. By design, :app:`Pyramid` is not a particularly opinionated web framework.
.. It has a relatively parsimonious feature set.  It contains no built in ORM
.. nor any particular database bindings.  It contains no form generation
.. framework.  It has no administrative web user interface.  It has no built in
.. text indexing.  It does not dictate how you arrange your code.

設計上 :app:`Pyramid` は特に主張の強いウェブフレームワークではありません。
それは比較的倹約的な (parsimonious) 特徴セットを持っています。組み込み
の ORM を含んでおらず、特定のデータベースバインディングも持っていません。
フォーム生成フレームワークが含まれていません。管理用のウェブユーザインター
フェースがありません。組み込みのテキストインデックシングを持っていません。
:app:`Pyramid` は、どのようにコードを構成するか指図しません。


.. Such opinionated functionality exists in applications and frameworks built
.. *on top* of :app:`Pyramid`.  It's intended that higher-level systems emerge
.. built using :app:`Pyramid` as a base.  See also :ref:`apps_are_extensible`.

そのような主張の強い機能は、 :app:`Pyramid` *の上に* 構築された
アプリケーションおよびフレームワークの中に存在します。基礎として
:app:`Pyramid` を使用して構築された、より高レベルのシステムが出現すること
が意図されます。 :ref:`apps_are_extensible` を見てください。


Pyramid Provides Too Many "Rails"
---------------------------------

.. :app:`Pyramid` provides some features that other web frameworks do not.
.. These are features meant for use cases that might not make sense to you if
.. you're building a simple bespoke web application:

:app:`Pyramid` は、他のウェブフレームワークにはないいくつかの特徴を提供
します。これらの特徴は、単純な特注の (bespoke) ウェブアプリケーションを
構築しているなら意味を成さないかもしれないユースケースのために意図され
た特徴です:


.. - An optional way to map URLs to code using :term:`traversal` which implies a
..   walk of a :term:`resource tree`.

- :term:`resource tree` の walk を意味する :term:`traversal` を使用して
  URL をコードにマッピングするオプションの方法。


.. - The ability to aggregate Pyramid application configuration from multiple
..   sources using :meth:`pyramid.config.Configurator.include`.

- :meth:`pyramid.config.Configurator.include` を使用して、複数のソースから
  Pyramid アプリケーション設定を集める能力。


.. - View and subscriber registrations made using :term:`interface` objects
..   instead of class objects (e.g. :ref:`using_resource_interfaces`).

- クラスオブジェクトの代わりに :term:`interface` オブジェクトを使用して
  作られたビューと subscriber の登録。(例 :ref:`using_resource_interfaces`)


.. - A declarative :term:`authorization` system.

- 宣言的な :term:`authorization` システム。


.. - Multiple separate I18N :term:`translation string` factories, each of which
..   can name its own domain.

- 複数の独立した I18N :term:`translation string` ファクトリ。各々の
  ファクトリは自分自身のドメインを名乗ることができます。


.. These features are important to the authors of :app:`Pyramid`.  The
.. :app:`Pyramid` authors are often commissioned to build CMS-style
.. applications.  Such applications are often frameworky because they have more
.. than one deployment.  Each deployment requires a slightly different
.. composition of sub-applications, and the framework and sub-applications often
.. need to be *extensible*.  Because the application has more than one
.. deployment, pluggability and extensibility is important, as maintaining
.. multiple forks of the application, one per deployment, is extremely
.. undesirable.  Because it's easier to extend a system that uses
.. :term:`traversal` from the outside than it is to do the same in a system that
.. uses :term:`URL dispatch`, each deployment uses a :term:`resource tree`
.. composed of a persistent tree of domain model objects, and uses
.. :term:`traversal` to map :term:`view callable` code to resources in the tree.
.. The resource tree contains very granular security declarations, as resources
.. are owned and accessible by different sets of users.  Interfaces are used to
.. make unit testing and implementation substitutability easier.

これらの特徴は :app:`Pyramid` の作者たちにとって重要です。
:app:`Pyramid` の作者たちは、しばしば CMS スタイルのアプリケーションを
構築するために委任されます。そのようなアプリケーションは複数のデプロイ
があるので、多くの場合フレームワーク的です。個々のデプロイはそれぞれ、
サブアプリケーションのわずかに異なる構成を要求します。また、フレームワーク
およびサブアプリケーションはしばしば *拡張可能* である必要があります。
アプリケーションに複数のデプロイがあるので、プラグ可能性と伸長性は重要です。
アプリケーション (デプロイあたり1つ) のマルチフォークの維持は非常に
望ましくない (undesirable) ので。 :term:`traversal` を使用するシステム
を外部から拡張する方が、 :term:`URL dispatch` を使用するシステムで
同じことをするより簡単なので、個々のデプロイはドメインモデルオブジェクト
の永続的なツリーから構成される :term:`resource tree` を使用し、
:term:`view callable` コードをツリー中のリソースにマッピングするために
:term:`traversal` を使用します。リソースが異なるユーザ集合によって所有
されアクセス可能なので、リソースツリーは非常に粒度の細かいセキュリティ
宣言を含んでいます。インタフェースはユニットテストおよび実装交換可能性
をより簡単にするために使用されます。


.. In a bespoke web application, usually there's a single canonical deployment,
.. and therefore no possibility of multiple code forks.  Extensibility is not
.. required; the code is just changed in-place.  Security requirements are often
.. less granular.  Using the features listed above will often be overkill for
.. such an application.

特注のウェブアプリケーションでは、通常単一の正統なデプロイがあります。
そしてしたがって、多数のコードフォークの可能性はありません。拡張可能性
は必要ではありません; コードは単に in-place で変更されます。セキュリティ
要件は多くの場合それほど細かい粒度ではありません。上記にリストされた特徴
を使用することは、多くの場合そのようなアプリケーションには行き過ぎ
(overkill) でしょう。


.. If you don't like these features, it doesn't mean you can't or shouldn't use
.. :app:`Pyramid`.  They are all optional, and a lot of time has been spent
.. making sure you don't need to know about them up-front.  You can build
.. "Pylons-1.X-style" applications using :app:`Pyramid` that are purely bespoke
.. by ignoring the features above.  You may find these features handy later
.. after building a bespoke web application that suddenly becomes popular and
.. requires extensibility because it must be deployed in multiple locations.

あなたがこれらの特徴を好きでない場合、それは :app:`Pyramid` を使用でき
ない、また使用すべきでないことを意味しません。それらはすべてオプションで、
それらのことを前もって知っている必要がないことを確かめるために多くの時間が
費やされました。上記の特徴を無視することにより、 :app:`Pyramid` を使用
して純粋に特注の "Pylons 1.X スタイル" のアプリケーションを構築すること
ができます。特注のウェブアプリケーションを構築した後で急に有名になり、
複数の場所にデプロイしなければならないため拡張性が必要になってから、
これらの特徴が便利なことに気がつくかもしれません。


Pyramid Is Too Big
------------------

.. "The :app:`Pyramid` compressed tarball is almost 2MB.  It must be
.. enormous!"

「 `Pyramid` の圧縮した tar 玉はほぼ 2MB だ。きっと巨大に違いない!」


.. No.  We just ship it with test code and helper templates.  Here's a
.. breakdown of what's included in subdirectories of the package tree:

いいえ。それにはテストコードとヘルパーのテンプレートが同梱されています。
ここで、パッケージツリーのサブディレクトリに含まれている内容物の内訳を
示します:


docs/

  3.0MB

pyramid/tests/

  1.1MB

pyramid/paster_templates/

  804KB


.. pyramid/ (except for ``pyramd/tests and pyramid/paster_templates``)

pyramid/ (``pyramd/tests`` と ``pyramid/paster_templates`` を除いて)


  539K

.. The actual :app:`Pyramid` runtime code is about 10% of the total size of the
.. tarball omitting docs, helper templates used for package generation, and test
.. code.  Of the approximately 19K lines of Python code in the package, the code
.. that actually has a chance of executing during normal operation, excluding
.. tests and paster template Python files, accounts for approximately 5K lines
.. of Python code.  This is comparable to Pylons 1.X, which ships with a little
.. over 2K lines of Python code, excluding tests.

実際の :app:`Pyramid` ランタイムコードは、ドキュメントとパッケージ生成
に使用されるヘルパーテンプレート、およびテストコードを除いて tar 玉の
合計サイズの約 10% です。パッケージに含まれる約 19,000 行の Python コード
の中で、通常の動作中に実際に実行される可能性のあるものは、テストと
paster テンプレート Python ファイルを除いて Python コードのおよそ 5,000 行
を占めます。これは Pylons 1.X と comparable です。それには、テストを除いて
2,000 行を少し超える Python コードが含まれています。


Pyramid Has Too Many Dependencies
---------------------------------

.. This is true.  At the time of this writing (Pyramid 1.3), the total number of
.. Python package distributions that :app:`Pyramid` depends upon transitively is
.. if you use Python 3.2 or Python 2.7 is 10.  If you use Python 2.6, Pyramid
.. will pull in 12 package distributions.  This is a lot more than zero package
.. distribution dependencies: a metric which various Python microframeworks and
.. Django boast.

それは真実です。これを書いている時点 (Pyramid 1.3) で、 :app:`Pyramid`
が推移的に依存する Python パッケージ配布物の総数は、 Python 3.2 あるいは
Python 2.7 を使用していれば 10 です。 Python 2.6 を使用していれば
Pyramid は 12 のパッケージ配布物を取得します。これは 0 よりはるかに多い
パッケージ依存性です: 様々な Python のマイクロフレームワークや Django
が誇るメトリック。


.. However, Pyramid 1.2 relied on 15 packages under Python 2.7 and 17 packages
.. under Python 2.6, so we've made progress here.  A port to Python 3 completed
.. in Pyramid 1.3 helped us shed a good number of dependencies by forcing us to
.. make better packaging decisions.

しかし、 Pyramid 1.2 は Python 2.7 で 15 のパッケージに、Python 2.6 で
17 パッケージに依存していました。したがって、現時点で進歩しています。
Pyramid 1.3 で完了した Python 3 への移植は、よりよいパッケージング上の
決断を強いることによって、かなりの数の依存性を削ぎ落とすことを助けました。


.. In the future, we may also move templating system dependencies out of the
.. core and place them in add-on packages, to be included by developers instead
.. of by the framework.  This would reduce the number of core dependencies by
.. about five, leaving us with only five remaining core dependencies.

将来的に、さらにテンプレートシステムへの依存性をコアから外部に移動させて、
アドオンパッケージにしようとしています。これによって、インクルードするか
どうかをフレームワークではなく開発者が決められるようになります。
これは、コアの依存性の数を約 5 まで減らすでしょう。残されたのは 5 つの
コア依存性だけです。



Pyramid "Cheats" To Obtain Speed
--------------------------------

.. Complaints have been lodged by other web framework authors at various times
.. that :app:`Pyramid` "cheats" to gain performance.  One claimed cheating
.. mechanism is our use (transitively) of the C extensions provided by
.. :mod:`zope.interface` to do fast lookups.  Another claimed cheating mechanism
.. is the religious avoidance of extraneous function calls.

:app:`Pyramid` はパフォーマンスを稼ぐために cheat (ずる) をしているという
不満を他のウェブフレームワークの作者から何度も聞きました。批判された
cheat 的なメカニズムの 1 つは、高速な検索を行うために :mod:`zope.interface`
によって提供される C 拡張を (推移的に) 使用していることです。批判された
別の cheat 的メカニズムは、外部 (extraneous) 関数呼び出しの宗教的な回避です。


.. If there's such a thing as cheating to get better performance, we want to
.. cheat as much as possible.  We optimize :app:`Pyramid` aggressively.  This
.. comes at a cost: the core code has sections that could be expressed more
.. readably.  As an amelioration, we've commented these sections liberally.

より良いパフォーマンスを得るために cheat 的な方法がある場合、私たちは
できるだけそれを利用しようとします。私たちは積極的に :app:`Pyramid` を
最適化しています。これにはコストが伴います: 中核コードの中にはより可読性に
優れた表現ができた箇所があります。改善として、私たちはこれらの箇所に大量の
コメントを書きました。


Pyramid Gets Its Terminology Wrong ("MVC")
------------------------------------------

.. "I'm a MVC web framework user, and I'm confused.  :app:`Pyramid` calls the
.. controller a view!  And it doesn't have any controllers."

「私は MVC ウェブフレームワークのユーザですが、混乱しています。
:app:`Pyramid` はコントローラーをビューと呼んでいます! そして
コントローラーがありません。」


.. If you are in this camp, you might have come to expect things about how your
.. existing "MVC" framework uses its terminology.  For example, you probably
.. expect that models are ORM models, controllers are classes that have methods
.. that map to URLs, and views are templates.  :app:`Pyramid` indeed has each of
.. these concepts, and each probably *works* almost exactly like your existing
.. "MVC" web framework. We just don't use the MVC terminology, as we can't
.. square its usage in the web framework space with historical reality.

このキャンプにいる人は、既存の「MVC」フレームワークがどのようにその用語
を使用するかに関する話を期待して来たのかもしれません。例えば、モデルとは
ORM モデルのこと、コントローラーは URL へのマッピングを持つクラスのこと、
ビューはテンプレートのこと、と恐らく期待します。 :app:`Pyramid` は確かに
これらの概念の各々を持っています。また、各々は恐らくあなたの既存の
「MVC」ウェブフレームワークとほとんど同じように *動きます* 。私たちは
単純に MVC 用語を使用しません。なぜなら、ウェブフレームワーク空間に
おける使用法を歴史上の現実性と調和させることができないからです。


.. People very much want to give web applications the same properties as common
.. desktop GUI platforms by using similar terminology, and to provide some frame
.. of reference for how various components in the common web framework might
.. hang together.  But in the opinion of the author, "MVC" doesn't match the web
.. very well in general. Quoting from the `Model-View-Controller Wikipedia entry
.. <http://en.wikipedia.org/wiki/Model–view–controller>`_:

人々は、ウェブアプリケーションに一般的なデスクトップ GUI プラットフォームと
同じ用語を使用することで同じ属性を与えて、一般的なウェブフレームワーク中で
様々なコンポーネントがどのように結合するかに関してある種の評価基準を提供する
ことを強く望みます。しかし、著者の見解では「MVC」は一般にウェブとあまり
適合しません。 `ウィキペディアの Model-View-Controller に観する記事
<http://en.wikipedia.org/wiki/Model–view–controller>`_ から引用すると:


.. .. code-block:: text
.. 
..   Though MVC comes in different flavors, control flow is generally as
..   follows:
.. 
..     The user interacts with the user interface in some way (for
..     example, presses a mouse button).
.. 
..     The controller handles the input event from the user interface,
..     often via a registered handler or callback and converts the event
..     into appropriate user action, understandable for the model.
.. 
..     The controller notifies the model of the user action, possibly  
..     resulting in a change in the model's state. (For example, the
..     controller updates the user's shopping cart.)[5]
.. 
..     A view queries the model in order to generate an appropriate
..     user interface (for example, the view lists the shopping cart's     
..     contents). Note that the view gets its own data from the model.
.. 
..     The controller may (in some implementations) issue a general
..     instruction to the view to render itself. In others, the view is
..     automatically notified by the model of changes in state
..     (Observer) which require a screen update.
.. 
..     The user interface waits for further user interactions, which
..     restarts the cycle.


.. code-block:: text

  MVC には様々に異なる派生形がありますが、制御フローは一般に以下の通りです:

    ユーザは、何らかの方法 (例えばマウスボタンを押す) でユーザインタ
    フェースと対話します。

    コントローラーは、ユーザインタフェースからの入力イベントを扱います。
    しばしば登録済のハンドラやコールバックによってイベントを受け取り、
    そのイベントを適切な (モデルに理解できる) ユーザアクションに変換します。

    コントローラーは、ユーザアクションをモデルに通知し、それは恐らく
    モデルの状態の変化を引き起こすでしょう。 (例えば、コントローラーは
    ユーザのショッピングカートを更新します) [5]

    ビューは適切なユーザインタフェースを生成するためにモデルにクエリを
    行います (例えば、ビューはショッピングカートの内容をリストします)。
    ビューがモデルからそれ自身のデータを得ることに注意してください。

    コントローラーは、 (いくつかの実装で) ビューに対してそれ自体を描画
    させるためにより一般的な指示を出すことがあります。他の実装では、
    ビューは画面更新を要求するモデルの状態変化 (オブザーバー) によって
    自動的に通知されます。

    ユーザインタフェースはさらにユーザとのインタラクションを待ち、
    サイクルを再開します。


.. To the author, it seems as if someone edited this Wikipedia definition,
.. tortuously couching concepts in the most generic terms possible in order to
.. account for the use of the term "MVC" by current web frameworks.  I doubt
.. such a broad definition would ever be agreed to by the original authors of
.. the MVC pattern.  But *even so*, it seems most MVC web frameworks fail to
.. meet even this falsely generic definition.

著者からすると、このウィキペディアの定義は、まるで誰かが現在のウェブ
フレームワークにおける用語「MVC」の使用を説明するために編集したかのようで、
可能な最も一般的な用語を使った回りくどい表現による概念のように見えます。
私は、そのような広い定義が MVC パターンのオリジナルの著者によって常に
同意されるかどうか疑わしく思います。しかし *そうであっても* 、ほとんどの
MVC ウェブフレームワークはこの疑わしい一般的な定義さえ満たさないように
見えます。


.. For example, do your templates (views) always query models directly as is
.. claimed in "note that the view gets its own data from the model"?  Probably
.. not.  My "controllers" tend to do this, massaging the data for easier use by
.. the "view" (template). What do you do when your "controller" returns JSON? Do
.. your controllers use a template to generate JSON? If not, what's the "view"
.. then?  Most MVC-style GUI web frameworks have some sort of event system
.. hooked up that lets the view detect when the model changes.  The web just has
.. no such facility in its current form: it's effectively pull-only.

例えば、「ビューがモデルからそれ自身のデータを得ることに注意してください」
と主張されるように、テンプレート (ビュー) は常にモデルに対して直接クエリー
を行うでしょうか？  恐らく、そうではありません。どちらかといえば「コントロー
ラー」がこれを行います。それは「ビュー」 (テンプレート) がより簡単に扱える
ようにデータを加工 (massaging) します。「コントローラー」が JSON を返す
場合、何をしますか？ コントローラーは JSON を生成するためにテンプレートを
使用しますか？ そうでなければ、そのとき「ビュー」は何でしょうか？ 多くの
MVC スタイルの GUI ウェブフレームワークは、ビューがモデルの変更を検知する
ために hook up されたなんらかのイベントシステムを持っています。現在の形式
のウェブには、そのような機能はまったくありません:それは事実上 pull-only
です。


.. So, in the interest of not mistaking desire with reality, and instead of
.. trying to jam the square peg that is the web into the round hole of "MVC", we
.. just punt and say there are two things: resources and views. The resource
.. tree represents a site structure, the view presents a resource.  The
.. templates are really just an implementation detail of any given view: a view
.. doesn't need a template to return a response.  There's no "controller": it
.. just doesn't exist.  The "model" is either represented by the resource tree
.. or by a "domain model" (like a SQLAlchemy model) that is separate from the
.. framework entirely.  This seems to us like more reasonable terminology, given
.. the current constraints of the web.

したがって、現実性を踏まえて誤りを犯さないことへの関心から、そして四角
い杭であるウェブを丸い穴である「MVC」の中へ押し込もうとする代わりに、
私たちはちょうど 2 つのものがあると言いましょう (punt and say):
リソースとビューです。リソースツリーはサイト構造を表わします。ビューは
リソースを示します。テンプレートは実際には単に任意のビューの実装詳細です:
ビューは、レスポンスを返すためにテンプレートを必要としません。「コント
ローラー」は、ありません: それはまったく存在していません。「モデル」は、
リソースツリー、またはフレームワークと完全に分離した「ドメインモデル」
(SQLAlchemy モデルのような) によって表わされます。現在のウェブの制約の
もとでは、これはより合理的な用語のように見えます。


.. _apps_are_extensible:

Pyramid Applications are Extensible; I Don't Believe In Application Extensibility
---------------------------------------------------------------------------------

.. Any :app:`Pyramid` application written obeying certain constraints is
.. *extensible*. This feature is discussed in the :app:`Pyramid` documentation
.. chapters named :ref:`extending_chapter` and :ref:`advconfig_narr`.  It is
.. made possible by the use of the :term:`Zope Component Architecture` and
.. within :app:`Pyramid`.

ある制約に従って書かれた :app:`Pyramid` アプリケーションはすべて *拡張
可能* です。この特徴は :app:`Pyramid` ドキュメンテーションの
:ref:`extending_chapter` と :ref:`advconfig_narr` の章で議論されています。
それは :app:`Pyramid` 内部を含めて :term:`Zope Component Architecture` を
使用することにより可能になります。


.. "Extensible", in this context, means:

この文脈における「拡張可能」の意味とは:


.. - The behavior of an application can be overridden or extended in a
..   particular *deployment* of the application without requiring that
..   the deployer modify the source of the original application.

- アプリケーションの振る舞いを、アプリケーションの特定の *デプロイ* の
  中で、オリジナルのアプリケーションのソースを修正せずにオーバーライド
  または拡張できます。


.. - The original developer is not required to anticipate any
..   extensibility plugpoints at application creation time to allow
..   fundamental application behavior to be overriden or extended.

- 基礎的なアプリケーションの振る舞いがオーバーライドまたは拡張可能に
  なるように、アプリケーション作成時にオリジナルの開発者が拡張のための
  プラグポイントを用意する必要がありません。


.. - The original developer may optionally choose to anticipate an
..   application-specific set of plugpoints, which may be hooked by
..   a deployer.  If he chooses to use the facilities provided by the
..   ZCA, the original developer does not need to think terribly hard
..   about the mechanics of introducing such a plugpoint.

- オリジナルの開発者は、オプションでアプリケーション特有のプラグポイント
  のセットを用意することを選ぶかもしれません。それはデプロイ担当者によって
  フックされるかもしれません。デプロイ担当者が ZCA によって提供される機
  能を使用することに決めれば、オリジナルの開発者はそのようなプラグポイントを
  導入するメカニズムに関してあまり深く考える必要はありません。


.. Many developers seem to believe that creating extensible applications is not
.. worth it.  They instead suggest that modifying the source of a given
.. application for each deployment to override behavior is more reasonable.
.. Much discussion about version control branching and merging typically ensues.

多くの開発者が、拡張可能なアプリケーションの作成に価値がないと信じてい
るようです。彼らは、その代わりに、各デプロイに対して振る舞いをオーバー
ライドするために与えられたアプリケーションのソースを修正することがより
合理的であると提言します。その後で、バージョン管理におけるブランチや
マージに関する多くの議論が典型的に続きます。


.. It's clear that making every application extensible isn't required.  The
.. majority of web applications only have a single deployment, and thus needn't
.. be extensible at all.  However, some web applications have multiple
.. deployments, and some have *many* deployments.  For example, a generic
.. content management system (CMS) may have basic functionality that needs to be
.. extended for a particular deployment.  That CMS system may be deployed for
.. many organizations at many places.  Some number of deployments of this CMS
.. may be deployed centrally by a third party and managed as a group.  It's
.. useful to be able to extend such a system for each deployment via preordained
.. plugpoints than it is to continually keep each software branch of the system
.. in sync with some upstream source: the upstream developers may change code in
.. such a way that your changes to the same codebase conflict with theirs in
.. fiddly, trivial ways.  Merging such changes repeatedly over the lifetime of a
.. deployment can be difficult and time consuming, and it's often useful to be
.. able to modify an application for a particular deployment in a less invasive
.. way.

すべてのアプリケーションを拡張可能にする必要がないことは明らかです。大
多数のウェブアプリケーションは単一のデプロイだけを持っており、したがっ
て拡張可能である必要はまったくありません。しかしながら、いくつかのウェ
ブアプリケーションには多数のデプロイがあります。また、いくつかには *多
くの* デプロイがあります。例えば、汎用的なコンテンツ管理システム(CMS)
は、特定のデプロイのために拡張される必要のある基本機能を持っているかも
しれません。その CMS システムは多くの場所に多くの組織のために展開するか
もしれません。この CMS の若干のデプロイは、第三者によって中心的に展開し、
グループとして管理されるかもしれません。上流のソースと同期してシステム
の各ソフトウェアブランチを絶えず維持することに比べて、事前に用意された
プラグポイントによってそのようなシステムをデプロイ毎に拡張できることは
有用です: 上流の開発者は、同じコードベースに対するあなたの変更と矛盾す
るような厄介で些細な変更をコードに加えるかもしれません。デプロイの一生
の間そのような変更を繰り返しマージすることは困難で、時間を浪費します。
また、それほど侵略的でない方法で特定のデプロイのためにアプリケーション
を修正できることは、多くの場合に有用です。


.. If you don't want to think about :app:`Pyramid` application extensibility at
.. all, you needn't.  You can ignore extensibility entirely.  However, if you
.. follow the set of rules defined in :ref:`extending_chapter`, you don't need
.. to *make* your application extensible: any application you write in the
.. framework just *is* automatically extensible at a basic level.  The
.. mechanisms that deployers use to extend it will be necessarily coarse:
.. typically, views, routes, and resources will be capable of being
.. overridden. But for most minor (and even some major) customizations, these
.. are often the only override plugpoints necessary: if the application doesn't
.. do exactly what the deployment requires, it's often possible for a deployer
.. to override a view, route, or resource and quickly make it do what he or she
.. wants it to do in ways *not necessarily anticipated by the original
.. developer*.  Here are some example scenarios demonstrating the benefits of
.. such a feature.

:app:`Pyramid` アプリケーションの拡張性に関して一切考えたくなければ、
その必要はありません。拡張性を完全に無視することができます。しかしながら、
:ref:`extending_chapter` に定義された規則のセットに従えば、アプリケーション
を拡張可能に *作る* 必要はありません: フレームワークの中で書くどんな
アプリケーションも、基礎的なレベルで自動的に拡張可能に *なります* 。
アプリケーションを拡張するためにデプロイ担当者が使用するメカニズムは
必然的に粗く (coarse) なります: 典型的には、ビュー、ルートおよびリソース
はオーバーライドできるでしょう。ほとんどの小さな (そしていくつかの主要な)
カスタマイズに関して、多くの場合にこれらは必要な唯一のオーバーライド用
のプラグポイントです: デプロイが要求する動作を完全にはアプリケーション
が行わない場合、デプロイ担当者は多くの場合ビュー、ルートあるいはリソース
をオーバーライドし、それに行ってほしいことを *オリジナルの開発者によって
必ずしも予想されない方法で* 素早く行うことができます。ここで、そのような
特徴の利点を示すいくつかの例シナリオを挙げます:


.. - If a deployment needs a different styling, the deployer may override the
..   main template and the CSS in a separate Python package which defines
..   overrides.

- あるデプロイが異なるスタイルを必要とする場合、 主要なテンプレートと
  CSS を、個別の Python パッケージの中でオーバーライドすることができます。


.. - If a deployment needs an application page to do something differently needs
..   it to expose more or different information, the deployer may override the
..   view that renders the page within a separate Python package.

- あるデプロイが、より多くのあるいは異なる情報を露出するために異なる
  アプリケーションページを必要とする場合、そのページをレンダリングする
  ビューを個別の Python パッケージの中でオーバーライドできます。


.. - If a deployment needs an additional feature, the deployer may add a view to
..   the override package.

- デプロイが追加機能を必要とする場合、オーバーライドパッケージにビュー
  を加えることができます。


.. As long as the fundamental design of the upstream package doesn't change,
.. these types of modifications often survive across many releases of the
.. upstream package without needing to be revisited.

このようなタイプの修正は、上流パッケージの基本デザインが変わらない限り、
改訂の必要なしに上流パッケージの多くのリリースを越えてしばしば残り
続けます。


.. Extending an application externally is not a panacea, and carries a set of
.. risks similar to branching and merging: sometimes major changes upstream will
.. cause you to need to revisit and update some of your modifications.  But you
.. won't regularly need to deal wth meaningless textual merge conflicts that
.. trivial changes to upstream packages often entail when it comes time to
.. update the upstream package, because if you extend an application externally,
.. there just is no textual merge done.  Your modifications will also, for
.. whatever its worth, be contained in one, canonical, well-defined place.

アプリケーションを外部から拡張することは万能薬でなく、ブランチとマージ
に似たいくつかの危険をもたらします: 時々、上流での大きな変化によって
あなたの修正のいくつかを見直し更新する必要があります。しかし、通常は
無意味な textual なマージコンフリクトに対処する必要がないでしょう。
そのような上流パッケージに対する重要でない変更は、上流パッケージが
更新される時にはしばしば起こりますが、アプリケーションを外部から拡張
する場合、 textual なマージは決して発生しないからです。あなたの修正も、
for whatever its worth 、一箇所の、正統で、十分に定義された場所に
含まれるでしょう。


.. Branching an application and continually merging in order to get new features
.. and bugfixes is clearly useful.  You can do that with a :app:`Pyramid`
.. application just as usefully as you can do it with any application.  But
.. deployment of an application written in :app:`Pyramid` makes it possible to
.. avoid the need for this even if the application doesn't define any plugpoints
.. ahead of time.  It's possible that promoters of competing web frameworks
.. dismiss this feature in favor of branching and merging because applications
.. written in their framework of choice aren't extensible out of the box in a
.. comparably fundamental way.

新しい機能やバグフィックスを得るためにアプリケーションをブランチさせて
絶えずマージすることは、明らかに有用です。 :app:`Pyramid` アプリケーショ
ンでも、他のアプリケーションの場合と同じやり方ができて、同じくらい有用
です。しかし、 :app:`Pyramid` で書かれたアプリケーションのデプロイの
場合は、アプリケーションが事前にプラグポイントを定義していなくても、
この必要を回避することが可能です。競合するウェブフレームワークの推進者
がブランチとマージを支持して、この特徴を見過ごすことはありえます。
なぜなら、彼らの選択したフレームワークで書かれたアプリケーションが
箱から出した状態では同じような基本的な方法で拡張可能ではないからです。


.. While :app:`Pyramid` application are fundamentally extensible even if you
.. don't write them with specific extensibility in mind, if you're moderately
.. adventurous, you can also take it a step further.  If you learn more about
.. the :term:`Zope Component Architecture`, you can optionally use it to expose
.. other more domain-specific configuration plugpoints while developing an
.. application.  The plugpoints you expose needn't be as coarse as the ones
.. provided automatically by :app:`Pyramid` itself.  For example, you might
.. compose your own directive that configures a set of views for a prebaked
.. purpose (e.g. ``restview`` or somesuch) , allowing other people to refer to
.. that directive when they make declarations in the ``includeme`` of their
.. customization package.  There is a cost for this: the developer of an
.. application that defines custom plugpoints for its deployers will need to
.. understand the ZCA or he will need to develop his own similar extensibility
.. system.

特定の拡張性を気にしながらアプリケーションを書かなくても :app:`Pyramid`
アプリケーションは基本的に拡張可能ですが、あなたが適度に冒険的ならば、
さらに、それを1ステップ進めることができます。 :term:`Zope Component
Architecture` に関してもっと学習すれば、アプリケーションを開発する際に、
よりドメインに特化した設定プラグポイントを露出するために任意にそれを
使用することができます。露出されるプラグポイントは、 :app:`Pyramid`
自体によって自動的に提供されるものよりは粒度が粗い (corse) 必要がありません。
例えば、あらかじめ決まった目的のために一揃いのビューを形成するための
独自ディレクティブを構成して (例えば ``restview`` やその類)、他の人々が
カスタマイズパッケージの ``includeme`` の中で宣言をする際にこの
ディレクティブを参照することを許すことができます。このためのコストがあります:
デプロイ担当者のためのカスタムプラグポイントを定義するアプリケーション
開発者は、 ZCA を理解する必要があるでしょう。さもなくば、自分自身で同様
の拡張性システムを開発する必要があるでしょう。


.. Ultimately, any argument about whether the extensibility features lent to
.. applications by :app:`Pyramid` are good or bad is mostly pointless. You
.. needn't take advantage of the extensibility features provided by a particular
.. :app:`Pyramid` application in order to affect a modification for a particular
.. set of its deployments.  You can ignore the application's extensibility
.. plugpoints entirely, and instead use version control branching and merging to
.. manage application deployment modifications instead, as if you were deploying
.. an application written using any other web framework.

究極的には、 :app:`Pyramid` によってアプリケーションに与えられた拡張性
の特徴がよいか悪いかに関するどんな議論もほとんど無意味です。特定の
:app:`Pyramid` アプリケーションによって提供される拡張性の特徴を、
そのデプロイの特定のセットのための修正に影響を与えるために利用する必要は
ありません。アプリケーションの拡張性プラグポイントを完全に無視し、
代わりに、あたかも他のウェブフレームワークも使用して書かれた
アプリケーションをデプロイしているように、アプリケーションのデプロイ用
の修正を管理するためにバージョン管理のブランチとマージを使用することが
できます。


Zope 3 Enforces "TTW" Authorization Checks By Default; Pyramid Does Not
-----------------------------------------------------------------------

Challenge
+++++++++

.. :app:`Pyramid` performs automatic authorization checks only at :term:`view`
.. execution time.  Zope 3 wraps context objects with a `security proxy
.. <http://wiki.zope.org/zope3/WhatAreSecurityProxies>`_, which causes Zope 3 to
.. do also security checks during attribute access.  I like this, because it
.. means:

:app:`Pyramid` は :term:`view` の実行時にのみ自動的な認可チェックを行ないます。
Zope 3 はコンテキストオブジェクトを `security proxy
<http://wiki.zope.org/zope3/WhatAreSecurityProxies>`_ でラップして、
属性アクセス中にさらにセキュリティ検査を行います。
私はこれが好きです。なぜなら:


.. #) When I use the security proxy machinery, I can have a view that
..    conditionally displays certain HTML elements (like form fields) or
..    prevents certain attributes from being modified depending on the
..    permissions that the accessing user possesses with respect to a context
..    object.

1) セキュリティプロキシメカニズムを使用する場合、特定の HTML 要素
   (form フィールドのような) を条件付きで表示したり、コンテキスト
   オブジェクトに関してアクセスしたユーザが所有する許可に依存して
   ある属性が修正されるのを防ぐビューを持つことができます。


.. #) I want to also expose my resources via a REST API using Twisted Web. If
..    Pyramid performed authorization based on attribute access via Zope3's
..    security proxies, I could enforce my authorization policy in both
..    :app:`Pyramid` and in the Twisted-based system the same way.

2) さらに、私は Twisted Web を使用して、REST API によってリソースを露出
   したい。もし Pyramid が Zope3 のセキュリティプロキシによって属性アク
   セスに基づいた認可を行なえば、 :app:`Pyramid` と Twisted ベースのシ
   ステムの両方で認可ポリシーを同じ方法で実施することができます。


Defense
+++++++

.. :app:`Pyramid` was developed by folks familiar with Zope 2, which has a
.. "through the web" security model.  This TTW security model was the precursor
.. to Zope 3's security proxies.  Over time, as the :app:`Pyramid` developers
.. (working in Zope 2) created such sites, we found authorization checks during
.. code interpretation extremely useful in a minority of projects.  But much of
.. the time, TTW authorization checks usually slowed down the development
.. velocity of projects that had no delegation requirements.  In particular, if
.. we weren't allowing untrusted users to write arbitrary Python code to be
.. executed by our application, the burden of through the web security checks
.. proved too costly to justify.  We (collectively) haven't written an
.. application on top of which untrusted developers are allowed to write code in
.. many years, so it seemed to make sense to drop this model by default in a new
.. web framework.

:app:`Pyramid` は Zope 2 に精通している人々によって開発されました。
Zope 2 には「through the web」セキュリティモデルがあります。この TTW
セキュリティモデルは Zope 3 のセキュリティプロキシの前身でした。
時間が経つにつれて、 :app:`Pyramid` 開発者 (Zope 2で働いている) がその
ようなサイトを作るうちに、私たちは少数のプロジェクトでコード解釈の間の
認可チェックが非常に有用だと分かりました。しかし大抵の場合、元々 delegation
の必要がなかったプロジェクトでは TTW 認可チェックによって開発速度が通常
遅くなりました。特に、信頼されていないユーザが私たちのアプリケーション
によって実行される任意の Python コードを書くことを認めていなかったなら、
ウェブセキュリティ検査を通じた負担が、正当化するにはあまりにもコストが
かかりすぎるということが証明されました。私たちは (集団的に) ここ何年も
の間 untrusted な開発者がその上でコードを書くことを認められているアプリ
ケーションを書いていません。したがって、新しいウェブフレームワーク中で、
デフォルトでこのモデルを落とすことは筋が通っているように思えました。


.. And since we tend to use the same toolkit for all web applications, it's just
.. never been a concern to be able to use the same set of restricted-execution
.. code under two web different frameworks.

また、私たちはすべてのウェブアプリケーションに同じツールキットを使用す
る傾向があるので、2つの異なるウェブフレームワークの下で同じ制限実行コード
が使用できることは、実際の関心事にはなりません (it's just never been
a concern)。


.. Justifications for disabling security proxies by default notwithstanding,
.. given that Zope 3 security proxies are viral by nature, the only requirement
.. to use one is to make sure you wrap a single object in a security proxy and
.. make sure to access that object normally when you want proxy security checks
.. to happen.  It is possible to override the :app:`Pyramid` traverser for a
.. given application (see :ref:`changing_the_traverser`).  To get Zope3-like
.. behavior, it is possible to plug in a different traverser which returns
.. Zope3-security-proxy-wrapped objects for each traversed object (including the
.. :term:`context` and the :term:`root`).  This would have the effect of
.. creating a more Zope3-like environment without much effort.

デフォルトでセキュリティプロキシを無効にすることの正当性にもかかわらず、
Zope 3 セキュリティプロキシが生来ウイルス的であることを踏まえて、
それを使用する唯一の要件は、単一のオブジェクトをセキュリティプロキシで
ラップして、プロキシセキュリティ検査が起こることを望む時に通常そのオブ
ジェクトにアクセスが起こるのが確実であることです。既存のアプリケーション
用の :app:`Pyramid` トラバーサーをオーバーライドすることは可能です
(:ref:`changing_the_traverser` を参照)。 Zope 3 のような振る舞いを得る
ために、トラバースされたそれぞれのオブジェクト (:term:`context` と
:term:`root` を含む) に対して Zope 3 セキュリティプロキシのラップ
オブジェクトを返す異なるトラバーサーをプラグインすることは可能です。
これは、多くの努力なしで、より Zope 3 に類似した環境を作成する効果が
あるでしょう。


.. _http_exception_hierarchy:

Pyramid Uses its Own HTTP Exception Class Hierarchy Rather Than ``webob.exc``
-----------------------------------------------------------------------------

.. note:: This defense is new as of Pyramid 1.1.

.. The HTTP exception classes defined in :mod:`pyramid.httpexceptions` are very
.. much like the ones defined in ``webob.exc``
.. (e.g. :class:`~pyramid.httpexceptions.HTTPNotFound`,
.. :class:`~pyramid.httpexceptions.HTTPForbidden`, etc).  They have the same
.. names and largely the same behavior and all have a very similar
.. implementation, but not the same identity.  Here's why they have a separate
.. identity:

:mod:`pyramid.httpexceptions` に定義された HTTP 例外クラスは、
``webob.exc`` に定義されたものに非常に似ています (例えば
:class:`~pyramid.httpexceptions.HTTPNotFound~,
:class:`~pyramid.httpexceptions.HTTPForbidden` など)。それらは同じ名前
を持ち、大部分は同じ振る舞いをします。そして実装も非常に類似しています。
しかし、まったく同一ではありません。ここに、それらが webob から独立して
いる理由を示します:


.. - Making them separate allows the HTTP exception classes to subclass
..   :class:`pyramid.response.Response`.  This speeds up response generation
..   slightly due to the way the Pyramid router works.  The same speedup could
..   be gained by monkeypatching ``webob.response.Response`` but it's usually
..   the case that monkeypatching turns out to be evil and wrong.

- それらを独立にすることで、 HTTP 例外クラスが
  :class:`pyramid.response.Response` をサブクラス化することを可能にします。
  Pyramid におけるルーティングの動作方法により、これによってわずかに
  レスポンス生成の速度が向上します。 ``webob.response.Response`` をモンキー
  パッチすることにより同じ速度向上が得られるかもしれませんが、通常それは
  モンキーパッチが有害で間違っているということが判明するケースです。


.. - Making them separate allows them to provide alternate ``__call__`` logic
..   which also speeds up response generation.

- それらを独立にすることで、さらにレスポンス生成を高速化する
  ``__call__`` の代替ロジックを提供することを可能にします。


.. - Making them separate allows the exception classes to provide for the proper
..   value of ``RequestClass`` (:class:`pyramid.request.Request`).

- それらを独立にすることで、例外クラスが ``RequestClass``
  (:class:`pyramid.request.Request`) の適切な値を提供することを可能にします。


.. - Making them separate allows us freedom from having to think about backwards
..   compatibility code present in ``webob.exc`` having to do with Python 2.4,
..   which we no longer support in Pyramid 1.1+.

- それらを独立にすることで、 ``webob.exc`` に含まれる Python 2.4 で動作
  させるための後方互換性コードについて考えることから解放されます。
  Pyramid 1.1+ で Python 2.4 はすでにサポート対象外です。


.. - We change the behavior of two classes
..   (:class:`~pyramid.httpexceptions.HTTPNotFound` and
..   :class:`~pyramid.httpexceptions.HTTPForbidden`) in the module so that they
..   can be used by Pyramid internally for notfound and forbidden exceptions.

- 私たちは、モジュールの中で 2 つのクラス
  (:class:`~pyramid.httpexceptions.HTTPNotFound` と
  :class:`~pyramid.httpexceptions.HTTPForbidden`) の振る舞いを変更して、
  Pyramid 内部でそれらを notfound および forbidden 例外に使用できるよう
  にします。


.. - Making them separate allows us to influence the docstrings of the exception
..   classes to provide Pyramid-specific documentation.

- それらを独立にすることで、例外クラスの docstring に対して Pyramid
  特有のドキュメンテーションを提供するように影響を及ぼすことを可能にします。


.. - Making them separate allows us to silence a stupid deprecation warning
..   under Python 2.6 when the response objects are used as exceptions (related
..   to ``self.message``).

- それらを独立にすることで、レスポンスオブジェクトが例外として使用され
  た場合に Python 2.6 で無意味な deprecation 警告が出るのを沈黙させる
  ことができます (``self.message`` に関連)


.. _simpler_traversal_model:

Pyramid has Simpler Traversal Machinery than Does Zope
------------------------------------------------------

.. Zope's default traverser:

Zop のデフォルトトラバーサーは:


.. - Allows developers to mutate the traversal name stack while traversing (they
..   can add and remove path elements).

- トラバースの間に、開発者がトラバース名前スタックを変化させることが
  できます (パス要素を加えたり削除したりすることができます)。


.. - Attempts to use an adaptation to obtain the next element in the path from
..   the currently traversed object, falling back to ``__bobo_traverse__``,
..   ``__getitem__`` and eventually ``__getattr__``.

- 現在トラバースされているオブジェクトからパス中の次の要素を得るために
  adaptation を使用して、さらに ``__bobo_traverse__``, ``__getitem__``,
  そして最終的に ``__getattr__`` にフォールバックします。


.. Zope's default traverser allows developers to mutate the traversal name stack
.. during traversal by mutating ``REQUEST['TraversalNameStack']``.  Pyramid's
.. default traverser (``pyramid.traversal.ResourceTreeTraverser``) does not
.. offer a way to do this; it does not maintain a stack as a request attribute
.. and, even if it did, it does not pass the request to resource objects while
.. it's traversing.  While it was handy at times, this feature was abused in
.. frameworks built atop Zope (like CMF and Plone), often making it difficult to
.. tell exactly what was happening when a traversal didn't match a view.  I felt
.. it was better to make folks that wanted the feature replace the traverser
.. rather than build that particular honey pot in to the default traverser.

Zope のデフォルトトラバーサーは、 ``REQUEST['TraversalNameStack']`` を
変化させることによりトラバーサル中に開発者がトラバーサル名前スタックを
変化させることを可能にします。 Pyramid のデフォルトトラバーサー
(``pyramid.traversal.ResourceTreeTraverser``)は、これを行う方法を提供し
ません; それはリクエスト属性としてスタックを維持しません。また、仮にそ
うだったとしても、それはトラバースしている間リソースオブジェクトにリク
エストを渡しません。この特徴は、時々手軽である一方で、 Zope の上に構築
されたフレームワーク (CMF や Ploneのような) の中で乱用され、トラバーサル
がビューと一致しなかった時に何が起こっていたか正確に伝えることをしばしば
困難にしました。私は、デフォルトトラバーサーに特別のハニーポットを構築
するのではなく、この特徴を望む人々にトラバーサーを交換させる方が良いと
感じました。


.. Zope uses multiple mechanisms to attempt to obtain the next element in the
.. resource tree based on a name.  It first tries an adaptation of the current
.. resource to ``ITraversable``, and if that fails, it falls back to attempting
.. number of magic methods on the resource (``__bobo_traverse__``,
.. ``__getitem__``, and ``__getattr__``).  My experience while both using Zope
.. and attempting to reimplement its publisher in ``repoze.zope2`` led me to
.. believe the following:

Zope は、名前に基づいてリソースツリーの次の要素を得るために多数のメカニズム
を使用します。それは、最初に ``ITraversable`` に対して現在のリソースの
adaptation を試みます。それが失敗する場合、リソース上のいくつかのマジック
メソッド (``__bobo_traverse__``, ``__getitem__``, ``__getattr__``) の
試行にフォールバックします。 Zope を使い ``repoze.zope2`` の publisher
を再実装している間に得た経験によって、私は下記のことを信じるように
なりました:


.. - The *default* traverser should be as simple as possible.  Zope's publisher
..   is somewhat difficult to follow and replicate due to the fallbacks it tried
..   when one traversal method failed.  It is also slow.

- *デフォルト* トラバーサーは、できるだけ単純であるべきです。 Zope の
  publisher は、1つのトラバース方法が失敗した時に別の方法にフォールバッ
  クするので、追いかけたり再現したりするのがやや困難です。さらに、それ
  は遅いです。


.. - The *entire traverser* should be replaceable, not just elements of the
..   traversal machinery.  Pyramid has a few big components rather than a
..   plethora of small ones.  If the entire traverser is replaceable, it's an
..   antipattern to make portions of the default traverser replaceable.  Doing
..   so is a "knobs on knobs" pattern, which is unfortunately somewhat endemic
..   in Zope.  In a "knobs on knobs" pattern, a replaceable subcomponent of a
..   larger component is made configurable using the same configuration
..   mechanism that can be used to replace the larger component.  For example,
..   in Zope, you can replace the default traverser by registering an adapter.
..   But you can also (or alternately) control how the default traverser
..   traverses by registering one or more adapters.  As a result of being able
..   to either replace the larger component entirely or turn knobs on the
..   default implementation of the larger component, no one understands when (or
..   whether) they should ever override the larger component entrirely.  This
..   results, over time, in a rusting together of the larger "replaceable"
..   component and the framework itself, because people come to depend on the
..   availability of the default component in order just to turn its knobs. The
..   default component effectively becomes part of the framework, which entirely
..   subverts the goal of making it replaceable.  In Pyramid, typically if a
..   component is replaceable, it will itself have no knobs (it will be solid
..   state).  If you want to influence behavior controlled by that component,
..   you will replace the component instead of turning knobs attached to the
..   component.

- トラバーサル機構の単なる要素ではなく、 *トラバーサー全体* が代替可能で
  あるべきです。 Pyramid には、大量の小さなコンポーネントではなく、少数の
  大きなコンポーネントがあります。トラバーサー全体が代替可能な場合、それ
  はデフォルトトラバーサーの一部を代替可能にすることのアンチパターンです。
  そのようにすることは「取っ手の上に取っ手」パターンです。それは不運にも
  Zope において少々固有の症状です。「取っ手の上に取っ手」パターンでは、
  大きなコンポーネントを切り替えるために使用されるのと同じメカニズムを
  用いて、大きなコンポーネントの代替可能なサブコンポーネントが設定可能に
  なります。例えば Zopeでは adapter を登録してデフォルトトラバーサーを切り
  替えることができます。しかし、さらに (あるいは別の方法として) 複数の
  adapter を登録することで、デフォルトトラバーサーがどのようにトラバース
  するかをコントロールすることもできます。大きなコンポーネントを完全に交
  換することも、大きなコンポーネントのデフォルト実装の取っ手を回す (=機能
  を調整する) ことも、どちらもできる結果として、いつ (あるいはどんな時に)
  大きなコンポーネントを完全にオーバーライドしなければならないかを誰も
  理解できませんでした。これにより、人々が単に機能を調整するために
  デフォルトコンポーネントを利用することに依存するようになってくるため、
  時間とともに大きな「代替可能な」コンポーネントとフレームワークそれ自身
  は共に錆びていきました。デフォルトコンポーネントは事実上フレームワーク
  の一部になります。それは、デフォルトコンポーネントを代替可能にするという
  ゴールを完全に破壊します。 Pyramid の中では、典型的に、コンポーネントが
  代替可能ならば、そのコンポーネントは調整部分を持たない (solid state) で
  しょう。そのコンポーネントによってコントロールされる振る舞いに影響を
  及ぼしたければ、コンポーネントに用意された機能調整用の取っ手を回す代わり
  に、コンポーネントを交換することになるでしょう。


.. _microframeworks_smaller_hello_world:

Microframeworks Have Smaller Hello World Programs
-------------------------------------------------

.. Self-described "microframeworks" exist: `Bottle <http://bottle.paws.de>`_ and
.. `Flask <http://flask.pocoo.org/>`_ are two that are becoming popular.  `Bobo
.. <http://bobo.digicool.com/>`_ doesn't describe itself as a microframework,
.. but its intended userbase is much the same.  Many others exist.  We've
.. actually even (only as a teaching tool, not as any sort of official project)
.. `created one using Pyramid <http://bfg.repoze.org/videos#groundhog1>`_ (the
.. videos use BFG, a precursor to Pyramid, but the resulting code is `available
.. for Pyramid too <http://github.com/Pylons/groundhog>`_). Microframeworks are
.. small frameworks with one common feature: each allows its users to create a
.. fully functional application that lives in a single Python file.

「マイクロフレームワーク」を自称しているフレームワークは存在します:
`Bottle <http://bottle.paws.de>`_ と `Flask
<http://flask.pocoo.org/>`_ の 2 つはポピュラーになっています。
`Bobo <http://bobo.digicool.com/>`_ はマイクロフレームワークを自称して
いませんが、その意図するユーザーベースはほとんど同じです。他にも多くの
ものが存在します。実際、私たちはすでに (何らかの公式プロジェクトとしてで
はなく、単に教育ツールとしてですが) `Pyramid を使用してマイクロフレーム
ワークを作成しています <http://bfg.repoze.org/videos#groundhog1>`_
(ビデオでは Pyramid の前身である BFG を使用していますが、生じるコードは
`ピラミッドでも利用可能です <http://github.com/Pylons/groundhog>`_)
マイクロフレームワークとは、ある共通の特徴を備えた小さなフレームワーク
のことです: それぞれのマイクロフレームワークは、ユーザが単一の Python
ファイル内で完全に機能するアプリケーションを作成することを可能にします。


.. Some developers and microframework authors point out that Pyramid's "hello
.. world" single-file program is longer (by about five lines) than the
.. equivalent program in their favorite microframework.  Guilty as charged.

何人かの開発者やマイクロフレームワークの作者は、 Pyramid の "hello
world" 単一ファイルプログラムが、彼らの好きなマイクロフレームワークで
書かれた等価なプログラムより (約5行以上) 長いと指摘します。
確かにその通りです (Guilty as charged)。


.. This loss isn't for lack of trying. Pyramid is useful in the same
.. circumstance in which microframeworks claim dominance: single-file
.. applications.  But Pyramid doesn't sacrifice its ability to credibly support
.. larger applications in order to achieve hello-world LoC parity with the
.. current crop of microframeworks.  Pyramid's design instead tries to avoid
.. some common pitfalls associated with naive declarative configuration schemes.
.. The subsections which follow explain the rationale.

このロスは、努力が足りなかったからではありません。 Pyramid はマイクロ
フレームワークが優勢と主張するのと同じ状況で役に立ちます: 単一ファイル
アプリケーション。しかし Pyramid は、現在のマイクロフレームワークに匹敵
する hello-world LoC (Line of Code = 行数) を達成するために、より大きな
アプリケーションを確実にサポートする能力を犠牲にしません。 Pyramid の
設計は、代わりに、素朴な宣言的な設定スキームに関連した、いくつかの共通
の落とし穴を回避しようとします。続くサブセクションでは、論理的根拠に
ついて説明します。


.. _you_dont_own_modulescope:

Application Programmers Don't Control The Module-Scope Codepath (Import-Time Side-Effects Are Evil)
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

.. Please imagine a directory structure with a set of Python files in it:

以下のような Python ファイルを含むディレクトリ構造を想像してください:


.. code-block:: text

    .
    |-- app.py
    |-- app2.py
    `-- config.py


.. The contents of ``app.py``:

``app.py`` の内容:


.. code-block:: python
    :linenos:

    from config import decorator
    from config import L
    import pprint

    @decorator
    def foo():
        pass

    if __name__ == '__main__':
        import app2
        pprint.pprint(L)


.. The contents of ``app2.py``:

``app2.py`` の内容:


.. code-block:: python
    :linenos:

    import app

    @app.decorator
    def bar():
        pass


.. The contents of ``config.py``:

``config.py`` の内容:


.. code-block:: python
  :linenos:

    L = []

    def decorator(func):
        L.append(func)
        return func


.. If we cd to the directory that holds these files and we run ``python app.py``
.. given the directory structure and code above, what happens?  Presumably, our
.. ``decorator`` decorator will be used twice, once by the decorated function
.. ``foo`` in ``app.py`` and once by the decorated function ``bar`` in
.. ``app2.py``.  Since each time the decorator is used, the list ``L`` in
.. ``config.py`` is appended to, we'd expect a list with two elements to be
.. printed, right?  Sadly, no:

これらのファイルを保持するディレクトリに cd して、上記のディレクトリ構造
およびコードを前提として ``python app.py`` を実行すると何が起こるでしょうか？
``decorator`` デコレータは 2 回使用されると推測されます。
``app.py`` の中でデコレートされた関数 ``foo`` で 1回、
``app2.py`` の中でデコレートされた関数 ``bar`` で 1回です。
デコレータが使用される都度 ``config.py`` の中のリスト ``L`` は append
されるので、2要素を含むリストが表示されることを期待しますよね。
悲しいことに違います:


.. code-block:: text

    [chrism@thinko]$ python app.py 
    [<function foo at 0x7f4ea41ab1b8>,
     <function foo at 0x7f4ea41ab230>,
     <function bar at 0x7f4ea41ab2a8>]


.. By visual inspection, that outcome (three different functions in the list)
.. seems impossible.  We only defined two functions and we decorated each of
.. those functions only once, so we believe that the ``decorator`` decorator
.. will only run twice.  However, what we believe is wrong because the code at
.. module scope in our ``app.py`` module was *executed twice*.  The code is
.. executed once when the script is run as ``__main__`` (via ``python app.py``),
.. and then it is executed again when ``app2.py`` imports the same file as
.. ``app``.

目視による検査では、その結果 (リスト中の3つの異なる関数) はありえない
ように思えます。定義した関数は 2つだけで、それぞれの関数を一度ずつ
デコレートしました。したがって、私たちは ``decorator`` デコレータが
2回しか走らないと確信します。しかしながら、その確信は間違っています。
``app.py`` モジュール中でモジュールスコープのコードが *2度実行されている*
からです。コードは、スクリプトが起動されるとき (``Python app.py`` による)
に ``__main__`` として一度実行され、次に ``app2.py`` がその同じファイル
をインポートする時に ``app`` として再び実行されます。


.. What does this have to do with our comparison to microframeworks?  Many
.. microframeworks in the current crop (e.g. Bottle, Flask) encourage you to
.. attach configuration decorators to objects defined at module scope.  These
.. decorators execute arbitrarily complex registration code which populates a
.. singleton registry that is a global defined in external Python module.  This
.. is analogous to the above example: the "global registry" in the above example
.. is the list ``L``.

これはマイクロフレームワークとの比較とどんな関係があるのでしょうか。
現在の多くのマイクロフレームワーク (例えば Bottle や Flask) は、
モジュールスコープで定義されたオブジェクトに設定デコレータを取り付ける
ことを奨励しています。これらのデコレータは、外部の Python モジュールに
定義されたグローバル変数であるシングルトンレジストリを populate する
任意の複雑な登録コードを実行します。これは上記の例と類似しています:
上記の例における「グローバルレジストリ」はリスト ``L`` です。


.. Let's see what happens when we use the same pattern with the `Groundhog
.. <https://github.com/Pylons/groundhog>`_ microframework.  Replace the contents
.. of ``app.py`` above with this:

`Groundhog <https://github.com/Pylons/groundhog>`_ マイクロフレームワーク
で同じパターンを使用する場合に何が起こるか確かめましょう。上記 ``app.py``
の内容をこれに置き替えてください:


.. code-block:: python
    :linenos:

    from config import gh

    @gh.route('/foo/')
    def foo():
        return 'foo'

    if __name__ == '__main__':
        import app2
        pprint.pprint(L)


.. Replace the contents of ``app2.py`` above with this:

上記 ``app2.py`` の内容をこれに置き替えてください:


.. code-block:: python
    :linenos:

    import app

    @app.gh.route('/bar/')
    def bar():
        'return bar'


.. Replace the contents of ``config.py`` above with this:

上記 ``config.py`` の内容をこれに置き替えてください:


.. code-block:: python
    :linenos:

    from groundhog import Groundhog
    gh = Groundhog('myapp', 'seekrit')


.. How many routes will be registered within the routing table of the "gh"
.. Groundhog application?  If you answered three, you are correct.  How many
.. would a casual reader (and any sane developer) expect to be registered?  If
.. you answered two, you are correct.  Will the double registration be a
.. problem?  With our Groundhog framework's ``route`` method backing this
.. application, not really.  It will slow the application down a little bit,
.. because it will need to miss twice for a route when it does not match.  Will
.. it be a problem with another framework, another application, or another
.. decorator?  Who knows.  You need to understand the application in its
.. totality, the framework in its totality, and the chronology of execution to
.. be able to predict what the impact of unintentional code double-execution
.. will be.

"gh" Groundhog アプリケーションのルーティングテーブル内にいくつのルーティング
設定が登録されるでしょうか? 3と答えたら正解です。カジュアルコードリーダー
(また任意の分別ある開発者) は、いくつ登録されていることを期待するでしょうか?
2と答えたら正解です。二重登録は問題になるでしょうか。このアプリケーションを
裏で支える私たちの Groundhog フレームワークの ``route`` メソッドでは、あまり
問題になりません。ルーティングが一致しない場合に1つのルーティングに対して2度
失敗する必要があるので、それはほんの少しアプリケーションを遅くするでしょう。
では、他のフレームワーク、他のアプリケーションあるいは別のデコレータに
関して問題になるでしょうか。誰にも分かりません。不慮のコード二重実行の
インパクトが何かを予測できるようになるためには、そのアプリケーション全体、
そのフレームワーク全体、および実行の時間的な順序関係 (chronology) を理解
する必要があります。


.. The encouragement to use decorators which perform population of an external
.. registry has an unintended consequence: the application developer now must
.. assert ownership of every codepath that executes Python module scope
.. code. Module-scope code is presumed by the current crop of decorator-based
.. microframeworks to execute once and only once; if it executes more than once,
.. weird things will start to happen.  It is up to the application developer to
.. maintain this invariant.  Unfortunately, however, in reality, this is an
.. impossible task, because, Python programmers *do not own the module scope
.. codepath, and never will*.  Anyone who tries to sell you on the idea that
.. they do is simply mistaken.  Test runners that you may want to use to run
.. your code's tests often perform imports of arbitrary code in strange orders
.. that manifest bugs like the one demonstrated above.  API documentation
.. generation tools do the same.  Some people even think it's safe to use the
.. Python ``reload`` command or delete objects from ``sys.modules``, each of
.. which has hilarious effects when used against code that has import-time side
.. effects.

外部レジストリの populate を行なうデコレータの使用を推奨することには、
意図しない結果があります: アプリケーション開発者は、今や Python モジュール
スコープのコードを実行するすべてのコードパスの所有権を主張しなければ
なりません。モジュールスコープのコードは、現在のデコレータに基づく
マイクロフレームワークでは必ずただ一度だけ実行されると仮定されます;
それが二度以上実行されれば、不思議なことが起こり始めるでしょう。この
不変式を維持することはアプリケーション開発者の責任です。しかしながら、
あいにくこれは実際には不可能なタスクです。なぜなら、 Python プログラマは
*モジュールスコープのコードパスを所有しておらず、また将来も決してそう
ならない* からです。それができるという考えをあなたに納得させようとする
人は誰も、単に誤解しています。あなたがコードのテストを実行するために
使おうとしたテストランナーは、しばしば奇妙な順番で任意のコードのインポート
を行ない、上記で実証されたようなバグを見つけ出します。
API ドキュメンテーション生成ツールも同じことをします。さらに Python の
``reload`` コマンドを使用したり ``sys.modules`` からオブジェクトを削除
したりすることが安全だと考えている人さえいます。どちらも、インポート時の
副作用があるコードに対して使用された場合にとてもおかしな効果があります。


.. Global-registry-mutating microframework programmers therefore will at some
.. point need to start reading the tea leaves about what *might* happen if
.. module scope code gets executed more than once like we do in the previous
.. paragraph.  When Python programmers assume they can use the module-scope
.. codepath to run arbitrary code (especially code which populates an external
.. registry), and this assumption is challenged by reality, the application
.. developer is often required to undergo a painful, meticulous debugging
.. process to find the root cause of an inevitably obscure symptom.  The
.. solution is often to rearrange application import ordering or move an import
.. statement from module-scope into a function body.  The rationale for doing so
.. can never be expressed adequately in the checkin message which accompanies
.. the fix and can't be documented succinctly enough for the benefit of the rest
.. of the development team so that the problem never happens again.  It will
.. happen again, especially if you are working on a project with other people
.. who haven't yet internalized the lessons you learned while you stepped
.. through module-scope code using ``pdb``.  This is a really pretty poor
.. situation to find yourself in as an application developer: you probably
.. didn't even know your or your team signed up for the job, because the
.. documentation offered by decorator-based microframeworks don't warn you about
.. it.

グローバルレジストリ書き換え型のマイクロフレームワークのプログラマは、
したがって、前節の中で行ったようにモジュールスコープコードが二度以上
実行された場合に何が起こる *かもしれない* のかに関して、ある時点で茶
柱占いを始める (start reading the tea leaves) 必要があるでしょう。
Python プログラマがモジュールスコープのコードパスを任意のコード
(特に外部レジストリに populate するコード) を実行するために使用できる
と考えて、この仮定が現実によって挑戦された場合、アプリケーション開発者
は必然的に不明瞭な徴候の根本的原因を見つけるために、しばしば苦痛に満ちた
綿密なデバッグプロセスを経験する必要があります。その解決策は、多くの場合
アプリケーションのインポート順を再整理するか、インポート文をモジュール
スコープから関数本体に移動させることです。そうすることの論理的根拠は、
修正を達成したチェックインメッセージの中で十分に表現することができず、
その問題が再び起こらないように開発チームの他のメンバーのために十分簡潔に
文書化することもできません。それは再び起こるでしょう。特に、あなたが
``pdb`` を使用してモジュールスコープコードをステップ実行する間に学習した
レッスンをまだ内面化していない他の人々とプロジェクトに取り組んでいるならば。
これは、アプリケーション開発者としては実際かなりひどい状況になっている
ことに気がつきます: デコレータに基づくマイクロフレームワークによって
提示されるドキュメンテーションではそれに関して警告がないので、あなたもしくは
その仕事のために契約したあなたのチームは、恐らく考えもしなかったでしょう。


.. Folks who have a large investment in eager decorator-based configuration that
.. populates an external data structure (such as microframework authors) may
.. argue that the set of circumstances I outlined above is anomalous and
.. contrived.  They will argue that it just will never happen.  If you never
.. intend your application to grow beyond one or two or three modules, that's
.. probably true.  However, as your codebase grows, and becomes spread across a
.. greater number of modules, the circumstances in which module-scope code will
.. be executed multiple times will become more and more likely to occur and less
.. and less predictable.  It's not responsible to claim that double-execution of
.. module-scope code will never happen.  It will; it's just a matter of luck,
.. time, and application complexity.

外部のデータ構造に populate するデコレータに基づく早期の (eager) 設定に
多大な投資をしている人々 (マイクロフレームワークの作者のような) は、私が
上記で概説した状況は変則的で不自然だと主張するかもしれません。
彼らは、それがまったく起こらないと主張するでしょう。アプリケーションを
2〜3 個のモジュールを越えて成長させるつもりでなければ、それは恐らく真実
です。しかしながら、コードベースが成長し、多数のモジュールに拡大していく
うちに、モジュールスコープのコードが複数回実行される状況はますます生じ
やすくなり、ますます予測しにくくなるでしょう。モジュールスコープのコード
の二重実行が起こらないと責任を持って主張することはできません。それは
いずれ起こります; 単に運や時間、アプリケーションの複雑さの問題です。


.. If microframework authors do admit that the circumstance isn't contrived,
.. they might then argue that real damage will never happen as the result of the
.. double-execution (or triple-execution, etc) of module scope code.  You would
.. be wise to disbelieve this assertion.  The potential outcomes of multiple
.. execution are too numerous to predict because they involve delicate
.. relationships between application and framework code as well as chronology of
.. code execution.  It's literally impossible for a framework author to know
.. what will happen in all circumstances.  But even if given the gift of
.. omniscience for some limited set of circumstances, the framework author
.. almost certainly does not have the double-execution anomaly in mind when
.. coding new features.  He's thinking of adding a feature, not protecting
.. against problems that might be caused by the 1% multiple execution case.
.. However, any 1% case may cause 50% of your pain on a project, so it'd be nice
.. if it never occured.

こうした状況が不自然でないことをマイクロフレームワークの作者が認めれば、
彼らはモジュールスコープのコードの二重実行 (あるいは三重実行など) の
結果として実際の損害が起こらないと主張するかもしれません。この主張は
信じない方が賢明です。複数回実行の潜在的な結果は、アプリケーションと
フレームワークコードの微妙な関係に加えてコード実行の時間的な順序関係を
含んでいるので、あまりにも数が多く予測することができません。フレーム
ワーク作者がすべての状況で何が起こるかを知ることは文字通り不可能です。
しかし、状況のある制限されたセットに対して特別に全知の能力を与えられた
としても、フレームワーク作者は新しい機能をコーディングする時にほとんど
確実に二重実行の異常性のことを気にしないでしょう。彼は機能を加えようと
思っていて、 1% の複数実行のケースによって引き起こされるかもしれない問題
に対して保護を忘れます。しかしながら、どんな 1% のケースも、プロジェクト
上のあなたの苦痛の 50% を引き起こす可能性があります。そのため、それが
起きなければ良いのですが (so it'd be nice if it never occured)。


.. Responsible microframeworks actually offer a back-door way around the
.. problem.  They allow you to disuse decorator based configuration entirely.
.. Instead of requiring you to do the following:

信頼できるマイクロフレームワークは、実際のところこの問題を回避するバックドア
を提供します。そのようなマイクロフレームワークでは、デコレータに基づく
設定を完全に廃止することができます。以下のようにすることを要求する代わりに:


.. code-block:: python
    :linenos:

    gh = Groundhog('myapp', 'seekrit')

    @gh.route('/foo/')
    def foo():
        return 'foo'

    if __name__ == '__main__':
        gh.run()


.. They allow you to disuse the decorator syntax and go almost-all-imperative:

デコレータシンタックスを廃止して、「ほとんど完全に命令的」にできるようにします:


.. code-block:: python
    :linenos:

    def foo():
        return 'foo'

    gh = Groundhog('myapp', 'seekrit')

    if __name__ == '__main__':
        gh.add_route(foo, '/foo/')
        gh.run()


.. This is a generic mode of operation that is encouraged in the Pyramid
.. documentation. Some existing microframeworks (Flask, in particular) allow for
.. it as well.  None (other than Pyramid) *encourage* it.  If you never expect
.. your application to grow beyond two or three or four or ten modules, it
.. probably doesn't matter very much which mode you use.  If your application
.. grows large, however, imperative configuration can provide better
.. predictability.

これは Pyramid ドキュメンテーションの中で推奨される一般的な稼働モードです。
いくつかの既存のマイクロフレームワーク (特に Flask) は同様にそれを可能
にします。 (Pyramid 以外の) どのフレームワークもそれを推奨しません。
アプリケーションが 2つや3つ、4あるいは10のモジュールを越えて成長するこ
とを予想しなければ、どのモードを使用するかは恐らくあまり重要ではありま
せん。しかしながら、あなたのアプリケーションが大きくなる場合、命令的な
設定はより良い予測を提供することができます。


.. note::

  .. Astute readers may notice that Pyramid has configuration decorators too.
  .. Aha!  Don't these decorators have the same problems?  No.  These decorators
  .. do not populate an external Python module when they are executed.  They
  .. only mutate the functions (and classes and methods) they're attached to.
  .. These mutations must later be found during a scan process that has a
  .. predictable and structured import phase.  Module-localized mutation is
  .. actually the best-case circumstance for double-imports; if a module only
  .. mutates itself and its contents at import time, if it is imported twice,
  .. that's OK, because each decorator invocation will always be mutating an
  .. independent copy of the object it's attached to, not a shared resource like
  .. a registry in another module.  This has the effect that
  .. double-registrations will never be performed.

  賢明な読者は Pyramid が設定デコレータも含んでいることに気づいたでしょう。
  おや! これらのデコレータは同じ問題を持っていませんか? いいえ。これら
  のデコレータは、実行された時に外部 Python モジュールに populate しま
  せん。それらは、単に取り付けられている関数 (およびクラスやメソッド)
  を書き換えます。これらの書き換えは、後で実行される予測可能で構造的な
  インポート過程を持った走査プロセスの間に見つけられます。モジュールに
  局所的な書き換えは、実際に二重インポートに対する最良の状況です; もし
  モジュールがインポート時に単にそれ自体とその内容を書き換えるなら、
  モジュールが 2度インポートされても、それは OK です。なぜなら、それぞれの
  デコレータの発動が、他のモジュールの中のレジストリのような共有資源
  ではなく、常にそのデコレータが取り付けられたオブジェクトの独立した
  コピーを書き換えるからです。これは二重登録が行なわれないという効果が
  あります。


Routes Need Relative Ordering
+++++++++++++++++++++++++++++

.. Consider the following simple `Groundhog
.. <https://github.com/Pylons/groundhog>`_ application:

次の単純な `Groundhog <https://github.com/Pylons/groundhog>`_
アプリケーションを考えてみてください:


.. code-block:: python
    :linenos:

    from groundhog import Groundhog
    app = Groundhog('myapp', 'seekrit')

    app.route('/admin')
    def admin():
        return '<html>admin page</html>'

    app.route('/:action')
    def action():
        if action == 'add':
           return '<html>add</html>'
        if action == 'delete':
           return '<html>delete</html>'
        return app.abort(404)

    if __name__ == '__main__':
        app.run()


.. If you run this application and visit the URL ``/admin``, you will see the
.. "admin" page.  This is the intended result.  However, what if you rearrange
.. the order of the function definitions in the file?

このアプリケーションを起動して URL ``/admin`` を訪れると "admin" ページが
見えるでしょう。これは意図した結果です。しかしながら、もしファイル中の
関数定義の順序を再配置したら、どうなるでしょうか。


.. code-block:: python
    :linenos:

    from groundhog import Groundhog
    app = Groundhog('myapp', 'seekrit')

    app.route('/:action')
    def action():
        if action == 'add':
           return '<html>add</html>'
        if action == 'delete':
           return '<html>delete</html>'
        return app.abort(404)

    app.route('/admin')
    def admin():
        return '<html>admin page</html>'

    if __name__ == '__main__':
        app.run()


.. If you run this application and visit the URL ``/admin``, you will now be
.. returned a 404 error.  This is probably not what you intended.  The reason
.. you see a 404 error when you rearrange function definition ordering is that
.. routing declarations expressed via our microframework's routing decorators
.. have an *ordering*, and that ordering matters.

このアプリケーションを起動して URL ``/admin`` を訪れると、今度は 404
エラーが返されるでしょう。これは恐らくあなたが意図したものではありません。
関数定義順を再配置した場合に 404 エラーが表示される理由は、このマイクロ
フレームワークのルーティングデコレータによって表現されたルーティング
宣言が *順番* を持ち、その順番が問題になるからです。


.. In the first case, where we achieved the expected result, we first added a
.. route with the pattern ``/admin``, then we added a route with the pattern
.. ``/:action`` by virtue of adding routing patterns via decorators at module
.. scope.  When a request with a ``PATH_INFO`` of ``/admin`` enters our
.. application, the web framework loops over each of our application's route
.. patterns in the order in which they were defined in our module.  As a result,
.. the view associated with the ``/admin`` routing pattern will be invoked: it
.. matches first.  All is right with the world.

予想された結果を達成した最初のケースでは、モジュールスコープでデコレータ
によってルーティングパターンを加えることにより、最初にパターン ``/admin``
を持つルーティングを加え、次にパターン ``/:action`` を持つルーティング
を加えました。 ``PATH_INFO`` が ``/admin`` であるリクエストがこの
アプリケーションに入力された場合、ウェブフレームワークは
アプリケーションのルーティングパターン各々に対してそれらがモジュールに
定義された順でループします。その結果、ルーティングパターン ``/admin``
に関連付けられたビューが起動されます: それは最初に一致します。
すべて世はこともなし、です。


.. In the second case, where we did not achieve the expected result, we first
.. added a route with the pattern ``/:action``, then we added a route with the
.. pattern ``/admin``.  When a request with a ``PATH_INFO`` of ``/admin`` enters
.. our application, the web framework loops over each of our application's route
.. patterns in the order in which they were defined in our module.  As a result,
.. the view associated with the ``/:action`` routing pattern will be invoked: it
.. matches first.  A 404 error is raised.  This is not what we wanted; it just
.. happened due to the order in which we defined our view functions.

予想された結果を達成しなかった２番目のケースでは、最初にパターン
``/:action`` を持つルーティングを加えました。次に、パターン ``/admin``
を持つルーティングを加えました。 ``PATH_INFO`` が ``/admin`` である
リクエストがこのアプリケーションに入力された場合、ウェブフレームワークは
アプリケーションのルーティングパターン各々に対してそれらがモジュールに
定義された順でループします。その結果、ルーティングパターン ``/:action``
に関連付けられたビューが起動されます: それは最初に一致します。
404 エラーが送出されます。これは望んだ結果ではありません;
単にビュー関数を定義した順番によってそれは起こりました。


.. This is because Groundhog routes are added to the routing map in import
.. order, and matched in the same order when a request comes in.  Bottle, like
.. Groundhog, as of this writing, matches routes in the order in which they're
.. defined at Python execution time.  Flask, on the other hand, does not order
.. route matching based on import order; it reorders the routes you add to your
.. application based on their "complexity".  Other microframeworks have varying
.. strategies to do route ordering.

これは、 Groundhog のルーティングがインポートされた順番でルーティング
マップに加えられ、リクエストが入ってきたときにも同じ順でマッチされる
からです。 Bottle は、この記述の時点では Groundhog と同様Python 実行時
に定義された順にルーティングマッチを行います。それに対し Flask は、
ルーティングマッチをインポート順に基づいて順序付けません; Flask は
ルーティングの「複雑さ」に基づいてアプリケーションに追加される
ルーティングを並び替えます。他のマイクロフレームワークは、様々な
ルーティング順の戦略を持っています。


.. Your application may be small enough where route ordering will never cause an
.. issue.  If your application becomes large enough, however, being able to
.. specify or predict that ordering as your application grows larger will be
.. difficult.  At some point, you will likely need to more explicitly start
.. controlling route ordering, especially in applications that require
.. extensibility.

あなたのアプリケーションは十分に小さいのでルーティング順に関する問題は
起こらないかもしれません。しかしながら、アプリケーションが大きくなれば、
ルーティング順を指定したり予想したりすることが、アプリケーションが
大きくなるにしたがって一層困難になるでしょう。ある時点で、恐らくより明
示的にルーティング順をコントロールし始める必要があるでしょう。特に拡張
性を要求するアプリケーションでは。


.. If your microframework orders route matching based on complexity, you'll need
.. to understand what is meant by "complexity", and you'll need to attempt to
.. inject a "less complex" route to have it get matched before any "more
.. complex" one to ensure that it's tried first.

あなたのマイクロフレームワークが複雑さに基づいてルーティングマッチの
順序を決めるなら、「複雑さ」が何を意味しているのかを理解する必要があるでしょう。
また、任意の「より複雑な」ルーティングより前に「より複雑でない」
ルーティングをマッチさせるために、そのルーティングが確実に最初になるように
試行錯誤して注入する必要があるでしょう。


.. If your microframework orders its route matching based on relative
.. import/execution of function decorator definitions, you will need to ensure
.. you execute all of these statements in the "right" order, and you'll need to
.. be cognizant of this import/execution ordering as you grow your application
.. or try to extend it.  This is a difficult invariant to maintain for all but
.. the smallest applications.

あなたのマイクロフレームワークが関数デコレータ定義の相対的なインポート /
実行に基づいてルーティングマッチの順番を決めるなら、それらのすべての
文が「正しい」順で実行されることを保証する必要があるでしょう。また、
アプリケーションを大きくしたり、拡張しようとするときはいつも、
このインポート / 実行順を意識する必要があるでしょう。これは最小の
アプリケーション以外では維持するのが困難な不変式です。


.. In either case, your application must import the non-``__main__`` modules
.. which contain configuration decorations somehow for their configuration to be
.. executed.  Does that make you a little uncomfortable?  It should, because
.. :ref:`you_dont_own_modulescope`.

いずれの場合も、あなたのアプリケーションはその設定が実行されるように
設定デコレータを含む非 ``__main__`` モジュールを何らかの形でインポート
しなければなりません。これはあなたを少し不快にしますか? するはずです。
なぜなら :ref:`you_dont_own_modulescope` なので。


.. Pyramid uses neither decorator import time ordering nor does it attempt to
.. divine the relative complexity of one route to another in order to define a
.. route match ordering.  In Pyramid, you have to maintain relative route
.. ordering imperatively via the chronology of multiple executions of the
.. :meth:`pyramid.config.Configurator.add_route` method.  The order in which you
.. repeatedly call ``add_route`` becomes the order of route matching.

Pyramid では、デコレータインポート時の順番を使用しません。また、
ルーティングマッチ順を定義するために、あるルーティングと別のルーティングの
相対的な複雑さを推測することも試みません。Pyramid では、相対的なルーティング
順を :meth:`pyramid.config.Configurator.add_route` メソッドの複数の実行
の時間的な順序関係によって命令的に維持しなければなりません。繰り返し
``add_route`` を呼び出した順番は、ルーティングマッチの順番になります。


.. If needing to maintain this imperative ordering truly bugs you, you can use
.. :term:`traversal` instead of route matching, which is a completely
.. declarative (and completely predictable) mechanism to map code to URLs.
.. While URL dispatch is easier to understand for small non-extensible
.. applications, traversal is a great fit for very large applications and
.. applications that need to be arbitrarily extensible.

この命令的な順序付けを維持する必要性が本当にあなたを困らせる場合、
ルーティングマッチの代わりに :term:`traversal` を使用することができます。
それはコードを URL にマップするための完全に宣言的な (かつ完全に予測可能な)
メカニズムです。URL ディスパッチは、小さな、拡張可能でないアプリケーション
に対してはより理解しやすい方法なのに対して、トラバーサルは、大規模な
アプリケーションや任意に拡張可能である必要があるアプリケーションに
大変適しています。


"Stacked Object Proxies" Are Too Clever / Thread Locals Are A Nuisance
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

.. Some microframeworks use the ``import`` statement to get a handle to an
.. object which *is not logically global*:

いくつかのマイクロフレームワークでは、 *論理的にはグローバルでない*
オブジェクトに対するハンドルを得るために ``import`` 文を使用します:


.. code-block:: python
    :linenos:

    from flask import request

    @app.route('/login', methods=['POST', 'GET'])
    def login():
        error = None
        if request.method == 'POST':
            if valid_login(request.form['username'],
                           request.form['password']):
                return log_the_user_in(request.form['username'])
            else:
                error = 'Invalid username/password'
        # this is executed if the request method was GET or the
        # credentials were invalid    


.. The `Pylons 1.X <http://pylonsproject.org>`_ web framework uses a similar
.. strategy.  It calls these things "Stacked Object Proxies", so, for purposes
.. of this discussion, I'll do so as well.

`Pylons 1.X <http://pylonsproject.org>`_ ウェブフレームワークは同様の
戦略を使用しています。 Pylons ではこれを "Stacked Object Proxies" と
呼んでいるので、この議論の目的のために私もそれに倣います。


.. Import statements in Python (``import foo``, ``from bar import baz``) are
.. most frequently performed to obtain a reference to an object defined globally
.. within an external Python module.  However, in normal programs, they are
.. never used to obtain a reference to an object that has a lifetime measured by
.. the scope of the body of a function.  It would be absurd to try to import,
.. for example, a variable named ``i`` representing a loop counter defined in
.. the body of a function.  For example, we'd never try to import ``i`` from the
.. code below:

Python において、インポート文 (``import foo``, ``from bar import baz``)
は外部の Python モジュール内でグローバルに定義されたオブジェクトへの
参照を得るために最も頻繁に実行されます。しかしながら、正常なプログラム
では、関数本体のスコープに従う寿命を持ったオブジェクトへの参照を得るために
インポート文は使用されません。例えば、関数の本体部で定義されたループ
カウンタを表わす ``i`` という名前の変数をインポートしようとすることは
不条理でしょう。例えば、私たちは以下のコードから ``i`` をインポート
しようとは決してしません:


.. code-block::  python
   :linenos:

   def afunc():
       for i in range(10):
           print i


.. By its nature, the *request* object created as the result of a WSGI server's
.. call into a long-lived web framework cannot be global, because the lifetime
.. of a single request will be much shorter than the lifetime of the process
.. running the framework.  A request object created by a web framework actually
.. has more similarity to the ``i`` loop counter in our example above than it
.. has to any comparable importable object defined in the Python standard
.. library or in normal library code.

その性質上、 WSGI サーバーが長寿命のウェブフレームワークを呼び出した結果
生成された *request* オブジェクトはグローバルにはなりえません。なぜなら
単一のリクエストの寿命はフレームワークを実行するプロセスの寿命よりも
はるかに短くなるからです。ウェブフレームワークによって生成された request
オブジェクトは、実際 Python 標準ライブラリや通常のライブラリコードの中で
定義されるインポート可能なオブジェクトに対する類似性よりも、上記例における
``i`` ループカウンタとの類似点をより多く持っています。


.. However, systems which use stacked object proxies promote locally scoped
.. objects such as ``request`` out to module scope, for the purpose of being
.. able to offer users a nice spelling involving ``import``.  They, for what I
.. consider dubious reasons, would rather present to their users the canonical
.. way of getting at a ``request`` as ``from framework import request`` instead
.. of a saner ``from myframework.threadlocals import get_request; request =
.. get_request()`` even though the latter is more explicit.

しかしながら、 stacked object proxies を使用するシステムでは、
``import`` を使ってユーザが簡単にコードを書く方法を提供 (offer users a
nice spelling involving ``import``) できるようにする目的で、モジュール
スコープを外れて (out to) *request* のようなローカルスコープの
オブジェクトが推奨されています。それらのシステムでは、私が疑わしい理由
と考えるもののために、 ``from framework import request`` が、より健全な
``from myframework.threadlocals import get_request; request =
get_request()`` の代わりに (後者がより明示的であっても) ``request`` を
得る正統な方法としてユーザに提示されます。


.. It would be *most* explicit if the microframeworks did not use thread local
.. variables at all.  Pyramid view functions are passed a request object; many
.. of Pyramid's APIs require that an explicit request object be passed to them.
.. It is *possible* to retrieve the current Pyramid request as a threadlocal
.. variable but it is a "in case of emergency, break glass" type of activity.
.. This explicitness makes Pyramid view functions more easily unit testable, as
.. you don't need to rely on the framework to manufacture suitable "dummy"
.. request (and other similarly-scoped) objects during test setup.  It also
.. makes them more likely to work on arbitrary systems, such as async servers
.. that do no monkeypatching.

マイクロフレームワークがスレッドローカル変数をまったく使用しなければ、
それは *最も* 明示的でしょう。 Pyramid ビュー関数には request オブジェ
クトが渡されます; Pyramid の多くの API は、request オブジェクトが明示的
に渡されることを要求します。スレッドローカル変数として現在の
Pyramid request を検索することは *可能* です。しかしそれは「緊急時には
ガラスを割ってください」タイプの活動です。この明瞭さは Pyramid ビュー
関数をより容易にユニットテスト可能にします。というのも、テストのセット
アップ中に適切な「ダミーの」 request (または同様のスコープを持つ他の)
オブジェクトを生成するために、フレームワークに依存する必要がないからです。
さらに、これによって任意のシステム (例えばモンキーパッチを行わない
async サーバー) で動作する可能性が高くなります。


Explicitly WSGI
+++++++++++++++

.. Some microframeworks offer a ``run()`` method of an application object that
.. executes a default server configuration for easy execution.

一部のマイクロフレームワークはアプリケーションオブジェクトの ``run()``
メソッドを提供していて、このメソッドは簡単に実行するために標準サーバー
設定を行います。


.. Pyramid doesn't currently try to hide the fact that its router is a WSGI
.. application behind a convenience ``run()`` API.  It just tells people to
.. import a WSGI server and use it to serve up their Pyramid application as per
.. the documentation of that WSGI server.

Pyramid は、現在のところルーターが簡便な ``run()`` API の背後にある
WSGI アプリケーションであるという事実を隠そうとしていません。それは、
Pyramid アプリケーションを起動するために WSGI サーバーをインポートし、
その WSGI サーバーのドキュメンテーションに従ってそれを使用するように
人々に単に伝えます。


.. The extra lines saved by abstracting away the serving step behind ``run()``
.. seem to have driven dubious second-order decisions related to API in some
.. microframeworks.  For example, Bottle contains a ``ServerAdapter`` subclass
.. for each type of WSGI server it supports via its ``app.run()`` mechanism.
.. This means that there exists code in ``bottle.py`` that depends on the
.. following modules: ``wsgiref``, ``flup``, ``paste``, ``cherrypy``, ``fapws``,
.. ``tornado``, ``google.appengine``, ``twisted.web``, ``diesel``, ``gevent``,
.. ``gunicorn``, ``eventlet``, and ``rocket``.  You choose the kind of server
.. you want to run by passing its name into the ``run`` method.  In theory, this
.. sounds great: I can try Bottle out on ``gunicorn`` just by passing in a name!
.. However, to fully test Bottle, all of these third-party systems must be
.. installed and functional; the Bottle developers must monitor changes to each
.. of these packages and make sure their code still interfaces properly with
.. them.  This expands the packages required for testing greatly; this is a
.. *lot* of requirements.  It is likely difficult to fully automate these tests
.. due to requirements conflicts and build issues.

``run()`` の背後のサーバ実行ステップを抽象の彼方に置くことによって削減
される追加の行は、一部のマイクロフレームワークにおいて API と関係する
dubious な二次の意思決定を drive したように見えます。例えば Bottle は
``app.run()`` メカニズムによってサポートする WSGIサーバーのそれぞれに対
して ``ServerAdapter`` のサブクラスを含んでいます。これは
``bottle.py`` の中に次のモジュールに依存するコードが存在することを意味
します: ``wsgiref``, ``flup``, ``paste``, ``cherrypy``, ``fapws``,
``tornado``, ``google.appengine``, ``twisted.web``, ``diesel``,
``gevent``, ``gunicorn``, ``eventlet``, ``rocket`` 。 ``run`` メソッド
にその名前を渡すことで、実行したいサーバーの種類を選択します。理論的に
は、これは素晴らしく思えます: 名前を渡すだけで ``gunicorn`` 上で
Bottle を試してみることができます! しかしながら、 Bottle を完全にテスト
するためには、これらのサードパーティシステムをすべてインストールして、
そのすべてが機能しなければなりません; Bottle の開発者は、これらのパッケー
ジの各々に対する変更を監視し、彼らのコードが依然としてそれらと適切に
インタフェースを持つことを確かめなければなりません。これは、テストに
要求されるパッケージを大幅に拡大します; これは *多数の* 要求です。要求
(されるパッケージ) の衝突やビルド時の問題によって、これらのテストを完全
に自動化することは恐らく難しいでしょう。


.. As a result, for single-file apps, we currently don't bother to offer a
.. ``run()`` shortcut; we tell folks to import their WSGI server of choice and
.. run it by hand.  For the people who want a server abstraction layer, we
.. suggest that they use PasteDeploy.  In PasteDeploy-based systems, the onus
.. for making sure that the server can interface with a WSGI application is
.. placed on the server developer, not the web framework developer, making it
.. more likely to be timely and correct.

その結果、1ファイルアプリケーションに対しては、私たちは現在わざわざ
``run()`` ショートカットを提示していません; 人々には、選択した WSGI
サーバーをインポートし、かつ手動でそれを実行するようにと伝えています。
サーバー抽象レイヤーを望む人々に対しては、 PasteDeploy を使用することを
提案します。 PasteDeploy に基づいたシステムでは、サーバーが WSGI アプリ
ケーションとインタフェースを持つことができることを保証する負担は、
ウェブフレームワークの開発者ではなくサーバー開発者に課せられます。
そのためよりタイムリーで、正しいことが期待されます。


Wrapping Up
+++++++++++

.. Here's a diagrammed version of the simplest pyramid application, where
.. comments take into account what we've discussed in the
.. :ref:`microframeworks_smaller_hello_world` section.

これは最も単純な Pyramid アプリケーションの図解されたバージョンで、
コメントは私たちが :ref:`microframeworks_smaller_hello_world` 節で
議論したことを考慮に入れています。


.. code-block:: python
   :linenos:

   from pyramid.response import Response         # explicit response, no TL
   from wsgiref.simple_server import make_server # explicitly WSGI

   def hello_world(request):  # accepts a request; no request thread local reqd
       # explicit response object means no response threadlocal
       return Response('Hello world!') 

   if __name__ == '__main__':
       from pyramid.config import Configurator
       config = Configurator()       # no global application object.
       config.add_view(hello_world)  # explicit non-decorator registration
       app = config.make_wsgi_app()  # explicitly WSGI
       server = make_server('0.0.0.0', 8080, app)
       server.serve_forever()        # explicitly WSGI


Pyramid Doesn't Offer Pluggable Apps
------------------------------------

.. It is "Pyramidic" to compose multiple external sources into the same
.. configuration using :meth:`~pyramid.config.Configurator.include`.  Any
.. number of includes can be done to compose an application; includes can even
.. be done from within other includes.  Any directive can be used within an
.. include that can be used outside of one (such as
.. :meth:`~pyramid.config.Configurator.add_view`, etc).

:meth:`~pyramid.config.Configurator.include` を使用して一つの同じ設定に
多数の外部ソースを結合するのが「Pyramid 流」です。アプリケーションを構成
するために任意の数の include が行えます; include はさらに他の include の
内部からも行えます。 include の内部では、その外部で使用できる任意の
ディレクティブ (:meth:`~pyramid.config.Configurator.add_view` などのような)
も使用することができます。


.. Pyramid has a conflict detection system that will throw an error if two
.. included externals try to add the same configuration in a conflicting way
.. (such as both externals trying to add a route using the same name, or both
.. externals trying to add a view with the same set of predicates).  It's awful
.. tempting to call this set of features something that can be used to compose a
.. system out of "pluggable applications".  But in reality, there are a number
.. of problems with claiming this:

Pyramid には衝突探知システムがあり、 2つの include された外部コンポーネント
が同じ設定を衝突する方法で加えようとした場合 (例えば、両方の外部コンポーネント
が同じ名前を使用してルーティングを加えようとした場合や、両方の外部コンポーネント
が同じ predicate のセットを持ったビューを加えようとした場合) にエラー
を投げます。これらの特徴のことを「プラグ可能なアプリケーション」から
システムを構成するために使用できるものと呼ぶことは大変魅力的です。
しかし、実際にはこの主張には多くの問題があります:


.. - The terminology is strained. Pyramid really has no notion of a 
..   plurality of "applications", just a way to compose configuration 
..   from multiple sources to create a single WSGI application.  That 
..   WSGI application may gain behavior by including or disincluding 
..   configuration, but once it's all composed together, Pyramid 
..   doesn't really provide any machinery which can be used to demarcate 
..   the boundaries of one "application" (in the sense of configuration 
..   from an external that adds routes, views, etc) from another. 

- 用語の混乱があります。 Pyramid には、実際には複数形の "applications" に
  対応する概念はありません。単に複数のソースから単一の WSGI アプリケー
  ションを生成するために設定を構成する方法があるだけです。設定を
  include するか disinclude することにより WSGI アプリケーションが振る
  舞いを獲得できたとしても、一旦それらすべてが一緒に設定されると、
  実際のところ1つの「アプリケーション」 (外部コンポーネントから追加された
  ルーティングやビューなどの設定の意味で) と別のアプリケーションの境界
  を区別するために使用することができる機構を Pyramid は提供しません。


.. - Pyramid doesn't provide enough "rails" to make it possible to integrate
..   truly honest-to-god, download-an-app-from-a-random-place
..   and-plug-it-in-to-create-a-system "pluggable" applications.  Because
..   Pyramid itself isn't opinionated (it doesn't mandate a particular kind of
..   database, it offers multiple ways to map URLs to code, etc), it's unlikely
..   that someone who creates something application-like will be able to
..   casually redistribute it to J. Random Pyramid User and have it just work by
..   asking him to config.include a function from the package.  This is
..   particularly true of very high level components such as blogs, wikis,
..   twitter clones, commenting systems, etc.  The integrator (the Pyramid
..   developer who has downloaded a package advertised as a "pluggable app")
..   will almost certainly have made different choices about e.g. what type of
..   persistence system he's using, and for the integrator to appease the
..   requirements of the "pluggable application", he may be required to set up a
..   different database, make changes to his own code to prevent his application
..   from shadowing the pluggable app (or vice versa), and any other number of
..   arbitrary changes.

- Pyramid は、本当に、神に誓って「適当な場所からアプリをダウンロードして、
  プラグインしてシステムを作成する」という「プラグ可能な」アプリケーション
  を統合することを可能にするために十分な "rails" を提供しません。
  Pyramid それ自体は主張の強いフレームワークではないので (特別の種類の
  データベースを要求せず、 URL をコードにマップする方法を多数提供し
  ている、等々)、誰かがアプリケーションの半完成品を作成して、カジュアルに
  再配布したものをどこかの Pyramid ユーザが受け取って、パッケージから機能を
  config.include するだけで動く、ということは起こりそうもありません。
  これは特に、ブログ、Wiki、 Twitter クローン、コメントシステムなどのよう
  な高レベルのコンポーネントにまさに該当します。インテグレータ (「プラグ
  可能アプリ」として宣伝されたパッケージをダウンロードした Pyramid 開発者)
  は、例えばどんなタイプの永続化システムを使用しているかということに関して
  ほとんど確実に異なる選択を行なっているでしょう。また、インテグレータは
  「プラグ可能アプリケーション」の要求を満たす (appease the requirements)
  ため、異なるデータベースをセットアップしたり、彼のアプリケーションが
  プラグ可能なアプリを隠すこと (あるいはその逆) を防ぐために彼自身のコード
  に変更を加えたり、その他任意の数の変更を要求されるかもしれません。


.. For this reason, we claim that Pyramid has "extensible" applications, 
.. not pluggable applications.  Any Pyramid application can be extended 
.. without forking it as long as its configuration statements have been 
.. composed into things that can be pulled in via ``config.include``. 

この理由で、 Pyramid はプラグ可能なアプリケーションではなく「拡張可能な」
アプリケーションを持つと主張します。どんな Pyramid アプリケーションも、
その設定文が ``config.include`` によって取り入れることができるものに
構成されている限り、フォークせずに拡張することができます。


.. It's also perfectly reasonable for a single developer or team to create a set
.. of interoperating components which can be enabled or disabled by using
.. config.include.  That developer or team will be able to provide the "rails"
.. (by way of making high-level choices about the technology used to create the
.. project, so there won't be any issues with plugging all of the components
.. together.  The problem only rears its head when the components need to be
.. distributed to *arbitrary* users.  Note that Django has a similar problem
.. with "pluggable applications" that need to work for arbitrary third parties,
.. even though they provide many, many more rails than does Pyramid.  Even the
.. rails they provide are not enough to make the "pluggable application" story
.. really work without local modification.

さらに、一人の開発者あるいはチームが config.include を使用して有効にしたり
無効にしたりできる、1セットの相互運用 (interoperating) コンポーネントを
作成することは完全に筋が通っています。開発者またはチームは "rails" を
提供できるでしょう (プロジェクトを作成するために使われる技術に関する
高レベルの選択を行なうことによって)。したがって、すべてのコンポーネントを
まとめてプラグインすることに問題はないでしょう。 *任意の* ユーザに
コンポーネントを配布する必要がある場合のみ、その問題は姿を現します。
任意のサードパーティーのために動く必要のある「プラグ可能なアプリケーション」
に関して Django が同様の問題を持っていることに注目してください。
たとえそれが Pyramid よりさらに多数の rails を提供していたとしてもです。
Pyramid が提供する rails が 「プラグ可能なアプリケーション」を作るために
十分でなかったとしても、ローカルの修正なしでストーリーは現実に働きます
(story really work)。


.. Truly pluggable applications need to be created at a much higher level than a
.. web framework, as no web framework can offer enough constraints to really
.. make them work out of the box.  They really need to plug into an application,
.. instead.  It would be a noble goal to build an application with Pyramid that
.. provides these constraints and which truly does offer a way to plug in
.. applications (Joomla, Plone, Drupal come to mind).

本当にプラグ可能なアプリケーションは、ウェブフレームワークよりはるかに
高いレベルで作成される必要があります。なぜなら、ウェブフレームワークは
そのようなアプリケーションを箱から出した状態で動かすのに十分な制約を
実際には提供することができないからです。実際には、その代わりにそれら
(=プラグ可能なアプリケーション) をアプリケーションにプラグインする必要が
あります。これらの制約を提供し、本当にアプリケーションをプラグインする
方法を提供できるアプリケーションを Pyramid で構築することは壮大なゴールと
なるでしょう (Joomla, Plone, Drupal が心に浮かびます)。


Pyramid Has Zope Things In It, So It's Too Complex
--------------------------------------------------

.. On occasion, someone will feel compelled to post a mailing list message that
.. reads something like this:

時々、メーリングリストにこのようなメッセージを投稿せざるをえないという
気持ちになる人がいるようです:


.. .. code-block:: text
..
..    had a quick look at pyramid ... too complex to me and not really
..    understand for which benefits.. I feel should consider whether it's time
..    for me to step back to django .. I always hated zope (useless ?)
..    complexity and I love simple way of thinking
   
.. code-block:: text

   Pyramid をちょっと見たんだけど ... 僕には複雑すぎてどんなメリットが
   あるのかよく分からないよ.. そろそろ django に step back するかどうか
   考えるべきじゃないかという気がしてるんだ .. 僕はずっと zope の
   (役に立たない?) 複雑さを嫌っていて、単純な考え方が好きなんだ。


.. (Paraphrased from a real email, actually.)

(実際には、本物の電子メールから意訳されたものです)


.. Let's take this criticism point-by-point.

この批判について順番に見ていきましょう。


Too Complex
+++++++++++

.. If you can understand this hello world program, you can use Pyramid:

もしこの hello world プログラムを理解することができるなら、あなたは
Pyramid を使うことができます:


.. code-block:: python
   :linenos:

   from wsgiref.simple_server import make_server
   from pyramid.config import Configurator
   from pyramid.response import Response

   def hello_world(request):
       return Response('Hello world!')

   if __name__ == '__main__':
       config = Configurator()
       config.add_view(hello_world)
       app = config.make_wsgi_app()
       server = make_server('0.0.0.0', 8080, app)
       server.serve_forever()


.. Pyramid has ~ 650 pages of documentation (printed), covering topics from the
.. very basic to the most advanced.  *Nothing* is left undocumented, quite
.. literally.  It also has an *awesome*, very helpful community.  Visit the
.. #pyramid IRC channel on freenode.net (irc://freenode.net#pyramid) and see.

Pyramid には (印刷すると) 650 ページにも及ぶドキュメンテーションがあり、
非常に基礎的なトピックから最も進んだトピックまでカバーしています。
ドキュメント化されていないものは文字通り *何もありません* 。さらに、
*最高の* とても頼りになるコミュニティーがあります。freenode.net の
#pyramid IRC チャンネル (irc://freenode.net#pyramid) を訪れてみてください。


Hate Zope
+++++++++

.. I'm sorry you feel that way.  The Zope brand has certainly taken its share of
.. lumps over the years, and has a reputation for being insular and mysterious.
.. But the word "Zope" is literally quite meaningless without qualification.
.. What *part* of Zope do you hate?  "Zope" is a brand, not a technology.

あなたがそのように感じたとしたら残念です。 Zope ブランドは、この数年に
わたり間違いなく一定のシェアを取っており、その insular (島国的) で不可解
な点に関して評判があります。しかし、単語 "Zope" は、修飾子がなければ文字
通り全く意味を持ちません。 Zope のどの *部分* が嫌いですか? "Zope" は技術
ではなくブランドです。


.. If it's Zope2-the-web-framework, Pyramid is not that.  The primary designers
.. and developers of Pyramid, if anyone, should know.  We wrote Pyramid's
.. predecessor (:mod:`repoze.bfg`), in part, because *we* knew that Zope 2 had
.. usability issues and limitations.  :mod:`repoze.bfg` (and now :app:`Pyramid`)
.. was written to address these issues.

それが Zope 2 ウェブフレームワークのことなら、 Pyramid はそれとは違います。
Pyramid の主要な設計者や開発者なら誰でも知っていることです。 *私たち* は
Zope 2 がユーザビリティの問題や制限を持っていることを知っていたので、
Pyramid の部分的な前身 (:mod:`repoze.bfg`) を書きました。 repoze.bfg
(そして今の :app:`Pyramid`) は、これらの問題を扱うために書かれました。


.. If it's Zope3-the-web-framework, Pyramid is *definitely* not that.  Making
.. use of lots of Zope 3 technologies is territory already staked out by the
.. :term:`Grok` project.  Save for the obvious fact that they're both web
.. frameworks, :mod:`Pyramid` is very, very different than Grok.  Grok exposes
.. lots of Zope technologies to end users.  On the other hand, if you need to
.. understand a Zope-only concept while using Pyramid, then we've failed on some
.. very basic axis.

それが Zope 3 ウェブフレームワークのことなら、 Pyramid は *はっきりと*
それとは違います。 Zope 3 の多数の技術を利用することはすでに
:term:`Grok` プロジェクトの縄張りとされている領域です。それらが両方とも
ウェブフレームワークであるという自明な事実を除けば、 :mod:`Pyramid` は
Grok とはとても、とても異なっています。 Grok はエンドユーザに多くの
Zope 技術を露出します。それに対して、あなたが Pyramid を使用する際に
Zope 特有の概念を理解する必要があれば、私たちはある非常に基礎的な評価軸
で失敗しています。


.. If it's just the word Zope: this can only be guilt by association.  Because a
.. piece of software internally uses some package named ``zope.foo``, it doesn't
.. turn the piece of software that uses it into "Zope".  There is a lot of
.. *great* software written that has the word Zope in its name.  Zope is not
.. some sort of monolithic thing, and a lot of its software is usable
.. externally.  And while it's not really the job of this document to defend it,
.. Zope has been around for over 10 years and has an incredibly large, active
.. community.  If you don't believe this,
.. http://pypi-ranking.info/author is an eye-opening reality
.. check.

それが単に単語 Zope のことなら: これは単に guilt by association (連座制)
です。ソフトウェアの一部が内部で ``zope.foo`` という名前のパッケージを
使用していますが、だからといってそれを使用するソフトウェアが "Zope" に
はなりません。その名前に単語 Zope を持った多くの *素晴らしい* ソフトウェア
が書かれています。 Zope はある種のモノリシックなソフトウェアではありません。
また、そのソフトウェアの多くは外部的に使用可能です。そして、本当はこれを
defence することはこのドキュメントの仕事ではないのですが、
Zope は10年以上の間存在してきて、信じられないほど大きく活動的な
コミュニティーがあります。もしこれが信じられない場合、
http://pypi-ranking.info/author を見れば、目を見張る
ような現実に気がつくでしょう (eye-opening reality check)。


Love Simplicity
+++++++++++++++

.. Years of effort have gone into honing this package and its documentation to
.. make it as simple as humanly possible for developers to use.  Everything is a
.. tradeoff, of course, and people have their own ideas about what "simple" is.
.. You may have a style difference if you believe Pyramid is complex.  Its
.. developers obviously disagree.

数年間の努力が、このパッケージとそのドキュメンテーションの仕上げに注がれ、
開発者が使用できるくらい可能な限り単純になるように人事が尽くされました。
もちろんすべてはトレードオフです。また、「単純」ということに関して人々は
自分の考えを持っています。 Pyramid が複雑であると考えているなら、
スタイルの違いがあるのかもしれません。そのような開発者は当然意見が一致
しないでしょう。


Other Challenges
----------------

.. Other challenges are encouraged to be sent to the `Pylons-devel
.. <http://groups.google.com/group/pylons-devel>`_ maillist.  We'll try to address
.. them by considering a design change, or at very least via exposition here.

他の challenge があればぜひ `Pylons-devel
<http://groups.google.com/group/pylons-devel>`_ メーリングリストに
送ってください。私たちは、設計変更を検討することでそれに取り組むか、
少なくともここにそれを記そうと思います。
