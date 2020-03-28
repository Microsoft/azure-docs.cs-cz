---
title: 'Úvodní příručka: Knihovna klientů Bing Autosuggest pro go | Dokumenty společnosti Microsoft'
description: Začínáme s klientskou knihovnou Bing Autosuggest pro Go a získejte návrhy hledání na základě částečných řetězců dotazů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 59f704377695be0f3253bdd0d7a7b82b460c86d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239011"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Úvodní příručka: Klientská knihovna Bing Autosuggest pro Go

Začínáme s klientskou knihovnou Bing Autosuggest pro Go. Podle těchto kroků nainstalujte knihovnu a vyzkoušejte naše příklady základních úkolů. 

Pomocí klientské knihovny Bing Autosuggest pro přejít získat návrhy hledání na základě částečných řetězců dotazu.

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) |  | [Ukázkový kód zdrojového](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go) [kódu knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Nejnovější verze [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Nastavení

### <a name="create-an-azure-resource"></a>Vytvoření prostředku Azure 

Začněte používat knihovnu klienta Bing Autosuggest vytvořením prostředku Azure. Vyberte typ zdroje, který je pro vás ten pravý:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

>[!NOTE]
> Koncové body pro nezkušební prostředky vytvořené po 1. Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Pomocí klíče a koncového bodu z prostředku, který jste vytvořili, vytvořte dvě proměnné prostředí pro ověřování:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`- Klíč zdroje pro ověřování vašich požadavků.
* `AUTOSUGGEST_ENDPOINT`- Koncový bod prostředku pro odesílání požadavků rozhraní API. Bude to vypadat takto: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Použijte pokyny pro váš operační systém.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
***

### <a name="create-a-new-go-project"></a>Vytvoření nového projektu Go

V okně konzoly (cmd, PowerShell, Terminal, Bash) vytvořte nový pracovní prostor pro projekt Go a přejděte na něj. Pracovní prostor bude obsahovat tři složky: 

* **src** - Tento adresář obsahuje zdrojový kód a balíčky. Všechny balíčky `go get` nainstalované pomocí příkazu budou umístěny zde.
* **pkg** - Tento adresář obsahuje zkompilované objekty balíčku Go. Všechny tyto soubory `.a` mají příponu.
* **bin** - Tento adresář obsahuje binární spustitelné soubory, které jsou vytvořeny při spuštění `go install`.

> [!TIP]
> Přečtěte si další informace o struktuře [pracovního prostoru Go](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje `$GOPATH` `$GOROOT`informace pro nastavení a .

Vytvoříme pracovní prostor s `my-app` názvem a požadované `src`podadresáře pro , `pkg`a `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro Přejít

Nyní nainstalujeme klientskou knihovnu pro Go: 

```bash
$ go get -u <library-location-or-url>
```

nebo pokud používáte dep, v rámci vašeho repo běhu:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Vytvoření aplikace Go

Dále vytvoříme soubor s `src/sample-app.go`názvem :

```bash
$ cd src
$ touch sample-app.go
```

Otevřete `sample-app.go` a přidejte název balíčku a importujte následující knihovny:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Vytvořte funkci `main`s názvem . Pak vytvořte proměnné prostředí pro klíč a koncový bod Bing Autosuggest.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Příklady kódu

Tyto ukázky kódu ukazují, jak dokončit základní úkoly pomocí knihovny klienta Bing Autosuggest pro Go:

* [Ověření klienta](#authenticate-the-client)
* [Odeslání požadavku na rozhraní API](#send-an-api-request)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE] 
> Tento rychlý start předpokládá, že jste [vytvořili proměnnou](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) prostředí `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`pro klíč automatického `BING_AUTOSUGGEST_ENDPOINT`návrhu Bingu s názvem a jeden pro koncový bod s názvem .

Ve `main()` funkci vytvořte instanci klienta pomocí koncového bodu a klíče. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Odeslání požadavku na rozhraní API

Ve stejné metodě použijte metodu [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta k odeslání dotazu bingu. Poté iterate přes [návrhy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) odpověď a vytisknout první návrh.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci `go run [arguments]` Go pomocí příkazu z adresáře aplikace.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro automatické návrhy Bingu](../tutorials/autosuggest.md)

## <a name="see-also"></a>Viz také

- [Co jsou Automatické návrhy Bingu?](../get-suggested-search-terms.md)
- [Referenční materiály rozhraní API pro automatické návrhy Bingu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
