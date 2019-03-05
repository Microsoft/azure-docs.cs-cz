---
title: 'Rychlý start: Dostávat informace o použití rozhraní API REST vyhledávání zpráv Bingu a Go'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak získat výsledky zpráv z rozhraní API pro vyhledávání zpráv Bingu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: rosh
ms.openlocfilehash: 6395b928913b9ea9ada83885cfd3a065619f0a57
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318563"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Rychlý start: Získat výsledky zpráv pomocí rozhraní API REST vyhledávání zpráv Bingu a Go

V tomto rychlém startu pomocí jazyka Go zavolá rozhraní API pro vyhledávání zpráv Bingu. Výsledky zahrnují názvy a adresy URL zprávy zdrojů identifikované pomocí řetězce dotazu.

## <a name="prerequisites"></a>Požadavky
* Nainstalujte [přejít binárních souborů](https://golang.org/dl/)
* Nainstalujte knihovnu go výstupu pro něj přehlednou tiskárny pro zobrazení výsledků
    * Nainstalujte tento libarary: `$ go get -u https://github.com/davecgh/go-spew`

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-libraries"></a>Vytvoření projektu a importovat knihovny

Vytvoření nového projektu přejít v prostředí IDE nebo editoru. Importujte `net/http` požadavkům, `ioutil` přečíst odpověď, a `encoding/json` pro zpracování textu JSON výsledků. Knihovna go výstupu je potřeba k parsování formátu JSON. 

```
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>Vytvoření struktury formátovat výsledky hledání zpráv

Struktura `NewsAnswer` naformátuje data poskytnutá v odpovědi. Odpověď JSON je poměrně složité a více změn.  Následující implementaci pokrývá základy.

```
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
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
        } `json: "image"` 
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Deklarace hlavní funkce a definování proměnných  

Následující kód deklaruje funkci main a přiřadí požadované proměnné. Ověřte správnost koncového bodu a nahraďte hodnotu `token` platným klíčem předplatného ze svého účtu Azure.

```
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

## <a name="query-and-header"></a>Query a header

Přidat dotaz řetězec a přístup klíče záhlaví

```
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>Žádost o získání

Vytvoření klienta a odešlete požadavek Get. 

```
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>Odeslání požadavku

Odeslat požadavek a čtení výsledků za použití `ioutil`.

```
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
resbody, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>Zpracování odpovědi

`Unmarshall` Funkce extrahuje informace z textu JSON vrácené rozhraní API pro vyhledávání zpráv.  Pak můžete zobrazit uzly ve výsledcích pomocí `go-spew` přehlednou tiskárny.

```
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
(string) (len=115) "http://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Co je pro vyhledávání zpráv Bingu](search-the-web.md)
