---
title: Aktualizace platformy pro řešení Azure VMware
description: Seznamte se s aktualizacemi platformy pro řešení Azure VMware.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 73bd1d088f9055ebd80a28c6247ea9dfa6229093
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586229"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aktualizace platformy pro řešení Azure VMware

Důležité aktualizace řešení Azure VMware se použijí od března 2021. Dostanete oznámení prostřednictvím Azure Service Health, která obsahuje časovou osu údržby. Další informace o klíčových procesech upgradu a funkcích v řešení Azure VMware najdete v tématu [aktualizace a upgrade privátního cloudu řešení Azure VMware](concepts-upgrades.md).

## <a name="march-15-2021"></a>15. března 2021 

- Služba řešení Azure VMware provede pracovní údržbu do 19. března 20201 k aktualizaci vCenter serveru ve vašem privátním cloudu na verzi vCenter Server 6,7 Update 3l.

- Během této doby nebude VMware vCenter k dispozici a nebude možné spravovat virtuální počítače (zastavit, spustit, vytvořit, odstranit). Škálování privátního cloudu (přidávání/odebírání serverů a clusterů) bude také nedostupné. Technologie VMware s vysokou dostupností (HA) bude nadále fungovat, aby poskytovala ochranu pro stávající virtuální počítače. 
 
Další informace o této verzi vCenter najdete v [poznámkách k verzi pro VMware vCenter Server 6,7 Update 3l](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4. března 2021

- Řešení Azure VMware uplatní opravy do 15. března 2021, do ESXi v existujících privátních cloudech až po [VMware ESXi 6,7, verze patch ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html).

- Popsaná řešení pro vSphere Stack, která jsou podle [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), se použijí i do 15. března 2021.

>[!NOTE]
>To není rušivé a nemělo by mít vliv na služby nebo úlohy Azure VMware. Během údržby se v vCenter zobrazí různé výstrahy VMware, například _ztracené síťové připojení v DVPorts_ a _ztráty odchozího připojení v DVPorts_, a v rámci údržby se budou automaticky mazat.

## <a name="post-update"></a>Po aktualizaci
Po dokončení se zobrazí novější verze komponent VMware. Pokud si všimnete jakýchkoli problémů nebo máte nějaké dotazy, obraťte se na náš tým podpory otevřením lístku podpory.





