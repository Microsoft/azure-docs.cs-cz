---
title: Protokolování auditu – databáze Azure pro PostgreSQL – jeden server
description: Koncepty pro protokolování auditu pgAudit v databázi Azure pro PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842465"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Protokolování auditu v databázi Azure pro PostgreSQL – jeden server

Protokolování auditu databázových aktivit v Azure Database for PostgreSQL – jeden server je k dispozici prostřednictvím rozšíření postgresql auditu: [pgAudit](https://www.pgaudit.org/). pgAudit poskytuje podrobné protokolování relace a/nebo auditu objektů.

> [!NOTE]
> pgAudit je ve verzi Preview v Azure Database for PostgreSQL.
> Rozšíření lze povolit pouze na serverech s podporou pro obecné účely a paměť.

Pokud chcete protokoly na úrovni prostředků Azure pro operace, jako je výpočetní a škálování úložiště, najdete v tématu [Protokol aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Aspekty použití
Ve výchozím nastavení příkazy protokolování pgAudit vysílá standardní protokolovací zařízení Postgres společně s normálními příkazy protokolování. Ve službě Azure Database for PostgreSQL je možné tyto soubory .log stáhnout prostřednictvím webu Azure Portal nebo rozhraní příkazového řádku. Maximální úložiště pro shromažďování souborů je 1 GB a každý soubor je k dispozici maximálně sedm dní (výchozí hodnota je tři dny). Tato služba je možnost krátkodobého úložiště.

Případně můžete nakonfigurovat všechny protokoly, které mají být emitovány do služby protokolu diagnostiky Azure Monitor. Pokud povolíte protokolování diagnostiky Azure Monitor, vaše protokoly se automaticky odešlou (ve formátu JSON) do Azure Storage, Event Hubs a/nebo Azure Monitor protokoly, v závislosti na vaší volbě.

Po povolení rozšíření pgAudit se na serveru generuje velký objem protokolování, což má vliv na výkon a úložiště protokolů. Doporučujeme využít službu protokolování diagnostiky Azure, která nabízí možnosti dlouhodobějšího úložiště a také funkce pro analýzy a upozorňování. Doporučujeme vypnout standardní protokolování, aby se snížil vliv dalšího protokolování na výkon:

   1. Nastavte parametr `logging_collector` na OFF. 
   2. Chcete-li tuto změnu použít, restartujte server.

Informace o tom, jak nastavit protokolování do Azure Storage, Event Hubs nebo Azure Monitor protokoly, navštivte diagnostické protokoly části [protokolu serveru článku](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalace pgAudit

Chcete-li nainstalovat pgAudit, musíte jej zahrnout do sdílených knihoven přednačtení serveru. Změna `shared_preload_libraries` parametru Postgres vyžaduje restartování serveru, aby se projevilo. Parametry můžete změnit pomocí [portálu Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)nebo [rozhraní REST API](/rest/api/postgresql/configurations/createorupdate).

Použití [portálu Azure](https://portal.azure.com):

   1. Vyberte svůj server Azure Database for PostgreSQL.
   2. Na postranním panelu vyberte **Parametry serveru**.
   3. Vyhledejte `shared_preload_libraries` parametr.
   4. Vyberte **pgaudit**.
   5. Chcete-li změnu použít, restartujte server.

   6. Připojte se k serveru pomocí klienta (jako psql) a povolte rozšíření pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Pokud se zobrazí chyba, potvrďte, že `shared_preload_libraries`jste po uložení server restartovali.

## <a name="pgaudit-settings"></a>pgAudit nastavení

pgAudit umožňuje konfigurovat protokolování relací nebo auditu objektů. [Protokolování auditu relace](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) vydává podrobné protokoly provedených příkazů. [Protokolování auditu objektů](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) je auditován pro konkrétní vztahy. Můžete nastavit jeden nebo oba typy protokolování. 

> [!NOTE]
> pgAudit nastavení jsou určeny gloabally a nelze zadat na úrovni databáze nebo role.

Po [instalaci pgAudit](#installing-pgaudit), můžete nakonfigurovat jeho parametry pro spuštění protokolování. [Dokumentace pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) poskytuje definici každého parametru. Nejprve otestujte parametry a potvrďte, že se vám dostává očekávané chování.

> [!NOTE]
> Nastavení `pgaudit.log_client` na ZAPNUTO přesměruje protokoly na klientský proces (jako psql) namísto zápisu do souboru. Toto nastavení by se obecně mělo nechávat zakázané. <br> <br>
> `pgaudit.log_level`je povolena `pgaudit.log_client` pouze v případě, že je zapnutá.

> [!NOTE]
> V Azure Database pro `pgaudit.log` PostgreSQL nelze `-` nastavit pomocí zástupce znaménka (mínus), jak je popsáno v dokumentaci pgAudit. Všechny požadované třídy příkazů (ČTENÍ, ZÁPIS atd.) je potřeba zadat zvlášť.

### <a name="audit-log-format"></a>Formát protokolu auditu
Každá položka auditu `AUDIT:` je označena v blízkosti začátku řádku protokolu. Formát zbytku záznamu je podrobně popsán v [dokumentaci pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Pokud potřebujete další pole ke splnění požadavků na audit, `log_line_prefix`použijte parametr Postgres . `log_line_prefix`je řetězec, který je výstup emituje na začátku každého řádku protokolu Postgres. Například následující `log_line_prefix` nastavení poskytuje časové razítko, uživatelské jméno, název databáze a ID procesu:

```
t=%m u=%u db=%d pid=[%p]:
```

Další informace `log_line_prefix`naleznete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Začínáme
Chcete-li rychle `pgaudit.log` začít, nastavte na `WRITE`a otevřete protokoly a zkontrolujte výstup. 

## <a name="viewing-audit-logs"></a>Zobrazení protokolů auditu
Pokud používáte soubory .log, budou protokoly auditu zahrnuty do stejného souboru jako protokoly chyb PostgreSQL. Soubory protokolu můžete stáhnout z [portálu](howto-configure-server-logs-in-portal.md) Azure nebo [rozhraní příkazového příkazu .](howto-configure-server-logs-using-cli.md) 

Pokud používáte protokolování diagnostiky Azure, způsob, jakým přistupujete k protokolům, závisí na tom, který koncový bod zvolíte. Azure Storage najdete v článku [účet úložiště protokolů.](../azure-monitor/platform/resource-logs-collect-storage.md) Centra událostí najdete v [článku datového proudu Azure protokoly.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Pro protokoly monitorování Azure protokoly jsou odesílány do pracovního prostoru, který jste vybrali. Postgres protokoly použít režim kolekce **AzureDiagnostics,** takže mohou být dotazováni z tabulky AzureDiagnostics. Pole v tabulce jsou popsána níže. Další informace o dotazování a upozorňování v přehledu [dotazů protokolů Azure Monitors.](../azure-monitor/log-query/log-query-overview.md)

Tento dotaz můžete použít k zahájení. Výstrahy můžete konfigurovat na základě dotazů.

Vyhledat všechny protokoly Postgres pro konkrétní server za poslední den
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Další kroky
- [Informace o protokolování v Azure Database pro PostgreSQL](concepts-server-logs.md)
- Zjistěte, jak nastavit parametry pomocí [portálu Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)nebo [rozhraní REST API](/rest/api/postgresql/configurations/createorupdate).
