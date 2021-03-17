---
title: Protokoly-Azure Database for PostgreSQL – jeden server
description: Popisuje konfiguraci protokolování, úložiště a analýzu na Azure Database for PostgreSQL – jeden server
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 621d5a6a91a8c22c52e6febc7c2638571f5bf113
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595797"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Protokoly v Azure Database for PostgreSQL – jeden server

Azure Database for PostgreSQL umožňuje konfigurovat a přistupovat ke standardním protokolům Postgres. Protokoly je možné použít k identifikaci, odstraňování potíží a opravě chyb konfigurace a k zajištění optimálního výkonu. Protokolovací informace, které můžete konfigurovat, a přístup zahrnují chyby, informace o dotazech, autovaku záznamů, připojení a kontrolní body. (Přístup k protokolům transakcí není k dispozici).

Protokolování auditu se zpřístupňuje prostřednictvím rozšíření Postgres pgaudit. Další informace najdete v článku věnovaném [koncepcím auditu](concepts-audit.md) .


## <a name="configure-logging"></a>Konfigurovat protokolování 
Na serveru můžete nakonfigurovat standardní protokolování Postgres pomocí parametrů protokolovacího serveru. Na každém serveru Azure Database for PostgreSQL `log_checkpoints` a `log_connections` jsou ve výchozím nastavení zapnuté. Existují další parametry, které je možné upravit, aby vyhovovaly vašim požadavkům na přihlášení: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Azure Database for PostgreSQL – parametry protokolování":::

Další informace o parametrech protokolu Postgres najdete v části dokumentace k [protokolování](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) a [co do protokolu](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) v dokumentaci k Postgres. Většina, ale ne všechny parametry protokolování Postgres jsou k dispozici pro konfiguraci v Azure Database for PostgreSQL.

