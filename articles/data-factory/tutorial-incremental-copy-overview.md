---
title: Přírůstkové kopírování dat pomocí Azure Data Factory | Microsoft Docs
description: Tyto kurzy vám ukážou, jak přírůstkově kopírovat data ze zdrojového úložiště dat do cílového úložiště dat. První zkopíruje data z jedné tabulky.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 1d8b31e55a2a230385730c924d3e6bcc6072e7ea
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520445"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Přírůstkové načtení dat ze zdrojového úložiště dat do cílového úložiště dat

V řešení integrace dat je přírůstkové (nebo rozdílové) načítání dat po počátečním úplném načtení dat často používaný scénář. Kurzy v této části vám ukážou různé způsoby načítání dat přírůstkově pomocí Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Rozdílové načítání dat z databáze pomocí meze
V tomto případě definujete mez ve zdrojové databázi. Mez je sloupec, který obsahuje časové razítko poslední aktualizace nebo narůstající klíč. Řešení rozdílového načítání načte změněná data mezi původní mezí a novou mezí. Pracovní postup pro tento přístup je znázorněn v následujícím diagramu: 

![Pracovní postup pro použití meze](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Podrobné pokyny najdete v následujících kurzech: 

- [Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Přírůstkové kopírování dat z více tabulek v místním SQL Serveru do služby Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Rozdílové načítání dat z databáze SQL pomocí technologie Change Tracking
Technologie Change Tracking je jednoduché řešení na SQL Serveru a ve službě Azure SQL Database, které poskytuje mechanismus efektivního sledování změn pro aplikace. Umožňuje aplikaci snadno identifikovat data, která byla vložena, aktualizována nebo odstraněna. 

Pracovní postup pro tento přístup je znázorněn v následujícím diagramu:

![Pracovní postup pro použití Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Podrobné pokyny najdete v následujícím kurzu: <br/>
[Přírůstkové kopírování dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Načítají se nové a změněné soubory pouze pomocí LastModifiedDate
Nové a změněné soubory můžete kopírovat pouze pomocí LastModifiedDate do cílového úložiště. ADF bude kontrolovat všechny soubory ze zdrojového úložiště, použijte filtr souboru podle jejich LastModifiedDate a kopírovat pouze nových a aktualizovaných souborů od posledního do cílového úložiště.  Je třeba upozornit, že pokud nechat ADF kontroly obrovské množství souborů, ale pouze několik souborů zkopírovat do cíle, stále byste očekávali dlouhá doba trvání kvůli skenování souboru je časově náročné i.   

Podrobné pokyny najdete v následujícím kurzu: <br/>
[Přírůstkové kopírování nové a změněné soubory podle LastModifiedDate z úložiště objektů Blob Azure do Azure Blob storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Načítají se nové soubory pouze pomocí času rozdělený název složky nebo souboru.
Můžete zkopírovat nového pouze pro soubory, kde soubory nebo složky se už čas rozdělený do oddílů pomocí timeslice informace jako součást názvu souboru nebo složky (například /yyyy/mm/dd/file.csv). Je většina přístup výkonu u nových souborů přírůstkové načítání. 

Podrobné pokyny najdete v následujícím kurzu: <br/>
[Přírůstkové kopírování nových souborů na základě čas dělené složka nebo název souboru z úložiště objektů Blob Azure do Azure Blob storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Další postup
Přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
