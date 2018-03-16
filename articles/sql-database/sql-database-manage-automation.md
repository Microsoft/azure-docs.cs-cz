---
title: "Správa databází Azure SQL pomocí Azure Automation | Microsoft Docs"
description: "Další informace o používání služby Azure Automation pro správu databáze Azure SQL ve velkém měřítku."
services: sql-database, automation
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/03/2017
ms.author: carlrab
ms.openlocfilehash: 0174b2b1dd5942e17ea60c2dce624c87fd1289c8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Správa databází Azure SQL pomocí Azure Automation.
Tento průvodce vás seznámí s služba Azure Automation a jak může sloužit ke zjednodušení správy vašich databází Azure SQL.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Služby Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušenou správu cloudu pomocí Automatizace procesu. Pomocí Azure Automation, dlouhotrvajících, ruční, problematických a často se opakujících úloh je možné automatizovat zvýšit spolehlivost, efektivitu a času na hodnotu pro vaši organizaci.

Azure Automation nabízí modul provádění vysoce spolehlivé a vysoce dostupné pracovního postupu, který rozšiřuje podle vašich potřeb podle růstu vaší organizace. Ve službě Azure Automation procesů může být spuštěna ručně, 3. stran systémy, nebo v naplánovaných intervalech tak, aby úlohy dojít přesně v případě potřeby.

Nižší provozní režie a uvolněte IT / DevOps zaměstnanci a zaměřit se na práci, kterou přidá obchodní value přesunutím vašeho cloudu spuštění úloh správy se automaticky automatizace Azure.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak Azure Automation pomoci spravovat databáze Azure SQL?
Azure SQL Database lze spravovat ve službě Azure Automation pomocí [rutiny prostředí PowerShell databáze SQL Azure](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/) které jsou k dispozici v [nástroje Azure PowerShell](/powershell/azure/overview). Automatizace Azure má tyto rutiny prostředí PowerShell databáze SQL Azure k dispozici předinstalované, aby mohli provést všechny úkoly správy SQL DB v rámci služby. Může také párovat tyto rutiny ve službě Azure Automation pomocí rutin pro jinými službami Azure, na automatizují komplexní úlohy v služeb Azure a systémech 3. stran.

Automatizace Azure má také možnost ke komunikaci se servery SQL přímo, vydáním příkazů SQL pomocí prostředí PowerShell.

[Galerie runbooků automatizace Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) obsahuje celou řadu produktu team a komunita runbooky tak, aby okamžitě začít s automatizací správu databáze SQL Azure, ostatní služby Azure a systémech 3. stran. Galerie runbooků patří:

* [Spouštění dotazů SQL na databázi systému SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Svisle škálování (nahoru nebo dolů) Azure SQL Database podle plánu](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Zkrácení tabulky SQL, pokud jeho databáze blíží maximální velikosti](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Index tabulky v databázi SQL Azure, pokud jsou vysoce fragmentován](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Azure Automation a jak může sloužit ke správě databází Azure SQL, postupujte podle následujících odkazech na další informace o Azure Automation.

* [Přehled služby Azure Automation](../automation/automation-intro.md)
* [Můj první runbook](../automation/automation-first-runbook-graphical.md)
* [Mapy kurzů Azure Automation.](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Azure Automation: Agenta SQL v cloudu](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

