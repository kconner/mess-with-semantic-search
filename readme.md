# mess-with-semantic-search

The PyPI package `llm` can run an embedding model on your data to build a semantic search database that you can then run searches against. Let's try it.

## Prerequisites

```sh
brew install jq
uv tool install --python 3.12 llm
llm install llm-sentence-transformers
llm sentence-transformers register intfloat/e5-large-v2
```

## Get some data and organize it a bit

Visit this the [public dataset](https://opendata.vancouver.ca/explore/dataset/public-art). On the export tab, get JSON and save it as `public-art.json`.

```sh
jq '[ .[] | select(.status == "In place") | { id: .registryid, title: .title_of_work, type: .type, description: .descriptionofwork, statement: .artistprojectstatement, neighbourhood: .neighbourhood } ]' public-art.json >munged.json
```

## Fill a vector embedding collection

```sh
llm embed-multi --model sentence-transformers/intfloat/e5-large-v2 --store vancouver-public-art munged.json
```

## Do a semantic search

```sh
llm similar vancouver-public-art -c 'dancing beaver'
```

A bit more readable:

```sh
llm similar vancouver-public-art -c 'dancing beaver' | jq --raw-output '.content + "\n"'
```

## Impressions

It's very fast to try, but the results for this first go were only OK. I don't know yet how to pick a great model or best arrange the data but this is a nice way to experiment.

## Clean up

```sh
llm collections delete vancouver-public-art
```
