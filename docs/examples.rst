========
Examples
========

Sometimes, an example is much easier to understand than abstract API docs, so here's some sample use cases.

Case 1: Simple Blog API
=======================

models.py
---------

.. code-block:: python

    from django.db import models
    from taggit.managers import TaggableManager

    class Post(models.Model):
        title = models.CharField(max_length=255)
        author = models.ForeignKey('auth.User')
        published = models.BooleanField(default=False)
        content = models.TextField(blank=True)
        tags = TaggableManager(blank=True)


serialiser.py
-------------

.. code-block:: python

    from nap import serialiser
    from nap.serialiser import fields

    class PostSerialiser(serialiser.ModelSerialiser):
        class Meta:
            model = models.Post

        tags = fields.Field()

        def deflate_tags(self, obj, \**kwargs):
            '''Turn the tags into a flat list of names'''
            return [tag.name for tag in obj.tags.all()]


publishers.py
-------------

.. code-block:: python

    from nap import rest
    from .serialiser import PostSerialiser

    class PostPublisher(rest.ModelPublisher):
        serialiser = PostSerialiser()

urls.py
-------

.. code-block:: python

    from .publishers import PostPublisher

    urlpatters = patterns('',
        (r'^api/', include(PostPublisher.patterns())),
    )

