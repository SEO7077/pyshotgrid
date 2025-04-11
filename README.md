<p align="center">
  <img src="https://github.com/fabiangeisler/pyshotgrid/blob/main/icons/pysg_logo.png?raw=true" />
</p>

`pyshotgrid` is a python package that gives you a pythonic and
object oriented way to talk to [Autodesk Flow Production Tracking](https://www.autodesk.com/products/flow-production-tracking/overview)
(formally known as ShotGrid).

# Quickstart

Install `pyshotgrid` via pip:

```shell
pip install pyshotgrid
```

You are now ready to use it in your project (For other installation methods see the
[Installation](https://fabiangeisler.github.io/pyshotgrid/installation.html) section in the documentation)!
Here is a quick example to list the "code" (aka. "name") of all shots from all projects:

```python
import pyshotgrid as pysg

site = pysg.new_site(base_url='https://example.shotgunstudio.com',
                     script_name='Some User',
                     api_key='$ome_password')

for project in site.projects():
    print(project["name"].get())
    for shot in project.shots():
        print(shot["code"].get())
```

# Features

In `pyshotgrid` you are working with [SGEntity][SGEntity] instances which each represent exactly one entity
in ShotGrid. Any operation on it is reflected to ShotGrid.
So for example you can :

* Get entity fields in ShotGrid
  ```python
  # Get the value of a field ...
  print(sg_project["name"].get())  # "foobar"
  # ... or get multiple fields at once.
  print(sg_project.get(["name", "tank_name"]))  # {"name": "foobar", "tank_name": "fb"}
  ```
* Update entity fields in ShotGrid
  ```python
  # Set the value of a field ...
  sg_project["name"].set("foobar")
  # ... or set multiple fields at once.
  sg_project.set({"name": "foobar", "tank_name": "fb"})
  ```
* Values are automatically converted to `pyshotgrid` objects which makes it
  possible to chain queries together.
  ```python
  # Name of the first Version in a Playlist.
  print(sg_playlist["versions"].get()[0]["code"].get())
  ```
* Get information about a field
  ```python
  print(sg_project["name"].data_type)     # "text"
  print(sg_project["name"].description)   # "The name of the project."
  print(sg_project["name"].display_name)  # "Project Name"
  ```
* Upload/Download to/from a field
  ```python
  sg_version['sg_uploaded_movie'].upload('/path/to/movie.mov')
  sg_version['sg_uploaded_movie'].download('/path/to/download/to/')
  ```
* Get the URL of the entity
  ```python
  print(sg_project.url)  # https://example.shotgunstudio.com/detail/Project/1
  ```
* Convert it to a regular dict, to use it in Autodesk [shotgun_api3][shotgun_api3].
  ```python
  sg_project.to_dict()  # {"type": "Project", "id": 1}
  ```
* Iterate over all fields
  ```python
  # Iterate over the fields directly to get some information about them...
  for field, value in sg_project.fields().items():
       print(field.display_name)
  # ... or iterate over the fields and values at the same time.
  for field_name, value in sg_project.all_field_values().items():
       print(field_name, value)
  ```
* Do you keep forgetting which field is the "name" of an entity? (was it "code" or "name"?)
  Just use the "SGEntity.name" property:
  ```python
  sg_project.name  # returns the "name" field.    Same as:  sg_project["name"]
  sg_shot.name     # returns the "code" field.    Same as:  sg_shot["code"]
  sg_task.name     # returns the "content" field. Same as:  sg_task["content"]
  ```

* It is possible to inherit from [SGEntity][SGEntity] and create implementations for specific
  entity types. `pyshotgrid` ships with a few common entities by default. For example
  the implementation for the `Project` entity ([SGProject][SGProject]) gives you additional functions
  to query shots, assets or publishes:
  ```python
  sg_project.shots()
  sg_project.assets()
  sg_project.publishes()
  ```
  Checkout the [overview of all the default entities][Overview default entities] and the
  [API documentation][API sg_default_entities] for all the extra functionality!
  As an additional bonus: You can customize and extend all these classes to your hearts content.
  Have a look at [How to add custom entities][How to add custom entities] in the docs.
