---
title: Správa databází pomocí Azure Automation
description: Přečtěte si, jak se služba Azure Automation dá používat ke správě databází Azure SQL ve velkém měřítku.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822811"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Správa databází Azure SQL pomocí Azure Automation

Tato příručka vám představí službu Azure Automation a jak ji můžete použít ke zjednodušení správy databází Azure SQL.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušení správy cloudu prostřednictvím automatizace procesů. Pomocí Azure Automation lze pomocí Azure Automation automatizovat, ruční, náchylné k chybám a často opakované úlohy, které lze automatizovat, aby se zvýšila spolehlivost, efektivita a doba od zvýšení hodnoty pro vaši organizaci.

Azure Automation poskytuje modul pro spuštění pracovních postupů s vysokou spolehlivostí a vysokou dostupností a který se škáluje tak, aby vyhovoval vašim potřebám s růstem vaší organizace. V Azure Automation procesy můžete vypouštět ručně, systémy třetích stran nebo v naplánovaných intervalech tak, aby k úkolům došlo přesně v případě potřeby.

Snižte provozní režii a uvolněte pracovníky IT / DevOps, abyste se soustředili na práci, která přidává obchodní hodnotu přesunutím úloh správy cloudu, které mají být automaticky spuštěny azure automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak může Azure Automation pomoct spravovat databáze Azure SQL?

Azure SQL Database se dá spravovat v Azure Automation pomocí [rutin Prostředí Azure SQL Database PowerShell,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) které jsou dostupné v [nástrojích Azure PowerShell](/powershell/azure/overview). Azure Automation má tyto rutiny Azure SQL Database PowerShell, které jsou k dispozici po vybalení, takže můžete provádět všechny úlohy správy SQL DB v rámci služby. Tyto rutiny můžete také spárovat v Azure Automation s rutinami pro jiné služby Azure a automatizovat složité úlohy napříč službami Azure a systémy třetích stran.

Azure Automation má také možnost komunikovat se servery SQL přímo vydáváním příkazů SQL pomocí prostředí PowerShell.

[Galerie runbooků Azure Automation](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) obsahuje celou řadu produktových týmů a sad Runbook komunity, které mohou začít automatizovat správu databází Azure SQL, dalších služeb Azure a systémů třetích stran. Sady runbooků galerie zahrnují:

- [Spuštění dotazů SQL v databázi serveru SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Vertikální škálování (nahoru nebo dolů) databáze Azure SQL podle plánu](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Zkrácení tabulky SQL, pokud se její databáze blíží maximální velikosti](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indexovat tabulky v azure sql databázi, pokud jsou vysoce fragmentované](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Azure Automation a jak ji můžete použít ke správě databází Azure SQL, postupujte podle těchto odkazů další informace o Azure Automation.

- [Přehled Azure Automation](../automation/automation-intro.md)
- [Můj první runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: Váš agent SQL v cloudu](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 