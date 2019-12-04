---
title: Protokolování auditu – Azure Database for PostgreSQL – jeden server
description: Koncepty pro protokolování auditu pgAudit v Azure Database for PostgreSQL-Single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 4a41e5eda3ca2bd92d78a81d73c1ad4c859e25a3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764555"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Protokolování auditu Azure Database for PostgreSQL – jeden server

Auditování protokolování databázových aktivit na serveru Azure Database for PostgreSQL je k dispozici prostřednictvím rozšíření audit PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit poskytuje podrobné protokolování relací a/nebo objektů auditu.

> [!NOTE]
> pgAudit je ve verzi Preview na Azure Database for PostgreSQL.
> Rozšíření lze povolit pouze na Pro obecné účely a paměťově optimalizovaných serverech.

Pokud chcete protokoly na úrovni prostředků Azure pro operace, jako jsou výpočty a škálování úložiště, si můžete prohlédnout v [protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md).

## <a name="usage-considerations"></a>Požadavky na využití
Ve výchozím nastavení příkazy protokolování pgAudit vysílá standardní protokolovací zařízení Postgres společně s normálními příkazy protokolování. Ve službě Azure Database for PostgreSQL je možné tyto soubory .log stáhnout prostřednictvím webu Azure Portal nebo rozhraní příkazového řádku. Maximální velikost úložiště pro kolekci souborů je 1 GB a každý soubor je k dispozici po dobu maximálně sedmi dnů (výchozí hodnota je tři dny). Tato služba je krátkodobá možnost uložení.

Případně můžete nakonfigurovat všechny protokoly, které se mají vysílat do služby diagnostiky protokolu Azure Monitor. Pokud povolíte protokolování diagnostiky Azure Monitor, vaše protokoly se automaticky odešlou (ve formátu JSON) do protokolů Azure Storage, Event Hubs nebo Azure Monitor, podle toho, co jste vybrali.

Po povolení rozšíření pgAudit se na serveru generuje velký objem protokolování, což má vliv na výkon a úložiště protokolů. Doporučujeme využít službu protokolování diagnostiky Azure, která nabízí možnosti dlouhodobějšího úložiště a také funkce pro analýzy a upozorňování. Doporučujeme vypnout standardní protokolování, aby se snížil vliv dalšího protokolování na výkon:

   1. Nastavte parametr `logging_collector` na OFF. 
   2. Restartujte server, aby se tato změna projevila.

Informace o tom, jak nastavit protokolování do protokolů Azure Storage, Event Hubs nebo Azure Monitor, najdete v části protokoly diagnostiky v [článku protokol serveru](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalace pgAudit

Chcete-li nainstalovat pgAudit, je třeba jej zahrnout do sdílených předem načtených knihoven serveru. Změna parametru `shared_preload_libraries` Postgres vyžaduje, aby se restart serveru projevil. Parametry můžete měnit pomocí [Azure Portal](howto-configure-server-parameters-using-portal.md), rozhraní příkazového [řádku Azure](howto-configure-server-parameters-using-cli.md)nebo [REST API](/rest/api/postgresql/configurations/createorupdate).

Použití [Azure Portal](https://portal.azure.com):

   1. Vyberte svůj server Azure Database for PostgreSQL.
   2. Na bočním panelu vyberte **parametry serveru**.
   3. Vyhledejte parametr `shared_preload_libraries`.
   4. Vyberte **pgaudit**.
   5. Restartujte server, aby se změna projevila.

   6. Připojte se k serveru pomocí klienta (například psql) a povolte rozšíření pgAudit.
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Pokud se zobrazí chyba, potvrďte, že jste restart serveru po uložení `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>nastavení pgAudit

pgAudit umožňuje konfigurovat protokolování auditu relace nebo objektů. [Protokolování auditu relace](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) generuje podrobné protokoly provedených příkazů. [Protokolování auditu objektů](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) je auditování v oboru pro konkrétní vztahy. Můžete zvolit nastavení jednoho nebo obou typů protokolování. 

> [!NOTE]
> nastavení pgAudit jsou určena gloabally a nelze je zadat na úrovni databáze nebo role.

Jakmile [nainstalujete pgAudit](#installing-pgaudit), můžete nakonfigurovat jeho parametry pro spuštění protokolování. [Dokumentace k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) poskytuje definici každého parametru. Nejprve otestujte parametry a potvrďte, že se vám zobrazuje očekávané chování.

> [!NOTE]
> Nastavení `pgaudit.log_client` na ZAPNUTo bude přesměrovat protokoly do klientského procesu (například psql) místo zápisu do souboru. Toto nastavení by se obecně mělo nechávat zakázané.

> [!NOTE]
> `pgaudit.log_level` je povoleno pouze v případě, že je `pgaudit.log_client` zapnuto. V Azure Portal v současnosti existuje chyba s `pgaudit.log_level`: zobrazí se pole se seznamem, což znamená, že je možné vybrat více úrovní. Musí se však vybrat pouze jedna úroveň. 

> [!NOTE]
> V Azure Database for PostgreSQL nelze `pgaudit.log` nastavit pomocí zástupce `-` (mínus), jak je popsáno v dokumentaci k pgAudit. Všechny požadované třídy příkazů (ČTENÍ, ZÁPIS atd.) je potřeba zadat zvlášť.

### <a name="audit-log-format"></a>Formát protokolu auditu
Každý záznam auditu je uveden `AUDIT:` poblíž začátku řádku protokolu. Formát zbytku položky je podrobně popsán v [dokumentaci k pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Pokud k splnění požadavků na audit potřebujete další pole, použijte parametr Postgres `log_line_prefix`. `log_line_prefix` je řetězec, který je výstupem na začátku každého řádku protokolu Postgres. Například následující nastavení `log_line_prefix` poskytuje časové razítko, uživatelské jméno, název databáze a ID procesu:

```
t=%m u=%u db=%d pid=[%p]:
```

Další informace o `log_line_prefix`najdete v [dokumentaci k PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Začínáme
Chcete-li rychle začít, nastavte `pgaudit.log` na `WRITE`a otevřete protokoly a zkontrolujte výstup. 


## <a name="next-steps"></a>Další kroky
- [Další informace o přihlašování Azure Database for PostgreSQL](concepts-server-logs.md)
- Naučte se, jak nastavit parametry pomocí [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)nebo [REST API](/rest/api/postgresql/configurations/createorupdate).
