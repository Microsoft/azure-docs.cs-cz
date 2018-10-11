---
title: Informace o mapování sítí pro replikaci virtuálních počítačů Hyper-V (s VMM) do Azure pomocí Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak nastavit mapování sítě pro replikaci virtuálních počítačů Hyper-V spravované v cloudech VMM pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: d683554a97a1616b0d4d7b1ae95d62b476de04eb
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078507"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-replication-to-azure"></a>Příprava mapování sítí pro replikaci virtuálních počítačů Hyper-V do Azure


Tento článek vám pomůže pochopit a příprava na mapování sítě při replikaci virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do Azure nebo do sekundární lokality, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Příprava mapování sítí pro replikaci do Azure

Pokud provádíte replikaci do Azure, sítě mapování mapuje sítě virtuálních počítačů na zdrojovém serveru VMM a cílové virtuální sítě Azure. Mapování provede následující akce:
    -  **Připojení k síti**– zajišťuje, že replikované virtuální počítače Azure jsou připojené k namapované síti. Všechny počítače, které převzetí služeb při selhání ve stejné síti můžete připojit k sobě navzájem i v případě selhání v plánech obnovení jinou.
    - **Brána sítě**– Pokud je v cílové síti Azure nastavená síťová brána, virtuální počítače můžete připojit k jiným místním virtuálním počítačům.

Mapování sítě funguje takto:

- Ke službě Azure virtual network namapujete zdrojové síti virtuálních počítačů ve VMM.
- Po převzetí služeb při selhání virtuálních počítačů Azure ve zdrojové síti připojí pro mapovanou cílovou virtuální sítí.
- Nové virtuální počítače, které jsou přidány do zdrojové síti virtuálních počítačů jsou připojené k namapované síti Azure, když dojde k replikaci.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak se virtuální počítač repliky po převzetí služeb při selhání připojí k této cílové podsíti.
- Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Příprava mapování sítí pro replikaci do sekundární lokality

Při replikaci do sekundární lokality, mapování sítě zajišťuje mapování mezi sítěmi virtuálních počítačů na zdrojovém serveru VMM a sítí virtuálních počítačů na cílovém serveru VMM. Mapování provede následující akce:

- **Připojení k síti**– připojí virtuální počítače k příslušným sítím po převzetí služeb při selhání. Virtuální počítač repliky se připojí k cílové síti, která se mapuje na zdrojové síti.
- **Optimální umístění virtuálního počítače**– virtuální počítače replik optimálně umístí na hostitelské servery technologie Hyper-V. Replikované virtuální počítače jsou umístěny na hostitelích, kteří mohou přistupovat k namapovanou síť virtuálních počítačů.
- **Žádné mapování sítě**– Pokud nenakonfigurujete mapování sítě, virtuální počítače replik se nepřipojí k žádné síti virtuálních počítačů po převzetí služeb při selhání.

Mapování sítě funguje takto:

- Je možné nakonfigurovat mapování sítě mezi sítěmi virtuálních počítačů na dva servery VMM nebo na jeden server VMM, pokud dvě lokality spravuje stejný server.
- Při mapování je správně nakonfigurovaná a je povolená replikace virtuálního počítače v primární lokalitě se připojí k síti a její repliky v cílovém umístění budou připojeny k její namapované síťové.
- Když vyberete cílovou síť virtuálních počítačů během mapování sítě v Site Recovery, cloudy VMM zdroje, které používají zdrojové síti virtuálních počítačů se zobrazí, spolu s dostupné cílové sítě virtuálních počítačů v cílové cloudech, které se používají pro ochranu.
- Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak virtuální počítač repliky se připojí k této cílové podsíti po převzetí služeb při selhání. Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se k první podsíti v síti virtuálního počítače.

## <a name="example"></a>Příklad:

Tady je příklad pro ilustraci tohoto mechanismu. Pojďme se na organizaci s dvěma umístěními v Brno a Ostrava.

**Umístění** | **Server VMM** | **Sítě virtuálních počítačů** | **Mapovat na**
---|---|---|---
New York | Nástroj VMM NewYork| VMNetwork1 NewYork | Mapovat na VMNetwork1 Chicago
 |  | VMNetwork2 NewYork | Nemapováno
