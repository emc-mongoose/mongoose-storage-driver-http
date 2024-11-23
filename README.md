[![Gitter chat](https://badges.gitter.im/emc-mongoose.png)](https://gitter.im/emc-mongoose)
[![Issue Tracker](https://img.shields.io/badge/Issue-Tracker-red.svg)](https://mongoose-issues.atlassian.net/projects/GOOSE)
[![CI status](https://gitlab.com/emc-mongoose/mongoose-storage-driver-http/badges/master/pipeline.svg)](https://gitlab.com/emc-mongoose/mongoose-storage-driver-http/commits/master)
[![Tag](https://img.shields.io/github/tag/emc-mongoose/mongoose-storage-driver-http.svg)](https://github.com/emc-mongoose/mongoose-storage-driver-http/tags)
[![Maven metadata URL](https://img.shields.io/maven-metadata/v/http/central.maven.org/maven2/com/github/emc-mongoose/mongoose-storage-driver-http/maven-metadata.xml.svg)](http://central.maven.org/maven2/com/github/emc-mongoose/mongoose-storage-driver-http)
[![Sonatype Nexus (Releases)](https://img.shields.io/nexus/r/http/oss.sonatype.org/com.github.emc-mongoose/mongoose-storage-driver-http.svg)](http://oss.sonatype.org/com.github.emc-mongoose/mongoose-storage-driver-http)
[![Docker Pulls](https://img.shields.io/docker/pulls/emcmongoose/mongoose-storage-driver-http.svg)](https://hub.docker.com/r/emcmongoose/mongoose-storage-driver-http/)

# HTTP Storage Driver

## 1. Configuration Reference

| Name                                           | Type         | Default Value    | Description                                      |
|:-----------------------------------------------|:-------------|:-----------------|:-------------------------------------------------|
| storage-net-http-headers                       | Map          | { "Connection" : "keep-alive", "Date": "#{date:formatNowRfc1123()}%{date:formatNowRfc1123()}", "User-Agent" : "mongoose-storage-driver-http/4.2.6" } | Custom HTTP headers section. A user may place here a key-value pair which will be used as HTTP header. The headers will be appended to every HTTP request issued.
| storage-net-http-uri-args                      | Map          | {}               | Custom URI query arguments according [RFC 2396](http://www.ietf.org/rfc/rfc2396.txt).The headers will be appended to every HTTP request issued.
| storage-net-http-read-metadata-only            | Map          | false            | Specifies whether Mongoose issues GET request or HEAD. HEAD is used when enabled.
| storage-net-http-max-chunk-size                | Integer      | 65536            | The limit, in bytes, at which Netty will send a chunk down the pipeline.

## 2. Custom HTTP Headers

Scenario example:
```javascript
var customHttpHeadersConfig = {
    "storage" : {
        "net" : {
            "http" : {
                "headers" : {
                    "header-name-0" : "header_value_0",
                    "header-name-1" : "header_value_1",
                    // ...
                    "header-name-N" : "header_value_N"
                }
            }
        }
    }
};

Load
    .config(customHttpHeadersConfig)
    .run();
```

**Note**:
> Don't use the command line arguments for the custom HTTP headers setting.

### 2.1. Expressions

Scenario example, note both the parameterized header name and value:
```javascript
var varHttpHeadersConfig = {
    "storage" : {
        "net" : {
            "http" : {
                "headers" : {
                    "x-amz-meta-${math:random(30) + 1}" : "${date:format("yyyy-MM-dd'T'HH:mm:ssZ").format(date:from(rnd.nextLong(time:millisSinceEpoch())))}"
                }
            }
        }
    }
};

Load
    .config(varHttpHeadersConfig)
    .run();
```

## 3. Custom URI Arguments

Custom URI query arguments may be set in the same way as custom HTTP headers.

```javascript
var uriQueryConfig = {
    "storage" : {
        "net" : {
            "http" : {
                "uri" : {
                    "args" : {
                        "foo": "bar",
                        "key1" : "val1"
                    }
                }
            }
        }
    }
};

Load
    .config(uriQueryConfig)
    .run();
```

will produce the HTTP requests with URIs like:
`/20190306.104255.627/kticoxcknpuy?key1=val1&foo=bar`


**Note**:
> Don't use the command line arguments for the custom HTTP URI query arguments setting.

### 3.1. Expressions

Example:
```javascript
var uriQueryConfig = {
    "storage" : {
        "net" : {
            "http" : {
                "uri" : {
                    "args" : {
                        "foo${rnd.nextInt()}" : "bar${time:millisSinceEpoch()}",
                        "key1" : "${date:formatNowIso8601()}",
                        "${e}" : "${pi}"
                    }
                }
            }
        }
    }
};

Load
    .config(uriQueryConfig)
    .run();
```

will produce the HTTP requests with URIs like:
`/20190306.104255.627/kticoxcknpuy?key1=2019-03-06T10:42:56,768&2.718281828459045=3.141592653589793&foo1130828259=bar1551868976768`

**Note**:
> Don't use both synchronous and asynchronous expressions for the query args simultaneously. All configured query args
> are collected into the single expression input.
