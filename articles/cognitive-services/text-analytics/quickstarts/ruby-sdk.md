---
title: 'Úvodní příručka: Klientská knihovna Text Analytics pro Ruby | Dokumenty společnosti Microsoft'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjišťujte jazyk pomocí klientské knihovny Ruby Text Analytics ze služby Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0d4d32a413dd22c55f1b2f01dce3a3df81f5f729
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77919664"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Úvodní příručka: Použití klientské knihovny Text Analytics pro Ruby

Začínáme s klientskou knihovnou Text Analytics. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

Pomocí klientské knihovny Text Analytics můžete provádět:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

> [!NOTE]
> Tento rychlý start se vztahuje pouze na analýzu textu verze 2.1. V současné době je klientská knihovna v3 pro Ruby nedostupná.

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [knihovny](https://github.com/Azure-Samples/cognitive-services-quickstart-code) [(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | 

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [Ruby](https://www.ruby-lang.org/)
* Jakmile budete mít předplatné <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure, vytvořte"  target="_blank">prostředek analýzy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> textu vytvořit prostředek analýzy textu na webu Azure Portal, abyste získali klíč a koncový bod. 
    * Budete potřebovat klíč a koncový bod z prostředku, který vytvoříte pro připojení aplikace k rozhraní API analýzy textu. Uděláte to později v rychlém startu.
    * Můžete použít bezplatnou cenovou úroveň vyzkoušet službu a upgradovat později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-ruby-application"></a>Vytvoření nové aplikace Ruby

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. Potom vytvořte `GemFile`soubor s názvem a soubor Ruby pro váš kód.

```console
mkdir myapp && cd myapp
```

Do `GemFile`aplikace přidejte následující řádky a přidejte klientskou knihovnu jako závislost.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

V souboru Ruby importujte následující balíčky.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objektový model 

Klient Text Analytics se ověřuje do Azure pomocí vašeho klíče. Klient poskytuje několik metod pro analýzu textu jako jeden řetězec nebo dávka. 

Text je odeslán do rozhraní `documents`API jako `dictionary` seznam , což `id` `text`jsou `language` objekty obsahující kombinaci , a atributy v závislosti na použité metodě. Atribut `text` ukládá text, který má `language`být analyzován `id` v počátku , a může být libovolná hodnota. 

Objekt odpovědi je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce v klientské knihovně Text Analytics pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Detekce jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte třídu s názvem `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

V této třídě vytvořte `initialize` funkci volanou k ověření klienta pomocí klíče a koncového bodu. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Mimo třídu použijte funkci klienta `new()` k vytvoření instance.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analýza mínění

V objektu klienta vytvořte funkci volanou, `AnalyzeSentiment()` která přebírá seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `sentiment()` klienta a získejte výsledek. Pak iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a skóre mínění. Skóre blíže k 0 označuje negativní sentiment, zatímco skóre blíže k 1 označuje pozitivní sentiment.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Mimo klientské funkce vytvořte novou `SentimentAnalysisExample()` funkci `TextAnalyticsClient` volanou, která přebírá dříve vytvořený objekt. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id` `Language` obsahovat `text` atribut , a atribut. Atribut `text` ukládá text, který `language` má být analyzován, je `id` jazyk dokumentu a může být libovolná hodnota. Potom volání funkce `AnalyzeSentiment()` klienta.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Zavolejte `SentimentAnalysisExample()` funkci.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Výstup

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Rozpoznávání jazyka

V objektu klienta vytvořte funkci volanou, `DetectLanguage()` která přebírá seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `detect_language()` klienta a získejte výsledek. Poté iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a zjištěný jazyk.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Mimo klientské funkce vytvořte novou `DetectLanguageExample()` funkci `TextAnalyticsClient` volanou, která přebírá dříve vytvořený objekt. Vytvořte seznam `LanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id`obsahovat `text` atribut , a atribut. Atribut `text` ukládá text, který má `id` být analyzován, a může být libovolná hodnota. Potom volání funkce `DetectLanguage()` klienta.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Zavolejte `DetectLanguageExample()` funkci.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Výstup

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Rozpoznávání entit

V objektu klienta vytvořte funkci volanou, `RecognizeEntities()` která přebírá seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `entities()` klienta a získejte výsledek. Potom iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a rozpoznané entity.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Mimo klientské funkce vytvořte novou `RecognizeEntitiesExample()` funkci `TextAnalyticsClient` volanou, která přebírá dříve vytvořený objekt. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id`obsahovat atribut , a `language`a. `text` Atribut `text` ukládá text, který `language` má být analyzován, je `id` jazyk textu a může být libovolná hodnota. Potom volání funkce `RecognizeEntities()` klienta.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Zavolejte `RecognizeEntitiesExample()` funkci.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Výstup

```console
===== ENTITY RECOGNITION =====
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

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

V objektu klienta vytvořte funkci volanou, `ExtractKeyPhrases()` která přebírá seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `key_phrases()` klienta a získejte výsledek. Pak iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a extrahované klíčové fráze.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Mimo klientské funkce vytvořte novou `KeyPhraseExtractionExample()` funkci `TextAnalyticsClient` volanou, která přebírá dříve vytvořený objekt. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id`obsahovat atribut , a `language`a. `text` Atribut `text` ukládá text, který `language` má být analyzován, je `id` jazyk textu a může být libovolná hodnota. Potom volání funkce `ExtractKeyPhrases()` klienta.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Zavolejte `KeyPhraseExtractionExample()` funkci.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

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
                rock
Document ID: 4
         Key phrases:
                fútbol
```
