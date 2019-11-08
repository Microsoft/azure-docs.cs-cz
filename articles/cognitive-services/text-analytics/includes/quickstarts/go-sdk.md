---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 9b148e413bc7dc6af7eff064e5ff3ec6385cfef4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750208"
---
[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) |  | [ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code) | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) [(GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/) .
* Nejnovější verze nástroje [Přejít](https://golang.org/dl/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-go-project"></a>Vytvoření nového projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt přejít a přejděte na něj. Váš pracovní prostor bude obsahovat tři složky: 

* **Src** – tento adresář obsahuje zdrojový kód a balíčky. Všechny balíčky nainstalované pomocí příkazu `go get` se tady nacházejí.
* **pkg** – tento adresář obsahuje kompilované objekty balíčku pro přechod. Všechny tyto soubory mají rozšíření `.a`.
* **bin** – tento adresář obsahuje binární spustitelné soubory, které se vytvoří při spuštění `go install`.

> [!TIP]
> Přečtěte si další informace o struktuře [pracovního prostoru přejít](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT`.

Vytvořte pracovní prostor nazvaný `my-app` a požadované podadresáře pro `src`, `pkg`a `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Instalace klientské knihovny Analýza textu pro přejít

Nainstalujte klientskou knihovnu pro přejít: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

nebo pokud používáte DEP, v rámci vašeho úložiště úložišť:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Vytvoření aplikace v cestách

Dále vytvořte soubor s názvem `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Otevřete `quickstart.go` v oblíbených rozhraní IDE nebo textovém editoru. Pak přidejte název balíčku a importujte následující knihovny:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objektový model 

Klient Analýza textu je objekt [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku. 

Text se pošle do rozhraní API jako seznam `documents`, což jsou `dictionary` objekty obsahující kombinaci `id`, `text`a `language` atributů v závislosti na použité metodě. Atribut `text` ukládá text, který má být analyzován v `language`původu, a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak pomocí Analýza textu klientské knihovny pro Python provést následující akce:

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta


V nové funkci vytvořte proměnné pro koncový bod a klíč předplatného prostředku. Získejte tyto hodnoty z proměnných prostředí `TEXT_ANALYTICS_SUBSCRIPTION_KEY` a `TEXT_ANALYTICS_ENDPOINT`. Pokud jste po zahájení úprav aplikace vytvořili tyto proměnné prostředí, budete muset zavřít a znovu otevřít Editor, integrované vývojové prostředí (IDE) nebo prostředí, které používáte pro přístup k proměnným.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

Vytvořte nový objekt [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) . Předejte svůj klíč do [AutoRest. Funkce NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) , která bude poté předána vlastnosti `authorizer` klienta.

[!code-go[Client creation ](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=client)]

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte novou funkci nazvanou `SentimentAnalysis()` a vytvořte klienta pomocí metody `GetTextAnalyticsClient()` vytvořené dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id`, `Language` a atribut `text`. Atribut `text` ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. 

Zavolejte funkci [mínění ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) klienta a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Zavolejte `SentimentAnalysis()` v projektu.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte novou funkci nazvanou `LanguageDetection()` a vytvořte klienta pomocí metody `GetTextAnalyticsClient()` vytvořené dříve. Vytvořte seznam objektů [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` a atribut `text`. Atribut `text` ukládá text, který má být analyzován, a `id` může být libovolná hodnota. 

Zavolejte [operaci DetectLanguage ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) klienta a získejte výsledek. Pak Projděte výsledky a vytiskněte identifikátor každého dokumentu a zjištěné jazyky.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Zavolejte `LanguageDetection()` v projektu.

### <a name="output"></a>Výstup

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte novou funkci nazvanou `ExtractEntities()` a vytvořte klienta pomocí metody `GetTextAnalyticsClient()` vytvořené dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat atribut `id`, `language`a `text`. Atribut `text` ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. 

Zavolejte [entity klienta ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a skóre extrahovaných entit.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Zavolejte `ExtractEntities()` v projektu.

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

Vytvořte novou funkci nazvanou `ExtractKeyPhrases()` a vytvořte klienta pomocí metody `GetTextAnalyticsClient()` vytvořené dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat atribut `id`, `language`a `text`. Atribut `text` ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota.

Zavolejte klíčová [slova klienta ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a extrahujte klíčové fráze.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Zavolejte `ExtractKeyPhrases()` v projektu.

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
