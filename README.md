# Stream-de-dados-do-Twitter-com-MongoDB-Pandas-e-Scikit-Learn

![img](https://user-images.githubusercontent.com/55844931/107976514-73983880-6f98-11eb-8a24-f43d973cfd36.png)

Basicamente o que faremos é um trabalho de Text Mining (técnica de processamento de linguagem natural para extrair informações relevantes de dados de textos).

Como o Twitter, possui basicamente textos, faremos uma operação de extract (extração), vamos gravar esses dados em um Data Base (MongoDB) e depois iremos aplicar algumas técnicas de analise.


# Preparando a conexão com o Twitter:

Primeiramente devemos Instalar o pacote tweepy no nosso SO (Sistema Operancional).

> `!pip install tweepy`

Esse pacote permite fazer a conexão entre o Python e o Twitter.

Em sequencia vamos importar alguns módulos que iremos utilizar nesse projeto.

> `from tweepy.streaming import StreamListener`

>` from tweepy import OAuthHandler`

> `from tweepy import Stream`

>` from datetime import datetime`

> `import json`

Agora, precisamos definir as chaves que nos permitem conectar ao Twitter. 

> `consumer_key = "xxxxxxxxx"` (API key)

> `consumer_secret = "xxxxxxxxx"` (API secret key)

> `access_token = "xxxxxxxxx"`

> `access_token_secret = "xxxxxxxxx"`

Em seguida iremos definir a conexão de atenticação, criando as chaves de autenticação:

> `auth = OAuthHandler(consumer_key, consumer_secret)`

Após criar o objeto de autenticação, passamos por meio da função `set_access_token`, os tokens de acesso a API.

> `auth.set_access_token(access_token, access_token_secret)`

Na sequencia criamos uma classe para capturar os stream de dados do Twitter e armazenar no MongoDB.

_obs: O MongoDB deve está conectado_ `brew services start mongodb-community`

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

_obs: No momento que voce estiver satisfeito com a quantidade de dados coletados, precione o botao "STOP" no seu Jupter Notebook._

<img width="980" alt="Captura de Tela 2021-02-15 às 14 03 33" src="https://user-images.githubusercontent.com/55844931/107977014-31232b80-6f99-11eb-810a-a574e30a6c16.png">

# Consultando os dados no MongoDB

Antes de tudo devemos desconectar o MongoDB, pois não iremos mais coletar tweets

> `mystream.disconnect()`

Em seguida, verificamos se realmentes os dados foram gravados no MongoDB

> `col.find_one()`

<img width="985" alt="Captura de Tela 2021-02-15 às 14 09 17" src="https://user-images.githubusercontent.com/55844931/107977122-63348d80-6f99-11eb-8c00-eada36b94140.png">


# Análise de Dados com Pandas e Scikit-Learn

Iremos agora, converter os tweets coletados, para o formato no Pandas (O Pandas, é uma expecie de excel para o Python, pois permite o trabalho com Data Frames, objeto no formato de linhas e colunas). O Scikit-Learn é um pacote para machine learning, com ele conseguimos criar modelos preditivos.

Primeiro iremos utilizar uma list comprehension, criando um dataset com dados retornados do MongoDB:

> `dataset = [{"created_at": item["created_at"], "text": item["text"],} for item in col.find()]`

Em seguida, importamos o Pandas

> `import pandas as pd`

Agora iremos converter o objeto `dataset` para o tipo `dataframe` do Pandas, granvando no objeto `df`

> `df = pd.DataFrame(dataset)`

Imprima o dataframe:

> `df`

<img width="560" alt="Captura de Tela 2021-02-15 às 14 11 35" src="https://user-images.githubusercontent.com/55844931/107977294-9d059400-6f99-11eb-8cb7-2364f8f4db91.png">

Agora, iremos importar a função `CountVectorizer` do Scikit-Learn, essa função nos permite contar quantas vezes uma determinada palavra aparece no nosso conjunto de dados.

> `from sklearn.feature_extraction.text import CountVectorizer`

Usando o método CountVectorizer para criar uma matriz de documentos
````python
cv = CountVectorizer()
count_matrix = cv.fit_transform(df.text)
````

Por fim, contamos o número de ocorrências das principais palavras em nosso dataset:

````python
word_count = pd.DataFrame(cv.get_feature_names(), columns=["word"])
word_count["count"] = count_matrix.sum(axis=0).tolist()[0]
word_count = word_count.sort_values("count", ascending=False).reset_index(drop=True)
word_count[:50]
````
<img width="168" alt="Captura de Tela 2021-02-15 às 14 13 53" src="https://user-images.githubusercontent.com/55844931/107977340-b3abeb00-6f99-11eb-9adb-f72e5ba43cbe.png">

