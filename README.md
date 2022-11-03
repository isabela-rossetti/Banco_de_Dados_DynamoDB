# Banco de Dados DynamoDB
Projeto desenvolvido com o objetivo de construir um banco de dados não relacional utilizando o DynamoDB dentro da AWS - Amazon e executar queries SQL para recuperação dos dados persistidos.

### Serviço utilizado
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando

### Comandos para execução do experimento no Windows PowerShell:


- Criação da tabela "Música"

```
aws dynamodb create-table `
	--table-name Musica `
	--attribute-definitions
		AttributeName=Artista,AttributeType=S
		AttributeName=TituloMusica,AttributeType=S  `
	--key-schema 
		AttributeName=Artista,KeyType=HASH
		AttributeName=TituloMusica,KeyType=RANGE `
	--provisioned-throughput
		ReadCapacityUnits=10,WriteCapacityUnits=5
```

- Inserindo um item

```
aws dynamodb put-item `
    --table-name Musica `
    --item file://itemmusic.json `
```

- Inserindo múltiplos itens

```
aws dynamodb batch-write-item `
    --request-items file://batchmusic.json
```

- Criando um index global secundário baseado no título do álbum

```
aws dynamodb update-table `
    --table-name Musica `
    --attribute-definitions AttributeName=TituloAlbum,AttributeType=S `
    --global-secondary-index-updates `
        "[{\"Create\":{\"IndexName\": \"TituloAlbum-index\",\"KeySchema\":[{\"AttributeName\":\"TituloAlbum\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Pesquisando item por artista

```
aws dynamodb query `
    --table-name Musica `
    --key-condition-expression "Artista = :artista" `
    --expression-attribute-values file://keyconditions.json
```
- Resultado da pesquisa

```
{
    "Items": [
        {
            "TituloMusica": {
                "S": "Bag of Grins"
            },
            "Artista": {
                "S": "Red Hot Chilli Peppers"
            },
            "TituloAlbum": {
                "S": "Return of the Dream Canteen"
            },
            "AnoMusica": {
                "S": "2022"
            }
        },
        {
            "TituloMusica": {
                "S": "Eddie"
            },
            "Artista": {
                "S": "Red Hot Chilli Peppers"
            },
            "TituloAlbum": {
                "S": "Return of the Dream Canteen"
            },
            "AnoMusica": {
                "S": "2022"
            }
        },
        {
            "TituloMusica": {
                "S": "Scar Tissue"
            },
            "Artista": {
                "S": "Red Hot Chilli Peppers"
            },
            "TituloAlbum": {
                "S": "Californication"
            },
            "AnoMusica": {
                "S": "1999"
            }
        }
    ],
    "Count": 3,
    "ScannedCount": 3,
    "ConsumedCapacity": null
}
```

- Pesquisando item por artista e título da música

```
aws dynamodb query `
    --table-name Musica `
    --key-condition-expression "Artista = :artista and TituloMusica = :musica" `
    --expression-attribute-values file://keyconditions2.json
```

- Resultado da pesquisa

```
{
    "Items": [
        {
            "TituloMusica": {
                "S": "Scar Tissue"
            },
            "Artista": {
                "S": "Red Hot Chilli Peppers"
            },
            "TituloAlbum": {
                "S": "Californication"
            },
            "AnoMusica": {
                "S": "1999"
            }
        }
    ],
    "Count": 1,
    "ScannedCount": 1,
    "ConsumedCapacity": null
}
```

- Pesquisa pelo index secundário baseado no título do álbum

```
aws dynamodb query `
    --table-name Musica `
    --index-name TituloAlbum-index `
    --key-condition-expression "TituloAlbum = :titulo" `
    --expression-attribute-values file://keyconditions3.json
```

- Resultado da pesquisa

```
{
    "Items": [
        {
            "TituloMusica": {
                "S": "Eddie"
            },
            "Artista": {
                "S": "Red Hot Chilli Peppers"
            },
            "TituloAlbum": {
                "S": "Return of the Dream Canteen"
            },
            "AnoMusica": {
                "S": "2022"
            }
        },
        {
            "TituloMusica": {
                "S": "Bag of Grins"
            },
            "Artista": {
                "S": "Red Hot Chilli Peppers"
            },
            "TituloAlbum": {
                "S": "Return of the Dream Canteen"
            },
            "AnoMusica": {
                "S": "2022"
            }
        }
    ],
    "Count": 2,
    "ScannedCount": 2,
    "ConsumedCapacity": null
}
```
