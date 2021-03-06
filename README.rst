======================
django-pure-pagination
======================

Description
======================

:Author:
    James Pacileo `@ignighted <http://twitter.com/ignighted>`_

:Version:
    0.1.0

:Description:
    django-pure-pagination provides advanced pagination features and is fully compatible with existing code based on Django's core pagination module. (aka no need to rewrite code!)

:Requirements:
    Django 1.2+


Introduction
============

The django app offers advanced pagination features without forcing major code changes within an existing project.

Django-pure-pagination is based upon Django's core pagination module and is therefore compatible with the existing api.

`Documentation for Django core pagination module <http://docs.djangoproject.com/en/dev/topics/pagination/>`_

Features
--------

1. Uses same API as **django.core.pagiantion** and therefore is fully compatible with existing code.

2. Has dynamic query string creation, which takes into consideration existing GET parameters.

3. Out-of-the-box html rendering of the pagination

4. Additional methods make it easier to render more advanced pagination templates.

TODO
----

1. generic views and class-based view Mixins

Installation
------------

Install package from PYPI:

::

    pip install django-pure-pagination

or clone and install from repository:

::

    git clone git@github.com:jamespacileo/django-pure-pagination.git
    cd django-pure-pagination
    python setup.py install

Add `purepagination` to INSTALLED_APPS

::

    INSTALLED_APPS = (
        ...
        'pure_pagination',
    )

Finally substitute **from django.core.paginator import Paginator** with **from pure_pagination import Paginator**

Settings
--------

A few settings can be set within settings.py

::

    PAGINATION_SETTINGS = {
        'PAGE_RANGE_DISPLAYED': 10,
        'MARGIN_PAGES_DISPLAYED': 2,
    }

**PAGE_RANGE_DISPLAYED** is the number of pages neighbouring the current page which will be displayed (default is 10)

**MARGIN_PAGES_DISPLAYED** is the number of pages neighbouring the first and last page which will be displayed (default is 2)

.. image:: http://i.imgur.com/LCqrt.gif

Usage example
-------------

Following is a simple example

view file: **views.py**

::

    # views.py
    from django.shortcuts import render_to_response

    from pure_pagination import Paginator, EmptyPage, PageNotAnInteger


    def index(request):

        try:
            page = request.GET.get('page', 1)
        except PageNotAnInteger:
            page = 1

        objects = ['john', 'edward', 'josh', 'frank']

        # Provide Paginator with the request object for complete querystring generation
        p = Paginator(objects, request=request)
        people = p.page(page)

        return render_to_response('index.html', {
            'people': people,
        }


template file: **index.html**

::

    {# index.html #}
    {% extends 'base.html' %}

    {% block content %}

    {% for person in people.object_list %}
        <div>
            First name: {{ person }}
        </div>
    {% endfor %}

    {# The following renders the pagination html #}
    <div id="pagination">
        {{ people.render }}
    </div>

    {% endblock %}


Usage
-----

There a few different way you can make use of the features introduced within django-pure-pagination.

Easiest way to render the pagination is to call the render method i.e. **{{ page.render }}**

Alternatively you can access the Page object low level methods yourself

::

    {% load i18n %}
    <div class="pagination">
        {% if current_page.has_previous %}
            <a href="?{{ current_page.previous_page_number.querystring }}" class="prev">&lsaquo;&lsaquo; {% trans "previous" %}</a>
        {% else %}
            <span class="disabled prev">&lsaquo;&lsaquo; {% trans "previous" %}</span>
        {% endif %}
        {% for page in current_page.pages %}
            {% if page %}
                {% ifequal page current_page.number %}
                    <span class="current page">{{ page }}</span>
                {% else %}
                    <a href="?{{ page.querystring }}" class="page">{{ page }}</a>
                {% endifequal %}
            {% else %}
                ...
            {% endif %}
        {% endfor %}
        {% if current_page.has_next %}
            <a href="?{{ current_page.next_page_number.querystring }}" class="next">{% trans "next" %} &rsaquo;&rsaquo;</a>
        {% else %}
            <span class="disabled next">{% trans "next" %} &rsaquo;&rsaquo;</span>
        {% endif %}
    </div>

