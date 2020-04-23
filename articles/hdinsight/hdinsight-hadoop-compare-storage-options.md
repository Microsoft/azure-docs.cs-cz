---
title: Porovnání možností úložiště pro použití s clustery Azure HDInsight
description: Poskytuje přehled typů úložišť a jejich fungování s Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869844"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porovnání možností úložiště pro použití s clustery Azure HDInsight

Při vytváření clusterů HDInsight si můžete vybrat mezi několika různými službami úložiště Azure:

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

Tento článek obsahuje přehled těchto typů úložišť a jejich jedinečné funkce.

## <a name="storage-types-and-features"></a>Typy a funkce úložiště

Následující tabulka shrnuje služby Azure Storage, které jsou podporované různými verzemi HDInsightu:

| Služba úložiště | Typ účtu | Typ oboru názvů | Podporované služby | Podporované úrovně výkonu | Podporované úrovně přístupu | Verze HDInsight | Typ clusteru |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Pro všeobecné účely V2 | Hierarchický (souborový systém) | Objekt blob | Standard | Horké, Cool, Archiv | 3.6+ | Všechny kromě Spark 2.1 a 2.2|
|Azure Storage| Pro všeobecné účely V2 | Objekt | Objekt blob | Standard | Horké, Cool, Archiv | 3.6+ | Všechny |
|Azure Storage| Pro všeobecné účely V1 | Objekt | Objekt blob | Standard | – | Všechny | Všechny |
|Azure Storage| Úložiště objektů blob** | Objekt | Objekt blob bloku | Standard | Horké, Cool, Archiv | Všechny | Všechny |
|Azure Data Lake Storage Gen1| – | Hierarchický (souborový systém) | – | – | – | 3.6 Pouze | Všechny kromě HBase |

**Pro clustery HDInsight mohou být pouze účty sekundárního úložiště typu BlobStorage a Objekt blob stránky není podporovanou možností úložiště.

Další informace o typech účtů Azure Storage najdete v tématu [Přehled účtu úložiště Azure.](../storage/common/storage-account-overview.md)

Další informace o úrovních přístupu k Azure Storage najdete v [tématu Úložiště objektů blob Azure: Premium (preview), Hot, Cool a Archive úrovně úložiště](../storage/blobs/storage-blob-storage-tiers.md)

Clustery můžete vytvořit pomocí kombinací služeb pro primární a volitelné sekundární úložiště. Následující tabulka shrnuje konfigurace úložiště clusteru, které jsou aktuálně podporovány v HDInsight:

| Verze HDInsight | Primární úložiště | Sekundární úložiště | Podporuje se |
|---|---|---|---|
| 3,6 & 4,0 | Obecný účel V1, obecný účel V2 | Obecné použití V1, obecné účely V2, BlobStorage(objekty blob bloku) | Ano |
| 3,6 & 4,0 | Obecný účel V1, obecný účel V2 | Data Lake Storage Gen2 | Ne |
| 3,6 & 4,0 | Úložiště datových jezer Gen2* | Data Lake Storage Gen2 | Ano |
| 3,6 & 4,0 | Úložiště datových jezer Gen2* | Obecné použití V1, obecné účely V2, BlobStorage(objekty blob bloku) | Ano |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Ne |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ano |
| 3.6 | Data Lake Storage Gen1 | Obecné použití V1, obecné účely V2, BlobStorage(objekty blob bloku) | Ano |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Ne |
| 4.0 | Data Lake Storage Gen1 | Všechny | Ne |
| 4.0 | Obecný účel V1, obecný účel V2 | Data Lake Storage Gen1 | Ne |

*=Může se jedná o jeden nebo více účtů Data Lake Storage Gen2, pokud jsou všechny nastaveny tak, aby používaly stejnou spravovanou identitu pro přístup ke clusteru.

> [!NOTE]
> Primární úložiště Data Lake Storage Gen2 není podporováno pro clustery Spark 2.1 nebo 2.2.

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Storage](./overview-azure-storage.md)
* [Přehled úložiště datového jezera Azure1 – přehled](./overview-data-lake-storage-gen1.md)
* [Přehled úložiště datového jezera Azure 2](./overview-data-lake-storage-gen2.md)
* [Úvod do úložiště datových jezer Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
