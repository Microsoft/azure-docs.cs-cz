---
title: 'Rychlý start: Analýza textu klientskou knihovnu pro cestách | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Získejte informace a ukázky kódu, které vám pomůžou rychle začít s používáním rozhraní API pro analýzu textu ve službě Azure Cognitive Services.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 1ba2ec6b5c0c59be7b7264f7558fbb393adcc2d8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142789"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>Rychlý start: Klientská knihovna pro analýzu textu pro přejít

Začínáme s klientskou knihovnou Analýza textu pro přejít. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. 

Pomocí klientské knihovny Analýza textu můžete přejít k provedení:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

[Ukázky dokumentace k](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | balíčku[zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | knihovny Referenční dokumentace[](https://github.com/Azure-Samples/cognitive-services-quickstart-code) [(GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/) .
* Nejnovější verze nástroje [Přejít](https://golang.org/dl/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu 

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Analýza textu pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte pro tento klíč [proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-go-project"></a>Vytvoření nového projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt přejít a přejděte na něj. Váš pracovní prostor bude obsahovat tři složky: 

* **Src** – tento adresář obsahuje zdrojový kód a balíčky. Všechny balíčky nainstalované s tímto `go get` příkazem budou umístěny zde.
* **pkg** – tento adresář obsahuje kompilované objekty balíčku pro přechod. Všechny tyto soubory mají `.a` rozšíření.
* **bin** – tento adresář obsahuje binární spustitelné soubory, které se vytvoří při spuštění `go install`.

> [!TIP]
> Přečtěte si další informace o struktuře [pracovního prostoru přejít](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT`.

Vytvořte pracovní prostor s `my-app` názvem a požadované podadresáře `src`pro `pkg`, a `bin`:

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

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"    
)
```

Do projektu přidejte následující funkce jako většinu parametrů a vlastností pro tento rychlý Start. očekává se řetězec a logické ukazatele.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

Ve `main` funkci aplikace vytvořte proměnné pro koncový bod a klíč předplatného prostředku. Získejte tyto hodnoty z proměnných prostředí TEXT_ANALYTICS_SUBSCRIPTION_KEY a TEXT_ANALYTICS_ENDPOINT. Pokud jste po zahájení úprav aplikace vytvořili tyto proměnné prostředí, budete muset zavřít a znovu otevřít Editor, integrované vývojové prostředí (IDE) nebo prostředí, které používáte pro přístup k proměnným.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if "" == os.Getenv(subscriptionKeyVar) {
    log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
}
var subscriptionKey string = os.Getenv(subscriptionKeyVar)
var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
if "" == os.Getenv(endpointVar) {
    log.Fatal("Please set/export the environment variable " + endpointVar + ".")
}
var endpoint string = os.Getenv(endpointVar)
```

## <a name="object-model"></a>Objektový model 

Klient Analýza textu je objekt [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku. 

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

V hlavní funkci projektu vytvořte nový objekt [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) . Předejte svůj klíč do [AutoRest. Funkce NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) , která se pak předává do `authorizer` vlastnosti klienta.

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte novou funkci s názvem `SentimentAnalysis()` , která převezme klienta vytvořeného dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` `Language` a `text` atribut. Atribut ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. `text` 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ve stejné funkci zavolejte funkci [mínění ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) klienta a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

```golang
result, err := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s\n", *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

V hlavní funkci projektu volejte `SentimentAnalysis()`.

### <a name="output"></a>Výstup

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte novou funkci s názvem `LanguageDetection()` , která převezme klienta vytvořeného dříve. Vytvořte seznam objektů [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` `text` atribut a. Atribut ukládá text, který má být analyzován, `id` a může být libovolná hodnota. `text` 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

Ve stejné funkci zavolejte [operaci DetectLanguage ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) klienta a získejte výsledek. Pak Projděte výsledky a vytiskněte identifikátor každého dokumentu a zjištěné jazyky.

```golang
result, err := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

V hlavní funkci projektu volejte `LanguageDetection()`.

### <a name="output"></a>Výstup

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte novou funkci s názvem `ExtractEntities()` , která převezme klienta vytvořeného dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` `text` atribut, `language`a. Atribut ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. `text` 

```golang
func ExtractEntities(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ve stejné funkci zavolejte [entity klienta ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a skóre extrahovaných entit.

```golang
    result, err := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

V hlavní funkci projektu volejte `ExtractEntities()`.

### <a name="output"></a>Výstup

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou funkci s názvem `ExtractKeyPhrases()` , která převezme klienta vytvořeného dříve. Vytvořte seznam objektů [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) s dokumenty, které chcete analyzovat. Každý objekt bude obsahovat `id` `text` atribut, `language`a. Atribut ukládá text, který má být analyzován, `language` je jazyk dokumentu a `id` může být libovolná hodnota. `text`

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ve stejné funkci zavolejte na klíčová slova klienta [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) a získejte výsledek. Potom Iterujte výsledky a vytiskněte ID každého dokumentu a extrahujte klíčové fráze.

```golang
    result, err := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

V hlavní funkci projektu volejte `ExtractKeyPhrases()`.

### <a name="output"></a>Výstup

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků přidružených ke skupině prostředků.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další postup


> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjistit jazyk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices)
