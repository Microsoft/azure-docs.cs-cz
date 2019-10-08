---
title: Protokoly serveru v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak Azure Database for PostgreSQL-Single server generuje protokoly dotazů a chyb a jak se konfiguruje uchovávání protokolů.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 17083029f2377037b99abfa3ce8371661eccb957
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029988"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Protokoly serveru v Azure Database for PostgreSQL – jeden server
Azure Database for PostgreSQL generuje protokoly dotazů a chyb. Dotazy a protokoly chyb lze použít k identifikaci, odstraňování potíží a opravě chyb konfigurace a k optimalizaci výkonu. (Přístup k protokolům transakcí není zahrnutý). 

## <a name="configure-logging"></a>Konfigurovat protokolování 
Protokolování můžete nakonfigurovat na serveru pomocí parametrů protokolovacího serveru. Na každém novém serveru **log_checkpoints** a **log_connections** jsou ve výchozím nastavení zapnuté. Existují další parametry, které je možné upravit, aby vyhovovaly vašim požadavkům na přihlášení: 

![Azure Database for PostgreSQL – parametry protokolování](./media/concepts-server-logs/log-parameters.png)

Další informace o těchto parametrech najdete v tématu [zasílání zpráv o chybách PostgreSQL a](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) v dokumentaci k protokolování. Informace o tom, jak nakonfigurovat parametry Azure Database for PostgreSQL, najdete v [dokumentaci k portálu](howto-configure-server-parameters-using-portal.md) nebo v dokumentaci k rozhraní příkazového [řádku](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Přístup k protokolům serveru prostřednictvím portálu nebo rozhraní příkazového řádku
Pokud jste povolili protokoly, můžete k nim přistupovat z úložiště protokolu Azure Database for PostgreSQL pomocí [Azure Portal](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md)a rozhraní Azure REST API. Soubory protokolu se obměňují každou hodinu nebo po dosažení velikosti 100 MB (podle toho, co nastane dříve). Dobu uchování tohoto úložiště protokolu můžete nastavit pomocí parametru **log @ no__t-1retention @ no__t-2period** přidruženého k vašemu serveru. Výchozí hodnota je 3 dny; maximální hodnota je 7 dní. Server musí mít dostatek přiděleného úložiště pro ukládání souborů protokolu. (Tento parametr uchování neřídí diagnostické protokoly Azure.)


## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for PostgreSQL je integrován s protokoly diagnostiky Azure Monitor. Po povolení protokolů na serveru PostgreSQL se můžete rozhodnout, že se mají vysílat do [Azure monitor protokolů](../azure-monitor/log-query/log-query-overview.md), Event Hubs nebo Azure Storage. 

> [!IMPORTANT]
> Tato diagnostická funkce pro protokoly serveru je k dispozici pouze v Pro obecné účely a v paměťově optimalizovaných [cenových úrovních](concepts-pricing-tiers.md).

Postup povolení diagnostických protokolů pomocí Azure Portal:

   1. Na portálu přejděte v navigační nabídce serveru Postgres na *nastavení diagnostiky* .
   2. Vyberte *Přidat nastavení diagnostiky*.
   3. Pojmenujte toto nastavení. 
   4. Vyberte preferované umístění pro příjem dat (účet úložiště, centrum událostí, Log Analytics). 
   5. Vyberte datové typy, které chcete.
   6. Uložte nastavení.

Následující tabulka popisuje, co je v každém protokolu. V závislosti na zvoleném výstupním koncovém bodu se můžou pole zahrnutá a pořadí, ve kterém se zobrazují, lišit. 

|**Pole** | **Popis** |
|---|---|
| TenantId | ID tenanta |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Časové razítko, kdy se protokol zaznamenal v UTC |
| Typ | Typ protokolu Always `AzureDiagnostics` |
| SubscriptionId | Identifikátor GUID předplatného, ke kterému server patří |
| ResourceGroup | Název skupiny prostředků, do které server patří |
| ResourceProvider | Název poskytovatele prostředků Always `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identifikátor URI prostředku |
| Prostředek | Název serveru |
| Kategorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Úroveň protokolování, příklad: protokol, chyba, upozornění |
| Zpráva | Zpráva primárního protokolu | 
| Domain (Doména) | Verze serveru, například: Postgres-10 |
| Detaily | Sekundární zpráva protokolu (Pokud je k dispozici) |
| ColumnName | Název sloupce (Pokud je k dispozici) |
| SchemaName | Název schématu (Pokud je k dispozici) |
| Typ DataType | Název datového typu (Pokud je k dispozici) |
| LogicalServerName | Název serveru | 
| _ResourceId | Identifikátor URI prostředku |
| Předvolba | Předpona řádku protokolu |



## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o přístupu k protokolům z [Azure Portal](howto-configure-server-logs-in-portal.md) nebo pomocí [Azure CLI](howto-configure-server-logs-using-cli.md).
- Přečtěte si další informace o [cenách Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
