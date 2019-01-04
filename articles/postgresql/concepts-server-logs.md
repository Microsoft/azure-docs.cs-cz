---
title: Protokolů serveru ve službě Azure Database for PostgreSQL
description: Tento článek popisuje, databáze Azure pro PostgreSQL generuje dotaz a chybových protokolů a jak uchování protokolu konfigurace.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 0e2dc2af6b4c7ddf531458136e6bcabb49be3b8f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538801"
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
Azure Database for PostgreSQL je integrovaná s diagnostické protokoly Azure monitoru. Jakmile povolíte protokoly na váš server PostgreSQL, můžete je mít znovu vygenerován pro [Log Analytics](../azure-monitor/log-query/log-query-overview.md), Event Hubs nebo Azure Storage. Další informace o tom, jak povolit diagnostické protokoly, najdete v části s postupy [dokumentace k diagnostickým protokolům](../azure-monitor/platform/diagnostic-logs-overview.md). 


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
| errorLevel | Protokolování úrovně, například: VŠIMNĚTE SI, ŽE PROTOKOL, CHYBA, |
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
