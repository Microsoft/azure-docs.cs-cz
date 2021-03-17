---
title: Protokolování auditu – Azure Database for PostgreSQL – flexibilní Server
description: Koncepty pro protokolování auditu pgAudit v Azure Database for PostgreSQL-flexibilním serveru.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b344e2a845a9da8333860599bd4ff9041108202f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588260"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Protokolování auditu Azure Database for PostgreSQL – flexibilní Server

Auditování protokolování databázových aktivit v Azure Database for PostgreSQL-flexibilní Server je k dispozici prostřednictvím rozšíření audit PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit poskytuje podrobné protokolování relací a/nebo objektů auditu.

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Pokud chcete protokoly na úrovni prostředků Azure pro operace, jako jsou výpočty a škálování úložiště, si můžete prohlédnout v [protokolu aktivit Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Požadavky na využití
Ve výchozím nastavení příkazy protokolování pgAudit vysílá standardní protokolovací zařízení Postgres společně s normálními příkazy protokolování. V Azure Database for PostgreSQL-flexibilní Server můžete nakonfigurovat všechny protokoly, které se mají odeslat do Azure Monitor úložiště protokolu pro pozdější analýzy v Log Analytics. Pokud povolíte Azure Monitor protokolování prostředků, protokoly se automaticky odešlou (ve formátu JSON) do protokolů Azure Storage, Event Hubs nebo Azure Monitor, podle toho, co jste vybrali.

Informace o tom, jak nastavit protokolování do protokolů Azure Storage, Event Hubs nebo Azure Monitor, najdete v části protokoly prostředků v [článku protokol serveru](concepts-logging.md).

## <a name="enabling-pgaudit"></a>Povolení pgAudit

Pokud chcete povolit pgAudit, musíte se k serveru připojit pomocí klienta (jako je psql) a povolit rozšíření pgAudit spuštěním následujícího příkazu:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>nastavení pgAudit

pgAudit umožňuje konfigurovat protokolování auditu relace nebo objektů. [Protokolování auditu relace](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) generuje podrobné protokoly provedených příkazů. [Protokolování auditu objektů](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) je auditování v oboru pro konkrétní vztahy. Můžete zvolit nastavení jednoho nebo obou typů protokolování. 

> [!NOTE]
> nastavení pgAudit jsou určena globálně a nelze je zadat na úrovni databáze nebo role.

Jakmile [povolíte pgAudit](#enabling-pgaudit), můžete nakonfigurovat jeho parametry pro spuštění protokolování. [Dokumentace k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) poskytuje definici každého parametru. Nejprve otestujte parametry a potvrďte, že se vám zobrazuje očekávané chování.

> [!NOTE]
> Nastavení na `pgaudit.log_client` zapnuto bude přesměrovat protokoly do klientského procesu (například psql) místo zápisu do souboru. Toto nastavení by se obecně mělo nechávat zakázané. <br> <br>
> `pgaudit.log_level` je povoleno pouze `pgaudit.log_client` v případě, že je zapnuto.

> [!NOTE]
> V Azure Database for PostgreSQL-flexibilní server `pgaudit.log` nelze nastavit pomocí `-` zástupce znaménka (minus), jak je popsáno v dokumentaci k pgAudit. Všechny požadované třídy příkazů (čtení, zápis atd.) by měly být jednotlivě určeny.

## <a name="audit-log-format"></a>Formát protokolu auditu
Každá položka auditu je uvedena `AUDIT:` poblíž začátku řádku protokolu. Formát zbytku položky je podrobně popsán v [dokumentaci k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Začínáme
Chcete-li rychle začít, nastavte `pgaudit.log` na `WRITE` a otevřete protokoly serveru a zkontrolujte výstup. 

## <a name="viewing-audit-logs"></a>Zobrazení protokolů auditu
Způsob přístupu k protokolům závisí na tom, který koncový bod zvolíte. Azure Storage najdete v článku [log Storage Account](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Event Hubs najdete v článku [streamování protokolů Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Protokoly Azure Monitor jsou odesílány do vybraného pracovního prostoru. Protokoly Postgres používají režim kolekce **AzureDiagnostics** , takže se dají dotazovat z tabulky AzureDiagnostics. Pole v tabulce jsou popsána níže. Přečtěte si další informace o dotazování a upozorňování v přehledu [dotazů Azure Monitorch protokolů](../../azure-monitor/logs/log-query-overview.md) .

Pomocí tohoto dotazu můžete začít. Výstrahy můžete konfigurovat na základě dotazů.

Vyhledat všechny položky pgAudit v protokolech Postgres pro určitý server za poslední den
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Další kroky
- [Další informace o přihlašování Azure Database for PostgreSQL – flexibilní Server](concepts-logging.md)
- [Naučte se, jak nastavit protokolování Azure Database for PostgreSQL-flexibilního serveru a jak získat přístup k protokolům.](howto-configure-and-access-logs.md)