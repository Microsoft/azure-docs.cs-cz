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
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 8afceb19af0d177415d0b68b5d38f19d18835af5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87291760"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Rychlý Start: použití klientské knihovny Analýza textu pro Ruby

Začněte s klientskou knihovnou Analýza textu. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

Použijte klientskou knihovnu Analýza textu k provedení těchto akcí:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

> [!NOTE]
> Tento rychlý Start platí pouze pro Analýza textu verze 2,1. V současné době není k dispozici Klientská knihovna v3 pro Ruby.

[Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics)  |  [Balíček (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Ruby](https://www.ruby-lang.org/)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. 
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Provedete to později v rychlém startu.
    * Pomocí cenové úrovně Free můžete službu vyzkoušet a později upgradovat na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-ruby-application"></a>Vytvoření nové aplikace v Ruby

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. Pak vytvořte soubor s názvem `GemFile` a soubor Ruby pro váš kód.

```console
mkdir myapp && cd myapp
```

V aplikaci `GemFile` přidejte následující řádky pro přidání klientské knihovny jako závislosti.

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

Text se pošle do rozhraní API jako seznam `documents` objektů, což jsou `dictionary` objekty obsahující kombinaci `id` `text` atributů, a `language` v závislosti na použité metodě. `text`Atribut ukládá text, který má být analyzován v původním umístění `language` , a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující s Analýza textu klientskou knihovnou pro Ruby:

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte třídu s názvem `TextAnalyticsClient` . 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

V této třídě vytvořte funkci nazvanou, `initialize` která ověří klienta pomocí klíče a koncového bodu. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Mimo třídu použijte `new()` k vytvoření instance funkci klienta.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analýza mínění

V objektu klienta vytvořte funkci `AnalyzeSentiment()` s názvem, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte `sentiment()` funkci klienta a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Mimo funkci klienta vytvořte novou funkci `SentimentAnalysisExample()` s názvem, která převezme objekt, který byl `TextAnalyticsClient` vytvořen dříve. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` `Language` a `text` atribut. `text`Atribut ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. Pak zavolejte `AnalyzeSentiment()` funkci klienta.

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

V objektu klienta vytvořte funkci `DetectLanguage()` s názvem, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte `detect_language()` funkci klienta a získejte výsledek. Pak Projděte výsledky a vytiskněte identifikátor každého dokumentu a zjištěné jazyky.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Mimo funkci klienta vytvořte novou funkci `DetectLanguageExample()` s názvem, která převezme objekt, který byl `TextAnalyticsClient` vytvořen dříve. Vytvořte seznam `LanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` a `text` atribut. `text`Atribut ukládá text, který má být analyzován, a `id` může být libovolná hodnota. Pak zavolejte `DetectLanguage()` funkci klienta.

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

V objektu klienta vytvořte funkci `RecognizeEntities()` s názvem, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte `entities()` funkci klienta a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a rozpoznané entity.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Mimo funkci klienta vytvořte novou funkci `RecognizeEntitiesExample()` s názvem, která převezme objekt, který byl `TextAnalyticsClient` vytvořen dříve. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` atribut, a `language` `text` . `text`Atribut ukládá text, který má být analyzován, `language` je jazyk textu a `id` může být libovolná hodnota. Pak zavolejte `RecognizeEntities()` funkci klienta.

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

V objektu klienta vytvořte funkci `ExtractKeyPhrases()` s názvem, která převezme seznam vstupních dokumentů, které budou vytvořeny později. Zavolejte `key_phrases()` funkci klienta a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a extrahované klíčové fráze.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Mimo funkci klienta vytvořte novou funkci `KeyPhraseExtractionExample()` s názvem, která převezme objekt, který byl `TextAnalyticsClient` vytvořen dříve. Vytvořte seznam `MultiLanguageInput` objektů obsahující dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` atribut, a `language` `text` . `text`Atribut ukládá text, který má být analyzován, `language` je jazyk textu a `id` může být libovolná hodnota. Pak zavolejte `ExtractKeyPhrases()` funkci klienta.

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
