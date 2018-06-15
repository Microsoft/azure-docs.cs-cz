---
title: Jak provádět dotazy na data grafu ve službě Azure Cosmos DB? | Dokumenty Microsoft
description: Zjistěte, jak provádět dotazy na data grafu ve službě Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: dfaf3fe1427c9069939f5421c6a87efb584ebe48
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763200"
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Kurz: Dotazování rozhraní Graph API služby Azure Cosmos DB pomocí Gremlin

Rozhraní [Graph API](graph-introduction.md) služby Azure Cosmos DB podporuje dotazy [Gremlin](https://github.com/tinkerpop/gremlin/wiki). Tento článek poskytuje ukázkové dokumenty a dotazy, které vám pomůžou začít. Podrobné referenční informace ke Gremlin najdete v článku [Podpora Gremlin](gremlin-support.md).

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Dotazování dat pomocí Gremlin

## <a name="prerequisites"></a>Požadavky

Aby tyto dotazy fungovaly, musíte mít účet služby Azure Cosmos DB a data grafu v kontejneru. Něco z toho nemáte? Vytvořte účet a naplňte databázi dokončením [5minutového rychlého startu](create-graph-dotnet.md) nebo [kurzu pro vývojáře](tutorial-query-graph.md). Následující dotazy můžete spustit pomocí [knihovny .NET služby Azure Cosmos DB pro grafy](graph-sdk-dotnet.md), [konzoly Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) nebo oblíbeného ovladače Gremlin.

## <a name="count-vertices-in-the-graph"></a>Výpočet vrcholů v grafu

Následující fragment kódu ukazuje, jak vypočítat počet vrcholů v grafu:

```
g.V().count()
```

## <a name="filters"></a>Filtry

Můžete provádět filtrování pomocí kroků Gremlin `has` a `hasLabel` a jejich kombinací pomocí operátorů `and`, `or` a `not` vytvářet složitější filtry. Azure Cosmos DB poskytuje na schématu nezávislé indexování všech vlastností v rámci vrcholů a stupňů pro zajištění rychlých dotazů:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projekce

Do výsledků dotazu můžete promítnout určité vlastnosti pomocí kroku `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Vyhledání souvisejících hran a vrcholů

Zatím jsme viděli pouze operátory dotazu, které fungují v jakékoli databázi. Grafy jsou rychlé a efektivní pro operace procházení, kdy potřebujete přecházet k souvisejícím hranám a vrcholům. Teď najdeme všechny přátele Thomase. Provedeme to pomocí kroku Gremlin `outE`, kterým vyhledáme všechny vnější hrany od Thomase, a pak pomocí kroku Gremlin `inV` přejdeme z těchto hran k vnitřním vrcholům:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Další dotaz provádí dva segmenty směrování a dvojím zavoláním kroků `outE` a `inV` vyhledá všechny přátele přátel Thomase. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Pomocí Gremlin můžete vytvářet i složitější dotazy a implementovat výkonnou logiku procházení grafů, včetně kombinování výrazů filtru, provádění smyček pomocí kroku `loop` a implementace podmíněné navigace pomocí kroku `choose`. Získejte další informace o tom, co můžete provádět díky [podpoře Gremlin](gremlin-support.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Zjistili jste, jak provádět dotazy pomocí Graphu. 

Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak globálně distribuovat data.

> [!div class="nextstepaction"]
> [Globální distribuce dat](tutorial-global-distribution-graph.md)
