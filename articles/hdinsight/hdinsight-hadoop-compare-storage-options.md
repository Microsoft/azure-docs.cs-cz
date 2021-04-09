---
title: Porovnání možností úložiště pro použití s clustery Azure HDInsight
description: Poskytuje přehled o typech úložišť a jejich spolupráci s Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b6dd0fd95280a65615d38ab11a2f9814f58586f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945844"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porovnání možností úložiště pro použití s clustery Azure HDInsight

Při vytváření clusterů HDInsight si můžete vybrat mezi několika různými službami Azure Storage:

* [Úložiště objektů BLOB v Azure se službou HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2 se službou HDInsight](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1 se službou HDInsight](./overview-data-lake-storage-gen1.md)

Tento článek obsahuje přehled těchto typů úložišť a jejich jedinečných funkcí.

## <a name="storage-types-and-features"></a>Typy a funkce úložiště

Následující tabulka shrnuje Azure Storage služby, které jsou podporovány v různých verzích služby HDInsight:

| Služba úložiště | Typ účtu | Typ oboru názvů | Podporované služby | Podporované úrovně výkonu | Podporované úrovně přístupu | Verze HDInsight | Typ clusteru |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Obecné účely v2 | Hierarchický (systém souborů) | Objekt blob | Standard | Horká, studená, archivní | 3.6 + | Vše kromě Spark 2,1 a 2,2|
|Azure Storage| Obecné účely v2 | Objekt | Objekt blob | Standard | Horká, studená, archivní | 3.6 + | Vše |
|Azure Storage| Obecné účely v1 | Objekt | Objekt blob | Standard | – | Vše | Vše |
|Azure Storage| Blob Storage * * | Objekt | Objekt blob bloku | Standard | Horká, studená, archivní | Vše | Vše |
|Azure Data Lake Storage Gen1| – | Hierarchický (systém souborů) | N/A | N/A | N/A | jenom 3,6 | Všechny kromě adaptérů HBA |
|Azure Storage| Objekt blob bloku| Objekt | Objekt blob bloku | Premium | –| 3.6 + | Jenom adaptéry HBA s akcelerovanými zápisy|
|Azure Data Lake Storage Gen2| Objekt blob bloku| Hierarchický (systém souborů) | Objekt blob bloku | Premium | –| 3.6 + | Jenom adaptéry HBA s akcelerovanými zápisy|

* * Pro clustery HDInsight může být pouze sekundární účty úložiště typu BlobStorage a objekt blob stránky není podporovanou možností úložiště.

Další informace o Azure Storage typech účtů najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md) .

Další informace o úrovních přístupu Azure Storage najdete v tématu [úložiště objektů BLOB v Azure: Premium (Preview), horké, studené a archivní úrovně úložiště.](../storage/blobs/storage-blob-storage-tiers.md)

Clustery můžete vytvářet pomocí kombinací služeb pro primární a volitelné sekundární úložiště. Následující tabulka shrnuje konfigurace úložiště clusteru, které jsou aktuálně podporované v HDInsight:

| Verze HDInsight | Primární úložiště | Sekundární úložiště | Podporováno |
|---|---|---|---|
| 3,6 & 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Yes |
| 3,6 & 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Data Lake Storage Gen2 | No |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Yes |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Yes |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | No |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Yes |
| 3,6 | Data Lake Storage Gen1 | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Yes |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | No |
| 4,0 | Data Lake Storage Gen1 | Všechny | No |
| 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Data Lake Storage Gen1 | No |

* = Může to být jeden nebo několik Data Lake Storage Gen2, pokud všechna nastavení používají stejnou spravovanou identitu pro přístup k clusteru.

> [!NOTE]
> Data Lake Storage Gen2 primární úložiště není pro clustery Spark 2,1 nebo 2,2 podporováno.

## <a name="data-replication"></a>Replikace dat

Azure HDInsight neukládá zákaznická data. Primárním prostředkem úložiště pro cluster jsou přidružené účty úložiště. Cluster můžete připojit k existujícímu účtu úložiště nebo vytvořit nový účet úložiště během procesu vytváření clusteru. Pokud se vytvoří nový účet, vytvoří se jako účet místně redundantního úložiště (LRS) a bude vyhovovat požadavkům na umístění dat v regionu, včetně těch, které jsou uvedené v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net).

Můžete ověřit, jestli je HDInsight správně nakonfigurovaný tak, aby ukládal data v jedné oblasti tím, že zajistí, že účet úložiště přidružený k vašemu HDInsight je LRS nebo jiná možnost úložiště, kterou jste uvedli v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net).
 
## <a name="next-steps"></a>Další kroky

* [Přehled Azure Storage v HDInsight](./overview-azure-storage.md)
* [Přehled Azure Data Lake Storage Gen1 v HDInsight](./overview-data-lake-storage-gen1.md)
* [Přehled Azure Data Lake Storage Gen2 v HDInsight](./overview-data-lake-storage-gen2.md)
* [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
