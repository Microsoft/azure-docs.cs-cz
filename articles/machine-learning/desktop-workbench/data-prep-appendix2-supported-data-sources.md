---
title: Podporované zdroje dat, které jsou k dispozici s přípravou dat Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje úplný seznam podporovaných zdrojů dat dostupných pro přípravu dat Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 88ed4fa43e5724cfe1d6f1555db947d77045cd2e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644534"
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Podporované zdroje dat pro přípravu dat Azure Machine Learning 
Tento článek popisuje, jak aktuálně podporovaných zdrojů dat pro přípravu dat Azure Machine Learning.

Podporovaných zdrojů dat pro tuto verzi jsou následující.

## <a name="types"></a>Typy 

### <a name="sql-server"></a>SQL Server
Čtení z místního SQL serveru nebo databáze Azure SQL.

#### <a name="options"></a>Možnosti
- Adresa serveru
- Důvěřovat serveru (sudý při certifikát na serveru není platný. Používejte opatrně)
- Typ ověřování (Windows, Server)
- Uživatelské jméno
- Heslo
- Pro připojení k databázi
- Příkaz jazyka SQL

#### <a name="notes"></a>Poznámky
- Sloupce variantu jazyka SQL nejsou podporovány.
- Sloupec čas je převeden na datum a čas přidáním čas z databáze datum 1970/1/1
- Při spuštění v clusteru Spark, všechna data související s sloupce (datum, datum a čas, datetime2, datetimeoffset) vyhodnotí nesprávné hodnoty dat před 1583
- Hodnoty ve sloupcích decimal, může dojít ke ztrátě přesnosti z důvodu převodu na desítkové

### <a name="directory-vs-file"></a>Adresář a soubor
Zvolte jeden soubor a načíst do přípravy dat. Typ souboru je analyzovat určit výchozí parametry pro připojení k souboru na další obrazovce.

Vyberte adresář nebo sadu souborů v adresáři (Výběr souboru je vícenásobného výběru). Kterýkoliv přístup soubory jsou čteny jako jednoho datového toku a jsou připojeny k sobě navzájem, záhlaví, v případě potřeby vynechají.

Podporované typy souborů jsou:
- S oddělovači (CSV, TSV, txt, atd.)
- Pevná šířka
- Prostý text
- Soubor JSON

### <a name="csv-file"></a>Soubor CSV
Čtení souboru čárkami oddělených hodnot z úložiště.

#### <a name="options"></a>Možnosti
- Oddělovač
- Poznámka
- Záhlaví
- Symbol desetinné čárky
- Kódování souboru
- Chcete-li přeskočit řádky

### <a name="tsv-file"></a>Soubor TSV
Čtení souboru kartu oddělených hodnot z úložiště.

#### <a name="options"></a>Možnosti
- Poznámka
- Záhlaví
- Kódování souboru
- Chcete-li přeskočit řádky

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Přečtěte si Excelový list jeden soubor současně tak, že zadáte název tabulky nebo číslo.

#### <a name="options"></a>Možnosti
- Název listu nebo číslo
- Záhlaví
- Chcete-li přeskočit řádky

### <a name="json-file"></a>Soubor JSON
Čtení souboru JSON z úložiště. Soubor je "sloučí" pro čtení.

#### <a name="options"></a>Možnosti
- Žádný

### <a name="parquet"></a>Parquet
Čtení Parquet datové sady, buď jeden soubor nebo složku.

Parquet jako formátu můžou mít různé formy v úložišti. Pro menší sady dat se někdy používá .parquet jeden soubor. Různé knihovny jazyka Python podporují čtení nebo zápis do jednoho .parquet souborů. Pro tuto chvíli přípravu dat Azure Machine Learning spoléhá na knihovny PyArrow Python pro čtení Parquet během interaktivní místní použití. Podporuje jeden .parquet souborů (pokud byly napsány jako takové, nikoli jako součást větších datových sad), a také Parquet datových sad.

Parquet datové sady je kolekce více než jeden soubor .parquet, z nichž každý představuje menší oddílu větších datových sad. Datové sady jsou obvykle obsaženy ve složce a jsou výstupní formát parquet výchozí pro platformy, jako je Spark a Hive.

>[!NOTE]
>Při čtení Parquet data, která je ve složce s více soubory .parquet, je nejbezpečnější a vyberte adresář pro čtení a **Parquet datovou sadu** možnost. Díky tomu PyArrow čtení celé složky namísto jednotlivých souborů. Tím se zajistí podporu pro složitější způsoby ukládání na disku, jako je například složka dělení Parquet pro čtení.

Horizontální navýšení kapacity provádění spoléhá na Spark Parquet čtení funkcí a podporují jednotlivé soubory i složky, podobně jako interaktivní místní použití.

#### <a name="options"></a>Možnosti
- Parquet datové sady. Tato možnost určuje, zda přípravu dat Azure Machine Learning rozbalí daný adresář a se pokusí přečíst každý soubor samostatně (nevybrané režimu), nebo jestli ho považuje za adresáři celé datové sady (vybraný režim). Vybraný režim PyArrow vybere nejlepší způsob, jak interpretovat soubory.


## <a name="locations"></a>Umístění
### <a name="local"></a>Místní
Místní pevný disk nebo namapované síťové umístění úložiště.

### <a name="sql-server"></a>SQL Server
Místního SQL serveru, nebo Azure SQL database.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob storage, který vyžaduje předplatné Azure.

