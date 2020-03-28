# tkit-mp-rest-client-plugin

Tkit microprofile  rest client generator plugin

[![License](https://img.shields.io/badge/license-Apache--2.0-green?style=for-the-badge&logo=apache)](https://www.apache.org/licenses/LICENSE-2.0)
[![Maven Central](https://img.shields.io/maven-central/v/org.tkit.maven/tkit-mp-restclient-plugin?logo=java&style=for-the-badge)](https://maven-badges.herokuapp.com/maven-central/org.tkit.maven/tkit-mp-restclient-plugin)

> Version 0.7.0 and later contains check for generated files. If the generator generated more files with the same
> name the generator throws exception. The combination of the parameters `apiName`, `pathPrefix` or `groupByTags` is use to generate
> corresponding java classes for the openAPI schema.

Example combination
* default configuration generated file for each REST path. `/admin/name`, `/admin` and `/user/name` will generate
two java classes `AdminRestClient` and `UserRestClient`
* if you add only `apiName=User` generator creates one java class `UserRestClient` for all REST method in openAPI schema.  
* `apiName=MyRest` and `pathPrefix=/admin` will generated one file `MyRestRestClient` for all REST method which start with `/admin` or `admin` path.
* `apiName=MyRest` and `pathPrefix=/` will generated one file with all REST method. This is equals to setting only the `apiName` parameter. 
* if you set the `groupByTags` to `true` the generator will group the REST api by the tags in the openAPI schema. This 
is default swagger generator but it could generated wrong java classes; depend on your openAPI schema and the parameter 
`apiName` and `pathPrefix` will be ignored.

> Method of the java class are base on the `operationId`. If there are same `operationId` in the same java class the tag 
> of the operation is add as prefix to the method. If there are same `tag`+ `operationId` methods in the same java classes 
> generator will add suffix `_<number>` to the method.
 
## Goal: codegen

```xml
<plugin>
    <groupId>org.tkit.maven</groupId>
    <artifactId>tkit-mp-restclient-plugin</artifactId>
    <version>0.7.0</version>
    <executions>
        <execution>
            <id>test</id>
            <goals>
                <goal>codegen</goal>
            </goals>
            <configuration>
                <inputSpec>src/main/resources/clients/openapi.yaml</inputSpec>
                <output>${project.build.directory}/generated-sources/mprestclient</output>
                <apiPackage>gen.org.tkit.test</apiPackage>
                <modelPackage>gen.org.tkit.test.model</modelPackage>
                <generateSupportingFiles>false</generateSupportingFiles>
                <apiInterfaceDoc>false</apiInterfaceDoc>
                <fieldGen>LOMBOK</fieldGen>
                <jsonLib>JACKSON</jsonLib>                
                <annotations>
                    <annotation>org.tkit.quarkus.log.interceptor.LoggerService</annotation>
                    <annotation>org.eclipse.microprofile.rest.client.inject.RegisterRestClient(configKey="my-client-key")</annotation>
                </annotations>
                <modelAnnotations>
                    <modelAnnotation>lombok.ToString</modelAnnotation>
                    <modelAnnotation>io.quarkus.runtime.annotations.RegisterForReflection</modelAnnotation>
                </modelAnnotations>
                <configOptions>
                    <sourceFolder>test</sourceFolder>
                </configOptions>
            </configuration>
        </execution>
    </executions>
</plugin>
```

#### Parameters

The plugin extends the parameter from: [Swagger maven plugin](https://github.com/swagger-api/swagger-codegen/tree/master/modules/swagger-codegen-maven-plugin)
Extended parameters:

|  Name | Default  | Values | Description  |
|---|---|---|---|
| formatter | true | | The google source code formatter  |
| apiName | | | The api name if this is set the generator will generate one file for all REST method |
| interfaceOnly | true | | The interface only |
| pathPrefix | | | The path prefix for all interfaces. Example 'v2/' or '/'. REST method which starts not with this prefix will be ignored. |
| apiSuffix | RestClient | | The api interface suffix |
| annotations | | | The list of custom annotations for the interface. |
| modelAnnotations | | | The list of custom annotations for the model. |
| restClient | true | | The flag to generate the micro-profile rest client for the interface. |
| returnResponse | true | | The return type will be the Response. |
| beanParamSuffix | BeanParam | | The bean parameter suffix. |
| beanParamCount | 9 | | The number of the parameters to group by the bean parameter. |
| jsonLib | JSONB | JACKSON,JSONB | The JSON implementation. |
| fieldGen | PUBLIC | LOMBOK,GET_SET,PUBLIC | The model field generator type. |
| dateLibrary | java8 | | The date library. |
| useBeanValidation | true | | Use the bean validation on the methods. |
| apiInterfaceDoc | true | | Generate the micro-profile annotation on the generated interface. |
| groupByTags | false | | Group the REST in the openAPI schema by tags (Default by swagger). Default is false to group the REST method by path |

## Release

### Create a release

```bash
mvn semver-release:release-create
```

### Create a patch branch
```bash
mvn semver-release:patch-create -DpatchVersion=x.x.0
```
