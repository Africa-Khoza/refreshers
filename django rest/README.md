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
    `snippet` is an instance of the model Snippets

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
    - Specify api view as parameter, eg. `class SnippetDetail(APIView):`
    - Handle request methods using functions instead. eg. `def get(self, request, format=None):` or `def post(self, request, format=None):`

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

12. Adding endpoints for our User models
    - Create users and add user as foreign key `owner = models.ForeignKey('auth.User', related_name='snippets', on_delete=models.CASCADE)`
    - Create user serializers and link to model `snippets = serializers.PrimaryKeyRelatedField(many=True, queryset=Snippet.objects.all())`
    - Associate Users to snippet,implicitly add a new user parameter to the create method:

        ```python
        def perform_create(self, serializer):
        serializer.save(owner=self.request.user)
        ```

    - And update your `<Model>Serializer` accordingly: `owner = serializers.ReadOnlyField(source='owner.username')`

13. Adding required permissions to views
    REST framework includes a number of permission classes that we can use to restrict who can access a given view. In this case the one we're looking for is IsAuthenticatedOrReadOnly, which will ensure that authenticated requests get read-write access, and unauthenticated requests get read-only access.

    - First add the following import in the views module `from rest_framework import permissions`
    - Then, add the following property to views requiring authentication. `permission_classes = [permissions.IsAuthenticatedOrReadOnly]`

14. Adding login to the Browsable API
    - Add `from django.urls import path, include` to the top of the `urls.py` file and add

        ```python
        urlpatterns += [
            path('api-auth/', include('rest_framework.urls')),
        ]
        ```

15. Enable instance owner to edit and delete instance while others can only view.
    - Create new `permissions`.py file and add

        ```python
        from rest_framework import permissions


        class IsOwnerOrReadOnly(permissions.BasePermission):
            """
            Custom permission to only allow owners of an object to edit it.
            """

            def has_object_permission(self, request, view, obj):
                # Read permissions are allowed to any request,
                # so we'll always allow GET, HEAD or OPTIONS requests.
                if request.method in permissions.SAFE_METHODS:
                    return True

                # Write permissions are only allowed to the owner of the snippet.
                return obj.owner == request.user
        ```

    - Add the new custom permission to the appropriate views.

16. Creating an endpoint for the root of our API instead of only having endpoints to objects.

    - Add the following to `views.py`:

        ```python
        from rest_framework.decorators import api_view
        from rest_framework.response import Response
        from rest_framework.reverse import reverse


        @api_view(['GET'])
        def api_root(request, format=None):
            return Response({
                'users': reverse('user-list', request=request, format=format),
                'snippets': reverse('snippet-list', request=request, format=format)
            })
        ```

    - Add line `path('', views.api_root),` to `urls.py`.

17. Creating an endpoint for the highlighted snippets - not sure what this means, ie. how it changes the functionality of the code.
    - Add to `views.py`:

        ```python
        from rest_framework import renderers

        class SnippetHighlight(generics.GenericAPIView):
            queryset = Snippet.objects.all()
            renderer_classes = [renderers.StaticHTMLRenderer]

            def get(self, request, *args, **kwargs):
                snippet = self.get_object()
                return Response(snippet.highlighted)
        ```

    - Add `path('snippets/<int:pk>/highlight/', views.SnippetHighlight.as_view()),` to `urls.py`

18. Hyperlinking our API - this is one of the many ways to create a relationship between to entities in our project, read more [here](https://www.django-rest-framework.org/tutorial/5-relationships-and-hyperlinked-apis/#hyperlinking-our-api)
    - Update your `serializers.py` with the following code

    ```python
    class SnippetSerializer(serializers.HyperlinkedModelSerializer):
    owner = serializers.ReadOnlyField(source='owner.username')
    highlight = serializers.HyperlinkedIdentityField(view_name='snippet-highlight', format='html')

    class Meta:
        model = Snippet
        fields = ['url', 'id', 'highlight', 'owner',
                  'title', 'code', 'linenos', 'language', 'style']


    class UserSerializer(serializers.HyperlinkedModelSerializer):
        snippets = serializers.HyperlinkedRelatedField(many=True, view_name='snippet-detail', read_only=True)

        class Meta:
            model = User
            fields = ['url', 'id', 'username', 'snippets']
    ```

19. Adding pagination
    - Add the following code to `settings.py`

    ```python
    REST_FRAMEWORK = {
        'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
        'PAGE_SIZE': 10
    }
    ```

Last point: continue to part 6 of tutorial.
