---
title: Podporované zdroje dat, které jsou k dispozici přípravy dat Azure Machine Learning | Microsoft Docs
description: Tento dokument obsahuje úplný seznam podporovaných zdrojů dat k dispozici pro přípravu dat Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 1a6ef0b928bd9a2d21db68a0d5476357b1d32dd1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831543"
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Podporované zdroje dat pro přípravu Azure Machine Learning dat. 
Tento článek popisuje aktuálně podporovaných zdrojů dat pro přípravu dat Azure Machine Learning.

Takto vypadají podporovaných zdrojů dat pro tuto verzi.

## <a name="types"></a>Typy 

### <a name="sql-server"></a>SQL Server
Čtení z místní systém SQL server nebo Azure SQL database.

#### <a name="options"></a>Možnosti
- Adresa serveru
- Důvěřovat serveru (sudé při certifikát na serveru není platný. Používejte opatrně)
- Typ ověřování (Windows, Server)
- Uživatelské jméno
- Heslo
- Pro připojení k databázi
- Dotaz SQL

#### <a name="notes"></a>Poznámky
- SQL variant sloupce nejsou podporovány.
- Sloupec čas je převést na typ datetime připojením čas z databáze k datu pod hodnotou 1970/1/1
- Při spuštění v clusteru Spark, všechna data související s sloupců (date, datetime, datetime2, datetimeoffset) vyhodnotí nesprávné hodnoty dat před 1583
- Hodnoty ve sloupcích decimal mohou ztratit přesnost z důvodu převodu do desítkové soustavy

### <a name="directory-vs-file"></a>Directory oproti souboru
Zvolte jeden soubor a přečtěte si ho do přípravy data. Typ souboru je analyzována určit výchozí parametry pro připojení k souboru na další obrazovce.

Vyberte adresář nebo sadu souborů v adresáři (Nástroje pro výběr souborů je vícenásobného výběru.). S buď přístupem soubory se čtou v jako jednoho datového toku a jsou připojeny k sobě navzájem hlavičky vynechají v případě potřeby.

Typy podporované souboru jsou:
- S oddělovači (.csv tsv, .txt, atd.)
- Pevná šířka
- Prostý text
- Soubor JSON

### <a name="csv-file"></a>Soubor CSV
Načtení souboru čárkami oddělených hodnot úložiště.

#### <a name="options"></a>Možnosti
- Oddělovač
- Poznámka
- Záhlaví
- Decimal – symbol
- Kódování souborů
- Řádky tak, aby přeskočil

### <a name="tsv-file"></a>Soubor TSV
Načtení souboru karta oddělených hodnot úložiště.

#### <a name="options"></a>Možnosti
- Poznámka
- Záhlaví
- Kódování souborů
- Řádky tak, aby přeskočil

### <a name="excel-xlsxlsx"></a>Aplikace Excel (.xls/.xlsx)
Přečtěte si jeden listu aplikace Excel soubor současně zadáním názvu list nebo číslo.

#### <a name="options"></a>Možnosti
- Název listu nebo číslo
- Záhlaví
- Řádky tak, aby přeskočil

### <a name="json-file"></a>Soubor JSON
Načtení souboru JSON úložiště. Soubor je "průmětu" na čtení.

#### <a name="options"></a>Možnosti
- Žádný

### <a name="parquet"></a>Parquet
Číst Parquet datové sady, buď jeden soubor nebo složku.

Parquet jako formátu můžou mít různé formy v úložišti. Pro menší sady dat se někdy používá .parquet jeden soubor. Různé knihovny Python podporovat čtení nebo zápis do jednoho .parquet souborů. V současné době přípravy dat Azure Machine Learning spoléhá na knihovně PyArrow Python pro čtení Parquet během místní interaktivní používání. Podporuje jeden .parquet soubory (Pokud napsané jako a nikoli jako součást větší datové sady), a také Parquet datových sad.

Datové sady Parquet je kolekce více než jeden soubor .parquet, z nichž každý představuje menší oddílu větší datové sady. Datové sady jsou obvykle obsažené ve složce a jsou výchozí formát výstupu parquet pro platformy, jako je například Spark a Hive.

>[!NOTE]
>Při čtení Parquet data, která je ve složce s více soubory .parquet, je nejbezpečnější a vyberte adresář pro čtení a **Parquet datové sady** možnost. Díky tomu PyArrow přečíst celou složku místo jednotlivých souborů. Tím se zajistí podpora pro čtení složitější způsoby Parquet ukládání na disku, jako je například vytváření oddílů složky.

Provádění Škálováním na více systémů využívá Spark Parquet čtení možnosti a podporuje jeden soubory, jakož i složek, podobně jako místní interaktivní použití.

#### <a name="options"></a>Možnosti
- Parquet datové sady. Tato možnost určuje, zda přípravu dat Azure Machine Learning rozšíří daný adresář a se pokusí přečíst každý soubor jednotlivě (nezaškrtnuté režim), nebo jestli zpracovává adresáři jako celé sady dat (vybraný režim). S vybraný režim PyArrow vybere nejlepší způsob, jak interpretovat soubory.


## <a name="locations"></a>Umístění
### <a name="local"></a>Místní
Místní pevný disk nebo namapované síťové umístění úložiště.

### <a name="sql-server"></a>SQL Server
Místní SQL Server, nebo Azure SQL database.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Objekt Blob úložiště Azure, který vyžaduje předplatné Azure.

