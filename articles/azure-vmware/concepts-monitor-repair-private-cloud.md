---
title: Koncepty – monitorování a oprava privátních cloudů řešení Azure VMware
description: Přečtěte si, jak řešení Azure VMware monitoruje a opravuje VMware ESXi servery v privátním cloudu řešení Azure VMware.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/03/2021
ms.openlocfilehash: 6174df429fd9b21c7f685c8ba14e6d5c0bba4c83
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538954"
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

Teď, když jste se seznámili s tím, jak řešení Azure VMware monitoruje a opravuje privátní cloudy, může být vhodné získat informace o:

- [Upgrade privátního cloudu řešení Azure VMware](concepts-upgrades.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).
