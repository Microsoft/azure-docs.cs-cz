---
title: Postupně kopírujte data
description: Tyto kurzy vám ukážou, jak přírůstkově kopírovat data ze zdrojového úložiště dat do cílového úložiště dat. První zkopíruje data z jedné tabulky.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: e87f8d648a617aab24008bd63596db31dd24ab68
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74926439"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Přírůstkové načtení dat ze zdrojového úložiště dat do cílového úložiště dat

V řešení integrace dat je přírůstkové (nebo rozdílové) načítání dat po počátečním úplném načtení dat často používaný scénář. Kurzy v této části vám ukážou různé způsoby načítání dat přírůstkově pomocí Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Rozdílové načítání dat z databáze pomocí vodoznaku
V tomto případě definujete mez ve zdrojové databázi. Mez je sloupec, který obsahuje časové razítko poslední aktualizace nebo narůstající klíč. Řešení rozdílového načítání načte změněná data mezi původní mezí a novou mezí. Pracovní postup pro tento přístup je znázorněn v následujícím diagramu: 

![Pracovní postup pro použití meze](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Podrobné pokyny najdete v následujících kurzech: 
- [Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Přírůstkové kopírování dat z více tabulek v místním SQL Serveru do služby Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

Šablony naleznete v následujících tématech:
- [Rozdílové kopírování s kontrolní tabulkou](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Rozdílové načítání dat z databáze SQL DB pomocí technologie sledování změn
Technologie Change Tracking je jednoduché řešení na SQL Serveru a ve službě Azure SQL Database, které poskytuje mechanismus efektivního sledování změn pro aplikace. Umožňuje aplikaci snadno identifikovat data, která byla vložena, aktualizována nebo odstraněna. 

Pracovní postup pro tento přístup je znázorněn v následujícím diagramu:

![Pracovní postup pro použití Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Podrobné pokyny najdete v následujícím kurzu: <br/>
- [Přírůstkové kopírování dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Načítání nových a změněných souborů pouze pomocí lastmodifieddate
Nové a změněné soubory můžete zkopírovat pouze pomocí lastmodifieddate do cílového úložiště. ADF prohledá všechny soubory ze zdrojového úložiště, použije filtr souborů podle data LastModifiedDate a zkopíruje nový a aktualizovaný soubor pouze od posledního okamžiku do cílového úložiště.  Mějte prosím na paměti, pokud necháte ADF skenovat obrovské množství souborů, ale pouze kopírovat několik souborů na místo určení, stále byste očekávat, že dlouhá doba trvání v důsledku skenování souborů je časově náročné stejně.   

Podrobné pokyny najdete v následujícím kurzu: <br/>
- [Přírůstkově kopírovat nové a změněné soubory na základě LastModifiedDate z úložiště objektů blob Azure do úložiště objektů blob Azure](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Šablony naleznete v následujících tématech:
- [Kopírování nových souborů na základě LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Načítání nových souborů pouze pomocí časem rozdělené složky nebo názvu souboru.
Můžete kopírovat pouze nové soubory, kde soubory nebo složky již byly časově rozděleny s informacemi o timeslice jako součást názvu souboru nebo složky (například /yyyy/mm/dd/file.csv). Jedná se o nejvýkonnější přístup pro postupné načítání nových souborů. 

Podrobné pokyny najdete v následujícím kurzu: <br/>
- [Přírůstkově kopírujte nové soubory na základě časově rozdělené složky nebo názvu souboru z úložiště objektů Blob Azure do úložiště objektů Blob Azure](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Další kroky
Přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
