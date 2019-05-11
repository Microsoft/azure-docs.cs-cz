---
title: 'Rychlý start: Volání službě Text Analytics pomocí sady Python SDK'
titleSuffix: Azure Cognitive Services
description: Získat informace a ukázky kódu můžete rychle začít používat rozhraní API pro analýzu textu ve službě Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: 2e9e26243511972c7d05dc55c8e041d83efcbc7d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546584"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Rychlý start: Volání službě Text Analytics pomocí sady Python SDK 
<a name="HOLTop"></a>

V tomto rychlém startu můžete začít analýzou jazyce s využitím sada Text Analytics SDK pro Python. REST API pro analýzu textu je kompatibilní s Většina programovacích jazyků, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu bez serializaci a deserializaci JSON.


> [!Note]
> * Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).
> * Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).  

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://www.python.org/)

* Rozhraní Text Analytics [sady SDK pro python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) můžete nainstalovat balíček pomocí:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Je také nutné mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) , které byly vygenerovány pro vás během registrace.

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvoření nové aplikace v Pythonu v oblíbeném editoru nebo prostředí IDE. Pak přidejte následující příkazy pro import do souboru.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Ověření vašich pověření

> [!Tip]
> Pro zabezpečené nasazení tajných kódů v produkční systémy doporučujeme použít [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Po provedení proměnnou pro váš klíč předplatného pro analýzu textu, vytvořit instanci `CognitiveServicesCredentials` objektu s ním.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Vytvoření klienta pro analýzu textu

Vytvořte nový `TextAnalyticsClient` objekt s `credentials` a `text_analytics_url` jako parametr. Použijte správný oblast Azure pro vaše předplatné pro analýzu textu (například `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Analýza mínění

Datová část rozhraní API se skládá ze seznamu `documents`, které jsou slovníky obsahující `id` a `text` atribut. `text` Text, který má být analyzován, úložišť atributů a `id` může být libovolná hodnota. 

```python
documents = [
  {
    "id": "1", 
    "language": "en", 
    "text": "I had the best day of my life."
  },
  {
    "id": "2", 
    "language": "en", 
    "text": "This was a waste of my time. The speaker put me to sleep."
  },  
  {
    "id": "3", 
    "language": "es", 
    "text": "No tengo dinero ni nada que dar..."
  },  
  {
    "id": "4", 
    "language": "it", 
    "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
  }
]
```

Volání `sentiment()` fungovat a získat výsledek. Potom iterování přes výsledky a tisknout ID každé dokumentu a skóre mínění. Skóre blíže 0 znamená negativní zabarvení, zatímco skóre blíže 1 označuje pozitivní mínění.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>Výstup

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Detekce jazyka

Vytvoří seznam adresářů, každý obsahuje dokument, který chcete analyzovat. `text` Text, který má být analyzován, úložišť atributů a `id` může být libovolná hodnota. 

```python
documents = [
    { 
        'id': '1', 
        'text': 'This is a document written in English.' 
    },
    {
        'id': '2', 
        'text': 'Este es un document escrito en Español.' 
    },
    { 
        'id': '3', 
        'text': '这是一个用中文写的文件' 
    }
]
``` 

Pomocí klienta vytvořili dříve, volání `detect_language()` a získat výsledek. Pak iterování přes výsledky a tisknout ID každé dokumentu a první vrácené jazyk.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>Výstup

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořit seznam adresářů, která bude obsahovat dokumenty, které chcete analyzovat. `text` Text, který má být analyzován, úložišť atributů a `id` může být libovolná hodnota. 


```python
documents = [
    {
        "id": "1",
        "language": "en", 
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es", 
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Pomocí klienta vytvořili dříve, volání `entities()` fungovat a získat výsledek. Potom iterování přes výsledky a tisknout ID každé dokumentu a entity jsou v něm obsažené.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Výstup

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořit seznam adresářů, která bude obsahovat dokumenty, které chcete analyzovat. `text` Text, který má být analyzován, úložišť atributů a `id` může být libovolná hodnota. 


```python
documents = [
    {
        "id": "1", 
        "language": "ja", 
        "text": "猫は幸せ"
    },
    {
        "id": "2", 
        "language": "de", 
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3", 
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4", 
        "language": "es", 
        "text": "A mi me encanta el fútbol!"
    }
]
```

Pomocí klienta vytvořili dříve, volání `key_phrases()` fungovat a získat výsledek. Potom iterování přes výsledky a tisknout ID každé dokumentu a klíčové fráze, které jsou v něm obsažené.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>Výstup

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je API pro analýzu textu?](../overview.md)
* [Ukázkové uživatelské scénáře](../text-analytics-user-scenarios.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)
