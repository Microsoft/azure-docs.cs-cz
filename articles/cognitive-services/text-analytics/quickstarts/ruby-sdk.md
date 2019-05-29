---
title: 'Rychlý start: Volání Text Analytics služby Cognitive Services pomocí sady SDK pro Ruby'
titleSuffix: Azure Cognitive Services
description: Získat informace a ukázky kódu můžete rychle začít používat rozhraní API pro analýzu textu ve službě Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 688887826fa803b616ca737bc8558aa17ed80e37
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297775"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Rychlý start: Volání služby pro analýzu textu pomocí sady SDK pro Ruby

<a name="HOLTop"></a>


V tomto rychlém startu můžete začít analýzou jazyce s využitím Text Analytics SDK pro Ruby. Zatímco [rozhraní Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) rozhraní REST API je kompatibilní s Většina programovacích jazyků, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

* [Ruby 2.5.5 nebo novější](https://www.ruby-lang.org/)
* Rozhraní Text analytics [sady SDK pro Ruby](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Kromě toho potřebujete [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) vygenerovaný během registrace. 

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Vytvořte projekt Ruby a instalace sady SDK

1. Vytvoření nového projektu ruby a přidejte nový soubor s názvem `Gemfile`.
2. Přidat Text Analytics SDK do projektu tak, že přidáte níže uvedeného kódu `Gemfile`.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Vytvoření klienta Text analytics

1. Vytvořte nový soubor s názvem `TextAnalyticsExamples.rb` v oblíbeném editoru nebo prostředí IDE. Importujte rozhraní Text Analytics SDK.

2. Objekt přihlašovacích údajů se použije klient pro analýzu textu. Vytvořte ho s `CognitiveServicesCredentials.new()` a předají se váš klíč předplatného.

3. Vytvoření klienta s správný koncový bod pro analýzu textu.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analýza mínění

Pomocí Text Analytics SDK nebo rozhraní API, můžete provádět analýzu subjektivního hodnocení na sadu záznamů text. Následující příklad zobrazí skóre mínění pro několik dokumentů.

1. Vytvořit novou funkci s názvem `SentimentAnalysisExample()` desetinný klienta text analytics vytvořené výše jako parametr.

2. Definujte sadu `MultiLanguageInput` objektů má být analyzován. Přidáte jazyk a text pro každý objekt. ID může být libovolná hodnota.

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

3. V rámci stejné funkce zkombinujte do seznam dokumentů. Přidejte ji tak `documents` pole `MultiLanguageBatchInput` objektu. 

4. Volání klienta `sentiment()` pracovat `MultiLanguageBatchInput` objektu jako parametr odesílat dokumenty. Pokud jsou vráceny žádné výsledky, vytiskněte.
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

5. Volání `SentimentAnalysisExample()` funkce.

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

Služba Text Analytics můžete rozpoznat jazyk textový dokument velkým počtem jazyky a národní prostředí. Následující příklad zobrazí několik dokumentů, které byly napsány v jazyce.

1. Vytvořit novou funkci s názvem `DetectLanguageExample()` , která přijímá klienta text analytics vytvořené výše jako parametr. 

2. Definujte sadu `LanguageInput` objektů má být analyzován. Přidáte jazyk a text pro každý objekt. ID může být libovolná hodnota.

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

3. V rámci stejné funkce zkombinujte do seznam dokumentů. Přidejte ji tak `documents` pole `LanguageBatchInput` objektu. 

4. Volání klienta `detect_language()` pracovat `LanguageBatchInput` objektu jako parametr odesílat dokumenty. Pokud jsou vráceny žádné výsledky, vytiskněte.
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

5. Volání funkce `DetectLanguageExample`

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

Služba Text Analytics můžete rozlišení a extrahovat různé entity (osoby, místa a věcí) v textové dokumenty. Následující příklad zobrazuje entity v několika příklad dokumentů.

1. Vytvořit novou funkci s názvem `Recognize_Entities()` desetinný klienta text analytics vytvořené výše jako parametr.

2. Definujte sadu `MultiLanguageInput` objektů má být analyzován. Přidáte jazyk a text pro každý objekt. ID může být libovolná hodnota.

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

3. V rámci stejné funkce zkombinujte do seznam dokumentů. Přidejte ji tak `documents` pole `MultiLanguageBatchInput` objektu. 

4. Volání klienta `entities()` pracovat `MultiLanguageBatchInput` objektu jako parametr odesílat dokumenty. Pokud jsou vráceny žádné výsledky, vytiskněte.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
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

5. Volání funkce `RecognizeEntitiesExample`
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

Služby pro analýzu textu můžete extrakce klíčových frází v věty. Následující příklad zobrazuje entity v několika příklad dokumentů v různých jazycích.

1. Vytvořit novou funkci s názvem `KeyPhraseExtractionExample()` desetinný klienta text analytics vytvořené výše jako parametr.

2. Definujte sadu `MultiLanguageInput` objektů má být analyzován. Přidáte jazyk a text pro každý objekt. ID může být libovolná hodnota.

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

3. V rámci stejné funkce zkombinujte do seznam dokumentů. Přidejte ji tak `documents` pole `MultiLanguageBatchInput` objektu. 

4. Volání klienta `key_phrases()` pracovat `MultiLanguageBatchInput` objektu jako parametr odesílat dokumenty. Pokud jsou vráceny žádné výsledky, vytiskněte.

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

5. Volání funkce `KeyPhraseExtractionExample`

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

## <a name="see-also"></a>Další informace najdete v tématech

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)
