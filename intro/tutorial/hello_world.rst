Hello Worldアプリ
*******************************************************

プロジェクトを作る
=======================

.. code-block:: console

   $ django-admin startproject mysite`

アプリケーションを作る
===========================================

.. code-block:: console

   $ python manage.py startapp polls

はじめてのビュー作成
===========================================

.. code-block:: python
   :caption: polls/views.py
   :linenos:

   from django.http import HttpResponse


   def index(request):
       return HttpResponse("Hello, world. You're at the polls index.")

.. code-block:: python
   :caption: polls/urls.py
   :linenos:

   from django.urls import path
   
   from . import views
   
   urlpatterns = [
       path('', views.index, name='index'),
   ]

.. code-block:: python
   :caption: mysite/urls.py
   :linenos:

   from django.contrib import admin
   from django.urls import include, path
   
   urlpatterns = [
       path('polls/', include('polls.urls')),
       path('admin/', admin.site.urls),
   ]

.. code-block:: console

   $ python manage.py runserver

