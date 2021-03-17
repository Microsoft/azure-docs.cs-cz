---
title: Přírůstkové kopírování dat ze zdrojového úložiště dat do cílového úložiště dat
description: Tyto kurzy vám ukážou, jak přírůstkově kopírovat data ze zdrojového úložiště dat do cílového úložiště dat. První zkopíruje data z jedné tabulky.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 7161fb30c8b445681b4cd577d8f8ac9fff5106df
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739241"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Přírůstkové načtení dat ze zdrojového úložiště dat do cílového úložiště dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V řešení integrace dat je přírůstkové (nebo rozdílové) načítání dat po počátečním úplném načtení dat často používaný scénář. Kurzy v této části vám ukážou různé způsoby načítání dat přírůstkově pomocí Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Rozdílové načítání dat z databáze pomocí meze
V tomto případě definujete mez ve zdrojové databázi. Mez je sloupec, který obsahuje časové razítko poslední aktualizace nebo narůstající klíč. Řešení rozdílového načítání načte změněná data mezi původní mezí a novou mezí. Pracovní postup pro tento přístup je znázorněn v následujícím diagramu: 

![Pracovní postup pro použití meze](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Podrobné pokyny najdete v následujících kurzech: 
- [Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Přírůstkové kopírování dat z více tabulek v instanci SQL Server do Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

V případě šablon se podívejte na tyto informace:
- [Rozdílové kopírování s kontrolní tabulkou](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Rozdílové načítání dat z databáze SQL pomocí technologie Change Tracking
Technologie Change Tracking je jednoduché řešení na SQL Serveru a ve službě Azure SQL Database, které poskytuje mechanismus efektivního sledování změn pro aplikace. Umožňuje aplikaci snadno identifikovat data, která byla vložena, aktualizována nebo odstraněna. 

Pracovní postup pro tento přístup je znázorněn v následujícím diagramu:

![Pracovní postup pro použití Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Podrobné pokyny najdete v následujícím kurzu: <br/>
- [Přírůstkové kopírování dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Načítání nových a změněných souborů pouze pomocí LastModifiedDate
Nové a změněné soubory můžete zkopírovat jenom pomocí LastModifiedDate do cílového úložiště. ADF bude kontrolovat všechny soubory ze zdrojového úložiště, použít filtr souborů podle jejich LastModifiedDate a zkopírovat pouze nový a aktualizovaný soubor od posledního času do cílového úložiště.  Počítejte s tím, že pokud chcete, aby soubor ADF kontroloval velké množství souborů, ale kopíruje jenom pár souborů na místo cíle   

Podrobné pokyny najdete v následujícím kurzu: <br/>
- [Přírůstkové kopírování nových a změněných souborů na základě LastModifiedDate ze služby Azure Blob Storage do Azure Blob Storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

V případě šablon se podívejte na tyto informace:
- [Kopírování nových souborů na základě LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Načítání nových souborů pouze pomocí dělené složky nebo názvu souboru.
Můžete kopírovat jenom nové soubory, ve kterých už je čas rozdělený na oddíly soubory nebo složky s timeslice informacemi jako součást názvu souboru nebo složky (například/yyyy/MM/DD/file.csv). Je to nejvíce výkonného přístupu pro přírůstkové načítání nových souborů. 

Podrobné pokyny najdete v následujícím kurzu: <br/>
- [Přírůstkové kopírování nových souborů na základě času v dělené složce nebo názvu souboru z úložiště objektů BLOB v Azure do Azure Blob Storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Další kroky
Přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
