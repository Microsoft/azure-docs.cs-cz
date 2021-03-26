---
title: Aktualizace platformy pro řešení Azure VMware
description: Seznamte se s aktualizacemi platformy pro řešení Azure VMware.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045208"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aktualizace platformy pro řešení Azure VMware

Řešení Azure VMware bude používat důležité aktualizace od března 2021. Prostřednictvím Azure Service Health, která zahrnuje časovou osu údržby, obdržíte oznámení. Další informace najdete v tématu [aktualizace a upgrade privátního cloudu řešení Azure VMware](concepts-upgrades.md).

## <a name="march-24-2021"></a>24. března 2021
Všechny nové privátní cloudy řešení Azure VMware se nasazují s použitím VMware vCenter verze 6.7 U3l a NSX-T verze 3.1.1. Všechny existující privátní cloudy budou aktualizovány a upgradovány **až do června 2021** až na výše zmíněné verze.

Dostanete e-mail s plánovaným datem a časem údržby. Upgrade můžete naplánovat znovu. E-mail také poskytuje podrobné informace o upgradovaných součástech, její vliv na úlohy, přístup k privátnímu cloudu a další služby Azure.  Hodinu před upgradem obdržíte oznámení a po jeho dokončení se znovu zobrazí.

## <a name="march-15-2021"></a>15. března 2021 

- Služba řešení Azure VMware provede údržbu do **19. března 2021** k aktualizaci serveru vCenter ve vašem privátním cloudu na verzi vCenter Server 6,7 Update 3l.

- VMware vCenter nebude během této doby k dispozici.  Nebudete tak moci spravovat vaše virtuální počítače (zastavení, spuštění, vytvoření, odstranění) nebo škálování privátního cloudu (přidávání a odebírání serverů a clusterů). Technologie VMware s vysokou dostupností (HA) ale bude i nadále fungovat, aby chránila stávající virtuální počítače. 
 
Další informace o této verzi vCenter najdete v [poznámkách k verzi pro VMware vCenter Server 6,7 Update 3l](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4. března 2021

- Řešení Azure VMware použije [VMware ESXi 6,7, verze patch ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) na stávající soukromé služby do **15. března 2021**.

- Popsaná řešení pro vSphere Stack, která jsou podle [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), se použijí i **do 15. března 2021**.

>[!NOTE]
>To není rušivé a nemělo by mít vliv na služby nebo úlohy Azure VMware. Během údržby se v vCenter zobrazí různé výstrahy VMware, například _ztracené síťové připojení v DVPorts_ a _ztráty odchozího připojení v DVPorts_, a v rámci údržby se budou automaticky mazat.

## <a name="post-update"></a>Po aktualizaci
Po dokončení se zobrazí novější verze komponent VMware. Pokud si všimnete jakýchkoli problémů nebo máte nějaké dotazy, obraťte se na náš tým podpory otevřením lístku podpory.