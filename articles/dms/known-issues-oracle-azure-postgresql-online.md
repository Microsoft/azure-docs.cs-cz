---
title: 'Známé problémy: migrace z Oracle na Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Přečtěte si o známých problémech a omezeních migrace u online migrací od Oracle až po Azure Database for PostgreSQL k jednomu serveru pomocí Azure Database Migration Service.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 2316e0314c9d8f36aee123d4a516f382f58e83e6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91291652"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Známé problémy/omezení migrace pro online migrace z Oracle do Azure DB pro PostgreSQL – jeden server

Známé problémy a omezení související s online migracemi od Oracle do Azure Database for PostgreSQL-Single server jsou popsány v následujících částech.

## <a name="oracle-versions-supported-as-a-source-database"></a>Verze Oracle podporované jako zdrojová databáze

Azure Database Migration Service podporuje připojení k:

- Oracle verze 10g, 11g a 12c.
- Oracle Enterprise, Standard, Express a Personal Edition.

Azure Database Migration Service nepodporuje připojení k databázím kontejnerů s více klienty (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Verze PostgreSQL podporované jako cílová databáze

Azure Database Migration Service podporuje migrace do Azure Database for PostgreSQL s jednou serverem verze 9,5, 9,6, 10 a 11. Aktuální informace o podpoře verzí v Azure Database for PostgreSQL-jednom serveru najdete v článku [podporované verze databáze PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) .

## <a name="datatype-limitations"></a>Omezení datového typu

Následující typy DataType **nebudou** migrovány:

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Vnořené tabulky
- Uživatelsky definované datové typy
- Poznámky
- Virtuální sloupce
- Materializovaná zobrazení založená na sloupci ROWID

Prázdné sloupce objektů BLOB a datový typ CLOB jsou také mapovány na hodnotu NULL v cíli.

## <a name="lob-limitations"></a>Omezení LOB

- Pokud je povolený režim LOB s omezeným rozsahem, budou se prázdné objekty LOBs s ve zdroji Oracle replikovat jako hodnoty NULL.
- Dlouhé názvy objektů (víc než 30 bajtů) nejsou podporované.
- Data v DLOUHÝch a DLOUHÝch nezpracovaných sloupcích nemůžou překročit 64 KB. Všechna data nad 64 KB budou zkrácena.
- Pouze v Oracle 12 nejsou žádné změny ve sloupcích LOB podporovány (migrováno).
- Aktualizace sloupců XMLTYPE a LOB nejsou podporovány (migrováno).

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

- Uživatel musí mít na serveru Oracle oprávnění DBA.
- Změny dat vyplývající z operací oddílů a dílčího oddílu (přidání, vyřazení, výměna a ZKRÁCENí) nebudou migrovány a mohou způsobit následující chyby:
  - U přidání operací může aktualizace a odstranění u přidaných dat vracet upozornění "0 řádků, které jsou ovlivněny".
  - U operací DROP a ZKRÁCENí může nová vložení způsobit chyby "duplicity".
  - V případě operací systému EXCHANGE může dojít k chybám "0 řádků", upozornění i duplicitám.
- Tabulky, jejichž názvy obsahují apostrofy, nelze replikovat.
