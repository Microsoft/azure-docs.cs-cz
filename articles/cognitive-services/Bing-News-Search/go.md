---
title: 'Rychlý Start: získání zpráv pomocí Vyhledávání zpráv Bingu REST API a přejít'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se k volání rozhraní API Bingu pro vyhledávání zpráv používá jazyk přejít. Výsledky zahrnují názvy a adresy URL zdrojů zpráv identifikovaných řetězcem dotazu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: aaeb905c9cdc1e7b74e21d3c191f6a24a94fcd7d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80053810"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Rychlý Start: získání výsledků zpráv pomocí Vyhledávání zpráv Bingu REST API a přejít

V tomto rychlém startu se k volání rozhraní API Bingu pro vyhledávání zpráv používá jazyk přejít. Výsledky zahrnují názvy a adresy URL zdrojů zpráv identifikovaných řetězcem dotazu.

## <a name="prerequisites"></a>Požadavky
* Instalace [binárních souborů přejít](https://golang.org/dl/)
* Pokud chcete zobrazit výsledky, nainstalujte knihovnu Spew pro IT, která je v podstatě.
    * Nainstalujte tuto knihovnu:`$ go get -u https://github.com/davecgh/go-spew`

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-a-project-and-import-libraries"></a>Vytvoření projektu a import knihoven

Vytvořte nový projekt přejít v integrovaném vývojovém prostředí nebo editoru. Pak importujte `net/http` požadavky, `ioutil` Přečtěte si odpověď a `encoding/json` zpracujte text JSON výsledků. Knihovna Spew je nutná k analýze formátu JSON. 

```go
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>Vytvoření struktury pro formátování výsledků hledání zpráv

Struktura `NewsAnswer` naformátuje data poskytnutá v odpovědi. Odpověď JSON je víceúrovňové a poměrně složitá.  Následující implementace pokrývá základy.

```go
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            } `json: "image"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Deklarace hlavní funkce a definování proměnných  

Následující kód deklaruje funkci main a přiřadí požadované proměnné. Ověřte správnost koncového bodu a nahraďte hodnotu `token` platným klíčem předplatného ze svého účtu Azure. Můžete použít globální koncový bod nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

```go
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>Dotaz a záhlaví

Přidat řetězec dotazu a přístup k záhlaví klíče

```go
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>Získat žádost

Vytvořte klienta a odešlete žádost o získání. 

```go
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>Odeslat žádost

Odešlete žádost a čtěte výsledky pomocí `ioutil`.

```go
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>Zpracování odpovědi

`Unmarshall` Funkce extrahuje informace z textu JSON VRÁCENého rozhraním vyhledávání zpráv API.  Pak můžete zobrazit uzly z výsledků pomocí tiskárny v `go-spew` podstatě.

```go
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>Výsledky

Výsledky obsahují název a adresu URL každého výsledku.

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je Vyhledávání zpráv Bingu](search-the-web.md)
