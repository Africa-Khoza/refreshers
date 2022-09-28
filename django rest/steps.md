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

Last point: start with part 2 of tutorial.
