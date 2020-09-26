---
title: Koncepty – monitorování a oprava privátních cloudů řešení Azure VMware
description: Přečtěte si, jak řešení Azure VMware monitoruje a opravuje VMware ESXi servery v privátním cloudu řešení Azure VMware.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: bee08304cd14f2aeec6995203638b5a37d9a861c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342316"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorování a oprava privátních cloudů řešení VMware Azure

Řešení Azure VMware nepřetržitě monitoruje VMware ESXi servery v privátním cloudu řešení Azure VMware. 

## <a name="what-azure-vmware-solution-monitors"></a>Co monitoruje řešení Azure VMware

Řešení Azure VMware monitoruje u hostitele následující chyby:  

- Stav procesoru 
- Stav paměti 
- Stav připojení a napájení 
- Stav ventilátoru hardwaru 
- Ztráta připojení k síti 
- Stav tabule hardwarového systému 
- Na discích hostitele síti vSAN došlo k chybám: 
- Hardwarové napětí 
- Stav teploty hardwaru 
- Stav hardwarového napájení 
- Stav úložiště 
- Chyba připojení 

> [!NOTE]
> Správci tenantů řešení Azure VMware nesmí upravit ani odstranit výše popsané alarmy VMware vCenter, protože jsou spravované rovinou řízení řešení Azure VMware na serveru vCenter. Tyto alarmy používá monitorování řešení Azure VMware k aktivaci procesu nápravy hostitele řešení Azure VMware.

## <a name="azure-vmware-solution-host-remediation"></a>Náprava hostitele řešení Azure VMware  

Když řešení Azure VMware detekuje snížení nebo selhání uzlu řešení Azure VMware v privátním cloudu tenanta, aktivuje proces nápravy hostitele. Náprava hostitele zahrnuje nahrazení chybného uzlu novým dobrým uzlem.  

Proces nápravy hostitele se spustí přidáním nového uzlu v pořádku v clusteru. Pokud je to možné, je poškozený hostitel umístěný v režimu údržby VMware vSphere. VMware vMotion slouží k přesunu virtuálních počítačů z vadného hostitele na jiné dostupné servery v clusteru, což může způsobit nenáročné migrace úloh za provozu. Ve scénářích, kde nelze umístit vadného hostitele do režimu údržby, je hostitel odebrán z clusteru.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [upgradech privátního cloudu řešení Azure VMware](concepts-upgrades.md).  
