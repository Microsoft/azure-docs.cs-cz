---
title: Správa Azure Cloud Services pomocí Azure Automation | Microsoft Docs
description: Přečtěte si, jak se služba Azure Automation dá použít ke správě cloudových služeb Azure ve velkém měřítku.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72439070"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Správa Azure Cloud Services pomocí Azure Automation
Tato příručka vás seznámí s Azure Automationovou službou a jejím použitím ke zjednodušení správy cloudových služeb Azure.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure, která zjednodušuje správu cloudu prostřednictvím automatizace procesů. Použití Azure Automation, dlouhotrvající, ruční, náchylné k chybám a často opakovaných úloh, může být automatizované, aby se zvýšila spolehlivost, efektivita a doba, jakou má vaše organizace hodnotu.

Azure Automation poskytuje vysoce spolehlivý a vysoce dostupný prováděcí modul pracovních postupů, který se škáluje podle vašich potřeb, jak vaše organizace roste. V Azure Automation můžou procesy aktivovat ručně, systémy třetích stran nebo v plánovaných intervalech tak, aby úkoly v případě potřeby byly přesně provedeny.

Snížení provozní režie a uvolnění zaměstnanců IT/DevOps, aby se mohli soustředit na práci, která zvyšuje obchodní hodnotu, a to tak, že přesouvá úlohy správy cloudu tak, aby se automaticky spouštěly pomocí Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak může Azure Automation pomáhat s správou cloudových služeb Azure?
Cloudové služby Azure je možné spravovat v Azure Automation pomocí rutin prostředí PowerShell, které jsou k dispozici v [nástrojích Azure PowerShell](/powershell/). Azure Automation mají tyto rutiny prostředí PowerShell služby Cloud Service dostupné, takže můžete provádět všechny úlohy správy cloudových služeb v rámci služby. Tyto rutiny můžete také spárovat v Azure Automation s rutinami pro další služby Azure, a automatizovat tak složité úlohy napříč službami Azure a systémy třetích stran.

Příklady použití Azure Automation ke správě Azure Cloud Services zahrnují:

* [Průběžné nasazování cloudové služby vždy, když se v úložišti objektů BLOB v Azure aktualizuje cscfg nebo cspkg](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Současné restartování instancí cloudových služeb paralelně s jednou upgradovací doménou](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Azure Automation a jak se dají použít ke správě cloudových služeb Azure, použijte následující odkazy, kde najdete další informace o Azure Automation.

* [Přehled Azure Automation](../automation/automation-intro.md)
* [Můj první runbook](../automation/automation-first-runbook-graphical.md)
