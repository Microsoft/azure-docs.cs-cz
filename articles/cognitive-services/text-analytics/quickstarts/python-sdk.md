---
title: 'Rychlý start: Analýza textu klientskou knihovnu pro Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít používat rozhraní API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: aahi
ms.openlocfilehash: 1d7ad19a58327ba508ccb4e47d12d3d0f50465f4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884006"
---
# <a name="quickstart-text-analytics-client-library-for-python"></a>Rychlý start: Klientská knihovna pro analýzu textu pro Python
<a name="HOLTop"></a>

Začněte s knihovnou klienta Analýza textu pro Python. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. 

Pomocí klientské knihovny Analýza textu pro Python proveďte následující:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází


[](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/) balíčku | [zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | knihovny Referenční dokumentace[(PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/)


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Analýza textu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md) nebo rozhraní příkazového [řádku Azure](../../cognitive-services-apis-create-account-cli.md) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředku na [Azure Portal](https://portal.azure.com/)

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte pro tento klíč [proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte novou aplikaci v Pythonu v upřednostňovaném editoru nebo integrovaném vývojovém prostředí. Pak importujte následující knihovny.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```python
# replace this endpoint with the correct one for your Azure resource. 
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
# This sample assumes you have created an environment variable for your key
key = os.environ["TEXT_ANALYTICS_SUBSCRIPTION_KEY"]
credentials = CognitiveServicesCredentials(key)
```

## <a name="object-model"></a>Objektový model

Klient Analýza textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku. 

Text se pošle do rozhraní API jako seznam `documents` `id`objektů, což jsou `dictionary` objekty obsahující kombinaci atributů, `text`a `language` v závislosti na použité metodě. Atribut ukládá text, který má být analyzován v původním `language`umístění, a `id` může být libovolná hodnota. `text` 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak pomocí Analýza textu klientské knihovny pro Python provést následující akce:

* [Ověření klienta](#authenticate-the-client)
* [Analýza subjektivního hodnocení](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte nový objekt [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) s `credentials` parametrem `text_analytics_url` a jako parametr. Použijte pro předplatné Analýza textu správnou oblast Azure (například `westcentralus`).

```python
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Analýza mínění

Zavolejte funkci [mínění ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    }
]
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Výstup

```console
Document Id:  1 , Sentiment Score:  0.87
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Pomocí dříve vytvořeného klienta volejte [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak projdete výsledky a vytiskněte ID každého dokumentu a první vrácený jazyk.

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    }
]
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Výstup

```console
Document Id:  1 , Language:  English
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Pomocí dříve vytvořeného klienta volejte funkci [Entities ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a entity, které jsou v něm obsažené.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    }
]
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```

### <a name="output"></a>Výstup

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Pomocí dříve vytvořeného klienta volejte funkci [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a klíčové fráze, které jsou v něm obsažené.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    }
]
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```


### <a name="output"></a>Výstup

```console
Document Id:  3
    Phrases:
         cat
         veterinarian
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků přidružených ke skupině prostředků.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjistit jazyk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py)