Informace o tom, jak nakonfigurovat parametry v Azure Database for PostgreSQL, najdete v [dokumentaci k portálu](howto-configure-server-parameters-using-portal.md) nebo v dokumentaci k rozhraní příkazového [řádku](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Konfigurace velkého objemu protokolů, například protokolování příkazů, může výrazně zvýšit nároky na výkon. 

## <a name="access-log-files"></a>Soubory Access. log
Výchozí formát protokolu v Azure Database for PostgreSQL je. log. Vzorový řádek z tohoto protokolu vypadá takto:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL poskytuje krátkodobé umístění úložiště pro soubory. log. Nový soubor začíná každé 1 hodinu nebo 100 MB, podle toho, co nastane dřív. Protokoly se připojují k aktuálnímu souboru, protože jsou vydávané z Postgres.  

Dobu uchovávání dat pro toto krátkodobé úložiště protokolu můžete nastavit pomocí `log_retention_period` parametru. Výchozí hodnota je 3 dny a maximální hodnota je 7 dnů. Umístění krátkodobého úložiště může obsahovat až 1 GB souborů protokolu. Po 1 GB se nejstarší soubory bez ohledu na dobu uchování odstraní, aby vytvářely místo pro nové protokoly. 

Pro dlouhodobé uchovávání protokolů a analýzy protokolů můžete stáhnout soubory. log a přesunout je do služby třetí strany. Soubory si můžete stáhnout pomocí [Azure Portal](howto-configure-server-logs-in-portal.md) [Azure CLI](howto-configure-server-logs-using-cli.md). Případně můžete nakonfigurovat nastavení diagnostiky Azure Monitor, které automaticky generuje vaše protokoly (ve formátu JSON) do dlouhodobých umístění. Další informace o této možnosti najdete v níže uvedené části. 

Generování souborů. log můžete zastavit nastavením parametru `logging_collector` na off. Vypnutí. generování souboru protokolu se doporučuje, pokud používáte Azure Monitor nastavení diagnostiky. Tato konfigurace sníží dopad dalšího protokolování na výkon.

## <a name="resource-logs"></a>Protokoly prostředků

Azure Database for PostgreSQL je integrován s nastavením diagnostiky Azure Monitor. Nastavení diagnostiky umožňuje odeslat protokoly Postgres ve formátu JSON, abyste Azure Monitor protokoly pro analýzy a upozorňování, Event Hubs pro streamování a Azure Storage k archivaci. 

> [!IMPORTANT]
> Tato diagnostická funkce pro protokoly serveru je k dispozici pouze v Pro obecné účely a v paměťově optimalizovaných [cenových úrovních](concepts-pricing-tiers.md).


### <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Nastavení diagnostiky pro server Postgres můžete povolit pomocí Azure Portal, CLI, REST API a PowerShellu. Kategorie protokolu, která se má vybrat, je **PostgreSQLLogs**. (Existují i jiné protokoly, které můžete nakonfigurovat, pokud používáte [úložiště dotazů](concepts-query-store.md).)

Postup povolení protokolů prostředku pomocí Azure Portal:

   1. Na portálu přejděte v navigační nabídce serveru Postgres na *nastavení diagnostiky* .
   2. Vyberte *Přidat nastavení diagnostiky*.
   3. Pojmenujte toto nastavení. 
   4. Vyberte preferovaný koncový bod (účet úložiště, centrum událostí a Log Analytics). 
   5. Vyberte typ protokolu **PostgreSQLLogs**.
   7. Uložte nastavení.

Pokud chcete povolit protokoly prostředků pomocí PowerShellu, rozhraní příkazového řádku nebo REST API, přejděte na článek [nastavení diagnostiky](../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Přístup k protokolům prostředků

Způsob přístupu k protokolům závisí na tom, který koncový bod zvolíte. Azure Storage najdete v článku [log Storage Account](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Event Hubs najdete v článku [streamování protokolů Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Protokoly Azure Monitor jsou odesílány do vybraného pracovního prostoru. Protokoly Postgres používají režim kolekce **AzureDiagnostics** , takže se dají dotazovat z tabulky AzureDiagnostics. Pole v tabulce jsou popsána níže. Přečtěte si další informace o dotazování a upozorňování v přehledu [dotazů Azure Monitorch protokolů](../azure-monitor/logs/log-query-overview.md) .

Níže jsou uvedené dotazy, které můžete zkusit začít. Výstrahy můžete konfigurovat na základě dotazů.

Vyhledat všechny protokoly Postgres pro konkrétní server za poslední den
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Vyhledat všechny pokusy o připojení jiného typu než localhost
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
Výše uvedený dotaz zobrazí výsledky za posledních 6 hodin pro všechny přihlašování Postgres serveru v tomto pracovním prostoru.

### <a name="log-format"></a>Formát protokolu

V následující tabulce jsou popsána pole pro typ **PostgreSQLLogs** . V závislosti na zvoleném výstupním koncovém bodu se můžou pole zahrnutá a pořadí, ve kterém se zobrazují, lišit. 

|**Pole** | **Popis** |
|---|---|
| TenantId | ID tenanta |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Časové razítko, kdy se protokol zaznamenal v UTC |
| Typ | Typ protokolu Stál `AzureDiagnostics` |
| SubscriptionId | Identifikátor GUID předplatného, ke kterému server patří |
| ResourceGroup | Název skupiny prostředků, do které server patří |
| ResourceProvider | Název poskytovatele prostředků Stál `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identifikátor URI prostředku |
| Prostředek | Název serveru |
| Kategorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Úroveň protokolování, příklad: protokol, chyba, upozornění |
| Zpráva | Zpráva primárního protokolu | 
| Doména | Verze serveru, například: Postgres-10 |
| Podrobnosti | Sekundární zpráva protokolu (Pokud je k dispozici) |
| ColumnName | Název sloupce (Pokud je k dispozici) |
| SchemaName | Název schématu (Pokud je k dispozici) |
| Typ DataType | Název datového typu (Pokud je k dispozici) |
| LogicalServerName | Název serveru | 
| _ResourceId | Identifikátor URI prostředku |
| Předpona | Předpona řádku protokolu |


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o přístupu k protokolům z [Azure Portal](howto-configure-server-logs-in-portal.md) nebo pomocí [Azure CLI](howto-configure-server-logs-using-cli.md).
- Přečtěte si další informace o [cenách Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Další informace o [protokolech auditu](concepts-audit.md)