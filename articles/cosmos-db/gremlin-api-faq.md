---
title: Nejčastější dotazy k rozhraní Gremlin API v Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy k rozhraní Gremlin API v Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 8f9e4211b05503f70987b9e476d9a55510bbd520
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078318"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Nejčastější dotazy k rozhraní Gremlin API v Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Tento článek vysvětluje odpovědi na některé nejčastější dotazy týkající se rozhraní Gremlin API v Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Jak vyhodnotit efektivitu dotazů Gremlin

Krok **executionProfile ()** Preview lze použít k poskytnutí analýzy plánu spouštění dotazů. Tento krok je nutné přidat na konec kteréhokoli dotazu Gremlin, jak je znázorněno v následujícím příkladu:

**Příklad dotazu**

```
g.V('mary').out('knows').executionProfile()
```

**Příklad výstupu**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Výstup výše uvedeného profilu ukazuje, kolik času stráví získáním objektů vrcholu, objektů Edge a velikosti pracovní sady dat. To se vztahuje na měření standardních nákladů pro Azure Cosmos DB dotazy.

## <a name="other-frequently-asked-questions"></a>Další nejčastější dotazy

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak se účtují RU/s při spouštění dotazů v databázi grafu?

Všechny objekty grafu, vrcholy a hrany se v back-endu zobrazují jako dokumenty JSON. Vzhledem k tomu, že jeden dotaz Gremlin může současně upravovat jeden nebo více objektů grafu, jsou náklady spojené s ním přímo spojeny s objekty, hranami, které jsou zpracovány dotazem. Jedná se o stejný postup, který Azure Cosmos DB používá pro všechna ostatní rozhraní API. Další informace najdete v tématu [o jednotkách žádostí v Azure Cosmos DB](request-units.md).

Poplatek za RU je založen na pracovní sadě dat procházení, nikoli v sadě výsledků dotazu. Například pokud se dotaz pokusí získat jeden vrchol jako výsledek, ale potřebuje projít více než jeden jiný objekt na cestě, pak budou náklady založeny na všech objektech grafu, které bude trvat, aby vypočítala jeden vrchol výsledku.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaká je maximální škála, kterou může databáze grafu mít Azure Cosmos DB rozhraní Gremlin API?

Azure Cosmos DB využívá [horizontální dělení](partitioning-overview.md) k automatickému zvýšení počtu požadavků na úložiště a propustnost. Maximální propustnost a kapacita úložiště úloh se určují podle počtu oddílů, které jsou přidružené k danému kontejneru. Kontejner rozhraní API Gremlin ale obsahuje specifickou sadu pokynů, které zajistí správné prostředí výkonu ve velkém měřítku. Další informace o dělení a osvědčených postupech najdete [v tématu dělení v Azure Cosmos DB](partitioning-overview.md) článku.

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Pro vývoj v jazyce C#/.NET je vhodné použít balíček Microsoft. Azure. Graphs nebo Gremlin.NET?

Rozhraní API pro Azure Cosmos DB Gremlin využívá Open Source ovladače jako hlavní konektory pro službu. Doporučenou možností je použít [ovladače, které podporuje Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak můžu chránit před útoky prostřednictvím injektáže pomocí ovladačů Gremlin?

Většina nativních ovladačů Apache Tinkerpop Gremlin umožňuje, aby možnost poskytovala slovník parametrů pro provádění dotazů. Toto je příklad, jak to provést v [Gremlin.NET](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) a v [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Proč se mi zobrazuje chyba kompilace dotazu Gremlin: nepovedlo se najít žádnou metodu?

Azure Cosmos DB rozhraní API Gremlin implementuje podmnožinu funkcí definovaných v oblasti povrchu Gremlin. Podporované kroky a další informace najdete v článku [Gremlin support](gremlin-support.md) .

Nejlepším řešením je přepsat požadované Gremlin kroky s podporovanými funkcemi, protože všechny základní kroky Gremlin jsou podporovány Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Proč se mi zobrazuje "WebSocketException: Server vrátil stavový kód" 200 ", když se očekával stavový kód" 101 "?

Tato chyba je nejspíš vyvolána, když se používá nesprávný koncový bod. Koncový bod, který generuje tuto chybu, má následující vzor:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Toto je koncový bod dokumentů pro vaši databázi grafu.  Správný koncový bod, který se má použít, je koncový bod Gremlin, který má následující formát:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Proč se mi zobrazuje chyba "RequestRateIsTooLarge"?

Tato chyba znamená, že přidělené jednotky žádostí za sekundu nejsou dostačující pro obsluhu dotazu. K této chybě obvykle dochází, když spustíte dotaz, který získá všechny vrcholy:

```
// Query example:
g.V()
```

Tento dotaz se pokusí načíst všechny vrcholy z grafu. Proto budou náklady na tento dotaz rovny alespoň počtu vrcholů z pohledu ru. Aby bylo možné tento dotaz vyřešit, je třeba upravit nastavení RU/s.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Proč se moje připojení ovladačů Gremlin vynechává nakonec?

Připojení Gremlin se provádí prostřednictvím připojení protokolu WebSocket. I když připojení protokolu WebSocket nemají určitou dobu provozu, Azure Cosmos DB rozhraní Gremlin API ukončí nečinné připojení po 30 minutách nečinnosti.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Proč se v nativních ovladačích Gremlin nedají používat volání rozhraní Fluent API?

Rozhraní API pro Azure Cosmos DB Gremlin ještě nepodporuje volání rozhraní API Fluent. Volání rozhraní API Fluent vyžadují funkci interního formátování známou jako podpora bytového kódu, kterou aktuálně nepodporuje Azure Cosmos DB rozhraní API pro Gremlin. Z důvodu stejného důvodu se v současné době nepodporují i nejnovější ovladače Gremlin-JavaScript.

## <a name="next-steps"></a>Další kroky

* [Podpora protokolu Azure Cosmos DB Gremlin](gremlin-support.md)
* Vytvoření, dotazování a procházení databáze Azure Cosmos DB graphu pomocí [konzoly Gremlin](create-graph-gremlin-console.md)
