---
title: Protokolů serveru ve službě Azure Database for PostgreSQL
description: Tento článek popisuje, databáze Azure pro PostgreSQL generuje dotaz a chybových protokolů a jak uchování protokolu konfigurace.
services: postgresql
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2ee9f750ff52b8afe4be54233f1374f523a789f4
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845159"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Protokolů serveru ve službě Azure Database for PostgreSQL 
Azure Database for PostgreSQL generuje dotaz a chybových protokolů. Dotaz a chybových protokolů umožňuje zjistit, řešit a opravit chyby v konfiguraci a neoptimální výkonu. (Přístup k protokolům transakce není součástí). 

## <a name="configure-logging"></a>Konfigurace protokolování 
Protokolování můžete nakonfigurovat na serveru s použitím parametrů protokolování serveru. Na každý nový server **log_checkpoints** a **log_connections** jsou standardně povoleny. Existují další parametry, můžete upravit tak, aby odpovídala vašim potřebám protokolování: 

![Azure Database for PostgreSQL – parametry protokolování](./media/concepts-server-logs/log-parameters.png)

Další informace o těchto parametrech naleznete v tématu PostgreSQL [generování sestav a protokolování chyb](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) dokumentaci. Další informace o konfiguraci – Azure Database for postgresql – parametrů, najdete v článku [dokumentaci k portálu](howto-configure-server-parameters-using-portal.md) nebo [dokumentace k rozhraní příkazového řádku](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Přístup k protokolům serveru prostřednictvím portálu nebo rozhraní příkazového řádku
Pokud jste povolili protokoly, které můžete přistupovat k nim z Azure Database for postgresql – protokol úložiště pomocí [webu Azure portal](howto-configure-server-logs-in-portal.md), [rozhraní příkazového řádku Azure](howto-configure-server-logs-using-cli.md)a rozhraní Azure REST API. Soubory protokolů otočit každou hodinu nebo velikost 100MB, podle toho, co nastane dřív. Můžete nastavit dobu uchování pomocí úložiště protokolu **protokolu\_uchování\_období** parametr přidružené k serveru. Výchozí hodnota je 3 dny; Maximální hodnota je 7 dní. Server musí mít dostatek přidělené úložiště pro uložení souborů protokolu. (Tento parametr uchovávání informací se neřídí diagnostické protokoly Azure).


## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for PostgreSQL je integrovaná s diagnostické protokoly Azure monitoru. Jakmile povolíte protokoly na váš server PostgreSQL, můžete je mít znovu vygenerován pro [Log Analytics](../azure-monitor/log-query/log-query-overview.md), Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly, najdete v části s postupy [dokumentace k diagnostickým protokolům](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). 


Následující tabulka popisuje, co je v každém protokolu. V závislosti na výstupního koncového bodu, kterou zvolíte, pole zahrnutá a pořadí, ve kterém jsou uvedeny, se můžou lišit. 

|**Pole** | **Popis** |
|---|---|
| TenantId | Vaše ID tenanta |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Časové razítko, kdy se přihlášení v protokolu ve standardu UTC |
| Typ | Typ protokolu. Vždy `AzureDiagnostics` |
| SubscriptionId | Identifikátor GUID pro předplatné, které server patří do |
| ResourceGroup | Název skupiny prostředků, do které patří server |
| ResourceProvider | Název poskytovatele prostředků. Vždy `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ID prostředku | Identifikátor URI prostředku |
| Prostředek | Název serveru |
| Kategorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Úroveň protokolování, například: LOG, chyby, upozornění |
| Zpráva | Primární protokolu zpráv | 
| Domain (Doména) | Verze serveru, například: postgres 10 |
| Detail | Zpráva protokolu sekundární (Pokud je k dispozici) |
| Název sloupce | Název sloupce (Pokud je k dispozici) |
| %{Schemaname/ | Název schématu (Pokud je k dispozici) |
| DatatypeName | Název datového typu (Pokud je k dispozici) |
| LogicalServerName | Název serveru | 
| _ResourceId | Identifikátor URI prostředku |

## <a name="next-steps"></a>Další postup
- Další informace o přístup k protokolům z [webu Azure portal](howto-configure-server-logs-in-portal.md) nebo [rozhraní příkazového řádku Azure](howto-configure-server-logs-using-cli.md).
- Další informace o [ceny Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
