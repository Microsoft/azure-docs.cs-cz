---
title: 'Rychlý start: Vytvoření, načtení a dotazování indexu pomocí prostředí PowerShell a rozhraní REST API – Azure Search'
description: Vytvoření, načtení a dotazování indexu pomocí Powershellu Invoke-RestMethod a rozhraní REST API Azure Search.
ms.date: 04/08/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 2deba4bf941d561fcef7c2dff804646732e7ce24
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268020"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Rychlý start: Vytvoření indexu Azure Search pomocí prostředí PowerShell a rozhraní REST API
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Azure Portal](search-create-index-portal.md)
> 

Tento článek vás provede procesem vytvoření, načtení a dotazování Azure Search [index](search-what-is-an-index.md) pomocí Powershellu a [rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Definice indexu a prohledávatelný obsah je k dispozici v textu žádosti jako obsah ve správném formátu JSON.

## <a name="prerequisites"></a>Požadavky

Tyto služby a nástroje se používají v tomto rychlém startu. 

[Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službou pro tento rychlý start. 

[Prostředí PowerShell 5.1 nebo novější](https://github.com/PowerShell/PowerShell)s použitím [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) pro sekvenční a interaktivní kroky.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-search"></a>Připojení ke službě Azure Search

V prostředí PowerShell, vytvořte **$headers** objekt pro uložení typu obsahu a klíč rozhraní API. Budete muset nastavit toto záhlaví jednou po dobu trvání relace, ale které přidáte do každého požadavku. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Vytvoření **$url** objekt, který určuje služby indexování kolekce. `mydemo` Název služby je určen jako zástupný symbol. Nahraďte ho platný vyhledávací službu v aktuálním předplatném v tomto příkladu.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

Spustit **Invoke-RestMethod** odeslat požadavek GET na službu a ověření připojení. Přidat **ConvertTo-Json** tak, abyste mohli zobrazit odpovědi odeslané ze služby.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Pokud služba je prázdný a nemá žádné indexy, výsledky jsou podobné následujícímu příkladu. V opačném případě uvidíte JSON s reprezentací provedených definic indexu.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
    "value":  [

              ]
}
```

## <a name="1---create-an-index"></a>1. Vytvoření indexu

Pokud používáte portál, index, musí existovat ve službě můžete načíst data. Tento krok definuje index a nasdílí změny do služby. [Vytvoření indexu (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) se používá pro tento krok.

Požadované elementy indexu patří název a kolekci polí. Kolekce polí definuje strukturu *dokumentu*. Každé pole má název, typ a atributy, které určují, jak se používá (například, zda je fulltextově prohledávatelné, filtrovatelné nebo retrievable ve výsledcích hledání). V rámci indexu, jeden z pole typu `Edm.String` musí být určena jako *klíč* pro identitu dokumentu.

Tento index je s názvem "hotels" a obsahuje definice pole, které vidíte níže. Určuje definici indexu [analyzátor jazyka](index-add-language-analyzers.md) pro `description_fr` pole, protože je určené k ukládání francouzského textu, které přidáme v pozdější příkladu.

Vložte tento příklad do prostředí PowerShell k vytvoření **$body** objekt, který obsahuje schéma indexu.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Nastavte identifikátor URI kolekce indexů pro vaši službu a *hotels* indexu.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

Spuštění příkazu s **$url**, **$headers**, a **$body** pro vytvoření indexu ve službě. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Výsledky by měly vypadat podobně jako tento (zkrácen na první dvě pole pro zkrácení):

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> Pro ověření, můžete také zkontrolovat seznam indexů na portálu, nebo znovu spusťte příkaz používá k ověření připojení ke službě zobrazíte *hotels* indexu uvedené v kolekci indexů.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – nahrání dokumentů

Pro vkládání dokumentů, pomocí požadavku HTTP POST do koncového bodu adresy URL vašeho indexu. Rozhraní REST API pro tuto úlohu je [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Vložte tento příklad do prostředí PowerShell k vytvoření **$body** objekt obsahující dokumenty, které chcete nahrát. 

Tato žádost obsahuje dvě úplné a částečné jeden záznam. Částečné záznam ukazuje, že můžou nahrávat dokumenty, neúplné. `@search.action` Parametr určuje, jak se dělá indexování. Platné hodnoty jsou odeslání, sloučení, mergeOrUpload a delete. Chování mergeOrUpload buď vytvoří nový dokument pro hotelId = 3, nebo aktualizuje obsah, pokud již existuje.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

Nastavte na koncový bod *hotels* dokumentace kolekce a zahrnují operace s indexem (indexy/hotels/docs/index).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

Spuštění příkazu s **$url**, **$headers**, a **$body** načtení dokumentů do indexu hotels.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Výsledky by měly vypadat podobně jako v následujícím příkladu. Měli byste vidět stavový kód 201. Popis všech stavových kódů, naleznete v tématu [stavové kódy HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
    "value":  [
                  {
                      "key":  "1",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "2",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "3",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3. Prohledání indexu

Tento krok ukazuje, jak zadávat dotazy na index pomocí [rozhraní API pro vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Nastavit koncový bod na *hotels* dokumentace kolekce a přidejte **hledání** parametr řetězce dotazu zahrnout. Tento řetězec znamená prázdné vyhledávání a vrátí unranked seznam všechny dokumenty.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

Spusťte příkaz k odeslání **$url** ke službě.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Výsledky by měly vypadat podobně jako následující výstup.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Zkuste několik další příklady dotazů syntaxe získat představu. Můžete řetězec hledání, dotazy verbatim $filter, omezit sadu výsledků, obor hledání na konkrétní pole a další.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Vyčištění 

Index by měl odstranit, pokud ho už nepotřebují. Bezplatná služba je omezena na tři indexy. Můžete chtít odstranit všechny indexy, které nepoužíváte aktivně tak, že můžete pokračovat dalšími tématy.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Další postup

Zkuste přidat francouzské popisy do indexu. Následující příklad obsahuje francouzské řetězce a ukazuje další vyhledávací akce. Pomocí mergeOrUpload můžete vytvořit nebo přidat do existujících polí. Následující řetězce musí být UTF-8.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
