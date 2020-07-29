---
title: Řešení potíží Azure Cosmos DB míry požadavků na velkou výjimku
description: Jak diagnostikovat a opravit četnost požadavků na velkou výjimku
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294107"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Diagnostika a řešení potíží Cosmos DB příliš mnoho požadavků
"Počet požadavků je moc velký" nebo kód chyby 429 označuje, že vaše požadavky jsou omezené.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení pro příliš mnoho požadavků.

### <a name="1-check-the-metrics"></a>1. Podívejte se na metriky.
Zákazník by měl kontrolovat [Azure Cosmos DB monitorování](monitor-cosmos-db.md) , aby zkontroloval, jestli počet výjimek 429.

## <a name="cause"></a>Příčina:
Spotřebované propustnosti (RU/s) přesáhla [zřízenou propustnost](set-throughput.md). Sada SDK bude automaticky opakovat požadavky na základě zadaných zásad opakování. Pokud se toto selhání často dostanou, zvažte zvýšení propustnosti kolekce. Zkontrolujte metriky na portálu, abyste viděli, jestli se vám zobrazují chyby 429. Zkontrolujte klíč oddílu a ujistěte se, že je výsledkem [ještě distribuce úložiště a objemu požadavků](partition-data.md).

## <a name="solution"></a>Řešení:
1. Zvyšte míru zřízené propustnosti pomocí [portálu nebo sady SDK](set-throughput.md) .
2. Přepněte databázi nebo kontejner na [Automatické škálování](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)