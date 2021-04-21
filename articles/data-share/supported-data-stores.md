---
title: Podporovaná úložiště dat ve službě Azure Data Share
description: Přečtěte si o úložištích dat podporovaných pro použití v Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812619"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Podporovaná úložiště dat ve službě Azure Data Share

Azure data Shared poskytuje otevřené a flexibilní sdílení dat, včetně možnosti sdílení z a do různých úložišť dat. Poskytovatelé dat můžou sdílet data z jednoho typu úložiště dat a příjemci dat můžou zvolit úložiště dat pro příjem dat. 

V tomto článku se seznámíte s bohatou sadou úložišť dat Azure, která podporuje Azure Data Share. Naučíte se také, jak můžou poskytovatelé dat a příjemci dat kombinovat různá úložiště dat. 

## <a name="supported-data-stores"></a>Podporované zdroje dat 

V následující tabulce jsou popsána úložiště dat, která podporuje sdílená data Azure. 

| Úložiště dat | Sdílení na základě úplných snímků | Sdílení na základě přírůstkových snímků | Probíhá sdílení 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (dříve Azure SQL Data Warehouse) |✓ | | |
| Vyhrazený fond SQL ve službě Azure synapse Analytics (pracovní prostor) |✓ | | |
| Průzkumník dat Azure | | |✓ |

## <a name="data-store-support-matrix"></a>Matice podpory úložiště dat

Azure Data Share umožňuje příjemcům dat zvolit úložiště dat pro příjem dat. Například data, která jsou sdílena z Azure SQL Database, lze přijímat do služby Azure Data Lake Storage Gen2, Azure SQL Database nebo Azure synapse Analytics. Když si zákazníci nastavili přijímající sdílenou složku, můžou zvolit formát pro příjem těchto dat. 

Následující tabulka vysvětluje kombinace a možnosti, které mohou uživatelé dat vybrat při přijímání a konfiguraci sdílení dat. Další informace najdete v tématu [Konfigurace mapování datových sad](how-to-configure-mapping.md).

| Úložiště dat | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (dříve SQL Data Warehouse) | Vyhrazený fond SQL synapse Analytics (pracovní prostor) | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (dříve SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Vyhrazený fond SQL synapse Analytics (pracovní prostor) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Sdílení z účtu úložiště
Azure Data Share podporuje sdílení souborů, složek a systémů souborů z Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2. Podporuje taky sdílení objektů blob, složek a kontejnerů z Azure Blob Storage. Můžete sdílet objekty blob bloku, Append nebo Page a přijímají se jako objekty blob bloku.

Když se v rámci sdílení na základě snímků sdílí systémy souborů, kontejnery nebo složky, můžou si příjemci dat vytvořit úplnou kopii sdílených dat. Nebo můžou použít funkci přírůstkového snímku ke kopírování jenom nových souborů nebo aktualizovaných souborů. 

Přírůstkový snímek je založen na době poslední změny souborů. Existující soubory, které mají stejný název jako soubory v přijatých datech, se přepíší ve snímku. Soubory, které jsou odstraněny ze zdroje, nejsou v cíli smazány. 

Další informace najdete v tématu [sdílení a příjem dat z Azure Blob Storage a Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Sdílení ze zdroje založeného na jazyce SQL
Azure Data Share podporuje sdílení tabulek i zobrazení z Azure SQL Database a analýzy Azure synapse (dřív Azure SQL Data Warehouse). Podporuje sdílení tabulek z vyhrazeného fondu SQL Azure synapse Analytics (pracovní prostor). Sdílení z fondu SQL bez serveru Azure synapse Analytics (v pracovním prostoru) není aktuálně podporováno. 

Příjemci dat se můžou rozhodnout, že data přijměte do Azure Data Lake Storage Gen2 nebo Azure Blob Storage jako soubor CSV nebo soubor Parquet. Můžou také přijímat data jako tabulky do Azure SQL Database a Azure synapse Analytics.

Když příjemci přijímají data do Azure Data Lake Storage Gen2 nebo Azure Blob Storage, budou úplné snímky přepsat obsah cílového souboru, pokud soubor již existuje. Po přijetí dat do tabulky a cílová tabulka už neexistuje, služba Azure Data Share vytvoří tabulku SQL pomocí zdrojového schématu. Pokud cílová tabulka již existuje a má stejný název, je vynechána a přepsána aktuálním úplným snímkem. Přírůstkové snímky se v současné době nepodporují.

Další informace najdete v tématech [sdílení a příjem dat z Azure SQL Database a Azure synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Sdílet z Průzkumník dat
Azure Data Share podporuje možnost místního sdílení databází z clusterů Azure Průzkumník dat. Poskytovatel dat může sdílet na úrovni databáze nebo clusteru. 

Když jsou data sdílena na úrovni databáze, příjemci dat mají přístup pouze k databázím, které jsou sdíleny poskytovatelem dat. Když poskytovatel sdílí data na úrovni clusteru, příjemci dat mají přístup ke všem databázím z clusteru poskytovatele, včetně všech budoucích databází, které poskytovatel dat vytvoří.

Pro přístup ke sdíleným databázím potřebují spotřebitelé dat svůj vlastní cluster Azure Průzkumník dat. Cluster se musí nacházet ve stejném datovém centru Azure jako cluster Azure Průzkumník dat poskytovatele dat. 

Když je vytvořen vztah sdílení, Azure Data Share vytvoří symbolický odkaz mezi clusterem poskytovatele a clusterem zákazníka. Data, která se ingestují do zdrojového clusteru pomocí dávkového režimu, se během několika minut objeví v cílovém clusteru.

Další informace najdete v tématu [sdílení a příjem dat z Azure Průzkumník dat](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .
