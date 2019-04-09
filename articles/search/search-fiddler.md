---
title: 'Rychlý start: Zkoumání rozhraní REST API v nástroji Postman – Azure Search'
description: Jak používat Postman k vydávání požadavků HTTP a rozhraní REST API volá do služby Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0e14131ce45d20b99c1b5d5885cb1eb24c975d03
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269108"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>Rychlý start: Prozkoumejte službu REST API služby Azure Search pomocí nástroje Postman
> [!div class="op_single_selector"]
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Jeden z nejjednodušších způsobů zkoumání [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice) využívá Postman nebo jiný webový nástroj pro testování k formulování požadavků HTTP a zkontrolovat odpovědi. S využitím správných nástrojů a pokynů můžete odesílat žádosti a zobrazovat odpovědi, ještě než začnete psát kód.

> [!div class="checklist"]
> * Stažení webového nástroje pro testování rozhraní API
> * Získejte klíč a adresa URL pro vaši vyhledávací službu
> * Připojení ke službě Azure Search
> * Vytvoření indexu
> * Načtení indexu
> * Prohledání indexu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete, a pak si [zaregistrujte službu Azure Search](search-create-service-portal.md).

## <a name="prerequisites"></a>Požadavky

Tyto služby a nástroje se používají v tomto rychlém startu. 

[Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službou pro tento rychlý start. 

[Desktopová aplikace postman](https://www.getpostman.com/) nebo [Telerik Fiddler](https://www.telerik.com/fiddler) slouží k odesílání žádostí do služby Azure Search.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-search"></a>Připojení ke službě Azure Search

V této části použijte nástroj web podle výběru nastavení připojení do služby Azure Search. Každý nástroj uchovává informace o této relaci, což znamená, že budete muset zadat klíč api-key a Content-Type jednou hlavičce žádosti.

Pro kteréhokoli z těchto nástrojů můžete potřebovat zvolte příkazu (GET, POST, PUT a tak dále), zadejte koncový bod adresy URL a některé úlohy, poskytují JSON v textu požadavku. Úplná adresa URL vypadá nějak takto:

    https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11

Všimněte si, že předpona HTTPS, název služby, název objektu (v tomto případě kolekci indexů) a [verze api-version](search-api-versions.md). Verze api-version je povinný, malá písmena řetězec zadán jako "? api-version = 2017-11-11" pro aktuální verzi. Verze rozhraní API jsou pravidelně aktualizovány. Zahrnutím verze api-version v každé žádosti získáte úplnou kontrolu nad tím, která se použije.  

Složení záhlaví požadavku obsahuje dva prvky, typu obsahu a klíč rozhraní api používá k ověření do služby Azure Search:

    api-key: <placeholder-api-key-for-your-service>
    Content-Type: application/json

V nástroji Postman by zformulujte podobnou žádost, která vypadá jako na následujícím snímku obrazovky. Zvolte **získat** jako příkaz, zadejte adresu URL a klikněte na tlačítko **odeslat**. Tento příkaz se připojí k Azure Search, načte kolekci indexů a vrátí stavový kód HTTP 200 úspěšném připojení. Pokud vaše služba již má indexy, odpověď bude také obsahovat definice indexu.

![Hlavička žádosti Postman][6]

## <a name="1---create-an-index"></a>1. Vytvoření indexu

Ve službě Azure Search obvykle vytvořit index před načtením s daty. [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) slouží rozhraní REST API pro tuto úlohu. 

Adresa URL je rozšířit `hotel` název indexu.

Chcete-li to provést v nástroji Postman:

1. Změňte operaci na **PUT**
2. Kopírovat tuto adresu URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11`
3. Zadejte definici indexu (viz dole) v textu požadavku
4. Klikněte na tlačítko **odeslat**

![Text žádosti Postman][8]

### <a name="index-definition"></a>Definice indexu

Kolekce polí definuje strukturu dokumentu. Každý dokument musí mít tato pole a každý pole musí mít datový typ. Pole řetězců se používají ve fulltextovém vyhledávání, takže pokud chcete umožnit prohledávání číselných údajů, přetypujte je na řetězce.

Atributy pole určují povolenou akci. Rozhraní REST API ve výchozím nastavení povolují mnoho akcí. Například všechny řetězce ve výchozím nastavení podporují prohledávání, načítání, filtrování a omezující vlastnosti. Často stačí nastavit atributy, pokud potřebujete vypnout chování.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

Po odeslání této žádosti byste měli získat odpověď HTTP 201, která značí úspěšné vytvoření indexu. Tuto akci můžete ověřit na portálu, ale nezapomeňte, že stránka portálu má intervaly obnovení, takže aktualizace může pár minut trvat.

> [!TIP]
> Pokud se zobrazí kód HTTP 504, ověřte, jestli je v adrese URL určený protokol HTTPS. Pokud se zobrazí kód HTTP 400 nebo 404, zkontrolujte, jestli v textu žádosti nejsou chyby způsobené kopírováním a vkládáním. Kód HTTP 403 obvykle znamená potíže s klíčem rozhraní API (buď je klíč neplatný, nebo je při určení klíče rozhraní API použitá nesprávná syntaxe).

## <a name="2---load-documents"></a>2 – nahrání dokumentů

Vytvoření indexu a jeho naplnění jsou samostatné kroky. Ve službě Azure Search obsahuje index veškerá prohledávatelná data, která můžete zadat jako dokumenty JSON. [Přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) slouží rozhraní REST API pro tuto úlohu. 

Adresa URL je rozšířit `docs` kolekce a `index` operace.

Chcete-li to provést v nástroji Postman:

1. Změňte operaci na **příspěvku**
2. Kopírovat tuto adresu URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`
3. Zadejte dokumentů JSON (viz dole) v textu požadavku
4. Klikněte na tlačítko **odeslat**

![Datová část žádosti Postman][10]

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON do indexu načíst

Text žádosti obsahuje čtyři dokumenty, které se mají přidat do indexu hotels.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
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
             "description_fr": "Hôtel le moins cher en ville",
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
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Během pár sekund by se v seznamu relace měla zobrazit odpověď HTTP 200. To znamená, že se dokumenty úspěšně vytvořily. 

Pokud se zobrazí kód 207, nejméně jeden dokument nešel nahrát. Pokud se zobrazí kód 404, hlavička nebo text žádosti obsahuje syntaktickou chybu. Ověřte, že jste změnili koncový bod tak, aby zahrnoval `/docs/index`.

> [!Tip]
> Pro vybrané zdroje dat můžete zvolit přístup s použitím alternativního *indexeru*, který zjednodušuje a snižuje množství kódu vyžadovaného pro indexování. Další informace najdete v tématu [Operace indexeru](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3. Prohledání indexu

Teď, když se načtou index a dokumenty, můžete posílat dotazy proti nim pomocí [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents) rozhraní REST API.

Adresa URL je rozšířen o řetězec dotazu zadaný pomocí operátoru vyhledávání.

Chcete-li to provést v nástroji Postman:

+ Změňte operaci na **získat**
+ Kopírovat tuto adresu URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ Klikněte na tlačítko **odeslat**

Tento dotaz vyhledá výraz „motel“ a ve výsledcích hledání vrátí počet dokumentů. Žádost a odpověď by měl vypadat podobně jako na následujícím snímku obrazovky pro nástroj Postman po kliknutí na **odeslat**. Stavový kód by měl být 200.

 ![Odpověď na dotaz v nástroji Postman][11]


## <a name="get-index-properties"></a>Získání vlastností indexu
Můžete také zadávat dotazy systémové informace můžete získat počet dokumentů a spotřebu úložiště: `https://mydemo.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

V nástroji Postman by vaše žádost měla vypadat podobně jako v následujícím příkladu a odpověď by měla obsahovat počet dokumentů a využité místo v bajtech.

 ![Dotaz na systém v nástroji Postman][12]

Všimněte si, že syntaxe api-version se liší. Pro tuto žádost použijte k připojení verze api-version znak `?`. `?` Odděluje cestu URL a z řetězce dotazu, zatímco znak & odděluje každý ' název = hodnota "pár v řetězci dotazu. V tomto dotazu je api-version první a také jedinou položkou v řetězci dotazu.

Další informace o tomto rozhraní API najdete v tématu [Získání statistik indexu (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


## <a name="use-fiddler"></a>Použití aplikace Fiddler

Tato část je ekvivalentní předchozí části, pouze pomocí Fiddleru snímky obrazovky a pokyny

### <a name="connect-to-azure-search"></a>Připojení ke službě Azure Search

Zformulujte podobnou žádost, která vypadá jako na následujícím snímku obrazovky. Zvolte **získat** jako příkaz. Fiddler přidá `User-Agent=Fiddler`. Pod tím můžete na nové řádky vložit dvě další hlavičky žádosti. Pomocí přístupového klíče správce vaší služby zahrňte typ obsahu a klíč api-key pro vaši službu.

Pro cíl zkopírujte v upravenou verzi tuto adresu URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11`

![Hlavička žádosti Fiddleru][1]

> [!Tip]
> Vypněte webový provoz a skrýt nadbytečné, nesouvisejících aktivitu protokolu HTTP. V aplikaci Fiddler od **souboru** nabídky, vypněte **zachycování provozu**. 

### <a name="1---create-an-index"></a>1. Vytvoření indexu

Změňte operaci na **UMÍSTIT**. Kopírují se upravenou verzi tuto adresu URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11`. Zkopírujte definici indexu do textu žádosti uvedené výše. Vaše stránka by měla vypadat podobně jako na následujícím snímku obrazovky. Klikněte na tlačítko **Execute** nahoře vpravo odešlete dokončenou žádost.

![Text žádosti Fiddleru][7]

### <a name="2---load-documents"></a>2 – nahrání dokumentů

Změňte operaci na **POST**. Změňte adresu URL tak, aby zahrnovala `/docs/index`. Zkopírujte dokumenty do textu žádosti podobně jako na následujícím snímku obrazovky a pak žádost proveďte.

![Datová část žádosti Fiddleru][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Tipy pro spouštění ukázkových dotazů ve Fiddleru

Následující příklad dotazu je převzatý z článku [Operace prohledání indexu (rozhraní API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Mnoho příkladů dotazů v tomto článku obsahuje mezery, které nejsou ve Fiddleru povolené. Před vložením nahraďte v řetězci dotazu každou mezeru znakem +, abyste mohli dotaz zkusit v aplikaci Fiddler.

**Před nahrazením mezer (v části lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**Po nahrazení mezer znakem + (v části lastRenovationDate + desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Tipy pro zobrazení statistik indexu ve Fiddleru

Ve Fiddleru klikněte na kartu **Kontroly**, klikněte na kartu **Hlavičky** a vyberte formát JSON. Měli byste vidět počet dokumentů a velikost úložiště (v KB).

## <a name="next-steps"></a>Další postup

Klienti REST jsou neocenitelní při zkoumání bez přípravy, ale teď, když víte, jak fungují rozhraní REST API, můžete pokračovat s kódem. Další kroky najdete na následujících odkazech:

+ [Rychlý start: Vytvoření indexu pomocí .NET SDK](search-create-index-dotnet.md)
+ [Rychlý start: Vytvoření indexu (REST) pomocí Powershellu](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png