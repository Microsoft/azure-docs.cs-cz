---
title: Zkoumání rozhraní REST API v aplikaci Fiddler nebo Postman webových služeb HTTP testovací nástroje – Azure Search
description: Způsob použití nástroje Fiddler nebo Postman k vydávání požadavků HTTP a volání rozhraní REST API na službu Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5946ff5e75e7c3fedf42c23200f61e821c0d9d61
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631392"
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Zkoumání rozhraní REST API služby Azure Search pomocí nástroje Fiddler nebo Postman

Jedním z nejjednodušších způsobů zkoumání [rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice) je použít nástroj Fiddler nebo Postman k formulování požadavků HTTP a zkontrolovat odpovědi. S využitím správných nástrojů a pokynů můžete odesílat žádosti a zobrazovat odpovědi, ještě než začnete psát kód.

> [!div class="checklist"]
> * Stažení webového nástroje pro testování rozhraní API
> * Získání klíče api-key a koncového bodu pro vaši vyhledávací službu
> * Konfigurace hlaviček žádosti
> * Vytvoření indexu
> * Načtení indexu
> * Prohledání indexu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete, a pak si [zaregistrujte službu Azure Search](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Stažení a instalace nástrojů

Následující nástroje se běžně používají při vývoji webových stránek, ale pokud znáte jiný nástroj, stále by měly platit pokyny v tomto článku.

+ [Desktopová aplikace Postman](https://www.getpostman.com/)
+ [Telerik Fiddler](https://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Získání klíče api-key a koncového bodu

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Na webu Azure Portal otevřete stránku vyhledávací služby z řídicího panelu nebo [svou službu vyhledejte](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služeb.
2. Získejte koncový bod v části **Přehled** > **Základy** > **Adresa URL**. Příkladem koncového bodu může být `https://my-service-name.search.windows.net`.
3. Získejte klíč api-key v části **Nastavení** > **Klíče**. Pokud byste chtěli převádět klíče, za účelem zajištění redundance existují dva klíče správce. Klíče správce udělují oprávnění k zápisu pro vaši službu, což je nezbytné k vytváření a načítání indexů. Pro operace zápisu můžete použít primární nebo sekundární klíč.

## <a name="configure-request-headers"></a>Konfigurace hlaviček žádosti

Každý nástroj v rámci relace uchovává informace o hlavičce žádosti, což znamená, že stačí zadat koncový bod adresy URL, verzi api-version, klíč api-key a content-type pouze jednou.

Úplná adresa URL by měla vypadat jako v následujícím příkladu, uživatelé by měli pouze nahradit zástupný název **`my-app`** za platnou hodnotu: `https://my-app.search.windows.net/indexes/hotels?api-version=2017-11-11`

Adresa URL služby se skládá z následujících prvků:

+ Předpona HTTPS.
+ Adresa URL služby získaná na webu Azure Portal.
+ Prostředek, což je operace, která vytváří objekt pro vaši službu. V tomto kroku to je index s názvem hotels.
+ Verze api-version, což je vyžadovaný řetězec zadaný malými písmeny jako „?api-version=2017-11-11“ pro aktuální verzi. [Verze rozhraní API](search-api-versions.md) se pravidelně aktualizují. Zahrnutím verze api-version v každé žádosti získáte úplnou kontrolu nad tím, která se použije.  

Hlavička žádosti se skládá ze dvou prvků – typu obsahu a klíče api-key popsaných v předchozí části:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Zformulujte podobnou žádost, která vypadá jako na následujícím snímku obrazovky. Jako operaci zvolte **PUT**. Fiddler přidá `User-Agent=Fiddler`. Pod tím můžete na nové řádky vložit dvě další hlavičky žádosti. Pomocí přístupového klíče správce vaší služby zahrňte typ obsahu a klíč api-key pro vaši službu.

![Hlavička žádosti Fiddleru][1]

> [!Tip]
> Můžete vypnout webový provoz a skrýt tak nadbytečnou aktivitu protokolu HTTP, která nesouvisí s úlohami, které provádíte. Ve Fiddleru přejděte do nabídky **Soubor** a vypněte možnost **Zachycování provozu**. 

### <a name="postman"></a>Postman

Zformulujte podobnou žádost, která vypadá jako na následujícím snímku obrazovky. Jako operaci zvolte **PUT**. 

![Hlavička žádosti Postman][6]

## <a name="create-the-index"></a>Vytvoření indexu

Text žádosti obsahuje definici indexu. Přidáním textu žádosti se dokončí požadavek, který vytvoří váš index.

Nejdůležitějším prvkem žádosti je kromě názvu indexu kolekce polí. Kolekce polí definuje schéma indexu. V každém poli zadejte jeho typ. Pole řetězců se používají ve fulltextovém vyhledávání, takže pokud chcete umožnit prohledávání číselných údajů, přetypujte je na řetězce.

Atributy pole určují povolenou akci. Rozhraní REST API ve výchozím nastavení povolují mnoho akcí. Například všechny řetězce ve výchozím nastavení podporují prohledávání, načítání, filtrování a omezující vlastnosti. Obvykle stačí atributy nastavit pouze v případě, že chcete vypnout určité chování. Další informace o atributech najdete v tématu [Vytvoření indexu (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
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

Pokud se zobrazí kód HTTP 504, ověřte, jestli je v adrese URL určený protokol HTTPS. Pokud se zobrazí kód HTTP 400 nebo 404, zkontrolujte, jestli v textu žádosti nejsou chyby způsobené kopírováním a vkládáním. Kód HTTP 403 obvykle znamená potíže s klíčem rozhraní API (buď je klíč neplatný, nebo je při určení klíče rozhraní API použitá nesprávná syntaxe).

### <a name="fiddler"></a>Fiddler

Zkopírujte definici indexu do textu žádosti podobně jako na následujícím snímku obrazovky a pak klikněte na **Execute** nahoře odešlete dokončenou žádost právo.

![Text žádosti Fiddleru][7]

### <a name="postman"></a>Postman

Zkopírujte definici indexu do textu žádosti podobně jako na následujícím snímku obrazovky a pak klikněte na tlačítko **odeslat** nahoře odešlete dokončenou žádost právo.

![Text žádosti Postman][8]

## <a name="load-documents"></a>Nahrání dokumentů

Vytvoření indexu a jeho naplnění jsou samostatné kroky. Ve službě Azure Search obsahuje index veškerá prohledávatelná data, která můžete zadat jako dokumenty JSON. Rozhraní API pro tuto operaci si můžete prohlédnout v tématu [Přidání, aktualizace a odstranění dokumentů (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

+ Pro účely tohoto kroku změňte operaci na **POST**.
+ Změňte koncový bod tak, aby zahrnoval `/docs/index`. Úplná adresa URL by měla vypadat takto: `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`.
+ Hlavičky žádosti ponechte tak, jak jsou. 

Text žádosti obsahuje čtyři dokumenty, které se mají přidat do indexu hotels.

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

### <a name="fiddler"></a>Fiddler

Změňte operaci na **POST**. Změňte adresu URL tak, aby zahrnovala `/docs/index`. Zkopírujte dokumenty do textu žádosti podobně jako na následujícím snímku obrazovky a pak žádost proveďte.

![Datová část žádosti Fiddleru][9]

### <a name="postman"></a>Postman

Změňte operaci na **POST**. Změňte adresu URL tak, aby zahrnovala `/docs/index`. Zkopírujte dokumenty do textu žádosti podobně jako na následujícím snímku obrazovky a pak žádost proveďte.

![Datová část žádosti Postman][10]

## <a name="query-the-index"></a>Dotazování indexu
Teď, když jsou index a dokumenty nahrané, můžete na ně vydávat dotazy. Další informace o tomto rozhraní API najdete v tématu [Prohledávání dokumentů (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

+ Pro účely tohoto kroku změňte operaci na **GET**.
+ Změňte koncový bod tak, aby zahrnoval parametry dotazu včetně vyhledávacích řetězců. Adresa URL dotazu může vypadat takto: `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`.
+ Hlavičky žádosti ponechte tak, jak jsou.

Tento dotaz vyhledá výraz „motel“ a ve výsledcích hledání vrátí počet dokumentů. Žádost a odpověď by měl vypadat podobně jako na následujícím snímku obrazovky pro nástroj Postman po kliknutí na **odeslat**. Stavový kód by měl být 200.

 ![Odpověď na dotaz v nástroji Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Tipy pro spouštění ukázkových dotazů ve Fiddleru

Následující příklad dotazu je převzatý z článku [Operace prohledání indexu (rozhraní API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Mnoho příkladů dotazů v tomto článku obsahuje mezery, které nejsou ve Fiddleru povolené. Před vložením nahraďte v řetězci dotazu každou mezeru znakem +, abyste mohli dotaz zkusit v aplikaci Fiddler.

**Před nahrazením mezer (v části lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**Po nahrazení mezer znakem + (v části lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

## <a name="query-index-properties"></a>Dotazování vlastností indexu
Můžete také zadat dotazy na informace o systému a získat počet dokumentů a spotřebu úložiště: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

V nástroji Postman by vaše žádost měla vypadat podobně jako v následujícím příkladu a odpověď by měla obsahovat počet dokumentů a využité místo v bajtech.

 ![Dotaz na systém v nástroji Postman][12]

Všimněte si, že syntaxe api-version se liší. Pro tuto žádost použijte k připojení verze api-version znak `?`. Znak ? odděluje cestu URL a řetězec dotazu, zatímco znak & odděluje každý pár název=hodnota v řetězci dotazu. V tomto dotazu je api-version první a také jedinou položkou v řetězci dotazu.

Další informace o tomto rozhraní API najdete v tématu [Získání statistik indexu (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Tipy pro zobrazení statistik indexu ve Fiddleru

Ve Fiddleru klikněte na kartu **Kontroly**, klikněte na kartu **Hlavičky** a vyberte formát JSON. Měli byste vidět počet dokumentů a velikost úložiště (v KB).

## <a name="next-steps"></a>Další postup

Klienti REST jsou neocenitelní při zkoumání bez přípravy, ale teď, když víte, jak fungují rozhraní REST API, můžete pokračovat s kódem. Další kroky najdete na následujících odkazech:

+ [Vytvoření indexu (REST)](search-create-index-rest-api.md)
+ [Import dat (REST)](search-import-data-rest-api.md)
+ [Prohledávání indexu (REST)](search-query-rest-api.md)

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