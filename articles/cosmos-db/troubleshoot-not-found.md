---
title: Řešení potíží s Azure Cosmos DB nenalezenými výjimkami
description: Naučte se diagnostikovat a opravovat nenalezené výjimky.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 22cce2c620d23ab477de5d92bb8c6d4f5ef5a493
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425120"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Diagnostika a řešení potíží s Azure Cosmos DB nenalezenými výjimkami
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Stavový kód HTTP 404 značí, že prostředek již neexistuje.

## <a name="expected-behavior"></a>Očekávané chování
Existuje mnoho platných scénářů, kde aplikace očekává kód 404 a správně zpracovává scénář.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Pro položku, která by měla existovat nebo existovat, byla vrácena výjimka nenalezena.
Tady jsou možné důvody, proč se vrátí stavový kód 404, pokud by položka existovala nebo existovala.

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>Relace čtení není pro vstupní token relace k dispozici

#### <a name="solution"></a>Řešení:
1. Aktualizujte aktuální sadu SDK na nejnovější dostupnou verzi. Nejběžnější příčiny této konkrétní chyby byly opraveny v nejnovějších verzích sady SDK.

### <a name="race-condition"></a>Konflikt časování
Existuje více instancí klienta sady SDK a čtení proběhlo před zápisem.

#### <a name="solution"></a>Řešení:
1. Výchozí konzistencí účtu pro Azure Cosmos DB je konzistence relací. Při vytvoření nebo aktualizaci položky vrátí odpověď token relace, který lze předat mezi instancemi sady SDK, aby bylo zaručeno, že žádost o čtení je čtena z repliky s touto změnou.
1. Změňte [úroveň konzistence](./consistency-levels.md) na [silnější úroveň](./consistency-levels.md).

### <a name="invalid-partition-key-and-id-combination"></a>Neplatný klíč oddílu a kombinace ID
Kombinace klíče oddílu a ID není platná.

#### <a name="solution"></a>Řešení:
Opravte aplikační logiku, která způsobila nesprávnou kombinaci. 

### <a name="invalid-character-in-an-item-id"></a>Neplatný znak v ID položky
Položka je vložena do Azure Cosmos DB s [neplatným znakem](/dotnet/api/microsoft.azure.documents.resource.id#remarks) v ID položky.

#### <a name="solution"></a>Řešení:
Změňte ID na jinou hodnotu, která neobsahuje speciální znaky. Pokud se změna ID nepoužívá, můžete kódování Base64 kódovat, aby se speciální znaky vyhnuly. Base64 může stále vytvořit název s neplatným znakem '/', který je nutné nahradit.

Položky, které jsou již vloženy do kontejneru pro ID, lze nahradit použitím hodnot identifikátorů RID místo odkazů založených na názvu.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Čas do živého vyprázdnění
Položka měla nastavenou vlastnost [Time to Live (TTL)](./time-to-live.md) . Položka byla vyprázdněna, protože vypršela platnost vlastnosti TTL.

#### <a name="solution"></a>Řešení:
Chcete-li zabránit mazání položky, změňte vlastnost TTL.

### <a name="lazy-indexing"></a>Opožděné indexování
[Opožděné indexování](index-policy.md#indexing-mode) se nezachycuje.

#### <a name="solution"></a>Řešení:
Počkejte, až indexování zachytí nebo změní zásady indexování.

### <a name="parent-resource-deleted"></a>Nadřazený prostředek smazán
Databáze nebo kontejner, ve kterém položka existuje, byla odstraněna.

#### <a name="solution"></a>Řešení:
1. [Obnovte](./configure-periodic-backup-restore.md#request-restore) nadřazený prostředek nebo prostředky znovu vytvořte.
1. Vytvořte nový prostředek, který nahradí odstraněný prostředek.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. v názvech kontejnerů nebo kolekcí se rozlišují velká a malá písmena.
U názvů kontejnerů nebo kolekcí se rozlišují velká a malá písmena v Cosmos DB.

#### <a name="solution"></a>Řešení:
Nezapomeňte použít přesný název při připojování k Cosmos DB.

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).
* [Diagnostikujte a řešte](troubleshoot-java-sdk-v4-sql.md) potíže při použití Azure Cosmos DB Java v4 SDK.
* Seznamte se s pokyny pro výkon pro [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
