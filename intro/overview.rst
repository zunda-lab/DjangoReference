Django の概要
*******************************************************

Djangoはスピーディーにかつ簡単にWeb開発ができるように考えられています。このレクチャーではDjangoの最も基本的なサンプルプログラムを解説することで、開発の基本を把握し、このあとのレクチャーに備えることを目的としています。

モデルの定義
=======================

この時点で、モデルはデータベースのテーブルの情報を表したものだと思ってください。テーブルの情報とはカラムの名前やデータ型などです。この例では二つのクラスReporterとAritileが二つのテーブルを表しています。

.. code-block:: python
   :caption: mysite/news/models.py
   :linenos:

    from django.db import models
    
    class Reporter(models.Model):
        full_name = models.CharField(max_length=70)
    
        def __str__(self):
            return self.full_name
    
    class Article(models.Model):
        pub_date = models.DateField()
        headline = models.CharField(max_length=200)
        content = models.TextField()
        reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)
    
        def __str__(self):
            return self.headline

Reporterテーブルにはfull_nameというCHAR型のカラムが、Aritcleテーブルにはpubdate, head_line, Content, reporterカラムが存在することを表しています。それぞれのクラスに定義しているstrメソッドはクラスをprintしたときに表示される文字列を決めているものです。

データベースのテーブルを生成
================================

モデルを定義したら、次は、モデルからデータベースのテーブルを生成します。ご覧のコマンドを利用します。

.. code-block:: console

    $ python manage.py makemigrations
    $ python manage.py migrate

makemigrationsコマンドでデータベースを操作するための準備をします。migrateコマンドでデータベースを操作します。この場合の例ではデータベースにテーブルが作成されます。

モデルの動きを試してみる
================================

モデルを定義して、データベースにテーブルが準備できたら、次はコマンドラインでモデルを試してみましょう。
    
.. code-block:: python
    
    # Import the models we created from our "news" app
    >>> from news.models import Article, Reporter
    
    # No reporters are in the system yet.
    >>> Reporter.objects.all()
    <QuerySet []>
    
    # Create a new Reporter.
    >>> r = Reporter(full_name='John Smith')
    
    # Save the object into the database. You have to call save() explicitly.
    >>> r.save()
    
    # Now it has an ID.
    >>> r.id
    1
    
    # Now the new reporter is in the database.
    >>> Reporter.objects.all()
    <QuerySet [<Reporter: John Smith>]>
    
    # Fields are represented as attributes on the Python object.
    >>> r.full_name
    'John Smith'
    
    # Django provides a rich database lookup API.
    >>> Reporter.objects.get(id=1)
    <Reporter: John Smith>
    >>> Reporter.objects.get(full_name__startswith='John')
    <Reporter: John Smith>
    >>> Reporter.objects.get(full_name__contains='mith')
    <Reporter: John Smith>
    >>> Reporter.objects.get(id=2)
    Traceback (most recent call last):
        ...
    DoesNotExist: Reporter matching query does not exist.
    
    # Create an article.
    >>> from datetime import date
    >>> a = Article(pub_date=date.today(), headline='Django is cool',
    ...     content='Yeah.', reporter=r)
    >>> a.save()
    
    # Now the article is in the database.
    >>> Article.objects.all()
    <QuerySet [<Article: Django is cool>]>
    
    # Article objects get API access to related Reporter objects.
    >>> r = a.reporter
    >>> r.full_name
    'John Smith'
    
    # And vice versa: Reporter objects get API access to Article objects.
    >>> r.article_set.all()
    <QuerySet [<Article: Django is cool>]>
    
    # The API follows relationships as far as you need, performing efficient
    # JOINs for you behind the scenes.
    # This finds all articles by a reporter whose name starts with "John".
    >>> Article.objects.filter(reporter__full_name__startswith='John')
    <QuerySet [<Article: Django is cool>]>
    
    # Change an object by altering its attributes and calling save().
    >>> r.full_name = 'Billy Goat'
    >>> r.save()
    
    # Delete an object with delete().
    >>> r.delete()
    
まずはimportで作ったモデルのクラスの使用を宣言します。all()メソッドを実行すると空のリストが返却されます。つまり、Reporterテーブルには一つもレコードが存在しないことを意味します。次はfull_nemaが John SmithのReporterオブジェクトを準備します。この段階ではデータベースのReporterテーブルには格納されていません。save()メソッドを実行することで始めてテーブルにINSERTされます。

r.id 属性を確認するとデータベースに格納して始めて付与されるidの数値が確認できます。再びall()メソッドを実行してみます。Reporterテーブルから1件取得できたことがわかります。r.full_nameのようにPythonのオブジェクト属性としてカラムの値を参照することができます。get(id=1)メソッドを実行するとidが1のレコードを取得するとこができます。full_name_startwith ではJohnで始めるレコードを取得できます。またfull_name_contains では mithが含まれるレコードを取得できます。

