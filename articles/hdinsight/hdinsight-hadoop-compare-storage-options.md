---
title: Porovnání možností úložiště pro použití s clustery Azure HDInsight
description: Poskytuje přehled o typech úložišť a jejich spolupráci s Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: bfbe311d9768923eee8c1b0cc4f3b4ec1a7ad69a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186301"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porovnání možností úložiště pro použití s clustery Azure HDInsight

Při vytváření clusterů HDInsight si můžete vybrat mezi několika různými službami Azure Storage:

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

Tento článek obsahuje přehled těchto typů úložišť a jejich jedinečných funkcí.

## <a name="storage-types-and-features"></a>Typy a funkce úložiště

Následující tabulka shrnuje Azure Storage služby, které jsou podporovány v různých verzích služby HDInsight:

| Služba úložiště | Typ účtu | Typ oboru názvů | Podporované služby | Podporované úrovně výkonu | Podporované úrovně přístupu | Verze HDInsight | Typ clusteru |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Obecné účely v2 | Hierarchický (systém souborů) | Objekt blob | Standard | Horká, studená, archivní | 3.6 + | Vše kromě Spark 2,1 a 2,2|
|Azure Storage| Obecné účely v2 | Objekt | Objekt blob | Standard | Horká, studená, archivní | 3.6 + | Všechny |
|Azure Storage| Obecné účely v1 | Objekt | Objekt blob | Standard | – | Všechny | Všechny |
|Azure Storage| Blob Storage * * | Objekt | Objekt blob bloku | Standard | Horká, studená, archivní | Všechny | Všechny |
|Azure Data Lake Storage Gen1| – | Hierarchický (systém souborů) | – | – | – | jenom 3,6 | Všechny kromě adaptérů HBA |

* * Pro clustery HDInsight může být pouze sekundární účty úložiště typu BlobStorage a objekt blob stránky není podporovanou možností úložiště.

Další informace o Azure Storage typech účtů najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md) .

Další informace o úrovních přístupu Azure Storage najdete v tématu [úložiště objektů BLOB v Azure: Premium (Preview), horké, studené a archivní úrovně úložiště.](../storage/blobs/storage-blob-storage-tiers.md)

Clustery můžete vytvářet pomocí kombinací služeb pro primární a volitelné sekundární úložiště. Následující tabulka shrnuje konfigurace úložiště clusteru, které jsou aktuálně podporované v HDInsight:

| Verze HDInsight | Primární úložiště | Sekundární úložiště | Podporuje se |
|---|---|---|---|
| 3,6 & 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Ano |
| 3,6 & 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Data Lake Storage Gen2 | Ne |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Ano |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Ano |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Ne |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ano |
| 3,6 | Data Lake Storage Gen1 | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Ano |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Ne |
| 4.0 | Data Lake Storage Gen1 | Všechny | Ne |
| 4.0 | Pro obecné účely V1, Pro obecné účely v2 | Data Lake Storage Gen1 | Ne |

* = Může to být jeden nebo několik Data Lake Storage Gen2 účtů, pokud všechna nastavení používají stejnou spravovanou identitu pro přístup k clusteru.

> [!NOTE]
> Data Lake Storage Gen2 primární úložiště není pro clustery Spark 2,1 nebo 2,2 podporováno.

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Storage](./overview-azure-storage.md)
* [Přehled služby Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Přehled služby Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
