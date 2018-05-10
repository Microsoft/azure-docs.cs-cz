---
title: O mapování sítě pro replikaci virtuálního počítače technologie Hyper-V (s VMM) do Azure pomocí Site Recovery | Microsoft Docs
description: Popisuje, jak nastavit mapování sítě pro replikaci virtuálních počítačů technologie Hyper-V spravované v cloudech VMM s Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: f7c6e3eeb8db75a3857e687fecc9ee2748e92696
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="prepare-network-mapping-for-hyper-v-vm-replication-to-azure"></a>Příprava mapování sítí pro replikaci virtuálních počítačů Hyper-V do Azure


Tento článek vám pomůže pochopit a připravit mapování sítě při replikaci virtuálních počítačů technologie Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do Azure nebo do sekundární lokality pomocí [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Příprava mapování sítě pro replikaci do Azure.

Když při replikaci do Azure, sítě mapování mapuje sítě virtuálních počítačů na zdrojovém serveru VMM a cílových virtuálních sítí Azure. Mapování provede následující akce:
    -  **Síťové připojení**– zajišťuje, že replikované virtuální počítače Azure jsou připojené k namapované síti. Všechny počítače, které převzetí služeb při selhání ve stejné síti může připojit k sobě navzájem i v případě, že při selhání v plánech různých obnovení.
    - **Brána sítě**– Pokud v cílové síti Azure nastavená brána sítě, virtuální počítače připojit k jiné místní virtuální počítače.

Mapování sítě funguje takto:

- Namapujete zdrojové síti virtuálních počítačů nástroje VMM na virtuální síť Azure.
- Po převzetí služeb při selhání virtuálních počítačů Azure ve zdroji sítě se připojí k namapované cílové virtuální síti.
- Nové virtuální počítače přidat do zdrojové síti virtuálních počítačů jsou připojené k namapované síti Azure, když dojde k replikaci.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak se virtuální počítač repliky po převzetí služeb při selhání připojí k této cílové podsíti.
- Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Příprava mapování sítě pro replikaci do sekundární lokality

Pokud replikujete do sekundární lokality, mapování sítě zajišťuje mapování mezi sítěmi virtuálních počítačů na zdrojovém serveru VMM a sítě virtuálních počítačů na cílovém serveru VMM. Mapování provede následující akce:

- **Síťové připojení**– připojí virtuální počítače na odpovídající sítě po převzetí služeb při selhání. Virtuální počítač repliky se připojí k cílové síti, který je namapovaný na zdrojovou síť.
- **Optimální umístění virtuálních počítačů**– optimálně umístí virtuální počítače replik na hostitelských serverech technologie Hyper-V. Virtuální počítače repliky jsou umístěny na hostitelích, kteří mohou přistupovat k namapovanou síť virtuálních počítačů.
- **Žádné mapování sítě**– Pokud nenakonfigurujete mapování sítě, virtuální počítače replik se nepřipojí k žádné síti virtuálních počítačů po převzetí služeb při selhání.

Mapování sítě funguje takto:

- Mapování sítě můžete nakonfigurovat sítě virtuálních počítačů na dvěma servery VMM nebo na jednom serveru VMM, pokud dvě lokality jsou spravovány stejném serveru.
- Při mapování správně nakonfigurovaná a je povolená replikace, virtuální počítač v primární lokalitě se připojí k síti a jeho repliky v cílovém umístění, budou připojené k jeho namapované síťové.
- Když vyberete cílovou síť virtuálních počítačů během mapování sítě v Site Recovery, cloudy VMM zdroje, které používají zdrojové síti virtuálních počítačů se zobrazí, společně s sítí dostupných cílových virtuálních počítačů v cílové cloudech, které se používají pro ochranu.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, na kterém se nachází zdrojový virtuální počítač, pak repliku virtuálního počítače se připojí k této cílové podsíti po převzetí služeb při selhání. Pokud není žádná cílová podsíť s odpovídajícím názvem, virtuální počítač se připojí k první podsíti v síti.

## <a name="example"></a>Příklad:

Tady je příklad pro ilustraci tento mechanismus. Podívejme se na organizaci s dvěma umístěními v New Yorku a Chicagu.

**Umístění** | **Server VMM** | **Sítě virtuálních počítačů** | **Mapovat na**
---|---|---|---
New York | VMM NewYork| VMNetwork1 NewYork | Mapovat do Chicaga VMNetwork1
 |  | VMNetwork2 NewYork | Není mapováno
Chicago | VMM Chicago| VMNetwork1 Chicago | Mapovat na VMNetwork1 NewYork
 | | VMNetwork1 Chicago | Není mapováno

V tomto příkladu:

- Pokud repliku, kterou virtuální počítač je vytvořen pro všechny virtuální počítač, který je připojen k VMNetwork1 NewYork, bude připojen k VMNetwork1 Chicagu.
- Pokud repliku, kterou virtuální počítač je vytvořen pro VMNetwork2 NewYork nebo VMNetwork2 Chicagu, nebude připojen k žádné síti.

Zde je, jak jsou cloudy VMM nastavit v našem příkladu organizace a logické sítě přidružené ke cloudům.

### <a name="cloud-protection-settings"></a>Nastavení ochrany cloudu

**Chráněném cloudu** | **Ochrana cloudu** | **Logické sítě (New Yorku)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>Není k dispozici</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>
SilverCloud2 | <p>Není k dispozici</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Nastavení sítě logické a virtuální počítač

**Umístění** | **Logické sítě** | **Přidružené sítě virtuálních počítačů**
---|---|---
New York | LogicalNetwork1 NewYork | VMNetwork1 NewYork
Chicago | LogicalNetwork1 Chicago | VMNetwork1 Chicago
 | LogicalNetwork2Chicago | VMNetwork2 Chicago

### <a name="target-network-settings"></a>Nastavení cílové sítě

Na základě tohoto nastavení, když vyberete Cílová síť virtuálních počítačů, v následující tabulce jsou možnosti, které budou k dispozici.

**Výběr** | **Chráněném cloudu** | **Ochrana cloudu** | **Cílová síť k dispozici**
---|---|---|---
VMNetwork1 Chicago | SilverCloud1 | SilverCloud2 | Dostupné
 | GoldCloud1 | GoldCloud2 | Dostupné
VMNetwork2 Chicago | SilverCloud1 | SilverCloud2 | Není k dispozici
 | GoldCloud1 | GoldCloud2 | Dostupné


Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, na kterém se nachází zdrojový virtuální počítač, pak virtuální počítač repliky se připojí k této cílové podsíti po převzetí služeb při selhání. Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.


### <a name="failback-behavior"></a>Chování navrácení služeb po obnovení

Pokud chcete zobrazit, co se stane, že v případě navrácení služeb po obnovení (zpětná replikace), Předpokládejme, že VMNetwork1 NewYork je namapována na VMNetwork1-Chicagu s následujícím nastavením.


**VIRTUÁLNÍ POČÍTAČ** | **Připojení k síti virtuálních počítačů**
---|---
VM1 | VMNetwork1 sítě
Virtuálního počítače 2 (repliky VM1) | VMNetwork1 Chicago

S těmito nastaveními pojďme si shrnout, co se stane, že v několika možných scénářích.

**Scénář** | **Výsledek**
---|---
Žádná změna v vlastnosti sítě virtuálních počítačů 2 po převzetí služeb při selhání. | Zůstane připojené ke zdrojové síti virtuálních počítačů 1.
Vlastnosti sítě virtuálních počítačů 2 se změní po převzetí služeb při selhání a není připojen. | 1 virtuální počítač není připojen.
Vlastnosti sítě virtuálních počítačů 2 se změní po převzetí služeb při selhání a je připojený k VMNetwork2 Chicagu. | Pokud není namapované VMNetwork2 Chicagu, bude odpojen virtuálních počítačů 1.
Mapování sítě VMNetwork1 Chicagu se změní. | 1 virtuální počítač bude připojený k síti nyní mapováno na VMNetwork1 Chicagu.



## <a name="next-steps"></a>Další postup

- [Další informace o](hyper-v-vmm-networking.md) IP adresování po převzetí služeb při selhání pro sekundární lokalita VMM.
- [Další informace o](concepts-on-premises-to-azure-networking.md) IP adresování po převzetí služeb při selhání do Azure.