次はArticleオブジェクトを定義してsave()します。all()メソッドで1件取得できました。ArticleとReporterはリレーションが定義してあるので、Article側からreporter属性を通じてReporterの情報を取り出すことができます。Reporter側からarticle_set で特定のレポーターの全てのArticleを取得できます。またxxxxでArticle側からフィルタで Reporterの名前に Johnが含まれるレコードを取得することができます。

属性を変更してsave()するだけでレコードをUPDATEすることができます。またdelete()メソッドはレコードを削除します。

このようにモデルを定義しただけで、モデルを直感的に操作するAPIが出来上がります。    

管理インタフェース
================================

モデルを定義すると動的な管理サイトが準備されます。準備された管理サイトを使うためにははadmin.pyに定義したモデルを追加するだけです。それだけで実運用に耐えうる管理サイトが出来上がります。この機能はDjangoの魅力の一つです。

.. code-block:: python
   :caption: mysite/news/models.py
   :linenos:

    from django.db import models
    
    class Article(models.Model):
        pub_date = models.DateField()
        headline = models.CharField(max_length=200)
        content = models.TextField()
        reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)

.. code-block:: python
   :caption: mysite/news/admin.py
   :linenos:

    from django.contrib import admin
    
    from . import models
    
    admin.site.register(models.Article)

管理サイトはログインを必要とし、データベースのレコードを追加・変更・削除できるサイトです。モデルを定義したらすぐ管理サイトを利用してデータ入力作業を行うことが可能です。

URLの定義
================================

次はURLの定義です。URLconfと呼ばれるモジュールはURLのパターンとPythonのコールバック関数を紐付けます。この仕組みはURLとPythonのブログラムを疎結合いする役割を持っています。

ReporterやArticleのURLconfは次のようなプログラムになります。

.. code-block:: python
   :caption: mysite/news/urls.py
   :linenos:

    from django.urls import path
    
    from . import views
    
    urlpatterns = [
        path('articles/<int:year>/', views.year_archive),
        path('articles/<int:year>/<int:month>/', views.month_archive),
        path('articles/<int:year>/<int:month>/<int:pk>/', views.article_detail),
    ]

このモジュールは型として考えてください。3行のpath()メソッドを使ってURLパスをviewモジュールのコールバック関数と紐づけています。一つ目は、URLがarticle/西暦年の数字/ の場合、この場合はviewsモジュールのyear_archiveメソッドがコールされます。次は、URLがarticle/西暦年の数字/月の数字/ の場合、この場合はviewsモジュールのyear_monthメソッドがコールされます。最後は、URLがarticle/西暦年の数字/プライマリーキー の場合、この場合はviewsモジュールのarticle_detailメソッドがコールされます。

ユーザがページをリクエストするとDjangoはurlpattersリストを順番に検査し、要求されたURLに一致してURLパターンで停止し、紐づ付いたメソッドをコールします。コールされる際にURLパターンの可変部分はメソッドのパラメータとして引き継がれます。

ビューを作る
===================================

ビューは一般的にURLパターンから引き継がれたパラメータに従って、データベースからデータを取得し、テンプレートをロードして、テンプレートの可変部分をデータベースの値などで埋め込んでレンダリングします。

.. code-block:: python
   :caption: mysite/news/views.py
   :linenos:

    from django.shortcuts import render
    
    from .models import Article
    
    def year_archive(request, year):
        a_list = Article.objects.filter(pub_date__year=year)
        context = {'year': year, 'article_list': a_list}
        return render(request, 'news/year_archive.html', context)

テンプレートを定義する
============================================

このHTMLテンプレートはメインコンテンツの部分です。

.. code-block:: html
   :caption: mysite/news/templates/news/year_archive.html
   :linenos:

    {% extends "base.html" %}
    
    {% block title %}Articles for {{ year }}{% endblock %}
    
    {% block content %}
    <h1>Articles for {{ year }}</h1>
    
    {% for article in article_list %}
        <p>{{ article.headline }}</p>
        <p>By {{ article.reporter.full_name }}</p>
        <p>Published {{ article.pub_date|date:"F j, Y" }}</p>
    {% endfor %}
    {% endblock %}

extendsの部分はテンプレートの継承機能を使うことを意味しています。この記載により、まずはbase.htmlというテンプレートをロードして、その中にある可変ブロックにyear_crchive.htmlを埋め込むことを意味しています。この継承機能によって複数のテンプレートの重複記述を減らすことができます。

year_archive.htmlには二つのブロックが定義されています。一つはblock title、二つ目はblock contentです。いずれもブロック定義の終わりはendblockです。baseテンプレートには、この二つのブロックを差し込む部分を定義しています。for artice in article_list から end for は取り出したレコード数分繰り返される部分です。article変数に入れてarticle変数のおhead_line属性をp要素の値として出力しています。テンプレート変数は二つの波括弧に囲まれた部分です。article.pub_dateの出力はパイブ（縦棒）によりdateフィルタに送られ、"F, j Y"の指定によって英語の月表示、日、4桁の西暦からなる文字列に整形され出力します。

