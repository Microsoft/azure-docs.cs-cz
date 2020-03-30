---
title: Podporovaná úložiště dat ve službě Azure Data Share
description: Přečtěte si o úložištích dat, která jsou podporovaná pro použití služby Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501802"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Podporovaná úložiště dat ve službě Azure Data Share

Azure Data Share poskytuje otevřené a flexibilní sdílení dat, včetně možnosti sdílení z různých úložišť dat a do nich. Zprostředkovatelé dat mohou sdílet data z jednoho typu úložiště dat a jejich spotřebitelé dat si mohou vybrat, do kterého úložiště dat mají přijímat data. 

V tomto článku se dozvíte o bohaté sadě úložišť dat Azure, které jsou podporované ve službě Azure Data Share. Můžete také najít informace o kombinacích úložišť dat, které mohou využívat poskytovatelé dat a spotřebitelé dat. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Jaká úložiště dat jsou ve službě Azure Data Share podporovaná? 

Níže uvedená tabulka podrobně popisuje podporované zdroje dat pro Azure Data Share. 

| Úložiště dat | Sdílení založené na snímku | Sdílení na místě 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Veřejná verze Preview | |
| Azure Synapse Analytics (dříve Azure SQL DW) |Veřejná verze Preview | |
| Průzkumník dat Azure | |Veřejná verze Preview |

## <a name="data-store-support-matrix"></a>Matice podpory úložiště dat

Azure Data Share nabízí spotřebitelům dat flexibilitu při rozhodování o úložišti dat pro přijímání dat do. Například data sdílená z Azure SQL Database můžete přijímat do Azure Data Lake Store Gen2, Azure SQL Database nebo Azure Synapse Analytics. Zákazníci si mohou při konfiguraci přijaté sdílené složky dat vybrat, ve kterém formátu mají přijímat data. 

Níže uvedená tabulka podrobně popisuje různé kombinace a volby, které mají spotřebitelé dat při přijímání a konfiguraci jejich sdílení dat. Další informace o konfiguraci mapování datových sad naleznete v [tématu konfigurace mapování datových sad](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (dříve Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Sdílení z účtu úložiště
Azure Data Share podporuje sdílení souborů, složek a souborových systémů z Azure Data Lake Gen1 a Azure Data Lake Gen2. Podporuje také sdílení objektů BLOB, složek a kontejnerů z Azure Blob Storage. Aktuálně je podporován pouze objekt blob bloku. Když jsou složky sdíleny v sdílení založeném na snímcích, může se příjemce dat rozhodnout vytvořit úplnou kopii dat sdílené složky nebo využít možnosti přírůstkového snímku ke kopírování pouze nových nebo aktualizovaných souborů. Existující soubory se stejným názvem budou přepsány.

## <a name="share-from-a-sql-based-source"></a>Sdílení ze zdroje založeného na SQL
Azure Data Share podporuje sdílení tabulek nebo zobrazení z Azure SQL Database a Azure Synapse Analytics (dříve Azure SQL DW). Spotřebitelé dat můžete zvolit přijetí dat do Azure Data Lake Store Gen2 nebo Azure Blob Storage jako csv nebo parketový soubor. Všimněte si, že ve výchozím nastavení jsou formáty souborů csv. Spotřebitel dat se může v případě potřeby rozhodnout pro příjem dat ve formátu parket. To lze provést v nastavení mapování datové sady při příjmu dat. 

Při přijímání dat do Azure Data Lake Store Gen2 nebo Azure Blob Storage, úplné snímky přepsat obsah cílového souboru. 

Příjemce dat se může rozhodnout pro příjem dat do tabulky podle svého výběru. V tomto scénáři pokud cílová tabulka ještě neexistuje, Azure Data Share vytvoří tabulku SQL se zdrojovým schématem. Pokud cílová tabulka již existuje se stejným názvem, bude vynechána a přepsána nejnovějším úplným snímkem. Při mapování cílové tabulky lze zadat alternativní schéma a název tabulky. Přírůstkové snímky nejsou aktuálně podporovány. 

Sdílení ze zdrojů založených na SQL má předpoklady související s pravidly brány firewall a oprávněními. Podrobnosti naleznete v části s požadavky na [sdílení dat.](share-your-data.md)

## <a name="share-from-azure-data-explorer"></a>Sdílení z Průzkumníka dat Azure
Azure Data Share podporuje možnost sdílení databází na místě z clusterů Azure Data Explorer. Zprostředkovatel dat může sdílet na úrovni databáze nebo clusteru. Při sdílení na úrovni databáze bude mít příjemce dat přístup pouze ke konkrétním databázím sdíleným poskytovatelem dat. Při sdílení na úrovni clusteru může příjemce dat přistupovat ke všem databázím z clusteru poskytovatele, včetně všech budoucích databází vytvořených poskytovatelem dat.

Pro přístup ke sdíleným databázím musí mít příjemce dat vlastní cluster Azure Data Explorer. Cluster Azure Data Explorer příjemce dat potřebuje najít ve stejném datovém centru Azure jako cluster Azure Data Explorer poskytovatele dat. Při vytváření vztahu sdílení dat Azure vytvoří symbolické propojení mezi poskytovatelem a clustery Azure Data Explorer spotřebitele.

Azure Data Explorer podporuje dva režimy ingestování dat: dávkové a streamování. Data přijatá z dávky ve sdílené databázi se zobrazí mezi několika sekund a několik minut na straně příjemce dat. Data přijatá z streamování může trvat až 24 hodin, než se zobrazí na straně spotřebitele dat. 

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak začít sdílet data, [pokračujte ve sdílení dat](share-your-data.md) kurzu.
