---
title: Řešení potíží s příliš velkým množstvím Azure Cosmos DB frekvence požadavků
description: Naučte se diagnostikovat a opravovat příliš velké výjimky pro rychlost požadavků.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f8b1129c94ecf80efb60a13a0b80b1cc1817ff3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871084"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnostika a řešení potíží s Azure Cosmos DB příliš velkým množstvím výjimek
Zpráva "počet požadavků je moc velká" nebo kód chyby 429 označuje, že vaše požadavky jsou omezené.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující část obsahuje známé příčiny a řešení pro příliš mnoho požadavků.

### <a name="check-the-metrics"></a>Kontrolovat metriky
Zkontrolujte [Azure Cosmos DB monitorování](monitor-cosmos-db.md) a podívejte se na počet výjimek 429.

#### <a name="cause"></a>Příčina:
Spotřebované propustnost (jednotky žádostí za sekundu) překročily [zřízenou propustnost](set-throughput.md). Sada SDK automaticky opakuje požadavky na základě zadaných zásad opakování. Pokud se toto selhání často dostanou, zvažte zvýšení propustnosti kolekce. Zkontrolujte metriky na portálu, abyste viděli, jestli se vám zobrazují chyby 429. Zkontrolujte klíč oddílu a ujistěte se, že je výsledkem [ještě distribuce úložiště a objemu požadavků](partition-data.md).

#### <a name="solution"></a>Řešení:
1. Zvyšte míru zřízené propustnosti pomocí [portálu nebo sady SDK](set-throughput.md) .
1. Přepněte databázi nebo kontejner na [Automatické škálování](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).