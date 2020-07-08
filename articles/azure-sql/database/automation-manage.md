---
title: Správa databází pomocí Azure Automation
description: Přečtěte si, jak se služba Azure Automation dá použít ke správě Azure SQL Database škálované.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3d785edb82e62363dd96d9fd67447f1be7ffe8f5
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982595"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Správa databází v Azure SQL Database pomocí Azure Automation

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tato příručka vás seznámí s Azure Automationovou službou a jejím použitím ke zjednodušení správy databází v Azure SQL Database.

## <a name="about-azure-automation"></a>O Azure Automation

[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure, která zjednodušuje správu cloudu prostřednictvím automatizace procesů. Použití Azure Automation, dlouhotrvající, ruční, náchylné k chybám a často opakovaných úloh, může být automatizované, aby se zvýšila spolehlivost, efektivita a doba, jakou má vaše organizace hodnotu. Informace o tom, jak začít, najdete v tématu [Azure Automation Úvod](../../automation/automation-intro.md)

Azure Automation poskytuje prováděcí modul pracovního postupu s vysokou spolehlivostí a vysokou dostupností, který se škáluje podle vašich potřeb, jak vaše organizace roste. V Azure Automation můžou procesy aktivovat ručně, systémy třetích stran nebo v plánovaných intervalech tak, aby úkoly v případě potřeby byly přesně provedeny.

Snížení provozní režie a uvolnění zaměstnanců IT/DevOps, aby se mohli soustředit na práci, která zvyšuje obchodní hodnotu, a to tak, že přesouvá úlohy správy cloudu tak, aby se automaticky spouštěly pomocí Azure Automation.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Jak Azure Automation můžou pomáhat se správou databází

Pomocí Azure Automation můžete spravovat databáze v Azure SQL Database pomocí [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) , které jsou k dispozici v [nástrojích pro Azure PowerShell](/powershell/azure/overview). Azure Automation mají tyto rutiny Azure SQL Database PowerShellu dostupné, abyste mohli provádět všechny úlohy správy SQL Database v rámci služby. Tyto rutiny můžete také spárovat v Azure Automation s rutinami pro další služby Azure, a automatizovat tak složité úlohy napříč službami Azure a systémy třetích stran.

Azure Automation taky může komunikovat přímo s SQL servery tím, že vydávají příkazy SQL pomocí PowerShellu.

Galerie runbooků a modulů pro [Azure Automation](../../automation/automation-runbook-gallery.md) nabízejí řadu runbooků od Microsoftu a komunity, kterou můžete naimportovat do Azure Automation. Pokud ho chcete použít, Stáhněte si Runbook z Galerie nebo můžete přímo importovat Runbooky z Galerie nebo z účtu Automation v Azure Portal.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Azure Automation a jak se dá použít ke správě Azure SQL Database, přečtěte si následující odkazy, kde najdete další informace o Azure Automation.

- [Přehled Azure Automation](../../automation/automation-intro.md)
- [Můj první runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
