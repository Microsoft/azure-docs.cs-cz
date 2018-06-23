---
title: Python rychlý úvodní kurz pro Azure kognitivní služby, analýza textu rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API Analytics Text v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: b4c02767320b71912050ad511811767e6b5decf4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342809"
---
# <a name="quickstart-for-text-analytics-api-with-python"></a>Rychlý start pro Analýza textu rozhraní API s Pythonem 
<a name="HOLTop"></a>

Tento návod ukazuje, jak k [zjistit jazyk](#Detect), [analyzovat postojích](#SentimentAnalysis), a [extrahovat klíče frází](#KeyPhraseExtraction) pomocí [rozhraní API Analytics Text](//go.microsoft.com/fwlink/?LinkID=759711)s Python.

V tomto příkladu můžete spustit jako poznámkového bloku Jupyter na [MyBinder](https://mybinder.org) kliknutím na spuštění vazač oznámení: 

[![Vazač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Odkazovat [definice rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) pro technická dokumentace k rozhraní API.

## <a name="prerequisites"></a>Požadavky

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **Text Analytics API**. Můžete použít **úroveň free pro 5 000 transakce za měsíc** k dokončení tohoto postupu.

Je také nutné mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) který byl vygenerován pro vás během registrace. 

Chcete-li pokračovat v tomto průvodci, nahraďte `subscription_key` s klíčem platné předplatné, který jste dříve získali.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, že oblast v `text_analytics_base_url` odpovídá jste použili při nastavení služby. Pokud používáte bezplatné zkušební verze klíč, není potřeba nic nezmění.


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>Zjištění jazyky

Rozhraní API jazyka detekce zjistí jazyk textového dokumentu, pomocí [metoda zjistit jazyk](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). Koncový bod služby zjišťování rozhraní API jazyka pro vaši oblast je k dispozici prostřednictvím na následující adrese URL:


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


Datová část rozhraní API se skládá z seznam `documents`, každý z zase obsahující `id` a `text` atribut. `text` Úložišť atributů text, který má být analyzován. 

Nahraďte `documents` slovníku u jakéhokoli jiného textu zjišťování jazyka. 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Další několika řádků kódu vyvolávající pomocí zjišťování rozhraní API jazyka `requests` knihovny v Pythonu Určuje jazyk, v dokumentech.


```python
import requests
from pprint import pprint
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}


Následující řádky kódu vykreslují JSON data jako tabulky HTML.


```python
from IPython.display import HTML
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analýza mínění

Rozhraní API Analysis postojích detexts postojích sadu záznamů text, pomocí [postojích metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Následující příklad skóre dva dokumenty, jeden v angličtině a druhý ve španělštině.

Koncový bod služby pro analýzu postojích je k dispozici pro vaši oblast prostřednictvím na následující adrese URL:


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


Jako příklad detekce jazyk, je služba poskytována s slovník s `documents` klíč, který se skládá z seznam dokumenty. Každý dokument je tvořený řazené kolekce členů `id`, `text` má být analyzován a `language` textu. Zjišťování jazyka rozhraní API z předchozí části můžete použít k naplnění tohoto pole. 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Postojích rozhraní API můžete teď používat k analýze v dokumentech jejich chráněny.


```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

    {'documents': [{'id': '1', 'score': 0.7673527002334595},
                   {'id': '2', 'score': 0.18574094772338867},
                   {'id': '3', 'score': 0.5}],
     'errors': []}


Postojích skóre pro dokument je od 0 $$ $ $1, s vyšší skóre označuje více kladné postojích.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrakce klíčových frází

Rozhraní API extrakce frázi klíč extrahuje frází klíč z textového dokumentu, pomocí [klíč frází metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Tato část průvodce extrahuje klíče frází pro angličtinu a slovenštinu dokumenty.

Koncový bod služby pro službu klíč frázi extrakce přistupuje prostřednictvím na následující adrese URL:


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


Kolekce dokumentů je stejný jako který byl použit pro analýzu postojích.


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```


    {'documents': [
        {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
        {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
        {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
        {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
     'errors': []
    }


Objekt JSON, které lze znovu vykreslit jako tabulky HTML pomocí následující řádky kódu:


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-linked-entities"></a>Identifikovat propojených entit

Rozhraní API propojení entita identifikuje dobře známé entity v textového dokumentu, pomocí [Entity propojení metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Následující příklad určuje entity pro angličtinu dokumenty.

Koncový bod služby pro službu serveru linking entity přistupuje prostřednictvím na následující adrese URL:


```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/entities


Zde je kolekce dokumentů:


```python
documents = {'documents' : [
  {'id': '1', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.'},
  {'id': '2', 'text': 'The Seattle Seahawks won the Super Bowl in 2014.'}
]}
```

Nyní dokumenty nelze odesílat do rozhraní API Analytics Text pro příjem odpovědi.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```
    {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "name": "Xbox One",
                        "matches": [
                            {
                                "text": "XBox One",
                                "offset": 23,
                                "length": 8
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Xbox One",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                        "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                    },
                    {
                        "name": "Ultra-high-definition television",
                        "matches": [
                            {
                                "text": "4K",
                                "offset": 63,
                                "length": 2
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Ultra-high-definition television",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                        "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                    }
                ]
            },
            {
                "id": "2",
                "entities": [
                    {
                        "name": "2013 Seattle Seahawks season",
                        "matches": [
                            {
                                "text": "Seattle Seahawks",
                                "offset": 4,
                                "length": 16
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "2013 Seattle Seahawks season",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                        "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                    }
                ]
            }
        ],
        "errors": []
    }

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu pomocí Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)
