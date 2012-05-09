
*NOTE*  please consider the django-adminfiles application as a better alternative:

https://github.com/carljm/django-adminfiles


====================
Django Admin Uploads
====================

File/media browser for Django admin interface. Designed to insert
images and files into textareas in the admin interface with a simple
GUI interface.

Currently, the following features have been written and are working:

- Django 1.3 support
- WYMeditor (bundled from master on 3rd August 2011).
    https://github.com/wymeditor/wymeditor/tree/master/src/wymeditor
- TinyMCE support (probably but not sure, still need to test)

Installation
============

#. Add the `admin_uploads` directory to your Python path.
#. Add `admin_uploads` to your `INSTALLED_APPS` setting so Django can
 find the template files and staticfiles associated with the Admin
 Uploads application.
#. Create an empty uploads folder in your MEDIA_ROOT where images will
 be saved.
#. Make sure you are using Django 1.3 convention of
 STATIC_ROOT/STATIC_URL for staticfiles and MEDIA_ROOT/MEDIA_URL for
 user generated content.

Configuration
=============

See Janis Leidel's blogpost for more comprehensive instructions on the
WYMeditor integration process in Django:
http://jannisleidel.com/2008/11/wysiwym-editor-widget-django-admin-interface/

#. Create a admin.py for any model that has a textfield for which you
 would like to apply either WYMEditor or WYMEditorUpload (with upload
 image capability). Then set the form in your Admin class, and
 register the model with the admin::

    from django import forms
    from django.db.models import get_model

    from django.contrib import admin

    from admin_uploads.widgets import WYMEditor, WYMEditorUpload

    from my_news_app.models import NewsEntry


    class NewsEntryAdminModelForm(forms.ModelForm):
        body = forms.CharField(required=False, widget=WYMEditorUpload())
        notes = forms.CharField(required=False, widget=WYMEditor())

        class Meta:
            model = NewsEntry


    class NewsEntryAdmin(admin.ModelAdmin):
        form = NewsEntryAdminModelForm
        list_display = ( "title", "status", "on_sites")
        prepopulated_fields = {"slug": ("title",)}


    admin.site.register(NewsEntry, NewsEntryAdmin)


For the javascript to work, you'll have to provide your own jQuery by creating a
template ``templates/admin/base_site.html`` and including the link to
the jQuery static file.::

    {% extends "admin/base.html" %}

    {% block extrahead %}
        <script type="text/javascript" src="{{ STATIC_URL }}js/jquery.min.js"></script>
    {% endblock %}

I'm using jQuery 1.6.2 without issue, but it should work fine
with older versions back to at least 1.3/1.4 as well. As noted in the
issues it would be more efficient to use the bundled jQuery.

TODOs and BUGS (out of date)
============================
#. Test Cross Browser
#. Test TinyMCE (I've only yet tested WYMeditor)
#. Add setting to choose if Flickr, YouTube, options available
#. on delete, remove deleted file/image from DOM
#. get request variable in widgets.py
#. use image icon on wymeditor & TinyMCEfor uploading? (easier way to
 use default WYM settings)
#. Add way to manage WYMeditor & TinyMCE options
#. Use Django's bundled version of jQuery.

BUG
#. Doesn't insert when multiple WYMeditors on same admin.
