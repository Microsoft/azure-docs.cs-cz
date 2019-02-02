---
title: Správa databází Azure SQL Database pomocí Azure Automation | Dokumentace Microsoftu
description: Další informace o používání služby Azure Automation pro správu databází Azure SQL ve velkém měřítku.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 05dfece2a2e69c5cdb3f08b51424720a1c2898db
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561928"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Správa databází Azure SQL Database pomocí Azure Automation.

Tato příručka vás seznámí s služby Azure Automation a jak ho lze zjednodušit správu vašich databází Azure SQL.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušení správu cloudu díky automatizaci procesu. Pomocí Azure Automation, je možné automatizovat časově náročné, ruční, problematických a často opakovaných úloh zvýšit spolehlivost, efektivitu a času na hodnotu pro vaši organizaci.

Azure Automation poskytuje spouštěcí modul pracovních postupů s vysokou spolehlivostí a vysokou dostupnost a, která se škáluje podle vašich potřeb podle růstu vaší organizace. Ve službě Azure Automation procesy můžete být spuštěna ručně, systémy třetích stran nebo v naplánovaných intervalech tak, aby úlohy stát přesně v případě potřeby.

Snižte operační režii a uvolnit tak IT / pracovníci DevOps a zaměřte se na práci, která vytváří obchodní hodnota přesunutím úkoly správy cloudu ke spuštění automaticky službou Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak pomáhá Azure Automation. Správa databází Azure SQL Database?

Azure SQL Database můžete spravovat ve službě Azure Automation s použitím [rutin Powershellu pro službu Azure SQL Database](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#sql) , které jsou k dispozici v [prostředí Azure PowerShell nástroje](/powershell/azure/overview). Tak, aby bylo možné provádět všechny úlohy správy vaší databáze SQL ve službě Azure Automation obsahuje tyto rutiny Powershellu pro službu Azure SQL Database k dispozici hned po spuštění. Můžete také spárovat tyto rutiny ve službě Azure Automation s rutinami pro dalšími službami Azure, automatizují komplexní úlohy napříč službami Azure a systémů třetích stran.

Azure Automation má také možnost ke komunikaci se servery SQL přímo, pomocí příkazů SQL pomocí prostředí PowerShell.

[v galerii runbooků Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) obsahuje celou řadu produktů týmu a komunity sady runbook začít s automatizací správy databází Azure SQL, dalších služeb Azure a systémech třetích stran. Galerie runbooků patří:

- [Spouštějte dotazy SQL proti databázi serveru SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Vertikální škálování (nahoru nebo dolů) služby Azure SQL Database podle plánu](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Zkrácení tabulky SQL, pokud se databáze blíží své maximální velikosti](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Pokud jsou vysoce fragmentován index tabulky ve službě Azure SQL Database](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili základy služby Azure Automation a jak lze použít ke správě databází Azure SQL, použijte tyto odkazy na další informace o službě Azure Automation.

- [Přehled Azure Automation](../automation/automation-intro.md)
- [Můj první runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: Agenta SQL v cloudu](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 