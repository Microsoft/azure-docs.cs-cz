---
title: Podporovaná úložiště dat ve službě Azure Data Share
description: Přečtěte si o úložištích dat, která jsou podporovaná pro použití Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ddd586bf9a88b1246fcf20fc19493f5003bbb530
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151747"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Podporovaná úložiště dat ve službě Azure Data Share

Azure data Shared poskytuje otevřené a flexibilní sdílení dat, včetně možnosti sdílení z a do různých úložišť dat. Poskytovatelé dat můžou sdílet data z jednoho typu úložiště dat a jejich příjemci dat si můžou zvolit, do kterého úložiště dat se mají přijímat data. 

V tomto článku se seznámíte s bohatou sadou úložišť dat Azure, která jsou podporovaná v Azure Data Share. Můžete také najít informace o kombinacích úložišť dat, která mohou využívat poskytovatelé dat a spotřebitelé dat. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Jaká úložiště dat jsou podporovaná v Azure Data Share? 

Následující tabulka uvádí podporované zdroje dat pro sdílenou složku Azure. 

| Úložiště dat | Sdílení na základě snímků | Místní sdílení 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Verze Public Preview | |
| Azure synapse Analytics (dříve Azure SQL DW) |Verze Public Preview | |
| Průzkumník dat Azure | |✓ |

## <a name="data-store-support-matrix"></a>Matice podpory úložiště dat

Azure Data Share nabízí flexibilitu uživatelů dat při rozhodování v úložišti dat, ve kterém se mají přijímat data. Například data, která jsou sdílena z Azure SQL Database, lze přijímat do Azure Data Lake Store Gen2, Azure SQL Database nebo Azure synapse Analytics. Zákazníci si můžou vybrat, v jakém formátu se mají data přijímat, když nakonfigurujete příchozí sdílenou složku. 

Následující tabulka obsahuje podrobnosti různých kombinací a možností, které příjemci dat mají při přijetí a konfiguraci jejich sdílení dat. Další informace o konfiguraci mapování datových sad naleznete v tématu [How to Configure DataSet Mapping](how-to-configure-mapping.md).

| Úložiště dat | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics | Průzkumník dat Azure
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure synapse Analytics (dříve Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Průzkumník dat Azure |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Sdílení z účtu úložiště
Azure Data Share podporuje sdílení souborů, složek a systémů souborů z Azure Data Lake Gen1 a Azure Data Lake Gen2. Podporuje taky sdílení objektů blob, složek a kontejnerů z Azure Blob Storage. V tuto chvíli se podporuje jenom objekt blob bloku. Když jsou systémy souborů, kontejnery nebo složky sdíleny ve sdílení založeném na snímcích, příjemce dat si může vytvořit úplnou kopii sdílených dat nebo využít možnost přírůstkového snímku pro kopírování pouze nových nebo aktualizovaných souborů. Přírůstkový snímek je založen na době poslední změny souborů. Existující soubory se stejným názvem budou přepsány.

Podrobnosti najdete [v tématu sdílení a příjem dat z Azure Blob Storage a Azure Data Lake Storage](how-to-share-from-storage.md) .

## <a name="share-from-a-sql-based-source"></a>Sdílení ze zdroje založeného na jazyce SQL
Azure Data Share podporuje sdílení tabulek nebo zobrazení z Azure SQL Database a Azure synapse Analytics (dříve Azure SQL DW). Příjemci dat se můžou rozhodnout, že data přijměte do Azure Data Lake Storage Gen2 nebo Azure Blob Storage jako soubor CSV nebo Parquet, a také do Azure SQL Database a Azure synapse Analytics jako tabulky.

Při přijímání dat do Azure Data Lake Store Gen2 nebo Azure Blob Storage zapíše úplné snímky obsah cílového souboru, pokud už existuje.
Když se do tabulky přijímají data a cílová tabulka ještě neexistuje, vytvoří Azure Data Share tabulku SQL se zdrojovým schématem. Pokud cílová tabulka již existuje se stejným názvem, bude vynechána a přepíše se nejnovějším úplným snímkem. Přírůstkové snímky se aktuálně nepodporují.

Podrobnosti najdete [v tématu sdílení a příjem dat z Azure SQL Database a Azure synapse Analytics](how-to-share-from-sql.md) .

## <a name="share-from-azure-data-explorer"></a>Sdílení z Azure Data Exploreru
Azure Data Share podporuje možnost místního sdílení databází z clusterů Azure Průzkumník dat. Poskytovatel dat může sdílet na úrovni databáze nebo clusteru. Při sdílení na úrovni databáze bude příjemce dat moct přistupovat jenom ke konkrétním databázím, které sdílí poskytovatel dat. Při sdílení na úrovni clusteru má příjemce dat přístup ke všem databázím z clusteru poskytovatele, včetně všech budoucích databází vytvořených poskytovatelem dat.

Pro přístup ke sdíleným databázím musí mít příjemce dat svůj vlastní cluster Azure Průzkumník dat. Cluster Azure Průzkumník dat pro příjemce dat musí najít ve stejném datovém centru Azure jako cluster Azure Průzkumník dat poskytovatele dat. Když je navázán vztah sdílení, Azure Data Share vytvoří symbolický odkaz mezi clustery Průzkumník dat Azure pro poskytovatele a uživatele. Data ingestovaná pomocí dávkového režimu do zdrojového clusteru Azure Průzkumník dat se budou zobrazovat v cílovém clusteru během několika sekund až po několik minut.

Podrobnosti najdete [v tématu sdílení a příjem dat z Azure Průzkumník dat](/azure/data-explorer/data-share) . 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .
