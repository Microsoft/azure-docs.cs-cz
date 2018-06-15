---
title: Přírůstkové kopírování dat pomocí Azure Data Factory | Microsoft Docs
description: Tyto kurzy vám ukážou, jak přírůstkově kopírovat data ze zdrojového úložiště dat do cílového úložiště dat. První zkopíruje data z jedné tabulky.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 7265e20bf89cc9dbc1c44e568e779f2d13f87685
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30174725"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Přírůstkové načtení dat ze zdrojového úložiště dat do cílového úložiště dat

V řešení integrace dat je přírůstkové (nebo rozdílové) načítání dat po počátečním úplném načtení dat často používaný scénář. Kurzy v této části vám ukážou různé způsoby načítání dat přírůstkově pomocí Azure Data Factory verze 2.

## <a name="delta-data-loading-by-using-a-watermark"></a>Rozdílové načítání dat pomocí meze
V tomto případě definujete mez ve zdrojové databázi. Mez je sloupec, který obsahuje časové razítko poslední aktualizace nebo narůstající klíč. Řešení rozdílového načítání načte změněná data mezi původní mezí a novou mezí. Pracovní postup pro tento přístup je znázorněn v následujícím diagramu: 

![Pracovní postup pro použití meze](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Podrobné pokyny najdete v následujících kurzech: 

- [Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Přírůstkové kopírování dat z více tabulek v místním SQL Serveru do služby Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>Rozdílové načítání dat pomocí technologie Change Tracking
Technologie Change Tracking je jednoduché řešení na SQL Serveru a ve službě Azure SQL Database, které poskytuje mechanismus efektivního sledování změn pro aplikace. Umožňuje aplikaci snadno identifikovat data, která byla vložena, aktualizována nebo odstraněna. 

Pracovní postup pro tento přístup je znázorněn v následujícím diagramu:

![Pracovní postup pro použití Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Podrobné pokyny najdete v následujícím kurzu: <br/>
[Přírůstkové kopírování dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>Další kroky
Přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat z jedné tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage](tutorial-incremental-copy-powershell.md)
