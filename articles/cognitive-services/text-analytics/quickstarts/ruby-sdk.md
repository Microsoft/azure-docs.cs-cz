---
title: 'Rychlý Start: Analýza textu klientskou knihovnu pro Ruby | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte jazyk pomocí klientské knihovny Analýza textu Ruby z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/29/2020
ms.author: aahi
ms.openlocfilehash: f34d4e50042f0fd05a224ff096c2b472224248f8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992398"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Rychlý Start: použití klientské knihovny Analýza textu pro Ruby

Začněte s klientskou knihovnou Analýza textu. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

Použijte klientskou knihovnu Analýza textu k provedení těchto akcí:

* Analýza subjektivního hodnocení
* Detekce jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

> [!NOTE]
> Tento rychlý Start platí pouze pro Analýza textu verze 2,1. V současné době není k dispozici Klientská knihovna v3 pro Ruby.

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) |  | [ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code) | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) [(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Ruby](https://www.ruby-lang.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu 

[!INCLUDE [text-analytics-resource-creation](../includes/quickstarts/resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Vytvoření nové aplikace v Ruby

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. Pak vytvořte soubor s názvem `GemFile`a soubor Ruby pro váš kód.

```console
mkdir myapp && cd myapp
```

V `GemFile`přidejte následující řádky pro přidání klientské knihovny jako závislosti.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

V souboru Ruby importujte následující balíčky.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objektový model 

Klient Analýza textu se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku. 

Text se pošle do rozhraní API jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak pomocí Analýza textu klientské knihovny pro Python provést následující akce:

* [Ověření klienta](#authenticate-the-client)
* [Analýza subjektivního hodnocení](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
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

V této třídě vytvořte funkci s názvem `initialize` k ověření klienta pomocí klíče a koncového bodu. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Mimo třídu použijte k vytvoření instance funkci `new()` klienta.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení

V objektu klienta vytvořte funkci s názvem `AnalyzeSentiment()`, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `sentiment()` klienta a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Mimo funkci klienta vytvořte novou funkci nazvanou `SentimentAnalysisExample()`, která převezme dříve vytvořený objekt `TextAnalyticsClient`. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id`, `Language` a atribut `text`. Atribut `text` ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. Pak zavolejte funkci `AnalyzeSentiment()` klienta.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Zavolejte funkci `SentimentAnalysisExample()`.

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

## <a name="language-detection"></a>Detekce jazyka

V objektu klienta vytvořte funkci s názvem `DetectLanguage()`, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `detect_language()` klienta a získejte výsledek. Pak Projděte výsledky a vytiskněte identifikátor každého dokumentu a zjištěné jazyky.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Mimo funkci klienta vytvořte novou funkci nazvanou `DetectLanguageExample()`, která převezme dříve vytvořený objekt `TextAnalyticsClient`. Vytvořte seznam `LanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id`a atribut `text`. Atribut `text` ukládá text, který má být analyzován, a `id` může být libovolná hodnota. Pak zavolejte funkci `DetectLanguage()` klienta.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Zavolejte funkci `DetectLanguageExample()`.

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

V objektu klienta vytvořte funkci s názvem `RecognizeEntities()`, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `entities()` klienta a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a rozpoznané entity.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Mimo funkci klienta vytvořte novou funkci nazvanou `RecognizeEntitiesExample()`, která převezme dříve vytvořený objekt `TextAnalyticsClient`. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id`, `language`a atribut `text`. Atribut `text` ukládá text, který má být analyzován, `language` je jazyk textu a `id` může být libovolná hodnota. Pak zavolejte funkci `RecognizeEntities()` klienta.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Zavolejte funkci `RecognizeEntitiesExample()`.

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

V objektu klienta vytvořte funkci s názvem `ExtractKeyPhrases()`, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte funkci `key_phrases()` klienta a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a extrahované klíčové fráze.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Mimo funkci klienta vytvořte novou funkci nazvanou `KeyPhraseExtractionExample()`, která převezme dříve vytvořený objekt `TextAnalyticsClient`. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id`, `language`a atribut `text`. Atribut `text` ukládá text, který má být analyzován, `language` je jazyk textu a `id` může být libovolná hodnota. Pak zavolejte funkci `ExtractKeyPhrases()` klienta.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Zavolejte funkci `KeyPhraseExtractionExample()`.

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
