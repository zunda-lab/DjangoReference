Hello Worldアプリ
*******************************************************

プロジェクトを作る
=======================

はじめにプロジェクトを作成します。プロジェクトとは「複数のアプリケーションを集めたもの」アプリケーションは「投票システムやブログシステムなどのWebアプリケーション」と考えてください。コマンドラインでプロジェクト作りたいディレクトに移動して「django-admin startproject mysite」と打ち込みます。

.. code-block:: console

    $ django-admin startproject mysite

コマンド実行が終わったら実行したディレクトリを確認してみましょう。mysiteディレクトリが作成されていることを確認できます。

次にDjango付属の開発用サーバーで、作ったプロジェクトを確認してみましょう。コマンドラインでmanage.pyがあるディレクトに移動して「python manage.py runserver」と打ち込みましょう。

.. code-block:: console

    $ python manage.py runserver

「Starting development server at http://127.0.0.1:8000/」が出力されれば起動できたということになります。ブラウザでにアクセスしてみましょう。ロケットの絵と"Congratulations!"が表示されればOKです。


アプリケーションを作る
===========================================

プロジェクトができたらアプリケーションの雛形を作ります。

プロジェクトにアプリケーションを作成すると規約に従ったディレクトリやファイル構成が自動作成されます。開発者はディクトリを作成することなどではなく、プログラムを作ることに集中することができます。

アプリケーションを作るにはmanage.pyファイルがあるディレクトリに移動して、コマンド「python manage.py startapp polls」を実行します。

.. code-block:: console

   $ python manage.py startapp polls

このコマンドを実行するとpollsディレクトリが作成されます。

ビューを作る
===========================================

最も簡単なビューを作ります。

pollsディレクトリのview.pyファイルにご覧のコードを書き込みます。indexメソッドは簡単な文字列を HttpResponseオブジェクトで返却するだけのプログラムです。

.. code-block:: python
   :caption: polls/views.py
   :linenos:

   from django.http import HttpResponse


   def index(request):
       return HttpResponse("Hello, world. You're at the polls index.")

このビューはブラウザにこの文字列を表示するものですが、ブラウザからアクセスするためには URLを決めて、そのURLとこのindexメソッドを紐づける必要があります。その紐付けを行うのが URLconfです。

投票アプリケーションのURLConfはpollsディレクトリにurls.pyを作って記述する必要があります。ご覧のコードを書きました。URLConfはurlpatternsというリストに定義するルールになっています。

.. code-block:: python
   :caption: polls/urls.py
   :linenos:

   from django.urls import path
   
   from . import views
   
   urlpatterns = [
       path('', views.index, name='index'),
   ]

実はこれだけではだめで、プロジェクトのURLConfにアプリケーションのURLConfを認識させる必要があります。エディタでmysiteディレクトリのurls.py を開いてdjango.urlsモジュールのincludeメソッドを使うことを宣言します。それからurlpatternsリストにpathメソッドを1行追加します。includeメソッドではpollsアプリケーションのurlsモジュールを取り込みます。URLはこのpathメソッドの第1パラメーターに指定された文字列pollsとpollsアプリケーションのurlsモジュールで指定した文字列を結合したものになります。これでindexビューをURLConfに紐付けることができました。

.. code-block:: python
   :caption: mysite/urls.py
   :linenos:

   from django.contrib import admin
   from django.urls import include, path
   
   urlpatterns = [
       path('polls/', include('polls.urls')),
       path('admin/', admin.site.urls),
   ]

開発用サーバで確認する
===========================================

開発用サーバーを立ち上げてブラウザで確認してみましょう。コマンドラインで「python manage.py runserver」を実行します。サーバーが立ち上がったらブラウザでにアクセスしてみます。ブラウザに "Hello, world. You're at the polls index." と表示されることが確認できました。

.. code-block:: console

   $ python manage.py runserver

