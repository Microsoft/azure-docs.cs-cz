---
title: 'Rychlý Start: Automatické návrhy Bingu klientskou knihovnu pro cestách | Microsoft Docs'
description: Začínáme s klientskou knihovnou Automatické návrhy Bingu pro přejít a získat návrhy hledání na základě částečných řetězců dotazů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e826d69bbf0f94d02799e9b93b26db8d4cbbba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474085"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Rychlý Start: Automatické návrhy Bingu klientskou knihovnu pro přejít

Začínáme s klientskou knihovnou Automatické návrhy Bingu pro přejít. Pomocí těchto kroků nainstalujte knihovnu a vyzkoušejte si naše příklady pro základní úlohy. 

Pomocí klientské knihovny Automatické návrhy Bingu můžete získat návrhy hledání na základě částečných řetězců dotazů.

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [ukázkový kód](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/) .
* Nejnovější verze nástroje [Přejít](https://golang.org/dl/)

## <a name="setting-up"></a>Nastavení

### <a name="create-an-azure-resource"></a>Vytvoření prostředku Azure 

Začněte používat klientskou knihovnu Automatické návrhy Bingu vytvořením prostředku Azure. Vyberte typ prostředku, který je pro vás nejvhodnější:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

>[!NOTE]
> Koncové body pro prostředky nevyužívající zkušební verzi vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Pomocí klíče a koncového bodu z prostředku, který jste vytvořili, vytvořte dvě proměnné prostředí pro ověřování:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY` – klíč prostředku pro ověřování vašich požadavků.
* `AUTOSUGGEST_ENDPOINT` – koncový bod prostředku pro odesílání požadavků rozhraní API. Bude vypadat takto: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Použijte pokyny pro váš operační systém.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změna projevila.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte `.bash_profile`a přidejte proměnnou prostředí:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změna projevila.
***

### <a name="create-a-new-go-project"></a>Vytvoření nového projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt přejít a přejděte na něj. Váš pracovní prostor bude obsahovat tři složky: 

* **Src** – tento adresář obsahuje zdrojový kód a balíčky. Všechny balíčky nainstalované pomocí příkazu `go get` se tady nacházejí.
* **pkg** – tento adresář obsahuje kompilované objekty balíčku pro přechod. Všechny tyto soubory mají rozšíření `.a`.
* **bin** – tento adresář obsahuje binární spustitelné soubory, které se vytvoří při spuštění `go install`.

> [!TIP]
> Přečtěte si další informace o struktuře [pracovního prostoru přejít](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT`.

Pojďme vytvořit pracovní prostor s názvem `my-app` a požadované podadresáře pro `src`, `pkg`a `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro přejít

Teď si nainstalujeme klientskou knihovnu pro přejít: 

```bash
$ go get -u <library-location-or-url>
```

nebo pokud používáte DEP, v rámci vašeho úložiště úložišť:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Vytvoření aplikace v cestách

Nyní vytvoříme soubor s názvem `src/sample-app.go`:

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

Vytvořte funkci s názvem `main`. Pak vytvořte proměnné prostředí pro svůj Automatické návrhy Bingu klíč a koncový bod.

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

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE] 
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč pro automatické návrhy Bingu s názvem `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`a druhý pro koncový bod s názvem `BING_AUTOSUGGEST_ENDPOINT`.

Ve funkci `main()` vytvořte instanci klienta s vaším koncovým bodem a klíčem. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Odeslat žádost o rozhraní API

Ve stejné metodě odešlete dotaz do Bingu pomocí metody [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta. Pak proiterujte odpověď na [návrhy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) a vytiskněte první návrh.

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

Spusťte aplikaci v jazyce přejít pomocí příkazu `go run [arguments]` z adresáře aplikace.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro automatické návrhy Bingu](../tutorials/autosuggest.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Co jsou automatické návrhy Bingu?](../get-suggested-search-terms.md)
- [Referenční materiály rozhraní API pro automatické návrhy Bingu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
