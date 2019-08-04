投票アプリ
*******************************************************

Database の設定
===========================================

.. code-block:: python
   :caption: mysite/settings.py

   LANGUAGE_CODE = 'ja'
   TIME_ZONE = 'Asia/Tokyo'

.. code-block:: console

   $ python manage.py migrate

モデルの作成
===========================================

.. code-block:: python
   :caption: polls/models.py
   :linenos:

   from django.db import models
   
   
   class Question(models.Model):
       question_text = models.CharField(max_length=200)
       pub_date = models.DateTimeField('date published')
   
   
   class Choice(models.Model):
       question = models.ForeignKey(Question, on_delete=models.CASCADE)
       choice_text = models.CharField(max_length=200)
       votes = models.IntegerField(default=0)


Django Adminの紹介
===========================================

ビューを書く
===========================================

フォームを書く
===========================================

汎用ビューを使う
===========================================

スタイルシートや画像を追加する
===========================================

