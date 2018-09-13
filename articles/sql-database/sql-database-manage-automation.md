---
title: Správa databází Azure SQL Database pomocí Azure Automation | Dokumentace Microsoftu
description: Další informace o používání služby Azure Automation pro správu databází Azure SQL ve velkém měřítku.
services: sql-database, automation
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 43476cfcae2035c3b8e94b4a5e264a0c8ff424e0
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715444"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Správa databází Azure SQL Database pomocí Azure Automation.
Tato příručka vás seznámí s služby Azure Automation a jak ho lze zjednodušit správu vašich databází Azure SQL.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušení správu cloudu díky automatizaci procesu. Pomocí Azure Automation, je možné automatizovat časově náročné, ruční, problematických a často opakovaných úloh zvýšit spolehlivost, efektivitu a času na hodnotu pro vaši organizaci.

Azure Automation poskytuje s vysoce spolehlivé a vysoce dostupného prováděcího modulu, která se škáluje podle vašich potřeb podle růstu vaší organizace. Ve službě Azure Automation procesy můžete má zahájit ručně, 3. stran systémy, nebo v naplánovaných intervalech, aby úlohy stát přesně v případě potřeby.

Snižte operační režii a uvolnit tak IT / pracovníci DevOps a zaměřte se na práci, která vytváří obchodní hodnota přesunutím úkoly správy cloudu ke spuštění automaticky službou Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak pomáhá Azure Automation. Správa databází Azure SQL Database?
Azure SQL Database můžete spravovat ve službě Azure Automation s použitím [rutin Powershellu pro službu Azure SQL Database](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#sql) , které jsou k dispozici v [prostředí Azure PowerShell nástroje](/powershell/azure/overview). Tak, aby bylo možné provádět všechny úlohy správy vaší databáze SQL ve službě Azure Automation obsahuje tyto rutiny Powershellu pro službu Azure SQL Database k dispozici hned po spuštění. Můžete také spárovat tyto rutiny ve službě Azure Automation s rutinami pro dalšími službami Azure, automatizují komplexní úlohy napříč službami Azure a systémech 3. stran.

Azure Automation má také možnost ke komunikaci se servery SQL přímo, pomocí příkazů SQL pomocí prostředí PowerShell.

[v galerii runbooků Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) obsahuje celou řadu produktů týmu a komunity sady runbook začít s automatizací správy databází Azure SQL, dalších služeb Azure a systémech 3. stran. Galerie runbooků patří:

* [Spouštějte dotazy SQL proti databázi serveru SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Vertikální škálování (nahoru nebo dolů) služby Azure SQL Database podle plánu](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Zkrácení tabulky SQL, pokud se databáze blíží své maximální velikosti](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Pokud jsou vysoce fragmentován index tabulky ve službě Azure SQL Database](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy služby Azure Automation a jak lze použít ke správě databází Azure SQL, použijte tyto odkazy na další informace o službě Azure Automation.

* [Přehled Azure Automation](../automation/automation-intro.md)
* [Můj první runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa kurzů Azure Automation](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Azure Automation: Agenta SQL v cloudu](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

