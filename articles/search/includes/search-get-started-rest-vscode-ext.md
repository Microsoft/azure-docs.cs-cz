---
title: zahrnout soubor
description: zahrnout soubor
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: include
ms.custom: include file
ms.date: 11/17/2020
ms.openlocfilehash: c84a7291e342a1acc465732cfbc350c571bba74d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714155"
---
Tento článek používá pro rozhraní REST API služby Azure Kognitivní hledání rozšíření pro Visual Studio Code (Preview).

> [!IMPORTANT] 
> Tato funkce je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Předpoklady

V tomto rychlém startu jsou vyžadovány následující služby a nástroje. 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [Azure Kognitivní hledání pro Visual Studio Code (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ [Vytvořte službu Azure kognitivní hledání](../search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

## <a name="copy-a-key-and-url"></a>Zkopírování klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu HTTP a přístupového klíče](../media/search-get-started-rest/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="install-the-extension"></a>Instalace rozšíření

Začněte tím, že otevřete [vs Code](https://code.visualstudio.com). Na řádku aktivity vyberte kartu **rozšíření** a pak vyhledejte *Azure kognitivní hledání*. Ve výsledcích hledání Najděte rozšíření a vyberte **nainstalovat**.

![Podokno rozšíření VS Code](../media/search-get-started-rest/download-extension.png "Stahuje se rozšíření VS Code.")

Alternativně můžete [rozšíření Azure kognitivní hledání](https://aka.ms/vscode-search) nainstalovat z webu vs Code Marketplace ve webovém prohlížeči.

Na řádku aktivity by se měla zobrazit nová karta Azure, pokud jste ji ještě nevytvořili.

![VS Code podokno Azure](../media/search-get-started-rest/azure-pane.png "Podokno Azure v VS Code")

## <a name="connect-to-your-subscription"></a>Připojení k vašemu předplatnému

Vyberte **Přihlásit se k Azure...** a přihlaste se k účtu Azure.

Měli byste vidět, že se zobrazí vaše předplatná. Vyberte předplatné, ve kterém se zobrazí seznam vyhledávacích služeb v rámci předplatného.

![VS Code předplatných Azure](../media/search-get-started-rest/subscriptions.png "Předplatná v VS Code")

Pokud chcete omezit zobrazené předplatné, otevřete paletu příkazů (CTRL + SHIFT + P nebo Cmd + Shift + P) a vyhledejte *Azure* nebo *Vyberte předplatná*. K dispozici jsou také příkazy k přihlášení a odhlášení z účtu Azure.

Když rozbalíte vyhledávací službu, zobrazí se položky stromu pro každý z Kognitivní hledáních prostředků: indexy, zdroje dat, indexery, dovednosti a mapování synonym.

![VS Code stromu Azure Search](../media/search-get-started-rest/search-tree.png "VS Code stromu Azure Search")

Tyto položky stromu se dají rozšířit tak, aby se zobrazily všechny prostředky ve vyhledávací službě.

## <a name="1---create-an-index"></a>1. Vytvoření indexu

Pokud chcete začít s Azure Kognitivní hledání, musíte nejprve vytvořit vyhledávací index. K tomu je potřeba použít [REST API vytvoření indexu](/rest/api/searchservice/create-index). 

S rozšířením VS Code se musíte starat jenom o tělo žádosti. V tomto rychlém startu poskytujeme definici vzorového indexu a odpovídající dokumenty.

### <a name="index-definition"></a>Definice indexu

Níže uvedená definice indexu je ukázkové schéma pro fiktivní hotely.

`fields`Kolekce definuje strukturu dokumentů v indexu vyhledávání. Každé pole má datový typ a řadu dalších atributů, které určují, jak lze pole použít.

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Nový index vytvoříte tak, že kliknete pravým tlačítkem na **indexy** a pak vyberete **vytvořit nový index**. Editor s názvem, který se podobá, se `indexes-new-28c972f661.azsindex` zobrazí jako překryvný. 

Vložte definici indexu z výše uvedeného do okna. Uložte soubor a po zobrazení výzvy klikněte na **Odeslat** , pokud chcete rejstřík aktualizovat. Tím se vytvoří index, který bude k dispozici ve stromovém zobrazení.

![Obrázek GIF při vytváření indexu](../media/search-get-started-rest/create-index.gif)

Pokud dojde k potížím s definicí indexu, měla by se zobrazit chybová zpráva s vysvětlením chyby.

![Chybová zpráva pro vytvoření indexu](../media/search-get-started-rest/create-index-error.png)

Pokud k tomu dojde, vyřešte problém a znovu soubor uložte.

## <a name="2---load-documents"></a>2. načtení dokumentů

Vytvoření indexu a jeho naplnění jsou samostatné kroky. V Azure Kognitivní hledání index obsahuje všechna hledaná data. V tomto scénáři se data poskytují jako dokumenty JSON. Pro tento úkol se používá [REST API přidat, aktualizovat nebo odstranit dokumenty](/rest/api/searchservice/addupdate-or-delete-documents) . 

Přidání nových dokumentů v VS Code:

1. Rozbalte `hotels-quickstart` index, který jste vytvořili. Klikněte pravým tlačítkem na **dokumenty** a vyberte **vytvořit nový dokument**.

    ![Vytvoření dokumentu](../media/search-get-started-rest/create-document.png)

2. Otevře se Editor JSON, který odvodil schéma indexu.

    ![Vytvoření formátu JSON dokumentu](../media/search-get-started-rest/create-document-2.png)

3. Níže vložte následující kód JSON a uložte soubor. Otevře se výzva k potvrzení změn. Kliknutím na **Odeslat** uložte změny.

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. Tento postup opakujte pro tři zbývající dokumenty.

    Dokument 2:
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    Dokument 3:
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    Dokument 4:
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
        }
    }
    ```

V tomto okamžiku byste měli vidět všechny čtyři dokumenty, které jsou k dispozici v části dokumenty.

![stav po nahrání všech dokumentů](../media/search-get-started-rest/create-document-finish.png)

## <a name="3---search-an-index"></a>3. Prohledání indexu

Teď, když se načte index a sada dokumentů, můžete pro ně vystavovat dotazy pomocí [hledání dokumentů REST API](/rest/api/searchservice/search-documents).

Postup v VS Code:

1. Klikněte pravým tlačítkem na index, který chcete vyhledat, a vyberte **Hledat index**. Tím se otevře Editor s názvem podobným `sandbox-b946dcda48.azs` .

    ![zobrazení vyhledávání rozšíření](../media/search-get-started-rest/search-vscode.png)

2. Jednoduchý dotaz se vyplní automaticky. Dotaz odešlete stisknutím **kombinace kláves CTRL + ALT + r** nebo **cmd + ALT + r** . Zobrazí se okno výsledky v okně vlevo.

    ![výsledky hledání v rozšíření](../media/search-get-started-rest/search-results.png)


### <a name="example-queries"></a>Ukázky dotazů

Vyzkoušejte si několik dalších příkladů dotazů, které vám pomohou s syntaxí. Níže jsou uvedené čtyři další dotazy, které můžete vyzkoušet. Do stejného editoru můžete přidat více dotazů. Po stisknutí **kombinace kláves CTRL + ALT + r** nebo **cmd + ALT + r** se ukazatel myši rozhodne, který dotaz bude odeslán.

![dotazování a výsledky vedle sebe](../media/search-get-started-rest/all-searches.png)

V prvním dotazu hledáme `boutique` a `select` jenom určitá pole. Osvědčeným postupem jsou pouze ta `select` pole, která potřebujete, protože navrácení zbytečných dat může do dotazů přidat latenci. Dotaz také nastaví `$count=true` , aby vrátil celkový počet výsledků s výsledky hledání.

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

V dalším dotazu určíme hledaný termín `wifi` a také zahrnete filtr, který vrátí jenom výsledky, u kterých je stav roven `'FL'` . Výsledky jsou také seřazené podle hotelu `Rating` .

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

V dalším kroku je hledání omezené na jediné prohledávatelné pole pomocí `searchFields` parametru. Je to skvělý způsob, jak dotaz zefektivnit, pokud víte, že máte jenom shody v určitých polích.

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

Další běžnou možností pro zahrnutí do dotazu je `facets` . Omezující vlastnosti umožňují vytvářet filtry na uživatelském rozhraní, které uživatelům usnadňují znalost hodnot, na které mohou filtrovat.

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## <a name="open-index-in-the-portal"></a>Otevřít index na portálu

Pokud chcete vyhledávací službu zobrazit na portálu, klikněte pravým tlačítkem na název vyhledávací služby a vyberte **otevřít na portálu**. Tím přejdete do služby Search v Azure Portal.