Chicago | VMM – Praha| VMNetwork1 Chicago | Mapovat na VMNetwork1 NewYork
 | | VMNetwork2 Chicago | Nemapováno

V tomto příkladu:

- Pokud replika, kterou virtuální počítač se vytvoří pro jakýkoli virtuální počítač, který je připojený k VMNetwork1 NewYork, budou připojeny k VMNetwork1 Chicagu.
- Pokud replika, kterou virtuální počítač se vytvoří pro VMNetwork2 NewYork nebo VMNetwork2 Chicago, nebude připojen k žádné síti.

Zde je, jak jsou nastavené cloudy VMM v naší ukázkové společnosti a logické sítě přidružené ke cloudům.

### <a name="cloud-protection-settings"></a>Nastavení ochrany cloudu

**Chráněnému cloudu** | **Ochrana cloudu** | **Logická síť (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>Není k dispozici</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>
SilverCloud2 | <p>Není k dispozici</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Nastavení logické a VM sítě

**Umístění** | **Logické sítě** | **Přidružené sítě virtuálních počítačů**
---|---|---
New York | LogicalNetwork1 NewYork | VMNetwork1 NewYork
Chicago | LogicalNetwork1 Chicago | VMNetwork1 Chicago
 | LogicalNetwork2Chicago | VMNetwork2 Chicago

### <a name="target-network-settings"></a>Nastavení cílové sítě

Na základě tohoto nastavení, když vyberete Cílová síť virtuálních počítačů, v následující tabulce jsou uvedeny volby, které bude k dispozici.

**Výběr** | **Chráněnému cloudu** | **Ochrana cloudu** | **Cílová síť k dispozici**
---|---|---|---
VMNetwork1 Chicago | SilverCloud1 | SilverCloud2 | K dispozici.
 | GoldCloud1 | GoldCloud2 | K dispozici.
VMNetwork2 Chicago | SilverCloud1 | SilverCloud2 | Není k dispozici.
 | GoldCloud1 | GoldCloud2 | K dispozici.


Pokud má cílová síť více podsítí a jedna z těchto podsítí má stejný název jako podsíť, ve které je umístěný zdrojový virtuální počítač, pak virtuální počítač repliky se připojí k této cílové podsíti po převzetí služeb při selhání. Pokud neexistuje žádná cílová podsíť s odpovídajícím názvem, připojí se virtuální počítač k první podsíti v síti.


### <a name="failback-behavior"></a>Chování navrácení služeb po obnovení

Pokud chcete zobrazit, co se stane v případě navrácení služeb po obnovení (zpětná replikace), Předpokládejme, že VMNetwork1 NewYork je namapována na VMNetwork1 Chicagu, s následujícími nastaveními.


**VIRTUÁLNÍ POČÍTAČ** | **Připojení k síti virtuálních počítačů**
---|---
VM1 | VMNetwork1 sítě
VM2 (repliky VM1) | VMNetwork1 Chicago

S těmito nastaveními Pojďme se podívat na co se děje v několika případech je to možné.

**Scénář** | **Výsledek**
---|---
Žádné změny v okně Vlastnosti sítě virtuálních počítačů 2 po převzetí služeb při selhání. | Zůstává připojené ke zdrojové síti virtuálních počítačů 1.
Vlastnosti sítě virtuálních počítačů 2 se změní po převzetí služeb při selhání a je odpojený. | 1 virtuální počítač je odpojena.
Vlastnosti sítě virtuálních počítačů 2 se změní po převzetí služeb při selhání a je připojen k VMNetwork2 Chicagu. | Pokud není namapována VMNetwork2 Chicago, bude odpojen virtuálního počítače 1.
Mapování sítě z Chicaga VMNetwork1 se změní. | 1 virtuální počítač se připojí k síti do Chicaga VMNetwork1 nyní namapována.



## <a name="next-steps"></a>Další postup

- [Další informace o](hyper-v-vmm-networking.md) přidělování IP adres po převzetí služeb při selhání do sekundární lokality VMM.
- [Další informace o](concepts-on-premises-to-azure-networking.md) přidělování IP adres po převzetí služeb při selhání do Azure.
