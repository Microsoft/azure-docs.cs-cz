---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.openlocfilehash: fc66cf75101d38ed1deac3b5368ee823dfdab821
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514950"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

[Referenční dokumentace](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  verze 3.1 [zdrojový kód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics)  |  knihovny v 3.1 [balíček verze 3.1 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [ukázky v 3.1](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Referenční dokumentace V3](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  knihovny v3 [balíček V3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [ukázky V3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[Referenční dokumentace v2](/python/api/overview/azure/cognitiveservices/textanalytics)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics)  |  knihovny v2 [balíček v2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/)  |  [ukázky v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Pokud chcete použít funkci analyzovat, budete potřebovat prostředek Analýza textu s cenovou úrovní Standard (y).

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), který obsahuje příklady kódu v tomto rychlém startu. 

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), který obsahuje příklady kódu v tomto rychlém startu. 


# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), který obsahuje příklady kódu v tomto rychlém startu. 

---

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu a vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektový model

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure. Klient nabízí několik metod pro analýzu textu. 

Při zpracování textu se odesílá do rozhraní API jako seznam `documents` , což je buď seznam řetězců, seznam reprezentace dict –, nebo jako seznam `TextDocumentInput/DetectLanguageInput` . `dict-like`Objekt obsahuje kombinaci parametrů `id` , `text` a `language/country_hint` . `text`Atribut ukládá text, který má být analyzován v původním umístění `country_hint` , a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod, jak analyzovat text jako dávku. 

Když je text dávkového zpracování odeslán do rozhraní API jako seznam `documents` objektů, což jsou `dictionary` objekty obsahující kombinaci `id` `text` atributů, a `language` v závislosti na použité metodě. `text`Atribut ukládá text, který má být analyzován v původním umístění `language` , a `id` může být libovolná hodnota. 

Objekt odpovědi je seznam obsahující analyzované informace pro každý dokument. 

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Klient Analýza textu je objekt [TextAnalyticsClient](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku. 

Text se pošle do rozhraní API jako seznam `documents` objektů, což jsou `dictionary` objekty obsahující kombinaci `id` `text` atributů, a `language` v závislosti na použité metodě. `text`Atribut ukládá text, který má být analyzován v původním umístění `language` , a `id` může být libovolná hodnota. 

---

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Analýza textu klientské knihovny pro Python:

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Dolování názoru](#opinion-mining)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Rozpoznávání identifikovatelných osobních údajů](#personally-identifiable-information-recognition) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)


# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>Ověření klienta

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte funkci pro vytvoření instance `TextAnalyticsClient` objektu pomocí `key` a `endpoint` vytvořeného výše. Pak vytvořte nového klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte funkci pro vytvoření instance `TextAnalyticsClient` objektu pomocí `key` a `endpoint` vytvořeného výše. Pak vytvořte nového klienta. Všimněte si, že `api_version=TextAnalyticsApiVersion.V3_0` by měl být definován pro použití verze 3,0.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Pokud jste nainstalovali v 5.1.0 klientské knihovny pomocí `pip install azure-ai-textanalytics --pre` , můžete zadat v 3.0 rozhraní API pro analýzu textu s `api_version` parametrem klienta. Tuto metodu použijte pouze `authenticate_client()` v případě, že je váš klient v 5.1.0 nebo novější.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Vytvořte funkci pro vytvoření instance `TextAnalyticsClient` objektu pomocí `key` a `endpoint` vytvořeného výše. Pak vytvořte nového klienta. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analýza mínění

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte novou funkci s názvem `sentiment_analysis_example()` , která převezme klienta jako argument, a pak zavolá `analyze_sentiment()` funkci. Vrácený objekt Response bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
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

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Dolování názoru

Chcete-li provést analýzu mínění s využitím dolování, vytvořte novou funkci s názvem `sentiment_analysis_with_opinion_mining_example()` , která převezme klienta jako argument, a potom zavolá `analyze_sentiment()` funkci s příznakem možnosti `show_opinion_mining=True` . Vrácený objekt Response bude obsahovat nejen popisek mínění a skóre celého vstupního dokumentu s analýzou mínění pro každou větu, ale také analýzu aspektů a stanovisek mínění.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                aspect = mined_opinion.aspect
                print("......'{}' aspect '{}'".format(aspect.sentiment, aspect.text))
                print("......Aspect score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    aspect.confidence_scores.positive,
                    aspect.confidence_scores.negative,
                ))
                for opinion in mined_opinion.opinions:
                    print("......'{}' opinion '{}'".format(opinion.sentiment, opinion.text))
                    print("......Opinion score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        opinion.confidence_scores.positive,
                        opinion.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Výstup

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' aspect 'food'
......Aspect score:
......Positive=0.01
......Negative=0.99

......'negative' opinion 'unacceptable'
......Opinion score:
......Positive=0.01
......Negative=0.99

......'negative' aspect 'service'
......Aspect score:
......Positive=0.01
......Negative=0.99

......'negative' opinion 'unacceptable'
......Opinion score:
......Positive=0.01
......Negative=0.99

......'positive' aspect 'concierge'
......Aspect score:
......Positive=1.00
......Negative=0.00

......'positive' opinion 'nice'
......Opinion score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `sentiment_analysis_example()` , která převezme klienta jako argument, a pak zavolá `analyze_sentiment()` funkci. Vrácený objekt Response bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu.


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
        print("Sentence: {}".format(sentence.text))
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

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Ověřte objekt klienta a zavolejte funkci [mínění ()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) . Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

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

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte novou funkci s názvem `language_detection_example()` , která převezme klienta jako argument, a pak zavolá `detect_language()` funkci. Vrácený objekt Response bude obsahovat nalezený jazyk v `primary_language` případě úspěchu a v `error` případě potřeby.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `country_hint` parametru můžete zadat kód země se dvěma písmeny. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `country_hint : ""` . 

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

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `language_detection_example()` , která převezme klienta jako argument, a pak zavolá `detect_language()` funkci. Vrácený objekt Response bude obsahovat nalezený jazyk v `primary_language` případě úspěchu a v `error` případě potřeby.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `country_hint` parametru můžete zadat kód země se dvěma písmeny. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `country_hint : ""` . 

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

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Pomocí dříve vytvořeného klienta volejte [detect_language ()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak projdete výsledky a vytiskněte ID každého dokumentu a první vrácený jazyk.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> Ve verzi `3.1` : 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte novou funkci s názvem `entity_recognition_example` , která převezme klienta jako argument, poté zavolá `recognize_entities()` funkci a projde výsledky. Vrácený objekt Response bude obsahovat seznam zjištěných entit v `entity` případě úspěchu, a `error` Pokud ne. Pro každou zjištěnou entitu vytiskněte kategorii a Sub-Category, pokud existuje.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Výstup

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Entity Linking

Vytvořte novou funkci s názvem `entity_linking_example()` , která převezme klienta jako argument, poté zavolá `recognize_linked_entities()` funkci a projde výsledky. Vrácený objekt Response bude obsahovat seznam zjištěných entit v `entities` případě úspěchu, a `error` Pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `entity` objektu jako seznam `match` objektů.

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
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Výstup

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Rozpoznávání identifikovatelných osobních údajů

Vytvořte novou funkci s názvem `pii_recognition_example` , která převezme klienta jako argument, poté zavolá `recognize_pii_entities()` funkci a projde výsledky. Vrácený objekt Response bude obsahovat seznam zjištěných entit v `entity` případě úspěchu, a `error` Pokud ne. Pro každou zjištěnou entitu vytiskněte kategorii a Sub-Category, pokud existuje.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Výstup

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0` : 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte novou funkci s názvem `entity_recognition_example` , která převezme klienta jako argument, poté zavolá `recognize_entities()` funkci a projde výsledky. Vrácený objekt Response bude obsahovat seznam zjištěných entit v `entity` případě úspěchu, a `error` Pokud ne. Pro každou zjištěnou entitu vytiskněte kategorii a Sub-Category, pokud existuje.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Výstup

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Entity Linking

Vytvořte novou funkci s názvem `entity_linking_example()` , která převezme klienta jako argument, poté zavolá `recognize_linked_entities()` funkci a projde výsledky. Vrácený objekt Response bude obsahovat seznam zjištěných entit v `entities` případě úspěchu, a `error` Pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `entity` objektu jako seznam `match` objektů.

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
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
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
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

> [!NOTE]
> Ve verzi 2,1 je odkaz na entitu součástí odpovědi NER.

Pomocí dříve vytvořeného klienta volejte funkci [Entities ()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a entity, které jsou v něm obsažené.

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

### <a name="key-phrase-extraction"></a>Extrakce klíčových frází

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte novou funkci s názvem `key_phrase_extraction_example()` , která převezme klienta jako argument, a pak zavolá `extract_key_phrases()` funkci. Výsledek bude obsahovat seznam zjištěných klíčových frází v `key_phrases` případě úspěchu a v `error` případě potřeby. Vytiskněte všechny zjištěné klíčové fráze.

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

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `key_phrase_extraction_example()` , která převezme klienta jako argument, a pak zavolá `extract_key_phrases()` funkci. Výsledek bude obsahovat seznam zjištěných klíčových frází v `key_phrases` případě úspěchu a v `error` případě potřeby. Vytiskněte všechny zjištěné klíčové fráze.

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

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Pomocí dříve vytvořeného klienta volejte funkci [key_phrases ()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a klíčové fráze, které jsou v něm obsažené.

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

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchronní použití rozhraní API s operací analyzovat

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Vytvořte novou funkci s názvem `analyze_example()` , která převezme klienta jako argument, a pak zavolá `begin_analyze()` funkci. Výsledkem bude dlouhodobá operace, která se bude dotazovat na výsledky.

```python
    def analyze_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = text_analytics_client.begin_analyze(
            documents,
            display_name="Sample Text Analysis",
            entities_recognition_tasks=[EntitiesRecognitionTask()]
        )

        result = poller.result()

        for page in result:
            for task in page.entities_recognition_results:
                print("Results of Entities Recognition task:")
                
                docs = [doc for doc in task.results if not doc.is_error]
                for idx, doc in enumerate(docs):
                    print("\nDocument text: {}".format(documents[idx]))
                    for entity in doc.entities:
                        print("Entity: {}".format(entity.text))
                        print("...Category: {}".format(entity.category))
                        print("...Confidence Score: {}".format(entity.confidence_score))
                        print("...Offset: {}".format(entity.offset))
                    print("------------------------------------------")

analyze_example(client)
```

### <a name="output"></a>Výstup

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

Můžete také použít operaci analyzovat ke zjištění kódu PII a extrakce klíčových frází. Podívejte se na téma [Analýza ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples/async_samples) na GitHubu.

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Tato funkce není k dispozici ve verzi 3,0.

# <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Tato funkce není k dispozici ve verzi 2,1.

---
