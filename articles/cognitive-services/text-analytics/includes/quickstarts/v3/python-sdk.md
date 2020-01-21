---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281134"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) |  | [ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples) | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) [(PiPy)](https://pypi.org/project/azure-ai-textanalytics/)

> [!NOTE]
> * V tomto rychlém startu se používá `3.0-preview` verze Analýza textu klientské knihovny, která zahrnuje verzi Public Preview pro vylepšené [Analýza mínění](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Například import klienta z oboru názvů `azure.ai.textanalytics.aio` a volání `analyze_sentiment()`namísto `analyze_sentiment()` z oboru názvů `azure.ai.textanalytics`.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu a vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se pomocí vašeho klíče ověřuje v Azure. Klient nabízí několik metod, jak analyzovat text jako dávku. V tomto rychlém startu se k rychlému posílání jednoduchých dokumentů používá kolekce funkcí.

Když se do rozhraní API pošle Text dávkového zpracování jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. Při zpracování jednoduchých dokumentů je třeba, aby se v níže uvedených příkladech zobrazil pouze vstup `text`.  

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Analýza textu klientské knihovny pro Python:

* [Analýza mínění](#sentiment-analysis) (Public Preview)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-public-preview) (Public Preview)
* [Rozpoznávání pojmenovaných entit – osobní informace](#named-entity-recognition---personal-information-public-preview) (Public Preview)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení

> [!NOTE]
> Níže uvedený kód je určen pro mínění Analysis v3, který je ve verzi Public Preview.

Vytvořte novou funkci nazvanou `sentiment_analysis_example()`, která převezme koncový bod a klíč jako argumenty a potom zavolá funkci `single_analyze_sentiment()`. Vrácený objekt Response bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Výstup

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>Detekce jazyka

Vytvořte novou funkci nazvanou `language_detection_example()`, která převezme koncový bod a klíč jako argumenty a potom zavolá funkci `single_detect_languages()`. Vrácený objekt odpovědi bude obsahovat zjištěný jazyk v `detected_languages`, pokud je to úspěšné, a `error`, pokud ne.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí parametru `country_hint` můžete zadat kód země se dvěma písmeny. Ve výchozím nastavení používá rozhraní API "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `country_hint : ""`. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Výstup

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>Rozpoznávání pojmenovaných entit (Public Preview)

> [!NOTE]
> Níže je uvedený kód pro rozpoznávání pojmenovaných entit v3, který je ve verzi Public Preview.

Vytvořte novou funkci nazvanou `entity_recognition_example`, která převezme koncový bod a klíč jako argumenty a pak zavolá funkci `single_recognize_entities()` a projde výsledky. Vrácený objekt odpovědi bude obsahovat seznam zjištěných entit v `entity` v případě úspěchu a `error`, pokud ne. Pro každou zjištěnou entitu, pokud existuje, vytiskněte její typ a dílčí typ.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Výstup

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Rozpoznávání pojmenovaných entit – osobní informace (Public Preview)

> [!NOTE]
> Níže uvedený kód slouží ke zjišťování osobních údajů pomocí rozpoznávání pojmenovaných entit v3, který je ve verzi Public Preview.

Vytvořte novou funkci nazvanou `entity_pii_example()`, která převezme koncový bod a klíč jako argumenty a pak zavolá funkci `single_recognize_pii_entities()` a získá výsledek. Potom Iterujte výsledky a vytiskněte entity.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Výstup

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Propojování entit

Vytvořte novou funkci nazvanou `entity_linking_example()`, která převezme koncový bod a klíč jako argumenty a pak zavolá funkci `single_recognize_linked_entities()` a projde výsledky. Vrácený objekt odpovědi bude obsahovat seznam zjištěných entit v `entities` v případě úspěchu a `error`, pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny pod objektem `entity` jako seznam objektů `match`.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Výstup

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou funkci nazvanou `key_phrase_extraction_example()`, která převezme koncový bod a klíč jako argumenty a potom zavolá funkci `single_extract_key_phrases()`. Výsledek bude obsahovat seznam zjištěných klíčových frází v `key_phrases` v případě úspěchu a `error`, pokud ne. Vytiskněte všechny zjištěné klíčové fráze.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Výstup

```console
    Key Phrases:
         cat
         veterinarian
```