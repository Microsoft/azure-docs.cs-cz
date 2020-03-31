---
title: Správa cloudových služeb Azure pomocí Azure Automation | Dokumenty společnosti Microsoft
description: Přečtěte si, jak se služba Azure Automation dá používat ke správě cloudových služeb Azure ve velkém měřítku.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72439070"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Správa cloudových služeb Azure pomocí Azure Automation
Tato příručka vám představí službu Azure Automation a jak ji můžete použít ke zjednodušení správy cloudových služeb Azure.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušení správy cloudu prostřednictvím automatizace procesů. Pomocí Azure Automation lze pomocí Azure Automation automatizovat, ruční, náchylné k chybám a často opakované úlohy, které lze automatizovat, aby se zvýšila spolehlivost, efektivita a doba od zvýšení hodnoty pro vaši organizaci.

Azure Automation poskytuje vysoce spolehlivý a vysoce dostupný modul provádění pracovních postupů, který se škáluje podle vašich potřeb podle růstu vaší organizace. V Azure Automation procesy můžete vypouštět ručně, systémy třetích stran nebo v naplánovaných intervalech tak, aby k úkolům došlo přesně v případě potřeby.

Snižte provozní režii a uvolněte pracovníky IT / DevOps, abyste se soustředili na práci, která přidává obchodní hodnotu přesunutím úloh správy cloudu, které mají být automaticky spuštěny azure automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak může Azure Automation pomoci spravovat cloudové služby Azure?
Cloudové služby Azure se můžou spravovat v Azure Automation pomocí rutin PowerShellu, které jsou dostupné v [nástrojích Azure PowerShell](/powershell/). Azure Automation má tyto rutiny powershellu cloudové služby, které jsou k dispozici ipo schránkou, takže můžete v rámci služby provádět všechny úlohy správy cloudových služeb. Tyto rutiny můžete také spárovat v Azure Automation s rutinami pro jiné služby Azure a automatizovat složité úlohy napříč službami Azure a systémy třetích stran.

Některé příklady použití Azure Automation ke správě Cloudových služeb Azure patří:

* [Průběžné nasazování cloudové služby při každé aktualizaci cscfg nebo cspkg v úložišti objektů Blob Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Paralelní restartování instancí cloudové služby po jedné upgradovací doméně](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Azure Automation a jak ji můžete použít ke správě cloudových služeb Azure, postupujte podle těchto odkazů a získejte další informace o Azure Automation.

* [Přehled Azure Automation](../automation/automation-intro.md)
* [Můj první runbook](../automation/automation-first-runbook-graphical.md)
