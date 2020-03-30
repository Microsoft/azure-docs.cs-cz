---
title: 'Známé problémy: Migrace z Databáze Oracle do Databáze Azure pro PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Seznamte se se známými problémy a omezeními migrace pomocí online migrací z Oracle do Azure Database for PostgreSQL-Single server pomocí služby Migrace databáze Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235246"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Známé problémy/omezení migrace díky online migracím z Oracle do Azure DB pro server PostgreSQL-Single

Známé problémy a omezení související s online migrací z Oracle do Azure Database pro server PostgreSQL-Single jsou popsány v následujících částech.

## <a name="oracle-versions-supported-as-a-source-database"></a>Verze Oracle podporované jako zdrojová databáze

Služba Migrace databáze Azure podporuje připojení k:

- Oracle verze 10g, 11g a 12c.
- Oracle Enterprise, Standard, Express a Personal Edition.

Služba migrace databáze Azure nepodporuje připojení k databázím kontejnerů s více klienty (CDB).

## <a name="postgresql-versions-supported-as-a-target-database"></a>PostgreSQL verze podporované jako cílová databáze

Služba Migrace databáze Azure podporuje migrace do databáze Azure pro server PostgreSQL-Single verze 9.5, 9.6, 10 a 11. See the article [Supported PostgreSQL database versions](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) for current information on version support in Azure Database for PostgreSQL-Single server.

## <a name="datatype-limitations"></a>Omezení datových typů

Následující datové typy **nebudou** migrovány:

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Vnořené tabulky
- Uživatelem definované datové typy
- Poznámky
- Virtuální sloupce
- Zhmotněné pohledy na základě sloupce ROWID

Prázdné sloupce BLOB/CLOB jsou také mapovány na hodnotu NULL v cíli.

## <a name="lob-limitations"></a>OMEZENÍ LOB

- Pokud je povolen režim LOB omezené velikosti, prázdné loby na zdroji Oracle jsou replikovány jako hodnoty NULL.
- Názvy dlouhých objektů (více než 30 bajtů) nejsou podporovány.
- Data ve sloupci LONG a LONG RAW nesmí překročit 64 kS. Všechna data nad 64 kk budou zkrácena.
- Pouze v oracle 12 nejsou podporovány žádné změny sloupců LOB (migrovány).
- UPDATEs na XMLTYPE a LOB sloupce nejsou podporovány (migrovány).

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

- Zákazníci musí používat SYSDBA pro připojení k Oracle.
- Změny dat vyplývající z operací oddílu/dílčího oddílu (PŘIDAT, DROP, EXCHANGE a Zkrátit) nebudou migrovány a mohou způsobit následující chyby:
  - Pro operace ADD aktualizace a odstranění na přidaná data může vrátit "0 řádků ovlivněna" upozornění.
  - Pro operace DROP a Zkrátit nové vloží může mít za následek chyby "duplicitní".
  - Pro operace EXCHANGE může dojít k upozornění "0 řádků" a "duplicitní" chyby.
- Tabulky, jejichž názvy obsahují apostrofy nelze replikovat.
