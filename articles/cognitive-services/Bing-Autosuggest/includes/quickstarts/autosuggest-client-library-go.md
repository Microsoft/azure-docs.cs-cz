---
title: Průvodce rychlým startem klientské knihovny Automatické návrhy Bingu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 8c0715b3570bf60205c83390ab93b272e49e8733
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371914"
---
Začínáme s klientskou knihovnou Automatické návrhy Bingu pro přejít. Pomocí těchto kroků nainstalujte knihovnu a vyzkoušejte si naše příklady pro základní úlohy.

Pomocí klientské knihovny Automatické návrhy Bingu můžete získat návrhy hledání na základě částečných řetězců dotazů.

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)  |  [Vzorový kód](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services).
* Nejnovější verze nástroje [Přejít](https://golang.org/dl/).

Začněte používat klientskou knihovnu Automatické návrhy Bingu vytvořením prostředku Azure. Vyberte typ prostředku, který je pro vás nejvhodnější:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Vytvoření proměnných prostředí

>[!NOTE]
> Koncové body prostředků vytvořených po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../../../cognitive-services-custom-subdomains.md).

Pomocí klíče a koncového bodu z prostředku, který jste vytvořili, vytvořte dvě proměnné prostředí pro ověřování:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: Klíč prostředku pro ověření vašich požadavků.
* `AUTOSUGGEST_ENDPOINT`: Koncový bod prostředku pro odesílání požadavků rozhraní API. Mělo by to vypadat takto: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Použijte pokyny pro váš operační systém.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

### <a name="macos"></a>[macOS](#tab/unix)

Upravte `.bash_profile` a přidejte proměnnou prostředí:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
***

## <a name="create-a-new-go-project"></a>Vytvoření nového projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt přejít a přejděte na něj. Váš pracovní prostor bude obsahovat tři složky:

* **Src** : Tento adresář obsahuje zdrojový kód a balíčky. Všechny balíčky nainstalované s tímto `go get` příkazem budou umístěny zde.
* **pkg** : Tento adresář obsahuje kompilované objekty balíčku pro přechod. Všechny tyto soubory mají `.a` rozšíření.
* **bin** : Tento adresář obsahuje binární spustitelné soubory, které jsou vytvořeny při spuštění `go install` .

> [!TIP]
> Přečtěte si další informace o struktuře [pracovního prostoru přejít](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT` .

Pojďme vytvořit pracovní prostor s názvem `my-app` a požadované podadresáře pro `src` , `pkg` a `bin` :

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro přejít

Teď si nainstalujeme klientskou knihovnu pro přejít:

```bash
$ go get -u <library-location-or-url>
```

nebo pokud používáte DEP, v rámci vašeho úložiště úložišť:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Vytvoření aplikace v cestách

Nyní vytvoříme soubor s názvem `src/sample-app.go` :

```bash
$ cd src
$ touch sample-app.go
```

Otevřete `sample-app.go` , přidejte název balíčku a pak importujte následující knihovny:

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

Vytvořte funkci s názvem `main` . Pak vytvořte proměnné prostředí pro svůj Automatické návrhy Bingu klíč a koncový bod:

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

Tyto ukázky kódu ukazují, jak dokončit základní úlohy pomocí Automatické návrhy Bingu klientské knihovny pro přejít:

* [Ověření klienta](#authenticate-the-client)
* [Odeslat žádost o rozhraní API](#send-an-api-request)

### <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)  pro klíč pro automatický návrh Bingu s názvem `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` a jednu pro váš koncový bod s názvem `BING_AUTOSUGGEST_ENDPOINT` .

Ve `main()` funkci vytvořte instanci klienta s vaším koncovým bodem a klíčem.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>Odeslat žádost o rozhraní API

Ve stejné metodě odešlete dotaz do Bingu pomocí metody [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta. Pak proiterujte odpověď na [návrhy](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) a vytiskněte první návrh.

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

Spusťte aplikaci v příkazu přejít pomocí `go run [arguments]` příkazu z adresáře aplikace.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Odstranění skupiny prostředků v Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Odstranění skupiny prostředků v Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro automatické návrhy Bingu](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Viz také

- [Co jsou Automatické návrhy Bingu?](../../get-suggested-search-terms.md)
- [Referenční materiály rozhraní API pro automatické návrhy Bingu verze 7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)