---
title: Konfigurace a přístup k protokolům – flexibilní Server – Azure Database for PostgreSQL
description: Přístup k protokolům databáze pro Azure Database for PostgreSQL flexibilní Server
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 319b7a40a87d29454b22a26088df313ef524dc56
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545816"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Konfigurace a přístup k protokolům v Azure Database for PostgreSQLm flexibilním serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Protokoly PostgreSQL jsou k dispozici na všech uzlech flexibilního serveru. Protokoly můžete dodávat do serveru úložiště nebo do služby Analytics Service. Protokoly je možné použít k identifikaci, odstraňování potíží a opravě chyb konfigurace a k zajištění optimálního výkonu.

## <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Nastavení diagnostiky pro server Postgres můžete povolit pomocí Azure Portal, CLI, REST API a PowerShellu. Kategorie protokolu, která se má vybrat, je **PostgreSQLLogs** .

Postup povolení protokolů prostředku pomocí Azure Portal:

1. Na portálu přejděte v navigační nabídce serveru Postgres na *nastavení diagnostiky* .
   
2. Vyberte *Přidat nastavení diagnostiky* .
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Tlačítko Přidat nastavení diagnostiky":::

3. Pojmenujte toto nastavení. 

4. Vyberte preferovaný koncový bod (účet úložiště, centrum událostí a Log Analytics). 

5. Vyberte typ protokolu **PostgreSQLLogs** .
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="Tlačítko Přidat nastavení diagnostiky":::

7. Uložte nastavení.

Pokud chcete povolit protokoly prostředků pomocí PowerShellu, rozhraní příkazového řádku nebo REST API, přejděte na článek [nastavení diagnostiky](../../azure-monitor/platform/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Přístup k protokolům prostředků

Způsob přístupu k protokolům závisí na tom, který koncový bod zvolíte. Azure Storage najdete v článku [log Storage Account](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) . Event Hubs najdete v článku [streamování protokolů Azure](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) .

Protokoly Azure Monitor jsou odesílány do vybraného pracovního prostoru. Protokoly Postgres používají režim kolekce **AzureDiagnostics** , takže se dají dotazovat z tabulky AzureDiagnostics. Pole v tabulce jsou popsána níže. Přečtěte si další informace o dotazování a upozorňování v přehledu [dotazů Azure Monitorch protokolů](../../azure-monitor/log-query/log-query-overview.md) .

Níže jsou uvedené dotazy, které můžete zkusit začít. Výstrahy můžete konfigurovat na základě dotazů.

Vyhledat všechny protokoly Postgres pro konkrétní server za poslední den

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Vyhledat všechny pokusy o připojení jiného typu než localhost

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

Výše uvedený dotaz zobrazí výsledky za posledních 6 hodin pro všechny přihlašování Postgres serveru v tomto pracovním prostoru.

## <a name="next-steps"></a>Další kroky

- [Začínáme s dotazy Log Analytics](../../azure-monitor/log-query/get-started-portal.md)
- Další informace o [službě Azure Event Center](../../event-hubs/event-hubs-about.md)