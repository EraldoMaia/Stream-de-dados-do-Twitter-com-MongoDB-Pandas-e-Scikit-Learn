# Stream-de-dados-do-Twitter-com-MongoDB-Pandas-e-Scikit-Learn
Basicamente o que faremos é um trabalho de Text Mining (técnica de processamento de linguagem natural para extrair informações relevantes de dados de textos).Como o Twitter, possui basicamente textos, faremos uma operação de extract (extração), vamos gravar esses dados em um Data Base (MongoDB) e depois iremos aplicar algumas técnicas de analise.

Primeiramente devemos Instalar o pacote tweepy no nosso SO (Sistema Operancional).

> `!pip install tweepy`

Esse pacote permite fazer a conexão entre o Python e o Twitter.
_OBS: O MongoDB deve está conectado_

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




