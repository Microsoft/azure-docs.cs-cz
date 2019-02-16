---
title: Jak implementovat vlastní synchronizaci pro optimalizaci pro vyšší dostupnost a výkon ve službě Azure Cosmos DB
description: Zjistěte, jak implementovat vlastní synchronizaci pro optimalizaci pro vyšší dostupnost a výkon ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 05432097ee80a01458a1f2e95c48123bf7f05d57
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309569"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Jak implementovat vlastní synchronizaci pro optimalizaci pro vyšší dostupnost a výkon

Azure Cosmos DB nabízí pět jasně definovaných úrovní konzistence můžete zvolit z vyvážit kompromis mezi konzistencí, výkonu a dostupnosti. Silná konzistence zajišťuje synchronní replikace dat a odolnosti uchovávaných v každé oblasti, kde je k dispozici účet Azure Cosmos. Tato konfigurace ale poskytuje nejvyšší úroveň odolnosti je za cenu výkonu a dostupnosti. Pokud aplikace chce, aby se ovládací prvek nebo zmírnit delší trvanlivost dat podle potřeby aplikace bez narušení dostupnosti, můžete použít vlastní synchronizace v aplikační vrstvě k dosažení požadované úrovně odolnosti.

Následující diagram znázorňuje vizuálně vlastní synchronizační model.

![Vlastní synchronizace](./media/how-to-custom-synchronization/custom-synchronization.png)

V tomto scénáři kontejneru Azure Cosmos se replikuje globálně v několika oblastech napříč více kontinentech. Pomocí silnou konzistenci pro všechny oblasti v tomto scénáři bude mít vliv na výkon. Zajistit vyšší úroveň odolnosti dat bez negativního vlivu latence zápisu aplikace slouží dvěma klienty, které sdílejí stejný token relace.

První klient může zapisovat data do místní oblast (například USA – západ). Druhý klienta (například v oblasti USA – východ) je čtení klient používá k zajištění synchronizace. Podle průchodu tokenu relace ze zápisu odpovědi na následující čtení zajistíte čtení synchronizace zápisy do oblasti USA – východ. Azure Cosmos DB zajistíte zápisy jsou vidět alespoň jedné oblasti a zaručeno nezbytné k překonání k oblastnímu výpadku, pokud původní oblast pro zápis, přestanou fungovat. V tomto scénáři je synchronizován při každém zápisu do oblasti USA – východ, snižuje tak latenci využívající silnou konzistenci napříč všemi oblastmi. V případě několika hlavními databázemi, kde zápisy se vyskytují v každé oblasti, je možné tento model rozšířit na synchronizaci do více oblastí současně.

## <a name="configure-the-clients"></a>Konfigurace klientů

Následující příklad ukazuje vytvoření instance dvěma klienty pro tento účel vrstvy přístupu k datům.

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementace vlastní synchronizace

Jakmile klienti jsou inicializovány, aplikace může provádět zápisy do místní oblast (oblasti USA – západ) a může vynutit synchronizaci zápisy do oblasti USA – východ následujícím způsobem.

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

Tento model je možné rozšířit na synchronizaci do více oblastí současně.

## <a name="next-steps"></a>Další postup

Další informace o globální distribuci a konzistence ve službě Azure Cosmos DB najdete v následujících článcích:

* [Volba úrovně konzistence správné ve službě Azure Cosmos DB](consistency-levels-choosing.md)

* [Kompromisy konzistencí, dostupností a výkonem ve službě Azure Cosmos DB](consistency-levels-tradeoffs.md)

* [Jak spravovat konzistence ve službě Azure Cosmos DB](how-to-manage-consistency.md)

* [Distribuce dělení a data ve službě Azure Cosmos DB](partition-data.md)
