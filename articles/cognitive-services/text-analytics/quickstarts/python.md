---
title: 'Rychlý start: Volání rozhraní API pro analýzu textu pomocí Pythonu'
titleSuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám usnadní začátky práce s rozhraním API pro analýzu textu ve službách Microsoft Cognitive Services v Azure.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 07b7327b01987d79a6447ed67de27b69c02c14ee
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268355"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Rychlý start: Volání rozhraní API pro analýzu textu ve službách Cognitive Services pomocí Pythonu 
<a name="HOLTop"></a>

V tomto návodu se dozvíte, jak [rozpoznat jazyk](#Detect), [analyzovat mínění](#SentimentAnalysis) a [extrahovat klíčové fráze](#KeyPhraseExtraction) pomocí [rozhraní API pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759711) s využitím Pythonu.

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraním API analýzu textu**. K dokončení tohoto návodu můžete použít **bezplatnou úroveň 5 000 transakcí za měsíc**.

Dále musíte mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) vygenerovaný během registrace. 

Abyste mohli pokračovat v tomto návodu, nahraďte `subscription_key` platným klíčem předplatného, který jste získali dříve.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, že oblast v proměnné `text_analytics_base_url` odpovídá oblasti, kterou jste použili při nastavování služby. Pokud používáte bezplatný zkušební klíč, není potřeba nic měnit.


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>Rozpoznávání jazyků

Rozhraní API pro rozpoznávání jazyka rozpozná jazyk textového dokumentu pomocí [metody Detect Language](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). Koncový bod služby rozhraní API pro rozpoznávání jazyka pro vaši oblast je dostupný na následující adrese URL:


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


Datová část určená pro rozhraní API se skládá ze seznamu dokumentů (`documents`), z nichž každý zase obsahuje atributy `id` a `text`. Atribut `text` slouží k uložení textu, který se má analyzovat. 

Slovník `documents` můžete nahradit jakýmkoli jiným textem, u kterého se má provést rozpoznávání jazyka. 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Dalších několik řádků kódu s využitím knihovny `requests` v Pythonu zavolá rozhraní API pro rozpoznávání jazyka, které určí jazyk v dokumentech.


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


Následující řádky kódu vykreslí data JSON jako tabulku HTML.


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

Rozhraní API pro analýzu mínění rozpozná mínění sady textových záznamů pomocí [metody Sentiment](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Následující příklad vyhodnotí dva dokumenty, jeden v angličtině a druhý ve španělštině.

Koncový bod služby pro analýzu mínění pro vaši oblast je dostupný na následující adrese URL:


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


Podobně jako v příkladu rozpoznávání jazyka je součástí služby i slovník s klíčem `documents`, který se skládá ze seznamu dokumentů. Každý dokument je řazená kolekce členů skládající se z hodnot `id`, `text` (text, který se má analyzovat) a `language` (jazyk textu). K vyplnění tohoto pole můžete použít rozhraní API pro rozpoznávání jazyka z předchozí části. 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Pomocí rozhraní API pro analýzu mínění teď můžete analyzovat mínění v dokumentech.


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


Skóre mínění dokumentu je v rozsahu $0$ až $1$, přičemž vyšší skóre značí pozitivnější mínění.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrakce klíčových frází

Rozhraní API pro extrakci klíčových frází extrahuje klíčové fráze z textového dokumentu pomocí [metody Key Phrases](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). V této části návodu se extrahují klíčové fráze z anglického i španělského dokumentu.

Koncový bod služby pro extrakci klíčových frází je dostupný na následující adrese URL:


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


Kolekce dokumentů je stejná jako kolekce použitá k analýze mínění.


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


Objekt JSON je opět možné vykreslit jako tabulku HTML pomocí následujících řádků kódu:


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-entities"></a>Identifikace entit

Rozhraní API pro entity identifikuje dobře známé entity v textovém dokumentu pomocí [metody Entities](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634). Následující příklad identifikuje entity pro anglické dokumenty.

Koncový bod služby pro spojování entit je dostupný na následující adrese URL:


```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities


Kolekce dokumentů je níže:


```python
documents = {'documents' : [
  {'id': '1', 'text': 'Jeff bought three dozen eggs because there was a 50% discount.'},
  {'id': '2', 'text': 'The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.'}
]}
```

Teď je možné tyto dokumenty odeslat do rozhraní API pro analýzu textu a přijmout odpověď.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Viz také 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)
