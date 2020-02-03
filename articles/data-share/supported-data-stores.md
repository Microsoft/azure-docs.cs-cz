---
title: Podporovaná úložiště dat v Azure Data Share
description: Přečtěte si o úložištích dat, která jsou podporovaná pro použití Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 5d4b1282b0a08657aea6f8a13aae7ed1fe49079b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964205"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Podporovaná úložiště dat v Azure Data Share

Azure data Shared poskytuje otevřené a flexibilní sdílení dat, včetně možnosti sdílení z a do různých úložišť dat. Poskytovatelé dat můžou sdílet data z jednoho typu úložiště dat a jejich příjemci dat si můžou zvolit, do kterého úložiště dat se mají přijímat data. 

V tomto článku se seznámíte s bohatou sadou úložišť dat Azure, která jsou podporovaná v Azure Data Share. Můžete také najít informace o kombinacích úložišť dat, která mohou využívat poskytovatelé dat a spotřebitelé dat. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Jaká úložiště dat jsou podporovaná v Azure Data Share? 

Následující tabulka uvádí podporované zdroje dat pro sdílenou složku Azure. 

| Úložiště dat | Sdílení na základě snímků | Místní sdílení 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Databáze SQL Azure |Ve verzi Public Preview | |
| Azure synapse Analytics (dříve Azure SQL DW) |Ve verzi Public Preview | |
| Průzkumník dat Azure | |Ve verzi Public Preview |

## <a name="data-store-support-matrix"></a>Matice podpory úložiště dat

Azure Data Share nabízí flexibilitu uživatelů dat při rozhodování v úložišti dat, ve kterém se mají přijímat data. Například data, která jsou sdílena z Azure SQL Database, lze přijímat do Azure Data Lake Store Gen2, Azure SQL Database nebo Azure synapse Analytics. Zákazníci si můžou vybrat, v jakém formátu se mají data přijímat, když nakonfigurujete příchozí sdílenou složku. 

Následující tabulka obsahuje podrobnosti různých kombinací a možností, které příjemci dat mají při přijetí a konfiguraci jejich sdílení dat. Další informace o konfiguraci mapování datových sad naleznete v tématu [How to Configure DataSet Mapping](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Databáze SQL Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Databáze SQL Azure | ✓ | | ✓| ✓| ✓|
| Azure synapse Analytics (dříve Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Sdílení z účtu úložiště
Azure Data Share podporuje sdílení souborů, složek a systémů souborů z Azure Data Lake Gen1 a Azure Data Lake Gen2. Podporuje taky sdílení objektů blob, složek a kontejnerů z Azure Blob Storage. Když se složky sdílí ve sdílení založeném na snímcích, příjemce dat se může rozhodnout vytvořit úplnou kopii sdílených dat nebo využít možnost přírůstkového snímku a kopírovat jenom nové nebo aktualizované soubory. Existující soubory se stejným názvem budou přepsány.

## <a name="share-from-a-sql-based-source"></a>Sdílení ze zdroje založeného na jazyce SQL
Azure Data Share podporuje sdílení tabulek nebo zobrazení z Azure SQL Database a Azure synapse Analytics (dříve Azure SQL DW). Příjemce dat se může rozhodnout, že data přijměte do Azure Data Lake Storage Gen2 nebo v Azure Blob Storage jako soubor CSV nebo Parquet. Úplný snímek přepíše obsah cílového souboru. Datový příjemce taky může data přijmout do tabulky SQL. Pokud cílová tabulka SQL není k dispozici na straně příjemce dat, Azure Data Share vytvoří tabulku SQL se zdrojovým schématem. Úplný snímek připojí obsah zdrojové tabulky k cílové tabulce SQL. Přírůstkový snímek se momentálně nepodporuje.

## <a name="share-from-azure-data-explorer"></a>Sdílení z Azure Průzkumník dat
Azure Data Share podporuje možnost místního sdílení databází z clusterů Azure Průzkumník dat. Poskytovatel dat může sdílet na úrovni databáze nebo clusteru. Při sdílení na úrovni databáze bude příjemce dat moct přistupovat jenom ke konkrétním databázím, které sdílí poskytovatel dat. Při sdílení na úrovni clusteru má příjemce dat přístup ke všem databázím z clusteru poskytovatele, včetně všech budoucích databází vytvořených poskytovatelem dat.

Pro přístup ke sdíleným databázím musí mít příjemce dat svůj vlastní cluster Azure Průzkumník dat. Cluster Azure Průzkumník dat pro příjemce dat musí najít ve stejném datovém centru Azure jako cluster Azure Průzkumník dat poskytovatele dat. Když je navázán vztah sdílení, Azure Data Share vytvoří symbolický odkaz mezi clustery Průzkumník dat Azure pro poskytovatele a uživatele.

Azure Průzkumník dat podporuje dva režimy přijímání dat: Batch a streaming. Data přijatá ze služby Batch ve sdílené databázi se zobrazí během několika sekund po dobu několika minut na straně příjemce dat. Data přijatá ze streamování můžou na straně spotřebitele dat trvat až 24 hodin. 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .
