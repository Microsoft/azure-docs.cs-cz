---
title: 'Rychlý start: Volání rozhraní API pro analýzu textu pomocí Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak pomocí Pythonu získat informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní API pro analýzu textu ve službě Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 0ef870b6b2d3b88b13c16c8c2acbfcee7ed551c1
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527237"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Rychlý Start: použití REST API Pythonu k volání služby Analýza textu rozpoznávání 
<a name="HOLTop"></a>

V tomto rychlém startu můžete začít s analýzou jazyka pomocí Analýza textu REST API a Pythonu. V tomto článku se dozvíte, jak [detekovat jazyk](#Detect), [analyzovat mínění](#SentimentAnalysis), [extrahovat klíčové fráze](#KeyPhraseExtraction)a [identifikovat propojené entity](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://python.org)

* Knihovna žádostí Pythonu
    
    Knihovnu můžete nainstalovat pomocí tohoto příkazu:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte novou aplikaci v Pythonu v oblíbených editorech nebo v integrovaném vývojovém prostředí. Do souboru přidejte následující importy.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

Následující části popisují, jak volat jednotlivé funkce rozhraní API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Rozpoznávání jazyků

Připojíte se `/text/analytics/v3.0/languages` k základnímu koncovému bodu analýza textu, abyste mohli vytvořit adresu URL pro rozpoznávání jazyka. Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

Datová část pro rozhraní API se skládá ze seznamu `documents` , který je řazenými kolekcemi členů obsahující `id` a `text` atributem. `text`Atribut ukládá text, který má být analyzován, a `id` může být libovolná hodnota. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

K posílání dokumentů do rozhraní API použijte knihovnu požadavků. Přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` a odešlete žádost pomocí `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Výstup

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analýza mínění

Chcete-li zjistit mínění (které rozsahy jsou kladné nebo záporné) sady dokumentů, přidejte `/text/analytics/v3.0/sentiment` do základního koncového bodu analýza textu, aby bylo možné vytvořit adresu URL pro rozpoznávání jazyka. Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Stejně jako u příkladu detekce jazyka vytvořte slovník s `documents` klíčem, který se skládá ze seznamu dokumentů. Každý dokument je řazená kolekce členů skládající se z hodnot `id`, `text` (text, který se má analyzovat) a `language` (jazyk textu). 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

K posílání dokumentů do rozhraní API použijte knihovnu požadavků. Přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` a odešlete žádost pomocí `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Výstup

Mínění skóre dokumentu je mezi 0,0 a 1,0 a s vyšším skóre, které signalizuje kladné mínění.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrakce klíčových frází
 
Pro extrakci klíčových frází ze sady dokumentů přidejte `/text/analytics/v3.0/keyPhrases` do základního koncového bodu analýza textu, aby bylo možné vytvořit adresu URL pro zjišťování jazyka. Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

Tato kolekce dokumentů se používá jako příklad analýzy mínění.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

K posílání dokumentů do rozhraní API použijte knihovnu požadavků. Přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` a odešlete žádost pomocí `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Výstup

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identifikace entit

Chcete-li identifikovat známé entity (osoby, místa a věci) v textových dokumentech, přihlaste se `/text/analytics/v3.0/entities/recognition/general` do základního koncového bodu analýza textu, aby bylo možné vytvořit adresu URL pro rozpoznávání jazyka. Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Vytvořte kolekci dokumentů, jako v předchozích příkladech. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

K posílání dokumentů do rozhraní API použijte knihovnu požadavků. Přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` a odešlete žádost pomocí `requests.post()` .

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Výstup

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza textu s Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Viz také 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)
