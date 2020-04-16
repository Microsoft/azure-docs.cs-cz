---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: aahi
ms.openlocfilehash: d58f294195efc393c07ecc3886c29e33dba02e6d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81421821"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

[v3 Referenční dokumentace](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [v3 Zdrojový kód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [knihovny v3 Balíček (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[v2 Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 Zdrojový kód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [knihovny v2 Ukázky balíčku (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure, vytvořte"  target="_blank">prostředek analýzy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> textu vytvořit prostředek analýzy textu na webu Azure Portal, abyste získali klíč a koncový bod. Po nasazení klikněte na **Přejít na prostředek**.
    * Budete potřebovat klíč a koncový bod z prostředku, který vytvoříte pro připojení aplikace k rozhraní API analýzy textu. Klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * Můžete použít bezplatnou cenovou úroveň (`F0`) vyzkoušet službu a upgradovat později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí:

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor kódu rychlého startu najednou? Najdete ji [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), který obsahuje příklady kódu v tomto rychlém startu. 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor kódu rychlého startu najednou? Najdete ji [na GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), který obsahuje příklady kódu v tomto rychlém startu. 

---

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace pythonu

Vytvořte nový soubor Pythonu a vytvořte proměnné pro koncový bod azure a klíč předplatného vašeho prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektový model

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Klient Text Analytics `TextAnalyticsClient` je objekt, který se ověřuje v Azure pomocí vašeho klíče. Klient poskytuje několik metod pro analýzu textu jako dávky. 

Při dávkovézpracování textu je odeslána `documents`do rozhraní `dictionary` API jako seznam `id` `text`, `language` které jsou objekty obsahující kombinaci , a atributy v závislosti na použité metodě. Atribut `text` ukládá text, který má `language`být analyzován `id` v počátku , a může být libovolná hodnota. 

Objekt odpovědi je seznam obsahující informace o analýze pro každý dokument. 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Klient Text Analytics je objekt [TextAnalyticsClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) který se ověřuje v Azure pomocí vašeho klíče. Klient poskytuje několik metod pro analýzu textu jako jeden řetězec nebo dávka. 

Text je odeslán do rozhraní `documents`API jako `dictionary` seznam , což `id` `text`jsou `language` objekty obsahující kombinaci , a atributy v závislosti na použité metodě. Atribut `text` ukládá text, který má `language`být analyzován `id` v počátku , a může být libovolná hodnota. 

---

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly v klientské knihovně Text Analytics pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Detekce jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte funkci pro vytvoření `TextAnalyticsClient` instance objektu pomocí `key` a `endpoint` vytvořené výše. Pak vytvořte nového klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Vytvořte funkci pro vytvoření `TextAnalyticsClient` instance objektu pomocí `key` a `endpoint` vytvořené výše. Pak vytvořte nového klienta. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte novou `sentiment_analysis_example()` funkci s názvem, která přebírá `analyze_sentiment()` klienta jako argument, pak volá funkci. Vrácený objekt odpovědi bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Length: {}]".format(sentence.grapheme_length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Výstup

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Ověřte objekt klienta a zavolejte funkci [sentiment().](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) Iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a skóre mínění. Skóre blíže k 0 označuje negativní sentiment, zatímco skóre blíže k 1 označuje pozitivní sentiment.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Výstup

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte novou `language_detection_example()` funkci s názvem, která přebírá `detect_language()` klienta jako argument, pak volá funkci. Vrácený objekt odpovědi bude obsahovat zjištěný jazyk v případě `primary_language` úspěchu `error` a pokud ne.

> [!Tip]
> V některých případech může být obtížné rozptýlit jazyky na základě vstupu. `country_hint` Parametr můžete použít k určení dvoupísmenný kód země. Ve výchozím nastavení rozhraní API používá "US" jako výchozí zeměHint, chcete-li odebrat toto chování, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Výstup

```console
Language:  French
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Pomocí klienta vytvořeného dříve, volání [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získat výsledek. Potom iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a první vrácený jazyk.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

> [!NOTE]
> Ve `3.0-preview`verzi : 
> * Propojení entit je samostatný požadavek než NER.

Vytvořte novou `entity_recognition_example` funkci s názvem, která přebírá `recognize_entities()` klienta jako argument, pak volá funkci a itetuje prostřednictvím výsledků. Vrácený objekt odpovědi bude obsahovat seznam `entity` zjištěných entit v případě úspěchu `error` a pokud ne. Pro každou zjištěnou entitu vytiskněte její kategorii a podkategorii, pokud existuje.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Výstup

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="entity-linking"></a>Entity Linking

Vytvořte novou `entity_linking_example()` funkci s názvem, která přebírá `recognize_linked_entities()` klienta jako argument, pak volá funkci a itetuje prostřednictvím výsledků. Vrácený objekt odpovědi bude obsahovat seznam `entities` zjištěných entit v případě úspěchu `error` a pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, výskyty stejné entity jsou seskupeny pod objektem `entity` jako seznam `match` objektů.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Výstup

```console
Linked Entities:

    Name:  Altair 8800     Id:  Altair 8800     Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Confidence Score: 0.00     Length: 11

    Name:  Bill Gates     Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Confidence Score: 0.00     Length: 10

        Text: Gates
        Confidence Score: 0.00     Length: 5

    Name:  Paul Allen     Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Confidence Score: 0.00     Length: 10

    Name:  Microsoft     Id:  Microsoft     Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Confidence Score: 0.00     Length: 9

        Text: Microsoft
        Confidence Score: 0.00     Length: 9

    Name:  April 4     Id:  April 4     Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Confidence Score: 0.00     Length: 7

    Name:  BASIC     Id:  BASIC     Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Confidence Score: 0.00     Length: 5
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

> [!NOTE]
> Ve verzi 2.1 je propojení entit zahrnuto v odpovědi ner.

Pomocí klienta vytvořeného dříve, volání [entity()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) funkce a získat výsledek. Potom iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a entity obsažené v něm.

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


#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte novou `key_phrase_extraction_example()` funkci s názvem, která přebírá `extract_key_phrases()` klienta jako argument, pak volá funkci. Výsledek bude obsahovat seznam zjištěných `key_phrases` klíčových frází `error` v případě úspěchu a pokud ne. Vytisknout všechny zjištěné klíčové fráze.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Výstup

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Pomocí klienta vytvořeného dříve, volání [funkce key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získat výsledek. Pak iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a klíčové fráze obsažené v něm.

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
