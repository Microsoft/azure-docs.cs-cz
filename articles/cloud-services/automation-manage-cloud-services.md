---
title: Správa cloudových služeb Azure pomocí Azure Automation | Dokumentace Microsoftu
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
ms.openlocfilehash: 40e91b4ba7ee62976b49975769bd1d1e656525f2
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54328864"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Správa cloudové služby Azure pomocí Azure Automation
Tato příručka vás seznámí s služby Azure Automation a jak ji můžete použít k zjednodušení správy cloudových službách Azure.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušení správu cloudu díky automatizaci procesu. Pomocí Azure Automation, je možné automatizovat časově náročné, ruční, problematických a často opakovaných úloh zvýšit spolehlivost, efektivitu a času na hodnotu pro vaši organizaci.

Azure Automation poskytuje s vysoce spolehlivé a vysoce dostupného prováděcího modulu, která se škáluje podle vašich potřeb podle růstu vaší organizace. Ve službě Azure Automation procesy můžete má zahájit ručně, 3. stran systémy, nebo v naplánovaných intervalech, aby úlohy stát přesně v případě potřeby.

Snižte operační režii a uvolnit tak IT / pracovníci DevOps a zaměřte se na práci, která vytváří obchodní hodnota přesunutím úkoly správy cloudu ke spuštění automaticky službou Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak Azure Automation pomáhá spravovat cloudové služby Azure?
Cloudové služby Azure můžete spravovat ve službě Azure Automation prostřednictvím rutin prostředí PowerShell, které jsou k dispozici v [prostředí Azure PowerShell nástroje](https://msdn.microsoft.com/library/azure/jj156055.aspx). Tak, aby bylo možné provádět všechny vaše úkoly správy cloudových služeb v rámci služby Azure Automation obsahuje tyto cloudové služby rutinách prostředí PowerShell dostupných hned po spuštění. Můžete také spárovat tyto rutiny ve službě Azure Automation s rutinami pro dalšími službami Azure, automatizují komplexní úlohy napříč službami Azure a systémech 3. stran.

Mezi příklad použití Azure Automation pro správu Azure Cloud Services patří:

* [Průběžné nasazování z cloudové služby, jakmile dojde k aktualizaci cscfg nebo cspkg ve službě Azure Blob storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Restartování instance cloudové služby paralelně jednu upgradovací doménu najednou](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy služby Azure Automation a jak ho lze použít ke správě cloudových služeb Azure, použijte tyto odkazy na další informace o službě Azure Automation.

* [Přehled Azure Automation](../automation/automation-intro.md)
* [Můj první runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa kurzů Azure Automation](https://azure.microsoft.com/documentation/learning-paths/automation/)
