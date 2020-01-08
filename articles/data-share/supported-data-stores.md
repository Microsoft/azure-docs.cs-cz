---
title: Podporovaná úložiště dat v Azure Data Share
description: Přečtěte si o úložištích dat, která jsou podporovaná pro použití Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438695"
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
| Průzkumník dat Azure | |[Omezená verze Preview](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Matice podpory úložiště dat

Azure Data Share nabízí flexibilitu uživatelů dat při rozhodování v úložišti dat, ve kterém se mají přijímat data. Například data, která jsou sdílena z Azure SQL Database, lze přijímat do Azure Data Lake Store Gen2, Azure SQL Database nebo Azure synapse Analytics. Zákazníci si můžou vybrat, v jakém formátu se mají data přijímat, když nakonfigurujete příchozí sdílenou složku. 

Následující tabulka obsahuje podrobnosti různých kombinací a možností, které příjemci dat mají při přijetí a konfiguraci jejich sdílení dat. Další informace o konfiguraci mapování datových sad naleznete v tématu [How to Configure DataSet Mapping](how-to-configure-mapping.md).

|  | Azure Blob Storage | Gen1 Azure SQL Data Lake | Gen2 Azure SQL Data Lake | Databáze SQL Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Databáze SQL Azure |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .
