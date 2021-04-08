---
title: Koncepty – monitorování a oprava privátních cloudů řešení Azure VMware
description: Přečtěte si, jak řešení Azure VMware monitoruje a opravuje VMware ESXi servery v privátním cloudu řešení Azure VMware.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633134"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorování a oprava privátních cloudů řešení VMware Azure

Řešení Azure VMware nepřetržitě monitoruje VMware ESXi servery v privátním cloudu řešení Azure VMware. 

## <a name="what-azure-vmware-solution-monitors"></a>Co monitoruje řešení Azure VMware

Řešení Azure VMware monitoruje na hostiteli tyto podmínky:  

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

Když řešení Azure VMware detekuje snížení nebo selhání uzlu řešení Azure VMware, spustí proces nápravy hostitele. Náprava hostitele zahrnuje nahrazení chybného uzlu novým dobrým uzlem.  

Náprava hostitelů začíná přidáním nového uzlu v pořádku v clusteru. Pokud je to možné, je poškozený hostitel umístěný v režimu údržby VMware vSphere. VMware vMotion přesouvá virtuální počítače z vadného hostitele na jiné dostupné servery v clusteru, což potenciálně umožňuje nulové výpadky migrace zatížení za provozu. Pokud se chybný hostitel nedá umístit do režimu údržby, hostitel se odebere z clusteru.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s tím, jak řešení Azure VMware monitoruje a opravuje privátní cloudy, může být vhodné získat informace o:

- [Upgrade privátního cloudu řešení Azure VMware](concepts-upgrades.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).
