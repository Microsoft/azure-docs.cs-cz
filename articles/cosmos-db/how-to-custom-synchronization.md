---
title: Jak implementovat vlastní synchronizaci pro optimalizaci pro vyšší dostupnost a výkon ve službě Azure Cosmos DB
description: Zjistěte, jak implementovat vlastní synchronizaci pro optimalizaci pro vyšší dostupnost a výkon ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: f10e260432a93a0413d65d6f5814d00a50e9465a
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560275"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementace vlastního synchronizace pro optimalizaci pro vyšší dostupnost a výkon

Azure Cosmos DB nabízí [pět jasně definovaných úrovní konzistence](consistency-levels.md) můžete zvolit z vyvážit kompromis mezi konzistencí, výkonu a dostupnosti. Silná konzistence pomáhá zajistit, že data synchronně replikuje a trvale uchovávaných v každé oblasti, kde je k dispozici účet Azure Cosmos. Tato konfigurace poskytuje nejvyšší úroveň odolnosti, ale je za cenu výkonu a dostupnosti. Pokud chcete, aby vaše aplikace řídit nebo uvolnit delší trvanlivost dat tak, aby odpovídala aplikace potřebuje bez narušení dostupnosti, můžete použít *vlastní synchronizaci* v aplikační vrstvě pro dosažení úroveň odolnosti je Chcete.

Následující obrázek znázorňuje vizuálně vlastní synchronizační model:

![Vlastní synchronizace](./media/how-to-custom-synchronization/custom-synchronization.png)

V tomto scénáři je kontejner služby Azure Cosmos globálně replikovat napříč několika oblastmi na více kontinentech. Pomocí silnou konzistenci pro všechny oblasti v tomto scénáři má vliv na výkon. K zajištění vyšší úroveň odolnosti dat, bez negativního vlivu latence zápisu, může aplikace použít dva klienti, které sdílejí stejný [tokenu relace](how-to-manage-consistency.md#utilize-session-tokens).

První klient může zapisovat data do místní oblast (například USA – západ). Čtení klienta, který se používá k zajištění synchronizace je druhý klient (například v oblasti USA – východ). O toku tokenu relace ze zápisu odpovědi na následující čtení, čtení zajišťuje synchronizaci zápisy do oblasti USA – východ. Azure Cosmos DB zajišťuje, že zápisy jsou vidět alespoň jedné oblasti. Jejich zaručeno, že k oblastnímu výpadku zvládnout situaci, kdy původní oblast pro zápis ocitne mimo provoz. V tomto scénáři při každém zápisu synchronizované do oblasti USA – východ, snižuje tak latenci využívající silnou konzistenci napříč všemi oblastmi. V případě několika hlavními databázemi, kde se objeví zápisy v každé oblasti, můžete rozšířit tento model se synchronizovat do více oblastí současně.

## <a name="configure-the-clients"></a>Konfigurace klientů

Následující příklad ukazuje vrstvy přístupu k datům, který vytvoří instanci dvěma klienty pro vlastní synchronizaci:

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

Po inicializaci klienti jsou aplikace můžete provádět zápisy do místní oblast (oblasti USA – západ) a vynucení synchronizace zápisy do oblasti USA – východ následujícím způsobem.

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

Můžete rozšířit modelu synchronizovat do více oblastí současně.

## <a name="next-steps"></a>Další postup

Další informace o globální distribuci a konzistence ve službě Azure Cosmos DB, přečtěte si tyto články:

* [Vyberte úroveň správné konzistence ve službě Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy konzistencí, dostupností a výkonem ve službě Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Správa konzistence ve službě Azure Cosmos DB](how-to-manage-consistency.md)
* [Distribuce dělení a data ve službě Azure Cosmos DB](partition-data.md)
