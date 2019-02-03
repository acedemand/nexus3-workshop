# Nexus3 Staging - Tagging
___
###### Notes;
- Staging and Tagging just available Nexus Repository Manager Pro
- Staging and Tagging capabilites have been made available by way of a REST API.
- For more information [Sonatype Document](https://help.sonatype.com/repomanager3).
## Tagging

Tagging is a feature available in Nexus Repository Manager Pro that provides the ability to mark a set of components with a tag so they can be logically associated to each other. 
    
Step by step tagging; 

#### Add Tag

```
POST /service/rest/v1/tags 
```
```sh
curl -u admin:admin123 -X POST --header 'Content-Type: application/json' http://127.0.0.1:8081/service/rest/v1/tags \
  -d '{
    "name": "project-abc-142",
    "attributes": {
        "jvm": "9",
        "built-by": "jenkins"
    }
}'
```
You can see the tag that you added;
![alt text](https://raw.githubusercontent.com/acedemand/nexus3-workshop/master/staging%26tagging-repo/pics/TagsMenu.png "Tagging Page")

#### Associate Components with a Tag
This endpoint allows you to search for components and associate them to an existing tag.
```
POST /service/rest/v1/tags/associate/{tagName}
```
```sh
curl -u admin:admin123 -X POST 'http://127.0.0.1:8081/service/rest/v1/tags/associate/project-abc-142?
    repository=my-company&
    group=com.mycompany&
    name=project-abc&
    version=2.1.1'
```
You can see the components tags;
![alt text](https://raw.githubusercontent.com/acedemand/nexus3-workshop/master/staging%26tagging-repo/pics/ComponenetMenu.png "Component Page")

#### Tagging During Component Upload
Tagging components can also be accomplished by including a tag when exercising the upload component REST endpoint provided by the Components API. 
```sh
curl -v -u admin:admin123 -X POST 'http://localhost:8081/service/rest/v1/components?repository=my-company' \
   -F groupId=com.mycompany \
   -F artifactId=project-abc \
   -F version=2.1.1 \
   -F asset1=@project-abc-2.1.1.jar \
   -F asset1.extension=jar \
   -F tag=project-abc-142
```
## Staging
The staging functionality in Nexus Repository Manager Pro supports promotion of software components matching your organization's software development life cycle phases by moving those components between repositories.
#### Move
Allows all components and assets matching a specified search query to be moved to a target repository.
```
POST service/rest/v1/staging/move/{repository}
```
```sh
curl -u admin:admin123 -X POST 'http://127.0.0.1:8081/service/rest/v1/staging/move/uat?
    tag=project-abc-142'
```
The response will include a payload that shows the components that were moved.
```json
{
    "status": 200,
    "message": "Move Successful",
    "data": {
        "destination": "uat",
        "components moved": [
            {
                "name": "project-abc",
                "group": "com.mycompany",
                "version": "2.1.1"
            }
        ]
    }
}
```

#### Delete
Allows for the deletion of all components and assets matching a specified search query.
```
POST /service/rest/v1/staging/delete
```
```sh
curl -u admin:admin123 -X POST 'http://127.0.0.1:8081/service/rest/v1/staging/delete?tag=project-abc-142'
```
This will remove all the components with the tag project-abc-142. As with Promote, any search criteria can be used though within the context of staging the tag parameter is most common. See Search API for more.


