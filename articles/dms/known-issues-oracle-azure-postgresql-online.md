---
title: Článek o známých problémech nebo omezeních migrace při online migracích od Oracle do Azure Database for PostgreSQL-Single server | Microsoft Docs
description: Přečtěte si o známých problémech nebo omezeních migrace s online migracemi od Oracle po Azure Database for PostgreSQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: ecd2750926fd04c84d2d6b48f5fde06363dd15df
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606760"
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
- ODKAZŮ
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

- Zákazníci musí použít SYSDBA k připojení k Oracle.
- Změny dat vyplývající z operací oddílů a dílčího oddílu (přidání, vyřazení, výměna a ZKRÁCENí) nebudou migrovány a mohou způsobit následující chyby:
  - U přidání operací může aktualizace a odstranění u přidaných dat vracet upozornění "0 řádků, které jsou ovlivněny".
  - U operací DROP a ZKRÁCENí může nová vložení způsobit chyby "duplicity".
  - V případě operací systému EXCHANGE může dojít k chybám "0 řádků", upozornění i duplicitám.
- Tabulky, jejichž názvy obsahují apostrofy, nelze replikovat.
