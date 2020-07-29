---
title: Řešení potíží s výjimkou Azure Cosmos DB nenalezena
description: Jak diagnostikovat a opravit nenalezené výjimky
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294155"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Diagnostika a řešení potíží Azure Cosmos DB nenalezeny
Stavový kód HTTP 404 představuje, že prostředek již neexistuje.

## <a name="expected-behavior"></a>Očekávané chování
Existuje řada platných scénářů, kde aplikace očekává 404 a správně zpracovává scénář.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Pro položku, která by měla existovat nebo existovat, nebyla vrácena položka nebyla nalezena.
Níže jsou uvedeny možné důvody, proč se vrátí stavový kód 404, pokud by položka měla nebo ukončí.

### <a name="1-race-condition"></a>1. konflikt časování
Existuje více instancí klienta sady SDK a čtení proběhlo před zápisem.

#### <a name="solution"></a>Řešení:
1. Výchozí konzistencí účtu pro Cosmos DB je konzistence relací. Pokud je položka vytvořena nebo aktualizována, odpověď vrátí token relace, který lze předat mezi instancemi sady SDK, aby bylo zaručeno, že žádost o čtení je čtena z repliky s touto změnou.
2. Změna [úrovně konzistence](consistency-levels-choosing.md) na [silnější úroveň](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. Neplatný klíč oddílu a kombinace ID
Kombinace klíče oddílu a ID není platná.

#### <a name="solution"></a>Řešení:
Opravte aplikační logiku, která způsobuje nesprávnou kombinaci. 

### <a name="3-invalid-character-in-item-id"></a>3. neplatný znak v ID položky
Položka je vložena do Cosmos DB s [neplatným znakem](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) v ID položky.

#### <a name="solution"></a>Řešení:
Doporučujeme, aby uživatelé změnili ID na jinou hodnotu, která neobsahuje speciální znaky. Pokud se změna ID neshoduje s možností, můžete ID kódování Base64, aby se speciální znaky vyhnuly.

Položky, které jsou již vloženy do kontejneru, ID lze nahradit pomocí hodnot identifikátoru RID místo odkazů založených na názvu.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. hodnota TTL (Time to Live) vyprázdnění
Položka měla nastavenou vlastnost [Time to Live (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) . Položka byla vyprázdněna, protože vypršela doba do provozu.

#### <a name="solution"></a>Řešení:
Pokud chcete zabránit vyprázdnit položku, změňte čas na živé.

### <a name="5-lazy-indexing"></a>5. opožděné indexování
[Opožděné indexování](index-policy.md#indexing-mode) nebylo zachytilo.

#### <a name="solution"></a>Řešení:
Počkejte, až indexování zachytí nebo změní zásady indexování.

### <a name="6-parent-resource-deleted"></a>6. nadřazený prostředek se odstranil.
Databáze nebo kontejner, ve kterém položka existuje, byla odstraněna.

#### <a name="solution"></a>Řešení:
1. [Obnovte](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) nadřazený prostředek nebo znovu vytvořte prostředky.
2. Vytvořit nový prostředek, který nahradí odstraněný prostředek

## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)