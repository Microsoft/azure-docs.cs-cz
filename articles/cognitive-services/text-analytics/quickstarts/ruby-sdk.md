---
title: 'Rychlý start: Volání služby Analýza textu pro rozpoznávání pomocí sady Ruby SDK'
titleSuffix: Azure Cognitive Services
description: Získejte informace a ukázky kódu, které vám pomůžou rychle začít s používáním rozhraní API pro analýzu textu ve službě Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: tasharm
ms.openlocfilehash: 5e1578d88d6e151479961aeaf7a54c130419f282
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142718"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Rychlý start: Volání služby Analýza textu pomocí sady Ruby SDK

<a name="HOLTop"></a>


Pomocí tohoto rychlého startu můžete začít s analýzou jazyka s Analýza textu SDK pro Ruby. I když je REST API [Analýza textu](//go.microsoft.com/fwlink/?LinkID=759711) kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb)

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

* [Ruby 2.5.5 nebo novější](https://www.ruby-lang.org/)
* Sada Text Analytics [SDK pro Ruby](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Vytvoření projektu Ruby a instalace sady SDK

1. Vytvořte nový projekt Ruby a přidejte nový soubor s názvem `Gemfile`.
2. Přidejte sadu Analýza textu SDK do projektu přidáním níže uvedeného kódu do `Gemfile`.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

## <a name="create-a-text-analytics-client"></a>Vytvoření klienta pro analýzu textu

1. Vytvořte nový soubor s názvem `TextAnalyticsExamples.rb` v oblíbených editorech nebo v integrovaném vývojovém prostředí. Importujte sadu Analýza textu SDK.

2. Klient Analýza textu použije objekt přihlašovacích údajů. Vytvořte ho pomocí `CognitiveServicesCredentials.new()` a předejte svůj klíč předplatného.

3. Vytvořte klienta se správným koncovým bodem Analýza textu.

```ruby
# encoding: UTF-8
# Without this encoding directive, you might get an error such as:
# sdk.rb:60: invalid multibyte char (UTF-8)

require 'azure_cognitiveservices_textanalytics'

include Azure::CognitiveServices::TextAnalytics::V2_1::Models

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

credentials = MsRestAzure::CognitiveServicesCredentials.new(subscription_key)

textAnalyticsClient =
    Azure::TextAnalytics::Profiles::Latest::Client.new({
        credentials: credentials
    })
textAnalyticsClient.endpoint = endpoint
```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analýza mínění

Pomocí Analýza textu SDK nebo rozhraní API můžete provádět analýzy mínění pro sadu textových záznamů. Následující příklad zobrazuje mínění skóre pro několik dokumentů.

1. Vytvořte novou funkci s názvem `SentimentAnalysisExample()` , která převezme klienta Text Analytics vytvořený výše jako parametr.

2. Definujte sadu `MultiLanguageInput` objektů, které se mají analyzovat. Přidejte jazyk a text pro každý objekt. ID může být libovolná hodnota.

```ruby
def SentimentAnalysisExample(client)
    # The documents to be analyzed. Add the language of the document. The ID can be any value.
    input_1 = MultiLanguageInput.new
    input_1.id = '1'
    input_1.language = 'en'
    input_1.text = 'I had the best day of my life.'

    input_2 = MultiLanguageInput.new
    input_2.id = '2'
    input_2.language = 'en'
    input_2.text = 'This was a waste of my time. The speaker put me to sleep.'

    input_3 = MultiLanguageInput.new
    input_3.id = '3'
    input_3.language = 'es'
    input_3.text = 'No tengo dinero ni nada que dar...'

    input_4 = MultiLanguageInput.new
    input_4.id = '4'
    input_4.language = 'it'
    input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
```

3. V rámci stejné funkce sloučí dokumenty do seznamu. Přidejte ho do `documents` pole `MultiLanguageBatchInput` objektu. 

4. Zavolejte `sentiment()` funkci klienta `MultiLanguageBatchInput` s objektem jako parametr pro odeslání dokumentů. Pokud se vrátí nějaké výsledky, vytiskněte je.
```ruby
    input_documents =  MultiLanguageBatchInput.new
    input_documents.documents = [input_1, input_2, input_3, input_4]

    result = client.sentiment(
        multi_language_batch_input: input_documents
    )
  
    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
            puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
    end
end
```

5. `SentimentAnalysisExample()` Zavolejte funkci.

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

Služba Analýza textu dokáže detekovat jazyk textového dokumentu napříč velkým počtem jazyků a národních prostředí. Následující příklad zobrazuje jazyk, ve kterém bylo napsáno několik dokumentů.

1. Vytvořte novou funkci s názvem `DetectLanguageExample()` , která převezme klienta Text Analytics vytvořený výše jako parametr. 

2. Definujte sadu `LanguageInput` objektů, které se mají analyzovat. Přidejte jazyk a text pro každý objekt. ID může být libovolná hodnota.

```ruby
def DetectLanguageExample(client)
    # The documents to be analyzed.
    language_input_1 = LanguageInput.new
    language_input_1.id = '1'
    language_input_1.text = 'This is a document written in English.'

    language_input_2 = LanguageInput.new
    language_input_2.id = '2'
    language_input_2.text = 'Este es un document escrito en Español..'

    language_input_3 = LanguageInput.new
    language_input_3.id = '3'
    language_input_3.text = '这是一个用中文写的文件'
```

3. V rámci stejné funkce sloučí dokumenty do seznamu. Přidejte ho do `documents` pole `LanguageBatchInput` objektu. 

4. Zavolejte `detect_language()` funkci klienta `LanguageBatchInput` s objektem jako parametr pro odeslání dokumentů. Pokud se vrátí nějaké výsledky, vytiskněte je.
```ruby
    input_documents = LanguageBatchInput.new
    input_documents.documents = [language_input_1, language_input_2, language_input_3]

    result = client.detect_language(
        language_batch_input: input_documents
    )

    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== LANGUAGE DETECTION ====='
        result.documents.each do |document|
            puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
        end
    else
        puts 'No results data..'
    end
end
```

5. Volání funkce`DetectLanguageExample`

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

Služba Analýza textu může v textových dokumentech odlišit a extrahovat různé entity (osoby, místa a věci). Následující příklad zobrazuje entity, které byly nalezeny v několika ukázkových dokumentech.

1. Vytvořte novou funkci s názvem `Recognize_Entities()` , která převezme klienta Text Analytics vytvořený výše jako parametr.

2. Definujte sadu `MultiLanguageInput` objektů, které se mají analyzovat. Přidejte jazyk a text pro každý objekt. ID může být libovolná hodnota.

```ruby
def RecognizeEntitiesExample(client)
    # The documents to be analyzed.
    input_1 = MultiLanguageInput.new
    input_1.id = '1'
    input_1.language = 'en'
    input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'

    input_2 = MultiLanguageInput.new
    input_2.id = '2'
    input_2.language = 'es'
    input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. Within the same function, combine the documents into a list. Add it to the `documents` field of a `MultiLanguageBatchInput` object. 

4. Call the client's `entities()` function with the `MultiLanguageBatchInput` object as a parameter to send the documents. If any results are returned, print them.

```ruby
    input_documents = MultiLanguageBatchInput.new
    input_documents.documents = [input_1, input_2]

    result = client.entities(
    multi_language_batch_input: input_documents
    )

    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== ENTITY RECOGNITION ====='
        result.documents.each do |document|
            puts "Document ID: #{document.id}"
            document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                    puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
            end
        end
    else
        puts 'No results data..'
    end
end
```

5. Volání funkce`RecognizeEntitiesExample`
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

Služba Analýza textu může extrahovat klíčové fráze ve větách. Následující příklad zobrazuje entity, které byly nalezeny v několika příkladech dokumentů v několika jazycích.

1. Vytvořte novou funkci s názvem `KeyPhraseExtractionExample()` , která převezme klienta Text Analytics vytvořený výše jako parametr.

2. Definujte sadu `MultiLanguageInput` objektů, které se mají analyzovat. Přidejte jazyk a text pro každý objekt. ID může být libovolná hodnota.

```ruby
def KeyPhraseExtractionExample(client)
    # The documents to be analyzed.
    input_1 = MultiLanguageInput.new
    input_1.id = '1'
    input_1.language = 'ja'
    input_1.text = '猫は幸せ'

    input_2 = MultiLanguageInput.new
    input_2.id = '2'
    input_2.language = 'de'
    input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'

    input_3 = MultiLanguageInput.new
    input_3.id = '3'
    input_3.language = 'en'
    input_3.text = 'My cat is stiff as a rock.'

    input_4 = MultiLanguageInput.new
    input_4.id = '4'
    input_4.language = 'es'
    input_4.text = 'A mi me encanta el fútbol!'
```

3. V rámci stejné funkce sloučí dokumenty do seznamu. Přidejte ho do `documents` pole `MultiLanguageBatchInput` objektu. 

4. Zavolejte `key_phrases()` funkci klienta `MultiLanguageBatchInput` s objektem jako parametr pro odeslání dokumentů. Pokud se vrátí nějaké výsledky, vytiskněte je.

```ruby
    input_documents =  MultiLanguageBatchInput.new
    input_documents.documents = [input_1, input_2, input_3, input_4]

    result = client.key_phrases(
        multi_language_batch_input: input_documents
    )

    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
            puts "Document Id: #{document.id}"
            puts '  Key Phrases'
            document.key_phrases.each do |key_phrase|
                puts "    #{key_phrase}"
            end
        end
    else
        puts 'No results data..'
    end
end
```

5. Volání funkce`KeyPhraseExtractionExample`

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Viz také:

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)
