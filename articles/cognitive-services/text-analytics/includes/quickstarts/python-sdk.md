---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 0803a847e9e864b361917df9f1a9c6b059ca2fe9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203397"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

[Referenční dokumentace k v3](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [Zdrojový kód knihovny v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Balíček v3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [Ukázky v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[Referenční dokumentace k v2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Zdrojový kód knihovny v2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Balíček v2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [Ukázky v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Můžete si ho [vytvořit zdarma](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)
* Jakmile máte předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Vytvoření prostředku Analýzy textu"  target="_blank">vytvořte si prostředek Analýza textu<span class="docon docon-navigate-external x-hidden-focus"></span></a> na webu Azure Portal. Získáte klíč a koncový bod. 
    * Tento klíč a koncový bod z vytvářeného prostředku budete potřebovat pro připojení vaší aplikace k rozhraní API pro analýzu textu. Uděláte to v další fázi tohoto rychlého zprovoznění.
    * Můžete si službu vyzkoušet s bezplatnou cenovou úrovní a pro produkční prostředí později upgradovat na placenou úroveň.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí:

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor s kódem rychlého startu najednou? Soubor s příklady kódu v tomto rychlém startu najdete [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py). 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor s kódem rychlého startu najednou? Soubor s příklady kódu v tomto rychlém startu najdete [na GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py). 

---

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu a proměnné pro koncový bod a klíč předplatného Azure vašeho prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektový model

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Klient Analýzy textu je objekt `TextAnalyticsClient`, který se ověřuje pro Azure pomocí vašeho klíče. Tento klient nabízí několik metod dávkové analýzy textu. 

Při dávkovém zpracování se text odesílá do rozhraní API ve formě seznamu `documents`, což jsou objekty `dictionary` obsahující kombinaci atributů `id`, `text` a `language` (v závislosti na zvolené metodě). Atribut `text` ukládá text, který se má analyzovat v původním jazyce (`language`) a `id` může být libovolná hodnota. 

Objekt odpovědi je seznam obsahující analytické informace pro jednotlivé dokumenty. 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Klient Analýzy textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python), který se ověřuje pro Azure pomocí vašeho klíče. Tento klient nabízí několik metod analýzy textu, a to ve formě jednoho řetězce nebo dávky. 

Text se do rozhraní API odesílá ve formě seznamu `documents`, což jsou objekty `dictionary` obsahující kombinaci atributů `id`, `text` a `language` (v závislosti na zvolené metodě). Atribut `text` ukládá text, který se má analyzovat v původním jazyce (`language`) a `id` může být libovolná hodnota. 

---

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak pomocí klientské knihovny Analýzy textu pro Python provádět následující úlohy:

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Spojování entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte funkci, která vytvoří instanci objektu `TextAnalyticsClient` s použitím hodnot `key` a `endpoint` vytvořených výše. Pak vytvořte nového klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Vytvořte funkci, která vytvoří instanci objektu `TextAnalyticsClient` s použitím hodnot `key` a `endpoint` vytvořených výše. Pak vytvořte nového klienta. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte novou funkci `sentiment_analysis_example()`, která jako argument přebírá objekt klienta a pak volá funkci `analyze_sentiment()`. Vrácený objekt odpovědi bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro jednotlivé věty.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
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

Ověřte objekt klienta a zavolejte funkci [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-). Iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a skóre mínění. Skóre blížící se 0 značí negativní mínění, zatímco skóre blížící se 1 značí pozitivní mínění.

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

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte novou funkci `language_detection_example()`, která jako argument přebírá objekt klienta a pak volá funkci `detect_language()`. Pokud vše proběhne úspěšně, vrácený objekt odpovědi bude obsahovat zjištěný jazyk v objektu `primary_language`, jinak bude obsahovat objekt `error`.

> [!Tip]
> V některých případech může být obtížné jednoznačně rozpoznat jazyk na základě vstupu. Pomocí parametru `country_hint` můžete zadat dvoupísmenný kód země. Rozhraní API ve výchozím nastavení jako hodnotu countryHint používá US. Pokud chcete toto chování zrušit, můžete tento parametr resetovat nastavením této hodnoty na prázdný řetězec – `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
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

S využitím klienta vytvořeného výše zavolejte funkci [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Potom iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a první vrácený jazyk.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER, Named Entity Recognition)

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0-preview`:
> * Rozpoznávání pojmenovaných entit zahrnuje samostatné metody pro detekci osobních údajů. 
> * Spojování entit je samostatný požadavek.

Vytvořte novou funkci `entity_recognition_example`, která jako argument přebírá objekt klienta, pak volá funkci `recognize_entities()` a iteruje přes seznam výsledků. Pokud vše proběhne úspěšně, vrácený objekt odpovědi bude obsahovat seznam zjištěných entit v objektu `entity`, jinak bude obsahovat objekt `error`. Vytiskněte kategorii a podkategorii (pokud existuje) každé zjištěné entity.

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.score, 2), "\n")

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

## <a name="using-ner-to-detect-personal-information"></a>Použití rozpoznávání pojmenovaných entit k detekci osobních údajů

Vytvořte novou funkci `entity_pii_example()`, která jako argument přebírá objekt klienta, pak volá funkci `recognize_pii_entities()` a získá výsledky. Potom iterujte přes seznam výsledků a vytiskněte entity.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tLength: ", entity.grapheme_length, "\tScore: {0:.2f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Výstup

```console
Personally Identifiable Information Entities:
    Text:  123-12-1234      Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Length:  11     Score: 0.85
```


## <a name="entity-linking"></a>Entity Linking

Vytvořte novou funkci `entity_linking_example()`, která jako argument přebírá objekt klienta, pak volá funkci `recognize_linked_entities()` a iteruje přes seznam výsledků. Pokud vše proběhne úspěšně, vrácený objekt odpovědi bude obsahovat seznam zjištěných entit v objektu `entities`, jinak bude obsahovat objekt `error`. Vzhledem k tomu, že jsou propojené entity jednoznačně identifikované, výskyty stejné entity se seskupují v rámci objektu `entity` jako seznam objektů `match`.

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.2f}".format(match.score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Výstup

```console
Linked Entities:

    Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.78     Length: 11

    Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.55     Length: 10

        Text: Gates
        Score: 0.55     Length: 5

    Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.53     Length: 10

    Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.47     Length: 9

        Text: Microsoft
        Score: 0.47     Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.25     Length: 7

    Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.28     Length: 5

```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

> [!NOTE]
> Ve verzi 2.1 je spojování entit součástí odpovědi NER.

S využitím klienta vytvořeného výše zavolejte funkci [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Potom iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a entity, které obsahuje.

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


#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte novou funkci `key_phrase_extraction_example()`, která jako argument přebírá objekt klienta a pak volá funkci `extract_key_phrases()`. Pokud vše proběhne úspěšně, výsledek bude obsahovat seznam zjištěných klíčových frází v objektu `key_phrases`, jinak bude obsahovat objekt `error`. Vytiskněte všechny zjištěné klíčové fráze.

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

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

S využitím klienta vytvořeného výše zavolejte funkci [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Potom iterujte přes seznam výsledků a vytiskněte ID každého dokumentu a klíčové fráze, které obsahuje.

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