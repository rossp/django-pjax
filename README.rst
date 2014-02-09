Django-PJAX
===========

This is a Django helper for `defunkt's jquery-pjax`__. 

Django-PJAX requires Django 1.3.

What's PJAX?
------------

__ https://github.com/defunkt/jquery-pjax

PJAX is essentially AHAH__ ("Asynchronous HTML and HTTP"), except with real
permalinks and a working back button. It lets you load just a portion of a
page (so things are faster) while still maintaining the usability of real
links.

__ http://www.xfront.com/microformats/AHAH.html

A demo makes more sense, so `check out the one defunkt put together`__

__ http://pjax.heroku.com/

Usage
-----

First, read about `how to use jQuery-PJAX`__ and pick one of the techniques there.

__ https://github.com/defunkt/jquery-pjax

Next, make sure the views you're PJAXing are using TemplateResponse__. You can't use Django-PJAX with a normal ``HttpResponse``; only ``TemplateResponse``. Decorate these views with the ``pjax`` decorator::

    from djpjax import pjax
    
    @pjax()
    def my_view(request):
        return TemplateResponse(request, "template.html", {'my': 'context'})

__ http://django.me/TemplateResponse

After doing this, if the request is made via jQuery-PJAX, the ``@pjax()``
decorator will automatically swap out ``template.html`` for
``template-pjax.html``. 

More formally: if the request is a PJAX request, the template used in your
``TemplateResponse`` will be replaced with one with ``-pjax`` before the file
extension. So ``template.html`` becomes ``template-pjax.html``,
``my.template.xml`` becomes ``my.template-pjax.xml``, etc. If there's no file
extension, the template name will just be suffixed with ``-pjax``.

You can also manually pick a PJAX template by passing it as an argument to
the decorator::

    from djpjax import pjax
    
    @pjax("pjax.html")
    def my_view(request):
        return TemplateResponse(request, "template.html", {'my': 'context'})

If you'd like to use Django 1.3's class-based views instead, a PJAX Mixin class
is provided as well. Simply use ``PJAXResponseMixin`` where you would normally have
used ``TemplateResponseMixin``, and your ``template_name`` will be treated the same
way as above. You can alternately provide a ``pjax_template_name`` class variable
if you want a specific template used for PJAX responses::

    from django.views.generic import View
    from djpjax import PJAXResponseMixin

    class MyView(PJAXResponseMixin, View):
        template_name = "template.html"
        pjax_template_name = "pjax.html"

        def get(self, request):
            return self.render_to_response({'my': 'context'})

That's it!

Using Template Extensions
-------------------------

If the content in your ``template-pjax.html`` file is very similar to your 
``template.html`` an alternative method of operation is to use the decorator 
``pjaxtend``, as follows::

    from djpjax import pjaxtend
    
    @pjaxtend()
    def my_view(request):
        return TemplateResponse(request, "template.html", {'my': 'context'})

Then, in your ``template.html`` file you can do the following::

    {% extends parent %}
    ...
    ...

Note that the template will extend ``base.html`` unless its a pjax request 
in which case it will extend ``pjax.html``.
 
If you want to define the parent for a standard http or pjax request, you can do 
so as follows::
 
    from djpjax import pjaxtend
    
    @pjaxtend('someapp/base.html', 'my-pjax-extension.html')
    def my_view(request):
        return TemplateResponse(request, "template.html", {'my': 'context'})
 
Using this approach you don't need to create many ``*-pjax.html`` files.

If you have a collision with the variable name ``parent`` you can specify the 
context variable to use as the third parameter to pjaxtexd, as follows::

	from djpjax import pjaxtend
    
    @pjaxtend('someapp/base.html', 'my-pjax-extension.html', 'my_parent')
    def my_view(request):
        return TemplateResponse(request, "template.html", {'my': 'context'})

Which would require the following in your template:

    {% extends my_parent %}
    ...
    ...

 
Testing
-------

Tests are run using nosetests. To install::

	pip install nose

And to run the tests::

	nosetests tests.py
