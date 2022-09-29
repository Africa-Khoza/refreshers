# Django Rest API

Setting up and building a rest api. Followed the tutorial on the official django rest api site.

## Steps

### Setting up

1. Install rest feature: `pip install djangorestframework`
2. Add the framework to the project `'rest_framework'` in `INSTALLED_APPS`
3. Create models
4. Create ModelSerializer class (add serializers.py in the app folder) and use:

    ```python
    class <Model>Serializer(serializers.ModelSerializer):
        class Meta:
            model = <Model>
            fields = ['id', <Model fields>]
    ```

5. Serialize (Model to Json):

    ```python
    serializer = <Model>Serializer(snippet) 
    serializer.data
    ```

6. Deserialize (Json to model):

    ```python
    data = JSONParser().parse(request)
    serializer = <Model>Serializer(data=data)
    serializer.is_valid()
    serializer.validated_data
    serializer.save()
    ```

7. Wrapping API views
    - The `@api_view` decorator for working with function based views.
    - The `APIView` class for working with class-based views.

8. Adding optional format suffixes to our URLs
To take advantage of the fact that our responses are no longer hardwired to a single content type let's add support for format suffixes to our API endpoints. Using format suffixes gives us URLs that explicitly refer to a given format, and means our API will be able to handle URLs such as <http://example.com/api/items/4.json>.

    - Start by adding a format keyword argument to both of the views, like so.

        ```python
        def snippet_list(request, format=None):
        ```

    - Then update the snippets/urls.py file slightly, to append a set of format_suffix_patterns in addition to the existing URLs.

        ```python
        from rest_framework.urlpatterns import format_suffix_patterns
        ...
        urlpatterns = format_suffix_patterns(urlpatterns)
        ```

9. Changing to class views
    - Specify api view as parameter, eg. 'class SnippetDetail(APIView):'
    - Handle request methods using functions instead.

10. Mixins: access api functions
    - first, add `generics.GenericAPIView)` as a parameter to the class

    ```python
    mixins.ListModelMixin - .list()
    mixins.CreateModelMixin - .create()
    mixins.UpdateModelMixin - .put()
    ```

11. Even better mixins!

    ```python
    (mixins.ListModelMixin,
    mixins.CreateModelMixin,
    generics.GenericAPIView) -> (generics.ListCreateAPIView)

    (mixins.RetrieveModelMixin,
    mixins.UpdateModelMixin,
    mixins.DestroyModelMixin,
    generics.GenericAPIView) -> (generics.RetrieveUpdateDestroyAPIView)
    ```

Last point: start with part 4 of tutorial.
