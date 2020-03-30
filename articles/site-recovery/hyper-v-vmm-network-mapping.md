---
title: Mapování sítě Technologie Hyper-V (s VMM) pomocí site recovery
description: Popisuje, jak nastavit mapování sítě pro zotavení po havárii virtuálních počítačích Hyper-V (spravovaných v cloudech VMM) do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082559"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Příprava mapování sítě pro zotavení po havárii virtuálního počítače Hyper-V do Azure


Tento článek vám pomůže pochopit a připravit se na mapování sítě při replikaci virtuálních počítačů Hyper-V v cloudech v cloudech Správce virtuálních strojů system center (VMM) do Azure nebo do sekundární lokality pomocí služby [Azure Site Recovery.](site-recovery-overview.md)


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Příprava mapování sítě pro replikaci do Azure

Když se replikujete do Azure, mapy mapování sítě mezi sítěmi VM na zdrojovém serveru VMM a cílí na virtuální sítě Azure. Mapování provádí následující:
-  **Síťové připojení**– Zajišťuje, že replikované virtuální počítače Azure jsou připojené k mapované síti. Všechny počítače, které převzetí služeb při selhání ve stejné síti se mohou vzájemně připojit, a to i v případě, že selhaly v různých plánech obnovení.
- **Síťová brána**– Pokud je v cílové síti Azure nastavená síť síť network, virtuální počítače se můžou připojit k jiným místním virtuálním počítačům.

Mapování sítě funguje takto:

- Namapovat zdrojovou síť virtuálních zařízení VMM na virtuální síť Azure.
- Po převzetí služeb při selhání virtuálních počítačích Azure ve zdrojové síti se připojí k namapované cílové virtuální síti.
- Nové virtuální počítače přidané do zdrojové sítě virtuálních počítačích jsou připojené k mapované síti Azure, když dojde k replikaci.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak se virtuální počítač repliky po převzetí služeb při selhání připojí k této cílové podsíti.
- Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Příprava síťového mapování pro replikaci do sekundární lokality

Při replikaci do sekundární lokality mapuje mapování sítě mezi sítěmi VM na zdrojovém serveru VMM a sítěmi VM na cílovém serveru VMM. Mapování provádí následující:

- **Síťové připojení**– připojí virtuální uživatele k příslušným sítím po převzetí služeb při selhání. Virtuální hotel repliky bude připojený k cílové síti, která je namapována do zdrojové sítě.
- **Optimální umístění virtuálních počítačích**– optimálně umístí virtuální počítače repliky na hostitelské servery Hyper-V. Virtuální počítače replik jsou umístěny na hostitelích, kteří mají přístup k mapovaným sítím virtuálních počítače.
- **Žádné mapování sítě**– Pokud nenakonfigurujete mapování sítě, nebudou virtuální počítače repliky po převzetí služeb při selhání připojeny k žádné síti virtuálních počítače.

Mapování sítě funguje takto:

- Mapování sítě lze nakonfigurovat mezi sítěmi VM na dvou serverech VMM nebo na jednom serveru VMM, pokud jsou dvě lokality spravovány stejným serverem.
- Pokud je mapování správně nakonfigurováno a je povolena replikace, virtuální počítače v primárním umístění budou připojeny k síti a jeho replika v cílovém umístění bude připojena k jeho mapované síti.
- Když vyberete cílovou síť virtuálních počítačích během mapování sítě v site recovery, zobrazí se zdrojové cloudy VMM, které používají zdrojovou síť virtuálních počítačích, spolu s dostupnými cílovými sítěmi virtuálních počítačových v cílových cloudech, které se používají k ochraně.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěn zdrojový virtuální počítač, bude virtuální počítač repliky po převzetí služeb při selhání připojen k této cílové podsíti. Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, virtuální virtuální bude připojen k první podsíti v síti.

## <a name="example"></a>Příklad

Zde je příklad pro ilustraci tohoto mechanismu. Vezmeme si organizaci se dvěma pobočkami v New Yorku a Chicagu.

**Umístění** | **Server VMM** | **Sítě virtuálních ms** | **Mapováno na**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mapováno na VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Není mapováno
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapováno na VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Není mapováno

V tomto příkladu:

- Když se vytvoří virtuální počítače repliky pro jakýkoli virtuální počítače, který je připojený k VMNetwork1-NewYork, bude připojený k VMNetwork1-Chicago.
- Při vytvoření virtuálního počítače repliky pro VMNetwork2-NewYork nebo VMNetwork2-Chicago, nebude připojen k žádné síti.

Tady je postup, jak se cloudy VMM nastavují v naší příkladové organizaci a logické sítě přidružené k cloudům.

### <a name="cloud-protection-settings"></a>Nastavení ochrany cloudu

**Chráněný cloud** | **Ochrana cloudu** | **Logická síť (New York)**  
---|---|---
Zlatý namrazek1 | Zlatý omrač2 |
Stříbrný cloud1| Stříbrný oblač2 |
Zlatý omrač2 | <p>Není k dispozici</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
Stříbrný oblač2 | <p>Není k dispozici</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Nastavení logické sítě a sítě virtuálních počítače

**Umístění** | **Logická síť** | **Přidružená síť virtuálních ms**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Nastavení cílové sítě

Na základě těchto nastavení, když vyberete cílovou síť virtuálních počítače, v následující tabulce jsou uvedeny volby, které budou k dispozici.

**Vybrat** | **Chráněný cloud** | **Ochrana cloudu** | **Cílová síť k dispozici**
---|---|---|---
VMNetwork1-Chicago | Stříbrný cloud1 | Stříbrný oblač2 | K dispozici.
 | Zlatý namrazek1 | Zlatý omrač2 | K dispozici.
VMNetwork2-Chicago | Stříbrný cloud1 | Stříbrný oblač2 | Není k dispozici.
 | Zlatý namrazek1 | Zlatý omrač2 | K dispozici.


Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je zdrojový virtuální počítač umístěn, bude virtuální počítač repliky po převzetí služeb při selhání připojen k této cílové podsíti. Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.


### <a name="failback-behavior"></a>Chování navrácení služeb po selhání

Chcete-li zobrazit, co se stane v případě navrácení služeb po obnovení (reverzní replikace), předpokládejme, že VMNetwork1-NewYork je mapována na VMNetwork1-Chicago, s následujícím nastavením.


**Vm** | **Připojení k síti virtuálních montovanek**
---|---
VM1 | Síť VMNetwork1
VM2 (replika VM1) | VMNetwork1-Chicago

S těmito nastaveními si projděme, co se děje v několika možných scénářích.

**Scénář** | **Výsledek**
---|---
Žádná změna vlastností sítě VM-2 po převzetí služeb při selhání. | VM-1 zůstane připojený ke zdrojové síti.
Síťové vlastnosti virtuálního počítače-2 se po převzetí služeb při selhání změní a budou odpojeny. | Virtuální počítač VM-1 je odpojen.
Síťové vlastnosti vm-2 se po převzetí služeb při selhání změní a jsou připojeny k VMNetwork2-Chicago. | Pokud není namapována vmnetwork2-Chicago, bude Virtuální počítač odpojen.
Síťové mapování sítě VMNetwork1-Chicago se změní. | VM-1 bude připojen k síti nyní mapované na VMNetwork1-Chicago.



## <a name="next-steps"></a>Další kroky

- [Další informace o](hyper-v-vmm-networking.md) Adresování IP po převzetí služeb při selhání do sekundární lokality VMM.
- [Další informace o](concepts-on-premises-to-azure-networking.md) IP adresování po převzetí služeb při selhání do Azure.
