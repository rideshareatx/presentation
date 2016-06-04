<!-- .slide: data-background="/Users/sblackmon/Documents/10000000000004B0000002A3105AAF6C6DE93BAD.jpg" -->
### ApacheCon NA 2016

#### Generating many resources from one set of schemas

![streams_logo.jpg](streams_logo.jpg "streams_logo.jpg")

<->

### Apache Streams (incubating)

- Unify a diverse world of digital profiles and online activities into common vocabularies and formats
- Facilitate easy and efficient collection, normalization, and processing.
- Make these datasets accessible across a variety of databases, devices, and platforms.
    * streaming
    * browsing
    * search
    * sharing
    * analytics
    * machine learning

<->

### Apache Streams Intro

===

#### Modules

Each module has it's own POM and dependency tree.

Each stream deployment needs to import only the modules it needs for what it wants to do.

===

#### Component

Components are the classes that do stuff within a stream.

Components are assembled into pipelines and executed using a runtime.

===

#### Architecture

![architecture.dot.svg](http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-master/architecture.dot.svg "architecture.dot.svg")

===

#### Pipelines

A Pipeline is a set of collection, processing, and storage components structured in a directed graph (cycles may be permitted)

Pipelines are packaged, deployed, started, and stopped together.

===

#### Runtimes

A Runtime is a module containing bindings that help setup and run a pipeline.

Runtimes may submit pipeline binaries to an existing cluster, or may launch the process(es) to execute the stream directly.  

<->

#### Example

![example.dot.svg](example.dot.svg "example.dot.svg")

<->

#### Schemas

Streams contains libraries and patterns for:

- specifying document schemas
- converting documents to and from ActivityStreams format
- generating source and resource files for binding to data objects in those formats.

<->

## What are Activity Streams?

Apache Streams has a preference for ActivityStreams formatted messages.

===

An activity has several standard primitive fields:

- id (required)
- verb (required)
- content (optional)
- summary (optional)
- published (optional)
- url (optional)

===

An activity has several standard sub-object fields:

- actor (required)
- object (optional)
- target (optional)
- generator (optional)
- provider (optional)

===

Activity objects have several standard fields:

- id (required)
- objectType (required)
- displayName (required)
- url (optional)

<->

## What is a schema?

A document that defines the characteristics of another document.

===

#### Example Schemas

===

##### Document: media_link

[src/test/resources/media_link.json](http://streams.staging.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-pojo/activity.json "src/test/resources/media_link.json")

    {
        "$schema": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-schemas/media_link.json#",
        "duration": 30000,
        "height": 480,
        "width": 640,
        "url": "http://youtube.com/s7hc30sb"
    }

===

##### Schema: media_link

[src/main/jsonschema/media_link.json](http://streams.staging.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-pojo/activity.json "src/test/resources/media_link.json")

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0",
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-schemas/media_link.json#",
        "type": "object",
        "title": "media_link",
        "javaInterfaces": ["java.io.Serializable"],
        "description": "Visual representation of an object in the form of an image, video or embedded HTML fragments",
        "properties": {
            "duration": {
                "title": "duration",
                "type": "number",
                "description": "A hint to the consumer about the length, in seconds, of the media resource identified by the url property. A media link MAY contain a \"duration\" property when the target resource is a time-based media item such as an audio or video."
            },
            "height": {
                "title": "height",
                "type": "integer",
                "description": "A hint to the consumer about the height, in pixels, of the media resource identified by the url property. A media link MAY contain a height property when the target resource is a visual media item such as an image, video or embeddable HTML page."
            },
            "width": {
                "title": "width",
                "type": "integer",
                "description": "A hint to the consumer about the width, in pixels, of the media resource identified by the url property. A media link MAY contain a width property when the target resource is a visual media item such as an image, video or embeddable HTML page."
            },
            "url": {
                "type": "string",
                "required": true,
                "description": "The IRI of the media resource being linked. A media link MUST have a url property."
            }
        }
    }

===

##### Document: place

[src/test/resources/objects/place.json](http://streams.staging.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-pojo/activity.json "src/test/resources/objects/place.json")

    {
        "objectType": "place",
        "displayName": "Some Random Location on Earth",
        "position": {
        "latitude": 34.34,
        "longitude": -127.23,
        "altitude": 100.05
        }
    }

===

##### Schema: place

[src/main/jsonschema/objectTypes/place.json](http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-schemas/objectTypes/place.json "src/main/jsonschema/objectTypes/place.json")

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0",
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-schemas/objectTypes/place.json#",
        "type": "object",
        "title": "place",
        "extends": {
            "$ref": "../object.json"
        },
        "properties": {
            "objectType": {
                "type": "string",
                "default": "place"
            },
            "address": {
              "type": "object",
              "extends": {
                "$ref": "http://www.json-schema.org/address"
              }
            },
            "position": {
                "type": "object",
                "properties": {
                    "altitude": {
                      "type": "number"
                    },
                    "latitude": {
                      "type": "number"
                    },
                    "longitude": {
                      "type": "number"
                    }
                }
            }
        }
    }

<->

## What's in a schema?

===

#### Basics

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0",
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-schemas/objectTypes/place.json#",
    }

