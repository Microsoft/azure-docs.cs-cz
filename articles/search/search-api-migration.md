---
title: Upgrade verzí rozhraní REST API
titleSuffix: Azure Cognitive Search
description: Zkontrolujte rozdíly ve verzích rozhraní API a zjistěte, které akce jsou nutné k migraci existujícího kódu do nejnovější verze rozhraní REST API služby Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112172"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Upgrade na nejnovější verzi rozhraní REST API služby Azure Cognitive Search

Pokud používáte předchozí verzi [rozhraní REST API vyhledávání](https://docs.microsoft.com/rest/api/searchservice/), tento článek vám pomůže upgradovat aplikaci tak, aby používala nejnovější obecně dostupnou verzi rozhraní API 2019-05-06.

Verze 2019-05-06 rozhraní REST API obsahuje některé změny z předchozích verzí. Ty jsou většinou zpětně kompatibilní, takže změna kódu by měla vyžadovat pouze minimální úsilí, v závislosti na verzi, kterou jste používali dříve. [Postup upgradu](#UpgradeSteps) popisuje změny kódu potřebné pro používání nových funkcí.

> [!NOTE]
> Instance služby Azure Cognitive Search podporuje řadu verzí rozhraní REST API, včetně dřívějších verzí. Můžete pokračovat v používání těchto verzí rozhraní API, ale doporučujeme migraci kódu na nejnovější verzi, abyste měli přístup k novým funkcím.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Co je nového ve verzi 2019-05-06
Verze 2019-05-06 je nejnovější obecně dostupná verze rozhraní REST API. Mezi funkce, které byly převedeny na obecně dostupný stav v této verzi rozhraní API, patří:

* [Automatické dokončování](index-add-suggesters.md) je funkce dopředného psaní, která doplňuje částečně zadaný vstup termínu.

* [Komplexní typy](search-howto-complex-data-types.md) poskytují nativní podporu pro data strukturovaných objektů v indexu vyhledávání.

* [Režimy analýzy JsonLines](search-howto-index-json-blobs.md), součást indexování objektů Blob Azure, vytvoří jeden vyhledávací dokument na entitu JSON, která je oddělena novým řádkem.

* [Obohacení umělou a i.](cognitive-search-concept-intro.md) poskytuje indexování, které využívá moduly obohacení ai cognitive services.

S touto obecně dostupnou aktualizací se shoduje několik verzí funkcí preview. Seznam nových funkcí náhledu najdete v [tématu Hledání rozhraní API REST verze 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Změny způsobující chyby

Existující kód obsahující následující funkce se přeruší na api-version=2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer pro Azure Cosmos DB – zdroj dat je teď "typ": "cosmosdb"

Pokud používáte [indexer Cosmos DB](search-howto-index-cosmosdb.md ), `"type": "documentdb"` `"type": "cosmosdb"`musíte změnit na .

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Chyby výsledků spuštění indexeru již nemají stav

Struktura chyb pro provádění indexeru `status` dříve měl prvek. Tento prvek byl odebrán, protože neposkytoval užitečné informace.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Rozhraní API zdroje dat indexeru již nevrací připojovací řetězce.

Z verze rozhraní API 2019-05-06 a 2019-05-06-Preview dále, rozhraní API zdroje dat již vrací připojovací řetězce v odpovědi na všechny operace REST. V předchozích verzích rozhraní API pro zdroje dat vytvořené pomocí POST Azure Cognitive Search vrátil **201** následovaný odpovědí OData, která obsahovala připojovací řetězec ve formátu prostého textu.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Kognitivní schopnost i kognitivní schopnosti rozpoznávání pojmenovaných entit je nyní ukončena.

Pokud zavoláte [schopnost Rozpoznávání entity názvu](cognitive-search-skill-named-entity-recognition.md) ve vašem kódu, volání se nezdaří. Funkce nahrazení je [rozpoznávání entit](cognitive-search-skill-entity-recognition.md). Měli byste být schopni nahradit odkaz na dovednosti žádnými dalšími změnami. Podpis rozhraní API je stejný pro obě verze. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu
Pokud upgradujete z předchozí verze GA 2017-11-11 nebo 2016-09-01, pravděpodobně nebudete muset provádět žádné změny kódu, než změnit číslo verze. Jediné situace, ve kterých budete muset změnit kód, jsou, když:

* Váš kód se nezdaří, pokud jsou vráceny nerozpoznané vlastnosti v odpovědi rozhraní API. Ve výchozím nastavení aplikace by měla ignorovat vlastnosti, které nerozumí.

* Váš kód zachová požadavky rozhraní API a pokusí se je znovu odeslat do nové verze rozhraní API. Například k tomu může dojít, pokud vaše aplikace přetrvává tokeny pokračování vrácené z rozhraní API vyhledávání (další informace vyhledejte `@search.nextPageParameters` v odkazu rozhraní API [vyhledávání).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Pokud se vás týká některá z těchto situací, bude pravděpodobně nutné odpovídajícím způsobem změnit kód. V opačném případě by neměly být nutné žádné změny, pokud nechcete začít používat [nové funkce](#WhatsNew) verze 2019-05-06.

Pokud upgradujete z verze rozhraní PREVIEW, platí také výše uvedené, ale musíte si také uvědomit, že některé funkce náhledu nejsou k dispozici ve verzi 2019-05-06:

* ["Více jako tento" dotazy](search-more-like-this.md)
* [Indexování objektů blob CSV](search-howto-index-csv-blobs.md)
* [Podpora rozhraní API MongoDB pro indexery Cosmos DB](search-howto-index-cosmosdb.md)

Pokud váš kód používá tyto funkce, nebudete moci upgradovat na rozhraní API verze 2019-05-06 bez odebrání jejich použití.

> [!IMPORTANT]
> Preview API jsou určeny pro testování a hodnocení a neměly by být používány v produkčním prostředí.
> 

### <a name="upgrading-complex-types"></a>Inovace složitých typů

Pokud váš kód používá složité typy se staršími verzemi rozhraní PREVIEW Preview 2017-11-11-Preview nebo 2016-09-01-Preview, existují některé nové a změněné limity ve verzi 2019-05-06, které si musíte být vědomi:

+ Byly sníženy limity hloubky dílčích polí a počet komplexních kolekcí na index. Pokud jste vytvořili indexy, které překračují tato omezení pomocí verze rozhraní API náhledu, jakýkoli pokus o jejich aktualizaci nebo znovu vytvořit pomocí rozhraní API verze 2019-05-06 se nezdaří. Pokud se vás to týká, budete muset přepracovat schéma tak, aby se vešel do nových limitů, a pak obnovit index.

+ V rozhraní api verze 2019-05-06 je nový limit na počet prvků komplexních kolekcí na dokument. Pokud jste vytvořili indexy s dokumenty, které překračují tyto limity pomocí verze rozhraní API náhledu, jakýkoli pokus o přeindexování dat pomocí rozhraní API verze 2019-05-06 se nezdaří. Pokud se vás to týká, budete muset před přeindexováním dat snížit počet složitých prvků kolekce na dokument.

Další informace najdete v [tématu Omezení služeb pro Azure Cognitive Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Jak upgradovat starou strukturu složitého typu

Pokud váš kód používá složité typy s jednou ze starších verzí rozhraní PREVIEW API, je možné, že používáte formát definice indexu, který vypadá takto:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Ve verzi rozhraní API 2017-11-11-Preview byl zaveden novější formát podobný stromu pro definování polí indexu. V novém formátu má každé komplexní pole kolekci polí, kde jsou definována jeho dílčí pole. V rozhraní API verze 2019-05-06 tento nový formát se používá výhradně a pokus o vytvoření nebo aktualizaci indexu pomocí starého formátu se nezdaří. Pokud máte indexy vytvořené pomocí starého formátu, budete muset použít rozhraní API verze 2017-11-11-Preview k jejich aktualizaci do nového formátu, než je bude možné spravovat pomocí rozhraní API verze 2019-05-06.

Indexy "plochý" můžete aktualizovat do nového formátu pomocí následujících kroků pomocí rozhraní API verze 2017-11-11-Preview:

1. Proveďte požadavek GET k načtení indexu. Pokud je již v novém formátu, je hotovo.

2. Přeložte index z "ploché" formát do nového formátu. Budete muset napsat kód pro to, protože není k dispozici žádný ukázkový kód k dispozici v době psaní tohoto článku.

3. Proveďte požadavek PUT a aktualizujte index na nový formát. Ujistěte se, že nezměníte žádné další podrobnosti indexu, jako je například prohledávatelnost/filtrovatelnost polí, protože to není povoleno rozhraním API indexu aktualizace.

> [!NOTE]
> Není možné spravovat indexy vytvořené pomocí starého "plochého" formátu z portálu Azure. Upgradujte indexy z "ploché" reprezentace na reprezentaci "stromu" co nejdříve.

## <a name="next-steps"></a>Další kroky

Projděte si referenční dokumentaci rozhraní REST API. Pokud narazíte na problémy, požádejte nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Vyhledávací služba REST API Reference](https://docs.microsoft.com/rest/api/searchservice/)

