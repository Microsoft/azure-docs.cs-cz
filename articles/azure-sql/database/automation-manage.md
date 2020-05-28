---
title: Správa databází pomocí Azure Automation
description: Přečtěte si, jak se služba Azure Automation dá použít ke správě Azure SQL Database škálované.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4b5378b5df36c158c3f401f214730b4f493f5def
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043608"
---
# <a name="managing-azure-sql-database-using-azure-automation"></a>Správa Azure SQL Database pomocí Azure Automation
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb.md)]

Tato příručka vás seznámí s Azure Automationovou službou a jejím použitím ke zjednodušení správy databází ve službě Azure SQL Database.

## <a name="what-is-azure-automation"></a>Co je Azure Automation

[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure, která zjednodušuje správu cloudu prostřednictvím automatizace procesů. Použití Azure Automation, dlouhotrvající, ruční, náchylné k chybám a často opakovaných úloh, může být automatizované, aby se zvýšila spolehlivost, efektivita a doba, jakou má vaše organizace hodnotu. Informace o tom, jak začít, najdete v tématu [Azure Automation Úvod](../../automation/automation-intro.md)

Azure Automation poskytuje prováděcí modul pracovního postupu s vysokou spolehlivostí a vysokou dostupností, který se škáluje podle vašich potřeb, jak vaše organizace roste. V Azure Automation můžou procesy aktivovat ručně, systémy třetích stran nebo v plánovaných intervalech tak, aby úkoly v případě potřeby byly přesně provedeny.

Snížení provozní režie a uvolnění zaměstnanců IT/DevOps, aby se mohli soustředit na práci, která zvyšuje obchodní hodnotu, a to tak, že přesouvá úlohy správy cloudu tak, aby se automaticky spouštěly pomocí Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-database"></a>Jak může Azure Automation pomáhat se správou Azure SQL Database

Azure SQL Database můžete spravovat v Azure Automation pomocí [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) , které jsou k dispozici v [nástrojích pro Azure PowerShell](/powershell/azure/overview). Azure Automation mají tyto rutiny Azure SQL Database PowerShellu dostupné, abyste mohli provádět všechny úlohy správy SQL Database v rámci služby. Tyto rutiny můžete také spárovat v Azure Automation s rutinami pro další služby Azure, a automatizovat tak složité úlohy napříč službami Azure a systémy třetích stran.

Azure Automation taky může komunikovat přímo s SQL servery tím, že vydávají příkazy SQL pomocí PowerShellu.

Galerie runbooků a modulů pro [Azure Automation](../../automation/automation-runbook-gallery.md) nejrůznější sady Runbook od Microsoftu a komunity, které můžete naimportovat do Azure Automation. Pokud ho chcete použít, Stáhněte si Runbook z Galerie nebo můžete přímo importovat Runbooky z Galerie nebo z účtu Automation v Azure Portal.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Azure Automation a jak se dá použít ke správě Azure SQL Database, přečtěte si následující odkazy, kde najdete další informace o Azure Automation.

- [Přehled Azure Automation](../../automation/automation-intro.md)
- [Můj první runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