===

#### Primitives

    {
        "type": "object",
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
          "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "#",
        "javaType" : "org.apache.streams.twitter.pojo.Tweet",
        "javaInterfaces": ["java.io.Serializable"],
        "properties": {
            "text": {
                "type": "string"
            },
            "retweeted": {
                "type": "boolean"
            },
            "in_reply_to_screen_name": {
                "type": "string"
            }
        }
    }

===

#### Formats

    {
        "type": "object",
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
          "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "#",
        "javaType" : "org.apache.streams.twitter.pojo.Tweet",
        "javaInterfaces": ["java.io.Serializable"],
        "properties": {
            "created_at": {
                "type" : "string",
                "format" : "date-time"
            }
        }
    }

===

#### Enumerations

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "#",
        "type": "object",
        "javaType" : "org.apache.streams.twitter.pojo.UserstreamEvent",
        "javaInterfaces": ["java.io.Serializable"],
        "description": "",
        "properties": {
            "event_type": {
                "type": "string",
                "enum" : [
                    "access_revoked",
                    "block",
                    "unblock",
                    "favorite",
                    "unfavorite",
                    "follow",
                    "unfollow",
                    "list_created",
                    "list_destroyed",
                    "list_updated",
                    "list_member_added",
                    "list_member_removed",
                    "list_user_subscribed",
                    "list_user_unsubscribed",
                    "user_update"
                ]
            }
        }
    }

===

#### Arrays

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "#",
        "type": "object",
        "javaType" : "org.apache.streams.twitter.pojo.FriendList",
        "javaInterfaces": ["java.io.Serializable"],
        "properties": {
            "friends": {
                "type": "array",
                "items": {
                    "type": "integer"
                }
            },
            "friends_str": {
                "type": "array",
                "items": {
                    "type": "string"
                }
            }
        }
    }

===

#### Sub-Objects

    {
        "id": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-contrib/streams-provider-twitter/User.json#",
        "entities": {
            "type": "object",
            "properties": {
                "user_mentions": {
                    "type": "array",
                    "items": {
                        "type": "object",
                        "id": {
                            "ignore_malformed": false,
                            "type": "integer"
                        },
                        "name": {
                            "type": "string"
                        },
                        "indices": {
                            "type": "array",
                            "items": [{
                                "type" : "integer"
                            }]
                        },
                        "screen_name": {
                            "type": "string"
                        },
                        "id_str": {
                            "type": "string"
                        }
                    }
                }
            }
        }
    }

===

#### Inheritance / References

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "#",
        "type": "object",
        "javaType" : "org.apache.streams.twitter.pojo.Retweet",
        "javaInterfaces": ["java.io.Serializable"],
        "extends": {
            "$ref":"tweet.json"
        },
        "properties": {
            "retweeted_status": {
                "type": "object",
                "required" : false,
                "description" : "Describes the tweet being retweeted.",
                "$ref" : "tweet.json"
            }
        }
    }

<->

### Why use schemas?

##### Interoperability

Describe real-world data with artifacts that are human and machine read-able.

Get all stakeholders on the same page as to what types and fields exist and what they represent.

##### Validation

Detect problems with how your code manipulates data at compile time.

Enforce constraints on data and identify invalid documents in-flight.

##### Portability

Schemas are programming language and execution technology agnostic.

Schemas can be published across network and organization boundaries.

<->

## Schema Best Practices

===

#### $schema

Document:

    {
        "$schema": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-schemas/activity.json"
    }

===

#### $maven

Schema:

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$groupId": "org.apache.streams",
        "$artifactId": "streams-schemas",
        "$version": "0.3-incubating-SNAPSHOT"
    }

Maven:

    <dependency>
        <groupId>org.apache.streams</groupId>
        <artifactId>streams-schemas</artifactId>
        <version>0.3-incubating-SNAPSHOT</version>
    </dependency>

===

#### Publish

Put your schemas on the web (or intranet)

    <distributionManagement>
        <site>
            <id>site.streams.master</id>
            <url>scm:svn:https://svn.apache.org/repos/asf/incubator/streams/site/trunk/content/site/${project.version}/${project.artifactId}/</url>
        </site>
    </distributionManagement>

===

#### Continuous Deployment

Push schemas automatically with every release, release candidate, and build snapshot

    mvn install deploy site-deploy

Use a stable url with a predictable path structure

http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-pojo/index.html

===

#### Recycle

