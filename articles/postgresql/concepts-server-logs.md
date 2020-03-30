---
title: Protokoly – databáze Azure pro PostgreSQL – jeden server
description: Popisuje konfiguraci, úložiště a analýzu protokolování v databázi Azure pro PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280492"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Protokoly v databázi Azure pro PostgreSQL – jeden server
Azure Database pro PostgreSQL umožňuje konfigurovat a přistupovat ke standardním protokolům Postgres. Protokoly lze identifikovat, řešení problémů a opravit chyby konfigurace a neoptimální výkon. Informace protokolování, které můžete konfigurovat a přistupovat, zahrnují chyby, informace o dotazech, záznamy automatického vakua, připojení a kontrolní body. (Přístup k protokolům transakcí není k dispozici).

Protokolování auditu je k dispozici prostřednictvím rozšíření Postgres, pgaudit. Další informace naleznete v článku [koncepty auditování.](concepts-audit.md)


## <a name="configure-logging"></a>Konfigurace protokolování 
Standardní protokolování Postgres můžete nakonfigurovat na serveru pomocí parametrů serveru protokolování. Na každé databázi Azure pro `log_checkpoints` `log_connections` PostgreSQL server a jsou ve výchozím nastavení zapnuté. K dispozici jsou další parametry, které můžete upravit tak, aby vyhovovaly vašim potřebám protokolování: 

![Databáze Azure pro PostgreSQL – parametry protokolování](./media/concepts-server-logs/log-parameters.png)

Další informace o parametrech protokolu Postgres najdete v části [Kdy protokolovat](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) a [Co protokolovat](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) v dokumentaci Postgres. Většina, ale ne všechny parametry protokolování Postgres jsou k dispozici ke konfiguraci v Azure Database pro PostgreSQL.

