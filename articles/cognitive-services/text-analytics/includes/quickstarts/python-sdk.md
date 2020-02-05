---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 30c65abcbf469dd4f6e8987aa1e2ee8a36fef17a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987878"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

[Referenční dokumentace v3](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [zdrojový kód knihovny v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 Package (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [V3 Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

[Referenční dokumentace v2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [zdrojového kódu knihovny v2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 Package (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 Samples](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3.x](https://www.python.org/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

---

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu a vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektový model

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

Klient Analýza textu je `TextAnalyticsClient` objekt, který se pomocí vašeho klíče ověřuje v Azure. Klient nabízí několik metod, jak analyzovat text jako dávku. V tomto rychlém startu se k rychlému posílání jednoduchých dokumentů používá kolekce funkcí.

Když se do rozhraní API pošle Text dávkového zpracování jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. Při zpracování jednoduchých dokumentů je třeba, aby se v níže uvedených příkladech zobrazil pouze vstup `text`.  

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Klient Analýza textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku. 

Text se pošle do rozhraní API jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. 

---

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Analýza textu klientské knihovny pro Python:

* [Analýza subjektivního hodnocení](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Ověřte objekt klienta a zavolejte funkci [mínění ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) . Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Výstup

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Detekce jazyka

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Pomocí dříve vytvořeného klienta volejte [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak projdete výsledky a vytiskněte ID každého dokumentu a první vrácený jazyk.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0-preview`:
> * NER obsahuje samostatné metody zjišťování osobních údajů. 
> * Odkaz na entitu je samostatný požadavek než NER.

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

## <a name="using-ner-to-detect-personal-information"></a>Použití NER k detekci osobních údajů

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

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

> [!NOTE]
> Ve verzi 2,1 je odkaz na entitu součástí odpovědi NER.

Pomocí dříve vytvořeného klienta volejte funkci [Entities ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a entity, které jsou v něm obsažené.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Výstup

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází


#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Pomocí dříve vytvořeného klienta volejte funkci [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a klíčové fráze, které jsou v něm obsažené.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Výstup

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---