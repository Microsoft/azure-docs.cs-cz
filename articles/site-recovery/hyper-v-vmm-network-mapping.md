---
title: Mapování sítě Hyper-V (s VMM) s Site Recovery
description: Popisuje, jak nastavit mapování sítě pro zotavení po havárii virtuálních počítačů Hyper-V (spravovaných v cloudech VMM) do Azure s Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 98e6f9e7cb937551e111625845ba07d09482eff5
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581155"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Příprava mapování sítě pro zotavení po havárii virtuálního počítače Hyper-V do Azure


Tento článek vám pomůže pochopit a připravit mapování sítě při replikaci virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do Azure nebo do sekundární lokality pomocí služby [Azure Site Recovery](site-recovery-overview.md) .


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Příprava mapování sítě pro replikaci do Azure

Když provádíte replikaci do Azure, mapování sítě mezi sítěmi virtuálních počítačů na zdrojovém serveru VMM a cílovými virtuálními sítěmi Azure. Mapování provádí následující:
-  **Síťové připojení**– zajistí, že se replikované virtuální počítače Azure připojí k mapované síti. Všechny počítače, které převezmou služby při selhání ve stejné síti, se můžou vzájemně propojit, i když převezmou služby při selhání v různých plánech obnovení.
- **Síťová brána**– Pokud je v cílové síti Azure nastavená síťová brána, virtuální počítače se můžou připojit k dalším místním virtuálním počítačům.

Mapování sítě funguje takto:

- Namapujete zdrojovou síť virtuálního počítače VMM na virtuální síť Azure.
- Po převzetí služeb při selhání se virtuální počítače Azure v zdrojové síti připojí k mapované cílové virtuální síti.
- Nové virtuální počítače přidané do zdrojové sítě virtuálních počítačů jsou připojené k namapované síti Azure, když dojde k replikaci.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak se virtuální počítač repliky po převzetí služeb při selhání připojí k této cílové podsíti.
- Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Příprava mapování sítě pro replikaci na sekundární lokalitu

Když provádíte replikaci do sekundární lokality, mapování sítě mezi sítěmi virtuálních počítačů na zdrojovém serveru VMM a sítě virtuálních počítačů na cílovém serveru VMM. Mapování provádí následující:

- **Síťové připojení**– připojí virtuální počítače k příslušným sítím po převzetí služeb při selhání. Virtuální počítač repliky se připojí k cílové síti, která je namapovaná na zdrojovou síť.
- **Optimální umístění virtuálního počítače**– optimálně umístí virtuální počítače repliky na hostitelské servery technologie Hyper-V. Virtuální počítače repliky jsou umístěné na hostitelích, kteří mají přístup k připojeným sítím virtuálních počítačů.
- **Bez mapování sítě**– Pokud mapování sítě nenakonfigurujete, nebudou virtuální počítače repliky po převzetí služeb při selhání připojené k žádným sítím virtuálních počítačů.

Mapování sítě funguje takto:

- Mapování sítě lze nakonfigurovat mezi sítěmi virtuálních počítačů na dvou serverech VMM nebo na jednom serveru VMM, pokud jsou dvě lokality spravovány stejným serverem.
- Pokud je mapování správně nakonfigurováno a je povolena replikace, virtuální počítač v primárním umístění bude připojen k síti a jeho replika v cílovém umístění bude připojena ke své mapované síti.
- Když při mapování sítě v Site Recovery vyberete cílovou síť virtuálních počítačů, zobrazí se zdrojové cloudy VMM, které používají zdrojovou síť virtuálních počítačů, spolu s dostupnými cílovými sítěmi virtuálních počítačů v cílových cloudech, které se používají k ochraně.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak se virtuální počítač repliky po převzetí služeb při selhání připojí k této cílové podsíti. Pokud neexistuje žádná cílová podsíť se shodným názvem, bude virtuální počítač připojen k první podsíti v síti.

## <a name="example"></a>Příklad

Zde je příklad pro ilustraci tohoto mechanismu. Pojďme pořizovat organizaci se dvěma umístěními v New Yorku a v Chicagu.

**Umístění** | **Server VMM** | **Sítě virtuálních počítačů** | **Namapováno na**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Namapováno na VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Nemapováno
Chicago | VMM-Chicago| VMNetwork1-Chicago | Namapováno na VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Nemapováno

V tomto příkladu:

- Když se vytvoří virtuální počítač repliky pro každý virtuální počítač, který je připojený k VMNetwork1-NewYork, připojí se k VMNetwork1 – Chicago.
- Když se vytvoří virtuální počítač repliky pro VMNetwork2-NewYork nebo VMNetwork2 – Chicago, nebude se připojit k žádné síti.

Tady je postup nastavení cloudů VMM v naší ukázkové organizaci a logické sítě přidružené k cloudům.

### <a name="cloud-protection-settings"></a>Nastavení ochrany cloudu

**Chráněný Cloud** | **Ochrana cloudu** | **Logická síť (Praha)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Nastavení logické sítě a sítě virtuálních počítačů

**Umístění** | **Logická síť** | **Přidružená síť virtuálních počítačů**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Nastavení cílové sítě

Když v závislosti na těchto nastaveních vyberete cílovou síť virtuálních počítačů, v následující tabulce jsou uvedeny možnosti, které budou k dispozici.

**Výběr** | **Chráněný Cloud** | **Ochrana cloudu** | **Cílová síť k dispozici**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | K dispozici
 | GoldCloud1 | GoldCloud2 | K dispozici
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Není k dispozici
 | GoldCloud1 | GoldCloud2 | K dispozici


Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak se virtuální počítač repliky po převzetí služeb při selhání připojí k této cílové podsíti. Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.


### <a name="failback-behavior"></a>Chování navrácení služeb po obnovení

Pokud se chcete podívat, co se stane v případě navrácení služeb po obnovení (reverzní replikace), předpokládáme, že VMNetwork1-NewYork je namapovaný na VMNetwork1-Chicago s následujícím nastavením.


**Virtuální počítač** | **Připojeno k síti virtuálních počítačů**
---|---
VM1 | VMNetwork1-Network
VM2 (Replika VM1) | VMNetwork1-Chicago

Pomocí těchto nastavení se podívejme na to, co se stane v několika možných scénářích.

**Scénář** | **Zaznamenaný**
---|---
Po převzetí služeb při selhání se ve vlastnostech sítě virtuálního počítače-2 nezměnily žádné změny. | Virtuální počítač-1 zůstává připojený ke zdrojové síti.
Po převzetí služeb při selhání se změní vlastnosti sítě virtuálního počítače-2 a dojde k jeho odpojení. | Virtuální počítač-1 je odpojený.
Vlastnosti sítě virtuálního počítače-2 se po převzetí služeb při selhání změnily a jsou připojené k VMNetwork2 – Chicago. | Pokud VMNetwork2-Chicago není namapovaná, virtuální počítač-1 se odpojí.
Mapování sítě VMNetwork1-Chicago je změněno. | Virtuální počítač – 1 bude nyní připojen k síti namapované na VMNetwork1 – Chicago.



## <a name="next-steps"></a>Další kroky

- [Další informace](hyper-v-vmm-networking.md) IP adresování po převzetí služeb při selhání do sekundární lokality VMM.
- [Další informace](concepts-on-premises-to-azure-networking.md) IP adresování po převzetí služeb při selhání do Azure
