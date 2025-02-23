# Vietnamese Analysis Plugin for OpenSearch

Vietnamese Analysis plugin integrates Vietnamese language analysis into OpenSearch. It uses [C++ tokenizer for Vietnamese](https://github.com/coccoc/coccoc-tokenizer) library developed by
CocCoc team for their Search Engine and Ads systems.

The plugin provides `vi_analyzer` analyzer, `vi_tokenizer` tokenizer and `vi_stop` stop filter. The `vi_analyzer` is composed of the `vi_tokenizer` tokenizer and `stop` filter.

## Example output

```
GET /_analyze
{
  "analyzer": "vi_analyzer",
  "text": "công nghệ thông tin việt nam"
}
```
The above sentence would produce the following terms:
```
["công nghệ", "thông tin", "việt nam"]

```

## Configuration

The `vi_analyzer` analyzer accepts the following parameters:

- `dict_path` The path to tokenizer dictionary on system. Defaults to `/usr/share/tokenizer/dicts`.
- `keep_punctuation` Keep punctuation marks as tokens. Defaults to `false`.
- `split_url` If it's enabled (`true`), a domain `duydo.me` is split into  `["duy", "do", "me"]`.
  If it's disabled (`false`) `duydo.me` is split into `["duydo", "me"]`. Defaults to `false`.
  
- `stopwords` A pre-defined stop words list like `_vi_` or an array containing a list of stop words. Defaults to [stopwords.txt](src/main/resources/org/apache/lucene/analysis/vi/stopwords.txt) file. 
- `stopwords_path` The path to a file containing stop words.


### Example configuration
In this example, we configure the `vi_analyzer` analyzer to keep punctuation marks and to use the custom list of stop words:

```
PUT my-vi-index-00001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_vi_analyzer": {
          "type": "vi_analyzer",
          "keep_punctuation": true,
          "stopwords": ["rất", "những"]
        }
      }
    }
  }
}

GET my-vi-index-00001/_analyze
{
  "analyzer": "my_vi_analyzer",
  "text": "công nghệ thông tin Việt Nam rất phát triển trong những năm gần đây."
}
```

The above example produces the following terms:
```
["công nghệ", "thông tin", "việt nam", "phát triển", "trong", "năm", "gần đây", "."]

```
## Build from Source
### Step 1: Build C++ tokenizer for Vietnamese library
```sh
git clone https://github.com/coccoc/coccoc-tokenizer.git
cd coccoc-tokenizer && mkdir build && cd build
cmake -DBUILD_JAVA=1 ..
make install
```

Refer [the repo](https://github.com/coccoc/coccoc-tokenizer) for more information to build the library.

### Step 2: Build OpenSearch

```sh
git clone https://github.com/opensearch-project/OpenSearch.git
cd OpenSearch
./gradlew publishToMavenLocal
```
Refer the documentation for more details: https://github.com/opensearch-project/opensearch-plugins  
NOTE: Once OpenSearch is published to public repositories, this step will not be needed.

### Step 3: Build the plugin

Clone the plugin’s source code:

```sh
git clone https://github.com/duydo/opensearch-analysis-vietnamese.git
```

Edit the `opensearch-analysis-vietnamese/pom.xml` to change the version of OpenSearch (same as plugin version) you want to build the plugin with:

```xml
...
<groupId>org.opensearch</groupId>
<artifactId>opensearch-analysis-vietnamese</artifactId>
<version>1.0.0-SNAPSHOT</version>
...
 ```

Build the plugin:
```sh
cd opensearch-analysis-vietnamese
mvn package
```

### Step 4: Installation the plugin on OpenSearch

```sh
bin/opensearch-plugin install file://target/releases/opensearch-analysis-vietnamese-1.0.0-SNAPSHOT.zip
```

## Compatible Versions
| Vietnamese Analysis Plugin | OpenSearch      |
| -------------------------- | --------------- |
| master                     | 1.0.0-SNAPSHOT |

## Thanks to
- [JetBrains](https://www.jetbrains.com) has provided a free license for [IntelliJ IDEA](https://www.jetbrains.com/idea).
- [CocCoc](https://coccoc.com) team has provided their C++ Vietnamese tokenizer library as open source.

## License
    
    This software is licensed under the Apache 2 license, quoted below.

    Copyright by Duy Do

    Licensed under the Apache License, Version 2.0 (the "License"); you may not
    use this file except in compliance with the License. You may obtain a copy of
    the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
    WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
    License for the specific language governing permissions and limitations under
    the License.
