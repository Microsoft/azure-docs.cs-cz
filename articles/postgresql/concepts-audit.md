---
title: Protokolování auditu – Azure Database for PostgreSQL – jeden server
description: Koncepty pro protokolování auditu pgAudit v Azure Database for PostgreSQL-Single server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 615297a4bf47d80c9313f011b90d343b7ae680e3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488040"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Protokolování auditu Azure Database for PostgreSQL – jeden server

Auditování protokolování databázových aktivit na serveru Azure Database for PostgreSQL je k dispozici prostřednictvím rozšíření audit PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit poskytuje podrobné protokolování relací a/nebo objektů auditu.

> [!NOTE]
> pgAudit je ve verzi Preview na Azure Database for PostgreSQL.
> Rozšíření lze povolit pouze na Pro obecné účely a paměťově optimalizovaných serverech.

Pokud chcete protokoly na úrovni prostředků Azure pro operace, jako jsou výpočty a škálování úložiště, si můžete prohlédnout v [protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Požadavky na využití
Ve výchozím nastavení příkazy protokolování pgAudit vysílá standardní protokolovací zařízení Postgres společně s normálními příkazy protokolování. Ve službě Azure Database for PostgreSQL je možné tyto soubory .log stáhnout prostřednictvím webu Azure Portal nebo rozhraní příkazového řádku. Maximální velikost úložiště pro kolekci souborů je 1 GB a každý soubor je k dispozici po dobu maximálně sedmi dnů (výchozí hodnota je tři dny). Tato služba je krátkodobá možnost uložení.

Případně můžete nakonfigurovat všechny protokoly, které se mají odeslat do úložiště protokolu Azure Monitor pro pozdější analýzy v Log Analytics. Pokud povolíte Azure Monitor protokolování prostředků, protokoly se automaticky odešlou (ve formátu JSON) do protokolů Azure Storage, Event Hubs nebo Azure Monitor, podle toho, co jste vybrali.

Po povolení rozšíření pgAudit se na serveru generuje velký objem protokolování, což má vliv na výkon a úložiště protokolů. Doporučujeme používat protokoly Azure Monitor, které nabízí možnosti pro delší dobu ukládání a také analýzy a funkce upozorňování. Doporučujeme vypnout standardní protokolování, aby se snížil vliv dalšího protokolování na výkon:

   1. Nastavte parametr `logging_collector` na off. 
   2. Restartujte server, aby se tato změna projevila.

Informace o tom, jak nastavit protokolování do protokolů Azure Storage, Event Hubs nebo Azure Monitor, najdete v části protokoly prostředků v [článku protokol serveru](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalace pgAudit

Chcete-li nainstalovat pgAudit, je třeba jej zahrnout do sdílených předem načtených knihoven serveru. Změna parametru Postgres vyžaduje, `shared_preload_libraries` aby se restart serveru projevil. Parametry můžete měnit pomocí [Azure Portal](howto-configure-server-parameters-using-portal.md), rozhraní příkazového [řádku Azure](howto-configure-server-parameters-using-cli.md)nebo [REST API](/rest/api/postgresql/configurations/createorupdate).

Použití [Azure Portal](https://portal.azure.com):

   1. Vyberte svůj server Azure Database for PostgreSQL.
   2. Na bočním panelu vyberte **parametry serveru**.
   3. Vyhledejte `shared_preload_libraries` parametr.
   4. Vyberte **pgaudit**.
   5. Restartujte server, aby se změna projevila.

   6. Připojte se k serveru pomocí klienta (například psql) a povolte rozšíření pgAudit.
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Pokud se zobrazí chyba, potvrďte, že jste po uložení Server restartovali `shared_preload_libraries` .

## <a name="pgaudit-settings"></a>nastavení pgAudit

pgAudit umožňuje konfigurovat protokolování auditu relace nebo objektů. [Protokolování auditu relace](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) generuje podrobné protokoly provedených příkazů. [Protokolování auditu objektů](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) je auditování v oboru pro konkrétní vztahy. Můžete zvolit nastavení jednoho nebo obou typů protokolování. 

> [!NOTE]
> nastavení pgAudit jsou určena gloabally a nelze je zadat na úrovni databáze nebo role.

Jakmile [nainstalujete pgAudit](#installing-pgaudit), můžete nakonfigurovat jeho parametry pro spuštění protokolování. [Dokumentace k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) poskytuje definici každého parametru. Nejprve otestujte parametry a potvrďte, že se vám zobrazuje očekávané chování.

> [!NOTE]
> Nastavení na `pgaudit.log_client` zapnuto bude přesměrovat protokoly do klientského procesu (například psql) místo zápisu do souboru. Toto nastavení by se obecně mělo nechávat zakázané. <br> <br>
> `pgaudit.log_level` je povoleno pouze `pgaudit.log_client` v případě, že je zapnuto.

> [!NOTE]
> V Azure Database for PostgreSQL `pgaudit.log` nelze nastavit pomocí `-` zástupce znaménka (minus), jak je popsáno v dokumentaci k pgAudit. Všechny požadované třídy příkazů (ČTENÍ, ZÁPIS atd.) je potřeba zadat zvlášť.

### <a name="audit-log-format"></a>Formát protokolu auditu
Každá položka auditu je uvedena `AUDIT:` poblíž začátku řádku protokolu. Formát zbytku položky je podrobně popsán v [dokumentaci k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Pokud k splnění požadavků na audit potřebujete další pole, použijte parametr Postgres `log_line_prefix` . `log_line_prefix` je řetězec, který je výstupem na začátku každého řádku protokolu Postgres. Například následující `log_line_prefix` nastavení poskytuje časové razítko, uživatelské jméno, název databáze a ID procesu:

```
t=%m u=%u db=%d pid=[%p]:
```

Další informace o `log_line_prefix` najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Začínáme
Chcete-li rychle začít, nastavte `pgaudit.log` na `WRITE` , a otevřete protokoly a zkontrolujte výstup. 

## <a name="viewing-audit-logs"></a>Zobrazení protokolů auditu
Pokud používáte soubory. log, budou se protokoly auditu zahrnovat do stejného souboru jako protokoly chyb PostgreSQL. Soubory protokolu si můžete stáhnout z webu Azure [Portal](howto-configure-server-logs-in-portal.md) nebo rozhraní příkazového [řádku](howto-configure-server-logs-using-cli.md). 

Pokud používáte protokolování prostředků Azure, způsob přístupu k protokolům závisí na tom, který koncový bod zvolíte. Azure Storage najdete v článku [log Storage Account](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) . Event Hubs najdete v článku [streamování protokolů Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) .

Protokoly Azure Monitor jsou odesílány do vybraného pracovního prostoru. Protokoly Postgres používají režim kolekce **AzureDiagnostics** , takže se dají dotazovat z tabulky AzureDiagnostics. Pole v tabulce jsou popsána níže. Přečtěte si další informace o dotazování a upozorňování v přehledu [dotazů Azure Monitorch protokolů](../azure-monitor/log-query/log-query-overview.md) .

Pomocí tohoto dotazu můžete začít. Výstrahy můžete konfigurovat na základě dotazů.

Vyhledat všechny protokoly Postgres pro konkrétní server za poslední den
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Další kroky
- [Další informace o přihlašování Azure Database for PostgreSQL](concepts-server-logs.md)
- Naučte se, jak nastavit parametry pomocí [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)nebo [REST API](/rest/api/postgresql/configurations/createorupdate).