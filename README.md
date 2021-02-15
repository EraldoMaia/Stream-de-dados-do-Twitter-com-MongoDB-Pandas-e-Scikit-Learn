# Stream-de-dados-do-Twitter-com-MongoDB-Pandas-e-Scikit-Learn
Basicamente o que faremos é um trabalho de Text Mining (técnica de processamento de linguagem natural para extrair informações relevantes de dados de textos).

Como o Twitter, possui basicamente textos, faremos uma operação de extract (extração), vamos gravar esses dados em um Data Base (MongoDB) e depois iremos aplicar algumas técnicas de analise.


# Preparando a conexão com o Twitter:

Primeiramente devemos Instalar o pacote tweepy no nosso SO (Sistema Operancional).

> `!pip install tweepy`

Esse pacote permite fazer a conexão entre o Python e o Twitter.

_obs: O MongoDB deve está conectado_

Em sequencia vamos importar alguns módulos que iremos utilizar nesse projeto.

> `from tweepy.streaming import StreamListener`

>` from tweepy import OAuthHandler`

> `from tweepy import Stream`

>` from datetime import datetime`

> `import json`

Agora, precisamos definir as chaves que nos permitem conectar ao Twitter. 
_Caso voce não saiba como obter essas chaves, eu explico com mais detalhes no meu blog:_ [eraldomaia.com/blog](url)

> `consumer_key = "xxxxxxxxx"`

> `consumer_secret = "xxxxxxxxx"`

> `access_token = "xxxxxxxxx"`

> `access_token = "xxxxxxxxx"`

Em seguida iremos definir a conexão de atenticação, criando as chaves de autenticação:

> `auth = OAuthHandler(consumer_key, consumer_secret)`

Após criar o objeto de autenticação, passamos por meio da função `set_access_token`, os tokens de acesso a API.

> `auth.set_access_token(access_token, access_token_secret)`

Na sequencia criamos uma classe para capturar os stream de dados do Twitter e armazenar no MongoDB.

```python 
class MyListener(StreamListener):
    def on_data(self, dados):
        tweet = json.loads(dados)
        created_at = tweet["created_at"]
        id_str = tweet["id_str"]
        text = tweet["text"]
        obj = {"created_at":created_at,"id_str":id_str,"text":text,}
        tweetind = col.insert_one(obj).inserted_id
        print (obj)
        return True
```
Dentro da classe `MyListener`, nos temos um metodo chamado de `on_data`, esse metodo vai coletar os dados e esses dados serão convertidos para o formato JSON
`tweet = json.loads(dados)`, utlizamos o formato JSON para que em seguida possamos gravar esses dados no MongoDB.

Em seguida, definimos as colunas que serão coletadas ` created_at = tweet["created_at"]`, `id_str = tweet["id_str"]`, `text = tweet["text"]`. 

Por ultimo inserimos os dados coletados no MongoDB `tweetind = col.insert_one(obj).inserted_id`


_obs: As colunas "created_at", "id_str", "text" foram retidas de acordo com a documentação da API do Twitter. Disponivel em: [https://developer.twitter.com/en/docs](url)_

Agora precisamos instanciar o objeto da classe `MyListener`:

> `mylistener = MyListener()`

Em seguida, criamos o objeto `mystream` que vai fazer a autenticação com a API:

> `mystream = Stream(auth, listener = mylistener)`

Passando a chave de autenticação `Auth` e o listener que será usado, que no caso será o objeto `mylistener`.

# Preparando a conexão com o MongoDB e coletando os Tweets:

Para conectar com o MongoDB, primeiramente importamos do pacote `PyMongo` o módulo `MongoClient`

> `from pymongo import MongoClient`

A partir dai, definimos a conexão com o localhost na porta 27017

> `client = MongoClient('localhost', 27017)`

Então criamos o banco de dados `twitterdb`

> `db = client.twitterdb`

Criamos também a coleção (Tabela no MongoDB) chamada de `tweets`

> `col = db.tweets`

Agora criamos uma lista, que terá as palavras chaves, que serão coletadas

> `keywords = ['Big Data', 'Python', 'Data Mining', 'Data Science']`

# Coletando os Tweets

Iniciamos então o filtro coletando e gravando os tweets no MongoDB

> `mystream.filter(track=keywords)`

