---
title: Samoobslužná ochrana fondu SQL bez serveru
description: Tato část obsahuje informace, které vám pomůžou při řešení problémů s fondem SQL bez serveru.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 12ff369cb931eb36014b7c9598b036afdc158750
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457192"
---
# <a name="self-help-for-serverless-sql-pool"></a>Samoobslužná ochrana pro fond SQL bez serveru

Tento článek obsahuje informace o řešení nejčastějších problémů s neserverovým fondem SQL ve službě Azure synapse Analytics.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>V synapse studiu je šedý fond SQL bez serveru šedý.

Pokud synapse Studio nemůže navázat připojení k fondu SQL bez serveru, všimnete si, že fond SQL bez serveru je šedý nebo zobrazuje stav "offline". K tomuto problému obvykle dochází, když dojde k jednomu z následujících případů:

1) Vaše síť brání komunikaci s back-endu Azure synapse. Nejčastější příčinou je blokování portu 1443. Pokud chcete získat, aby fond SQL bez serveru fungoval, odblokujte tento port. Další problémy by mohly zabránit tomu, aby fond SQL bez serveru fungoval, a [Další informace najdete v kompletní příručce pro odstraňování potíží](../troubleshoot/troubleshoot-synapse-studio.md).
2) Nemáte oprávnění k přihlášení do fondu SQL bez serveru. Pokud chcete získat přístup, jeden ze správců pracovního prostoru Azure Synapse by vás měl přidat do role správce pracovního prostoru nebo správce SQL. [Další informace najdete v úplném průvodci řízením přístupu](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>Dotaz se nezdařil, protože soubor nelze otevřít.

Pokud se dotaz nezdařil s chybou říká ' soubor nelze otevřít, protože neexistuje nebo je používán jiným procesem ' a Vy jste si jisti, že oba soubory existují a že se nepoužívá v jiném procesu, znamená to, že fond SQL bez serveru nemůže získat přístup k souboru. K tomuto problému obvykle dochází, protože vaše Azure Active Directory identita nemá práva pro přístup k souboru. Ve výchozím nastavení se fond SQL bez serveru snaží získat přístup k souboru pomocí Azure Active Directory identity. Chcete-li tento problém vyřešit, musíte mít správná oprávnění pro přístup k souboru. Nejjednodušší je udělit sami sobě roli Přispěvatel dat v objektech blob služby Storage pro účet úložiště, který se pokoušíte dotazovat. [Další informace najdete v úplném průvodci řízením přístupu k úložišti pomocí Azure Active Directory](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Dotaz se nezdařil, protože jej nelze provést z důvodu aktuálních omezení prostředků. 

Pokud se dotaz nezdařil s chybovou zprávou "Tento dotaz nelze provést z důvodu aktuálních omezení prostředků", znamená to, že fond SQL bez serveru nebude schopen ho v tuto chvíli spustit v důsledku omezení prostředků: 

- Zajistěte, aby byly použity datové typy přiměřených velikostí. Také určete schéma pro soubory Parquet pro sloupce řetězců, protože budou ve výchozím nastavení VARCHAR (8000). 

- Pokud vaše dotazy cílí na soubory CSV, zvažte [vytvoření statistiky](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- Pokud chcete optimalizovat dotaz, přejděte k [osvědčeným postupům pro výkon pro fond SQL bez serveru](best-practices-sql-on-demand.md) .  

## <a name="create-statement-is-not-supported-in-master-database"></a>PŘÍKAZ CREATE STATEMENT není v hlavní databázi podporován.

Pokud se dotaz nezdařil s chybovou zprávou:

> Nepovedlo se spustit dotaz. Chyba: vytvoření externí tabulky/zdroje dat/datový rozsah databáze/formát souboru není v hlavní databázi podporován. 

To znamená, že hlavní databáze ve fondu SQL bez serveru nepodporuje vytváření:
  - Externí tabulky
  - Externí zdroje dat
  - Přihlašovací údaje v oboru databáze
  - Formáty externích souborů

Řešení:

  1. Vytvořit uživatelskou databázi:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Příkaz EXECUTE Create v kontextu <DATABASE_NAME>, který se dřív nezdařil pro hlavní databázi. 
  
  Příklad pro vytvoření formátu externího souboru:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Další kroky

Další informace o používání fondu SQL bez serveru najdete v následujících článcích:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)

- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)

- [Dotazování konkrétních souborů](query-specific-files.md)

- [Dotazování souborů Parquet](query-parquet-files.md)

- [Dotazování vnořených typů Parquet](query-parquet-nested-types.md)

- [Dotazování souborů JSON](query-json-files.md)

- [Vytváření a používání zobrazení](create-use-views.md)

- [Vytvoření a použití externích tabulek](create-use-external-tables.md)

- [Ukládání výsledků dotazů do úložiště](create-external-table-as-select.md)
