---
title: Řešení potíží s příliš velkým množstvím Azure Cosmos DB frekvence požadavků
description: Naučte se diagnostikovat a opravovat příliš velké výjimky pro rychlost požadavků.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411333"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnostika a řešení potíží s Azure Cosmos DB příliš velkým množstvím výjimek
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zpráva "počet požadavků je moc velká" nebo kód chyby 429 označuje, že vaše požadavky jsou omezené.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující část obsahuje známé příčiny a řešení pro příliš mnoho požadavků.

### <a name="check-the-metrics"></a>Kontrolovat metriky
Zkontrolujte [Azure Cosmos DB monitorování](monitor-cosmos-db.md) a podívejte se na počet výjimek 429.

#### <a name="cause"></a>Příčina:
Spotřebované propustnost (jednotky žádostí za sekundu) překročily [zřízenou propustnost](set-throughput.md). Sada SDK automaticky opakuje požadavky na základě zadaných zásad opakování. Pokud se toto selhání často dostanou, zvažte zvýšení propustnosti kolekce. Zkontrolujte metriky na portálu, abyste viděli, jestli se vám zobrazují chyby 429. Zkontrolujte klíč oddílu a ujistěte se, že je výsledkem [ještě distribuce úložiště a objemu požadavků](partitioning-overview.md).

#### <a name="solution"></a>Řešení:
1. Zvyšte míru zřízené propustnosti pomocí [portálu nebo sady SDK](set-throughput.md) .
1. Přepněte databázi nebo kontejner na [Automatické škálování](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).
* [Diagnostikujte a řešte](troubleshoot-java-sdk-v4-sql.md) potíže při použití Azure Cosmos DB Java v4 SDK.
* Seznamte se s pokyny pro výkon pro [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).