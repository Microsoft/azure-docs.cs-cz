---
title: Správa cloudových služeb Azure pomocí Azure Automation | Microsoft Docs
description: Další informace o používání služby Azure Automation ke správě cloudových služeb Azure ve velkém měřítku.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 6b5acac1b8647c324988c316cd5602b3dba98a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842934"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Správa cloudových služeb Azure pomocí Azure Automation.
Tento průvodce vás seznámí s služba Azure Automation a jak může sloužit ke zjednodušení správy Azure cloudových služeb.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Služby Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušenou správu cloudu pomocí Automatizace procesu. Pomocí Azure Automation, dlouhotrvajících, ruční, problematických a často se opakujících úloh je možné automatizovat zvýšit spolehlivost, efektivitu a času na hodnotu pro vaši organizaci.

Azure Automation nabízí modul provádění vysoce spolehlivé a vysoce dostupné pracovního postupu, který rozšiřuje podle vašich potřeb podle růstu vaší organizace. Ve službě Azure Automation procesů může být spuštěna ručně, 3. stran systémy, nebo v naplánovaných intervalech tak, aby úlohy dojít přesně v případě potřeby.

Nižší provozní režie a uvolněte IT / DevOps zaměstnanci a zaměřit se na práci, kterou přidá obchodní value přesunutím vašeho cloudu spuštění úloh správy se automaticky automatizace Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak Azure Automation pomoci spravovat cloudové služby Azure?
Cloudové služby Azure lze spravovat ve službě Azure Automation pomocí rutin prostředí PowerShell, které jsou k dispozici v [nástroje Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatizace Azure má tyto k dispozici cloudové rutiny prostředí PowerShell služby předinstalované, aby mohli provést všechny úkoly správy cloudové služby v rámci služby. Může také párovat tyto rutiny ve službě Azure Automation pomocí rutin pro jinými službami Azure, na automatizují komplexní úlohy v služeb Azure a systémech 3. stran.

Některé příklad použití Azure Automation pro správu Azure Cloud Services patří:

* [Nepřetržité nasazení cloudové služby při každé aktualizaci cscfg nebo cspkg v Azure Blob storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Restartování instance cloudové služby paralelně jednu upgradovací doménu najednou](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Azure Automation a jak může sloužit ke správě cloudových služeb Azure, postupujte podle následujících odkazech na další informace o Azure Automation.

* [Přehled služby Azure Automation](../automation/automation-intro.md)
* [Můj první runbook](../automation/automation-first-runbook-graphical.md)
* [Mapy kurzů Azure Automation.](https://azure.microsoft.com/documentation/learning-paths/automation/)
