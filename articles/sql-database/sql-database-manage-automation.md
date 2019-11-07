---
title: Správa databází Azure SQL Database pomocí Azure Automation
description: Přečtěte si, jak se služba Azure Automation dá použít ke správě databází SQL Azure ve velkém měřítku.
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
ms.openlocfilehash: 0e29cd85c63bb2f6e083f9cda9abd48fa6d9975a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689453"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Správa databází Azure SQL Database pomocí Azure Automation

Tato příručka vás seznámí s Azure Automationovou službou a jejím použitím ke zjednodušení správy databází SQL Azure.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure, která zjednodušuje správu cloudu prostřednictvím automatizace procesů. Použití Azure Automation, dlouhotrvající, ruční, náchylné k chybám a často opakovaných úloh, může být automatizované, aby se zvýšila spolehlivost, efektivita a doba, jakou má vaše organizace hodnotu.

Azure Automation poskytuje prováděcí modul pracovního postupu s vysokou spolehlivostí a vysokou dostupností, který se škáluje podle vašich potřeb, jak vaše organizace roste. V Azure Automation můžou procesy aktivovat ručně, systémy třetích stran nebo v plánovaných intervalech tak, aby úkoly v případě potřeby byly přesně provedeny.

Snížení provozní režie a uvolnění zaměstnanců IT/DevOps, aby se mohli soustředit na práci, která zvyšuje obchodní hodnotu, a to tak, že přesouvá úlohy správy cloudu tak, aby se automaticky spouštěly pomocí Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak může Azure Automation pomáhat při správě databází SQL Azure?

Azure SQL Database můžete spravovat v Azure Automation pomocí [rutin prostředí Azure SQL Database PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) , které jsou k dispozici v [nástrojích pro Azure PowerShell](/powershell/azure/overview). Azure Automation mají tyto rutiny Azure SQL Database PowerShellu dostupné, abyste mohli provádět všechny úlohy správy SQL DB v rámci služby. Tyto rutiny můžete také spárovat v Azure Automation s rutinami pro další služby Azure, a automatizovat tak složité úlohy napříč službami Azure a systémy třetích stran.

Azure Automation taky může komunikovat přímo s SQL servery tím, že vydávají příkazy SQL pomocí PowerShellu.

[Galerie sady runbook Azure Automation](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) obsahuje nejrůznější produktový tým a runbooky komunity, které vám pomohou začít automatizovat správu databází SQL Azure, dalších služeb Azure a systémů třetích stran. Runbooky v galerii zahrnují:

- [Spouštění dotazů SQL pro databázi SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Vertikální škálování (nahoru nebo dolů) Azure SQL Database podle plánu](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Zkrátit tabulku SQL, pokud se její databáze blíží své maximální velikosti](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indexuje tabulky v Azure SQL Database, pokud jsou vysoce fragmentované](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Azure Automation a jak se dají použít ke správě databází Azure SQL, použijte následující odkazy, kde najdete další informace o Azure Automation.

- [Přehled Azure Automation](../automation/automation-intro.md)
- [Můj první runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: váš Agent SQL v cloudu](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 