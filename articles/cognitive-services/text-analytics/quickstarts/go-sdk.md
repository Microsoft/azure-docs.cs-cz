---
title: 'Úvodní příručka: Klientská knihovna Text Analytics pro Go | Dokumenty společnosti Microsoft'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjišťujte jazyk pomocí klientské knihovny Go Text Analytics ze služby Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0b4495616c750b2b3e8431e011d71ae8671af1ef
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77912632"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Úvodní příručka: Použití klientské knihovny Text Analytics pro možnost Přejít

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [knihovny](https://github.com/Azure-Samples/cognitive-services-quickstart-code) [(GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | 

> [!NOTE]
> Tento rychlý start se vztahuje pouze na analýzu textu verze 2.1. V současné době je klientská knihovna v3 pro Go není k dispozici.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Nejnovější verze [Go](https://golang.org/dl/)
* Jakmile budete mít předplatné <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure, vytvořte"  target="_blank">prostředek analýzy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> textu vytvořit prostředek analýzy textu na webu Azure Portal, abyste získali klíč a koncový bod. 
    * Budete potřebovat klíč a koncový bod z prostředku, který vytvoříte pro připojení aplikace k rozhraní API analýzy textu. Uděláte to později v rychlém startu.
    * Můžete použít bezplatnou cenovou úroveň vyzkoušet službu a upgradovat později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-go-project"></a>Vytvoření nového projektu Go

V okně konzoly (cmd, PowerShell, Terminal, Bash) vytvořte nový pracovní prostor pro projekt Go a přejděte na něj. Pracovní prostor bude obsahovat tři složky: 

* **src** - Tento adresář obsahuje zdrojový kód a balíčky. Všechny balíčky `go get` nainstalované pomocí příkazu budou umístěny zde.
* **pkg** - Tento adresář obsahuje zkompilované objekty balíčku Go. Všechny tyto soubory `.a` mají příponu.
* **bin** - Tento adresář obsahuje binární spustitelné soubory, které jsou vytvořeny při spuštění `go install`.

> [!TIP]
> Přečtěte si další informace o struktuře [pracovního prostoru Go](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje `$GOPATH` `$GOROOT`informace pro nastavení a .

Vytvořte pracovní `my-app` prostor s názvem a `src` `pkg`požadované `bin`podadresáře pro , a :

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Instalace klientské knihovny Text Analytics pro přejít

Nainstalujte klientskou knihovnu pro Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

nebo pokud používáte dep, v rámci vašeho repo běhu:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Vytvoření aplikace Go

Dále vytvořte soubor `src/quickstart.go`s názvem :

```bash
$ cd src
$ touch quickstart.go
```

Otevřete `quickstart.go` ve svém oblíbeném ide nebo textovém editoru. Potom přidejte název balíčku a importujte následující knihovny:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objektový model 

Klient Text Analytics je objekt [BaseClient,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) který se ověřuje v Azure pomocí vašeho klíče. Klient poskytuje několik metod pro analýzu textu jako jeden řetězec nebo dávka. 

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


V nové funkci vytvořte proměnné pro koncový bod azure a klíč předplatného vašeho prostředku.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Vytvořte nový objekt [BaseClient.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) Podej klíč k [autoopěrce. NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) funkce, která pak bude předána `authorizer` vlastnosti klienta.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte novou `SentimentAnalysis()` funkci s názvem `GetTextAnalyticsClient()` a vytvořte klienta pomocí metody vytvořené dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id` `Language` obsahovat `text` atribut , a atribut. Atribut `text` ukládá text, který `language` má být analyzován, je `id` jazyk dokumentu a může být libovolná hodnota. 

Zavolejte funkci [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) klienta a získejte výsledek. Pak iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a skóre mínění. Skóre blíže k 0 označuje negativní sentiment, zatímco skóre blíže k 1 označuje pozitivní sentiment.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

hovoru `SentimentAnalysis()` ve vašem projektu.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte novou `LanguageDetection()` funkci s názvem `GetTextAnalyticsClient()` a vytvořte klienta pomocí metody vytvořené dříve. Vytvořte seznam objektů [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id` obsahovat atribut a. `text` Atribut `text` ukládá text, který má `id` být analyzován, a může být libovolná hodnota. 

Zavolejte klienta [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) a získat výsledek. Poté iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a zjištěný jazyk.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Zavolejte `LanguageDetection()` do svého projektu.

### <a name="output"></a>Výstup

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte novou `ExtractEntities()` funkci s názvem `GetTextAnalyticsClient()` a vytvořte klienta pomocí metody vytvořené dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id` `language`obsahovat `text` atribut , a atribut. Atribut `text` ukládá text, který `language` má být analyzován, je `id` jazyk dokumentu a může být libovolná hodnota. 

Zavolejte entity [klienta()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) a získat výsledek. Pak iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a extrahované entity skóre.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

hovoru `ExtractEntities()` ve vašem projektu.

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

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou `ExtractKeyPhrases()` funkci s názvem `GetTextAnalyticsClient()` a vytvořte klienta pomocí metody vytvořené dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) obsahující dokumenty, které chcete analyzovat. Každý objekt bude `id` `language`obsahovat `text` atribut , a atribut. Atribut `text` ukládá text, který `language` má být analyzován, je `id` jazyk dokumentu a může být libovolná hodnota.

Zavolejte klientovi [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) a získejte výsledek. Pak iterate prostřednictvím výsledků a vytisknout ID každého dokumentu a extrahované klíčové fráze.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Zavolejte `ExtractKeyPhrases()` do svého projektu.

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
