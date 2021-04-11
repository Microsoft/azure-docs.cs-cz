---
title: Protokolování auditu – Azure Database for PostgreSQL – škálovatelné (Citus)
description: Koncepty pro protokolování auditu pgAudit v Azure Database for PostgreSQL – s škálovatelným škálováním (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 8a36062a2d29bcec10279d73211526a0dcba619e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702109"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Protokolování auditu Azure Database for PostgreSQL – škálovatelné (Citus)

Audit protokolování databázových aktivit ve Azure Database for PostgreSQL – Citus (PostgreSQL) je k dispozici prostřednictvím rozšíření auditu: [pgAudit](https://www.pgaudit.org/). pgAudit poskytuje podrobné protokolování relací a/nebo objektů auditu.

> [!IMPORTANT]
> pgAudit je ve verzi Preview na Azure Database for PostgreSQL – škálovatelné (Citus)

Pokud chcete protokoly na úrovni prostředků Azure pro operace, jako jsou výpočty a škálování úložiště, si můžete prohlédnout v [protokolu aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Požadavky na využití
Ve výchozím nastavení příkazy protokolování pgAudit vysílá standardní protokolovací zařízení Postgres společně s normálními příkazy protokolování. V Azure Database for PostgreSQL – Citus () můžete nakonfigurovat všechny protokoly, které se mají odeslat do Azure Monitor úložiště protokolu pro pozdější analýzy v Log Analytics. Pokud povolíte Azure Monitor protokolování prostředků, protokoly se automaticky odešlou (ve formátu JSON) do protokolů Azure Storage, Event Hubs nebo Azure Monitor, podle toho, co jste vybrali.

## <a name="enabling-pgaudit"></a>Povolení pgAudit

Rozšíření pgAudit je předem nainstalované a povolené na všech uzlech skupiny serverů Citus (). K povolení není nutná žádná akce.

## <a name="pgaudit-settings"></a>nastavení pgAudit

pgAudit umožňuje konfigurovat protokolování auditu relace nebo objektů. [Protokolování auditu relace](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) generuje podrobné protokoly provedených příkazů. [Protokolování auditu objektů](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) je auditování v oboru pro konkrétní vztahy. Můžete zvolit nastavení jednoho nebo obou typů protokolování. 

> [!NOTE]
> nastavení pgAudit jsou určena globálně a nelze je zadat na úrovni databáze nebo role.
>
> Nastavení pgAudit jsou také určena pro jednotlivé uzly ve skupině serverů. Chcete-li provést změnu u všech uzlů, je nutné ji použít pro každý uzel zvlášť.

Aby bylo možné spustit protokolování, je nutné nakonfigurovat parametry pgAudit. [Dokumentace k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) poskytuje definici každého parametru. Nejprve otestujte parametry a potvrďte, že se vám zobrazuje očekávané chování.

> [!NOTE]
> Nastavení na `pgaudit.log_client` zapnuto bude přesměrovat protokoly do klientského procesu (například psql) místo zápisu do souboru. Toto nastavení by se obecně mělo nechávat zakázané. <br> <br>
> Nastavení `pgaudit.log_level` je povolené pouze v případě, že je povolené nastavení `pgaudit.log_client`.

> [!NOTE]
> V Azure Database for PostgreSQL-Citus se `pgaudit.log` nedá nastavit pomocí `-` zástupce znaménka (minus), jak je popsáno v dokumentaci pgAudit. Všechny požadované třídy příkazů (čtení, zápis atd.) by měly být jednotlivě určeny.

## <a name="audit-log-format"></a>Formát protokolu auditu
Každá položka auditu je uvedena `AUDIT:` poblíž začátku řádku protokolu. Formát zbytku položky je podrobně popsán v [dokumentaci k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Začínáme
Chcete-li rychle začít, nastavte `pgaudit.log` na `WRITE` a otevřete protokoly serveru a zkontrolujte výstup. 

## <a name="viewing-audit-logs"></a>Zobrazení protokolů auditu
Způsob přístupu k protokolům závisí na tom, který koncový bod zvolíte. Azure Storage najdete v článku [log Storage Account](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Event Hubs najdete v článku [streamování protokolů Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Protokoly Azure Monitor jsou odesílány do vybraného pracovního prostoru. Protokoly Postgres používají režim kolekce **AzureDiagnostics** , takže se dají dotazovat z tabulky AzureDiagnostics. Pole v tabulce jsou popsána níže. Přečtěte si další informace o dotazování a upozorňování v přehledu [dotazů Azure Monitorch protokolů](../azure-monitor/logs/log-query-overview.md) .

Pomocí tohoto dotazu můžete začít. Výstrahy můžete konfigurovat na základě dotazů.

Vyhledat všechny položky pgAudit v protokolech Postgres pro určitý server za poslední den
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Další kroky

- [Naučte se, jak nastavit protokolování Azure Database for PostgreSQL – Citus a jak získat přístup k protokolům.](howto-hyperscale-logging.md)