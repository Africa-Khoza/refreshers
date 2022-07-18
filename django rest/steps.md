# Django Rest API

Setting up and building a rest api. Followed the tutorial on the official django rest api site.

## Steps

### Setting up

1. Install rest feature: `pip install djangorestframework`
2. Add the framework to the project `'rest_framework'` in `INSTALLED_APPS`
3. Create models
4. Create ModelSerializer class (add serializers.py in the app folder) and use:

    ```
    class <Model>Serializer(serializers.ModelSerializer):
        class Meta:
            model = <Model>
            fields = ['id', <Model fields>]
    ```

5. Serialize (Model to Json):

    ```
    serializer = <Model>Serializer(snippet) 
    serializer.data
    ```

6. Deserialize (Json to model):

    ```
    serializer = <Model>Serializer(data=data)
    serializer.is_valid()
    serializer.validated_data
    serializer.save()
    ```

7. 