Informace o konfiguraci parametrů v Azure Database pro PostgreSQL najdete v [dokumentaci k portálu](howto-configure-server-parameters-using-portal.md) nebo [v dokumentaci k rozhraní příkazového řádku](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Konfigurace velkého objemu protokolů, například protokolování příkazu, může přidat významnou režii výkonu. 

## <a name="access-log-files"></a>Přístup k souborům protokolu
Výchozí formát protokolu v Azure Database for PostgreSQL je .log. Ukázková čára z tohoto protokolu vypadá takto:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL poskytuje umístění krátkodobého úložiště pro soubory .log. Nový soubor začíná každých 1 hodinu nebo 100 MB, podle toho, co nastane dříve. Protokoly jsou připojeny k aktuálnímu souboru, jak jsou vydávány z Postgres.  

Můžete nastavit dobu uchování pro toto krátkodobé `log_retention_period` úložiště protokolu pomocí parametru. Výchozí hodnota je 3 dny a maximální hodnota je 7 dnů. Umístění krátkodobého úložiště může obsahovat až 1 GB souborů protokolu. Po 1 GB budou nejstarší soubory, bez ohledu na dobu uchování, odstraněny, aby se uvolnilo místo pro nové protokoly. 

Pro dlouhodobější uchovávání protokolů a analýzy protokolů můžete stáhnout soubory .log a přesunout je do služby jiného výrobce. Soubory si můžete stáhnout pomocí [portálu Azure](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md). Případně můžete nakonfigurovat nastavení diagnostiky Azure Monitoru, který automaticky vyzařuje protokoly (ve formátu JSON) do dlouhodobějších umístění. Další informace o této možnosti naleznete v následující části. 

Generování souborů .log můžete zastavit nastavením parametru `logging_collector` OFF. Vypnutí generování souborů protokolu se doporučuje, pokud používáte nastavení diagnostiky Azure Monitoru. Tato konfigurace sníží dopad na výkon další protokolování.

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for PostgreSQL je integrovaná s diagnostickým nastavením Azure Monitoru. Diagnostická nastavení umožňuje odesílat protokoly Postgres ve formátu JSON do protokolů monitorování Azure pro analýzy a upozorňování, Centra událostí pro streamování a Azure Storage pro archivaci. 

> [!IMPORTANT]
> Tato diagnostická funkce pro protokoly serveru je k dispozici pouze v [cenových úrovních](concepts-pricing-tiers.md)optimalizované pro obecné účely a paměť .


### <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky
Diagnostická nastavení pro server Postgres můžete povolit pomocí portálu Azure Portal, CLI, ROZHRANÍ REST API a Powershellu. Kategorie protokolu, kterou chcete vybrat, je **PostgreSQLLogs**. (Existují další protokoly, které můžete nakonfigurovat, pokud používáte [úložiště dotazů](concepts-query-store.md).)

Povolení diagnostických protokolů pomocí portálu Azure:

   1. Na portálu přejděte do *části Nastavení diagnostiky* v navigační nabídce serveru Postgres.
   2. Vyberte *Přidat diagnostické nastavení*.
   3. Pojmenujte toto nastavení. 
   4. Vyberte upřednostňovaný koncový bod (účet úložiště, centrum událostí, analýza protokolů). 
   5. Vyberte typ protokolu **PostgreSQLLogs**.
   7. Uložte nastavení.

Chcete-li povolit diagnostické protokoly pomocí rozhraní Powershell, CLI nebo REST API, navštivte článek [o nastavení diagnostiky.](../azure-monitor/platform/diagnostic-settings.md)

### <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

Způsob přístupu k protokolům závisí na tom, který koncový bod zvolíte. Azure Storage najdete v článku [účet úložiště protokolů.](../azure-monitor/platform/resource-logs-collect-storage.md) Centra událostí najdete v [článku datového proudu Azure protokoly.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Pro protokoly monitorování Azure protokoly jsou odesílány do pracovního prostoru, který jste vybrali. Postgres protokoly použít režim kolekce **AzureDiagnostics,** takže mohou být dotazováni z tabulky AzureDiagnostics. Pole v tabulce jsou popsána níže. Další informace o dotazování a upozorňování v přehledu [dotazů protokolů Azure Monitors.](../azure-monitor/log-query/log-query-overview.md)

Následují dotazy, které můžete zkusit začít. Výstrahy můžete konfigurovat na základě dotazů.

Vyhledat všechny protokoly Postgres pro konkrétní server za poslední den
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Hledat všechny pokusy o připojení mimo localhost
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
Výše uvedený dotaz zobrazí výsledky za posledních 6 hodin pro všechny protokolování serveru Postgres v tomto pracovním prostoru.

### <a name="log-format"></a>Formát protokolu

Následující tabulka popisuje pole pro typ **PostgreSQLLogs.** V závislosti na zvoleném výstupním koncovém bodu se mohou zahrnutá pole a pořadí, ve kterém se zobrazí, lišit. 

|**Pole** | **Popis** |
|---|---|
| TenantId | ID vašeho klienta |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Časové razítko při zaznamenání protokolu v utc |
| Typ | Typ protokolu. Vždy`AzureDiagnostics` |
| SubscriptionId | Identifikátor GUID pro předplatné, do kterého server patří |
| ResourceGroup | Název skupiny prostředků, do které server patří |
| ResourceProvider | Název zprostředkovatele prostředků. Vždy`MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identifikátor URI prostředku |
| Prostředek | Název serveru |
| Kategorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Errorlevel | Úroveň protokolování, příklad: LOG, ERROR, NOTICE |
| Zpráva | Primární zpráva protokolu | 
| Domain (Doména) | Verze serveru, příklad: postgres-10 |
| Podrobnosti | Sekundární zpráva protokolu (je-li k dispozici) |
| ColumnName | Název sloupce (je-li k dispozici) |
| SchemaName | Název schématu (je-li k dispozici) |
| Název datového typu | Název datového typu (je-li k dispozici) |
| Název logického serveru | Název serveru | 
| _ResourceId | Identifikátor URI prostředku |
| Předpona | Předpona řádku protokolu |


## <a name="next-steps"></a>Další kroky
- Další informace o přístupu k protokolům z [webu Azure Portal](howto-configure-server-logs-in-portal.md) nebo Azure [CLI](howto-configure-server-logs-using-cli.md).
- Další informace o [cenách Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/).
- Další informace o [protokolech auditu](concepts-audit.md)