Recycle upstream object, object fragments, fields, and enumerations

<->

## Resolving Schemas

===

#### Relative path

[collection.json](http://streams.staging.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-schemas/collection.json "collection.json")

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "id": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-schemas/collection.json#",
        "type": "object",
        "title": "collection",
        "properties": {
            "items": {
                "type": "array",
                "required": true,
                "items": {
                    "type": "object",
                    "extends": {
                        "$ref": "./object.json"
                    }
                }
            }
        }      
    }

===

#### Absolute url

[objectTypes/place.json](http://streams.staging.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-schemas/objectTypes/place.json "objectTypes/place.json")

    {
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0",
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-schemas/objectTypes/place.json#",
        "type": "object",
        "properties": {
            "address": {
              "type": "object",
              "extends": {
                "$ref": "http://www.json-schema.org/address"
              }
            }
        }
    }

===

#### Within file

[Tweet.json](http://streams.staging.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-contrib/streams-provider-twitter/Retweet.json "Tweet.json")

    {
        "type": "object",
        "$schema": "http://json-schema.org/draft-03/schema",
        "$license": [
            "http://www.apache.org/licenses/LICENSE-2.0"
        ],
        "id": "http://streams.incubator.apache.org/site/0.3-incubating-SNAPSHOT/streams-project/streams-contrib/streams-provider-twitter/tweet.json#",
        "javaType" : "org.apache.streams.twitter.pojo.Tweet",
        "javaInterfaces": ["java.io.Serializable"],
        "properties": {
            "retweeted_status": {
                "type": "object",
                "required" : false,
                "description" : "Describes the tweet being retweeted.",
                "$ref" : "#"
            }
        }
    }

<->

## Schema Design Considerations

#### Questions to ask

- Who generates the data?
- Whose code could change the data format?
- What types are declared?
- What fields are observed?
- Is there an existing object type field?
- Do any fields represent dates or date-times?
- Do any other fields represent enumerations?
- Is the generator reusing fields or object fragments?
- What urls are provided and why?

<->

## Source and Resource Generation

Use schemas to make our lives easier.

- Source

    * Used primarily for development and testing
    * Make it easier to write quality readable code

- Resource

    * Used primarily for operations and integration
    * Configure other technologies in our stack

===

#### Proposed Approach

1. Collect, host, and maintain schemas for widely used data types
2. Write transforms to convert a directory of schemas into a directory of (re)source files
3. Publish binary artifacts runnable via CLI, maven, or JDK to apply transforms.
4. Externalize classes that recursively analyze, resolve, and traverse schemas.

===

##### Collect, host, and maintain schemas for widely used data types

- streams-schemas contains json schemas for the full activitystreams 1.0 vocabulary

        $ find \* -name \*.json | grep schemas | grep jsonschema | wc -l
        133

- streams-contrib contains json schemas for non-activitystreams APIs

        $ find \* -name \*.json | grep provider | grep jsonschema | wc -l
        39

===

##### Write transforms to convert a directory of schemas into a directory of (re)source files

![plugins.dot.svg](plugins.dot.svg "plugins.dot.svg")

===

##### Publish artifacts that apply transforms.

- Maven

        mvn org.apache.streams.plugins:streams-plugin-pojo:0.3-incubating-SNAPSHOT:generate-sources

- CLI

        java -jar streams-plugin-pojo-jar-with-dependencies.jar StreamsPojoSourceGenerator src/main/jsonschema target/generated-sources

- SDK

        StreamsPojoGenerationConfig config = new StreamsPojoGenerationConfig();
        config.setSourceDirectory("src/main/resources");
        config.setTargetDirectory("target/generated-sources");
        StreamsPojoSourceGenerator generator = new StreamsPojoSourceGenerator(config);
        (new Thread(generator)).start();

===

##### Externalize classes that recursively analyze, resolve, and traverse schemas.

- Schema

        public class Schema {
            private final URI id;
            private final URI uri;
            private final JsonNode content;
            private final Schema parent;
            private final boolean generate;
        }

- SchemaStore

        package org.apache.streams.schema;

        import com.fasterxml.jackson.databind.node.ObjectNode;
        import com.google.common.base.Optional;

        import java.net.URI;
        import java.util.Comparator;
        import java.util.Iterator;

        public interface SchemaStore extends Comparator<Schema> {

            Schema create(URI uri);

            Schema create(Schema parent, String path);

            void clearCache();

            Integer getSize();

            Optional<Schema> getById(URI id);

            Optional<Schema> getByUri(URI uri);

            Integer getFileUriCount();

            Integer getHttpUriCount();

            Iterator<Schema> getSchemaIterator();

            ObjectNode resolveProperties(Schema schema, ObjectNode fieldNode, String resourceId);

            @Override
            int compare(Schema left, Schema right);
        }
