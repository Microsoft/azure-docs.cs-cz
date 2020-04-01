---
title: Jak auditovat operace roviny řízení Azure Cosmos DB
description: Zjistěte, jak auditovat operace roviny ovládacího prvku, jako je přidání oblasti, propustnost aktualizace, převzetí služeb při selhání oblasti, přidání virtuální sítě atd.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420292"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Jak auditovat operace roviny řízení Azure Cosmos DB

Operace roviny řízení zahrnují změny účtu nebo kontejneru Azure Cosmos. Například vytvořit účet Azure Cosmos, přidat oblast, aktualizovat propustnost, oblast převzetí služeb při selhání, přidat virtuální síť atd. Tento článek vysvětluje, jak auditovat operace roviny ovládacího prvku v Azure Cosmos DB.

## <a name="disable-key-based-metadata-write-access"></a>Zakázat přístup pro zápis metadat založených na klíčích
 
Před auditem operací roviny ovládacího prvku v Azure Cosmos DB zakažte přístup pro zápis metadat založených na klíči ve vašem účtu. Když je zakázán přístup pro zápis metadat založených na klíčích, klienti, kteří se připojují k účtu Azure Cosmos prostřednictvím klíčů účtů, nemají přístup k účtu. Přístup pro zápis můžete `disableKeyBasedMetadataWriteAccess` zakázat nastavením vlastnosti na hodnotu true. Po nastavení této vlastnosti může dojít ke změnám libovolného prostředku od uživatele s rolí a pověřeními na základě rolí (RBAC) a pouze s rolí. Další informace o nastavení této vlastnosti najdete v článku [Zabránění změnám z sad SDK.](role-based-access-control.md#preventing-changes-from-cosmos-sdk)

 Při vypínání přístupu pro zápis metadat zvažte následující body:

* Vyhodnoťte a ujistěte se, že vaše aplikace neuskutečňují volání metadat, která mění výše uvedené prostředky (například vytvořit kolekci, aktualizovat propustnost, ...) pomocí sady SDK nebo klíčů účtu.

* V současné době portál Azure používá klíče účtů pro operace metadat, a proto tyto operace budou blokovány. Případně můžete k provedení takových operací použít nasazení šablon Azure CLI, SDK nebo Resource Manager.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Povolení diagnostických protokolů pro operace roviny řízení

Diagnostické protokoly pro operace roviny řízení můžete povolit pomocí portálu Azure. K povolení přihlášení k operacím roviny řízení použijte následující kroky:

1. Přihlaste se k [portálu Azure portal](https://portal.azure.com) a přejděte na svůj účet Azure Cosmos.

1. Otevřete podokno **Nastavení diagnostiky,** zadejte **název,** který mají protokoly vytvořit.

1. Vyberte **ControlPlaneRequests** pro typ protokolu a vyberte možnost **Odeslat do analýzy protokolů.**

Protokoly můžete také uložit do účtu úložiště nebo datového proudu do centra událostí. Tento článek ukazuje, jak odeslat protokoly do analýzy protokolu a potom dotaz na ně. Po povolení trvá několik minut, než se diagnostické protokoly projeví. Všechny operace roviny řízení prováděné po tomto bodu lze sledovat. Následující snímek obrazovky ukazuje, jak povolit protokoly roviny ovládacího prvku:

![Povolení protokolování požadavků na rovinu ovládacího prvku](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Zobrazit operace řídicí roviny

Po zapnutí protokolování můžete pomocí následujících kroků vysledovat operace pro konkrétní účet:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Otevřete kartu **Monitor** z levé navigace a vyberte podokno **Protokoly.** Otevře ui, kde můžete snadno spouštět dotazy s tímto konkrétním účtem v oboru. Spuštěním následujícího dotazu zobrazíte protokoly roviny ovládacího prvku:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Následující snímky obrazovky zachycují protokoly při přidání virtuální sítě do účtu Azure Cosmos:

![Řízení roviny protokolů při přidání virtuální sítě](./media/audit-control-plane-logs/add-ip-filter-logs.png)

Následující snímky obrazovky zachycují protokoly při aktualizaci propustnosti tabulky Cassandra:

![Řízení roviny protokolů při aktualizaci propustnost](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifikace identity přidružené ke konkrétní operaci

Pokud chcete ladit dále, můžete identifikovat konkrétní operaci v **protokolu aktivit** pomocí ID aktivity nebo časové razítko operace. Časové razítko se používá pro některé klienty Resource Manageru, kde ID aktivity není explicitně předáno. Protokol aktivit poskytuje podrobnosti o identitě, se kterou byla operace zahájena. Následující snímek obrazovky ukazuje, jak používat ID aktivity a najít operace s ním spojené v protokolu aktivit:

![Použití ID aktivity a vyhledání operací](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>Další kroky

* [Prozkoumejte Azure Monitor pro Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitorování a ladění metrik v Azure Cosmos DB](use-metrics.md)