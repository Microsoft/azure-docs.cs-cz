---
title: 'Rychlý start: Použití Pythonu k volání rozhraní Text Analytics API'
titleSuffix: Azure Cognitive Services
description: Získat informace a ukázky kódu můžete rychle začít používat rozhraní API pro analýzu textu ve službě Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/09/2019
ms.author: aahi
ms.openlocfilehash: fb411b7e36d8658c5f46294a3b7025c3e93928e7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540121"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Rychlý start: Pomocí rozhraní Python API REST k volání Text Analytics služby Cognitive Services 
<a name="HOLTop"></a>

V tomto rychlém startu můžete začít analýzou jazyce s využitím REST API pro analýzu textu a Python. Tento článek vám ukáže, jak k [rozpoznání jazyka](#Detect), [analýza sentimentu](#SentimentAnalysis), [extrakce klíčových frází](#KeyPhraseExtraction), a [identifikovat propojených entit](#Entities).

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://python.org)

* [Koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) , která byla vygenerována během registrace.

* Požaduje knihovna Python
    
    Nainstalujte knihovny pomocí tohoto příkazu:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvoření nové aplikace v Pythonu v oblíbeném editoru nebo prostředí IDE. Přidejte následující importy do souboru.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
from IPython.display import HTML
```

Vytvoření proměnné pro váš klíč předplatného a koncový bod pro REST API pro analýzu textu. Ověřte, jestli jste použili při registraci odpovídá oblasti koncový bod (například `westcentralus`). Pokud používáte bezplatné zkušební verze klíče, nemusíte nic měnit.
    
```python
subscription_key = "<ADD YOUR KEY HERE>"
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
```

Následující části popisují, jak volat jednotlivé funkce rozhraní API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Rozpoznávání jazyků

Připojit `languages` do základního koncového bodu pro analýzu textu a vytvoří adresu URL detekce jazyka. Příklad: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = text_analytics_base_url + "languages"
```

Datová část rozhraní API se skládá ze seznamu `documents`, které jsou řazené kolekce členů obsahující `id` a `text` atribut. `text` Text, který má být analyzován, úložišť atributů a `id` může být libovolná hodnota. 

```python
documents = { "documents": [
    { "id": "1", "text": "This is a document written in English." },
    { "id": "2", "text": "Este es un document escrito en Español." },
    { "id": "3", "text": "这是一个用中文写的文件" }
]}
```

Použijte knihovnu požadavky odesílat dokumenty k rozhraní API. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví a odeslat žádost s `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Výstup

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analýza mínění

Chcete-li rozpoznávání mínění (který se pohybuje mezi pozitivní nebo negativní) sady dokumentů, přidejte `sentiment` do základního koncového bodu pro analýzu textu a vytvoří adresu URL detekce jazyka. Příklad: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = text_analytics_base_url + "sentiment"
```

Jako v příkladu detekce jazyka vytvořit slovník s `documents` klíč, který se skládá ze seznamu dokumentů. Každý dokument je řazená kolekce členů skládající se z hodnot `id`, `text` (text, který se má analyzovat) a `language` (jazyk textu). 

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Použijte knihovnu požadavky odesílat dokumenty k rozhraní API. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví a odeslat žádost s `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Výstup

Skóre mínění v dokumentu je mezi 0,0 a 1,0, s vyšší skóre označuje další pozitivní mínění.

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[

  ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrahovat klíčové fráze
 
Chcete-li extrahovat klíčové fráze ze sady dokumentů, přidejte `keyPhrases` do základního koncového bodu pro analýzu textu a vytvoří adresu URL detekce jazyka. Příklad: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = text_analytics_base_url + "keyPhrases"
```

Tato kolekce dokumentů je stejný slouží například analýza mínění.

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Použijte knihovnu požadavky odesílat dokumenty k rozhraní API. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví a odeslat žádost s `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Výstup

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identifikace entit

Chcete-li identifikovat dobře známé entity (lidé, místa a věcí) v textové dokumenty, přidejte `keyPhrases` do základního koncového bodu pro analýzu textu a vytvoří adresu URL detekce jazyka. Příklad: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
entities_url = text_analytics_base_url + "keyPhrases"
```

Vytvoření kolekce dokumentů, stejně jako v předchozích příkladech. 

```python
documents = {"documents" : [
  {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

Použijte knihovnu požadavky odesílat dokumenty k rozhraní API. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví a odeslat žádost s `requests.post()`.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
```

### <a name="output"></a>Výstup

```json
{
  "documents":[
    {
      "id":"1",
      "keyPhrases":[
        "Bill Gates",
        "Paul Allen",
        "BASIC interpreters",
        "Altair",
        "Microsoft"
      ]
    }
  ],
  "errors":[]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)
