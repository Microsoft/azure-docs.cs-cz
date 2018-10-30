---
title: Zachování IP adresy pro převzetí služeb při selhání virtuálních počítačů Azure | Dokumentace Microsoftu
description: Popisuje, jak zachovat IP adresy po převzetí služeb při selhání virtuálních počítačů Azure pro zotavení po havárii do sekundární oblasti Azure Site Recovery
ms.service: site-recovery
ms.date: 10/16/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 86adaa21a069c168b512231ba231940bfa2ef9e8
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213028"
---
# <a name="ip-address-retention-for-azure-vm-failover"></a>Uchování IP adres pro převzetí služeb při selhání virtuálního počítače Azure

Azure Site Recovery nabízí zotavení po havárii pro virtuální počítače Azure. Při převzetí služeb při selhání z jedné oblasti Azure do jiné, zákazníci často vyžadují uchováváním jejich konfigurace protokolu IP. Site Recovery standardně napodobuje zdrojová virtuální síť a podsíť struktury při vytváření těchto prostředků v cílové oblasti. Site Recovery pro virtuální počítače Azure nakonfigurovaný se statickou privátní IP adresy, také udržuje nezaručené pokusí zřídit stejnou privátní IP adresu na cílovém virtuálním počítači, pokud tuto IP adresu již neblokuje prostředku Azure nebo replikovaný virtuální počítač.

Pro jednoduché aplikace nad výchozí konfigurace je vše, co je potřeba. Pro složitější podnikové aplikace možná budou zákazníci ke zřízení dalších síťových prostředků k zajištění post-převzetí služeb při selhání připojení s ostatními součástmi svoji infrastrukturu. Tento článek vysvětluje požadavky na síť převzetí služby při selhání virtuálních počítačů Azure z jedné oblasti do jiného při zachování virtuálního počítače IP adresy.

## <a name="azure-to-azure-connectivity"></a>Připojení k Azure do Azure

První scénář považujeme **firmy A** , který má všechny své infrastruktury aplikace běžící v Azure. Obchodní kontinuity a dodržování předpisů z důvodů **firmy A** rozhodne pomocí Azure Site Recovery můžete ochránit své aplikace.

Zadaný požadavek na dobu uchování IP (například pro vazby aplikace), firmy A má stejnou virtuální síť a podsíť strukturu v cílové oblasti. Pokud chcete dál snížit plánovanou dobu obnovení (RTO), **firmy A** využívá uzlech replik pro SQL Always ON, řadičů domény a podobně a tyto repliky uzly jsou umístěny v jiné virtuální síti v cílové oblasti. Použití jiným adresním prostorem pro uzly repliky umožňuje **firmy A** k navázání připojení VPN typu site-to-site mezi zdrojovou a cílovou oblastí, které by jinak možná v případě, že stejné adresní prostor se používá na obou koncích .

Zde je, jak Síťová architektura vypadá před převzetí služeb při selhání:
- Aplikace virtuální počítače jsou hostované v Azure východní Asie, využívá virtuální sítě Azure s adres 10.1.0.0/16 místa. Tato virtuální síť jmenuje **zdrojová virtuální síť**.
- Aplikační úlohy jsou rozděleny mezi třemi podsítěmi – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, v uvedeném pořadí s názvem **podsíť 1**, **podsíť 2**, **podsítě 3**.
- Azure jihovýchodní Asie cílové oblasti a má virtuální síť pro obnovení, které imituje konfiguraci adresy prostoru a podsítě ve zdroji. Tato virtuální síť jmenuje **virtuální sítě pro zotavení**.
- Uzly repliky, například oprávnění pro Always On, řadič domény, atd. jsou umístěny ve virtuální síti s adresa místa 10.2.0.0/16 uvnitř 4 podsítě s 10.2.4.0/24 adresu. Virtuální síť jmenuje **virtuální sítě Azure** a je na Azure jihovýchodní Asie.
- **Zdrojové virtuální sítě** a **virtuální sítě Azure** připojeni pomocí připojení site-to-site VPN.
- **Virtuální sítě pro zotavení** není spojena s jinou virtuální síť.
- **Společnosti A** přiřadí/ověří cílová IP adresa pro replikované položky. V tomto příkladu cílová IP adresa je stejná jako zdrojová IP adresa pro každý virtuální počítač.

![Připojení k Azure do Azure před převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Převzetí služeb při selhání celé oblasti

V případě regionálního výpadku **firmy A** můžete obnovit jeho celé nasazení rychle a snadno pomocí Azure Site Recovery výkonné [plány obnovení](site-recovery-create-recovery-plans.md). Už nastavený cílová IP adresa pro každý virtuální počítač před převzetí služeb při selhání, **společnosti A** můžete orchestrovat převzetí služeb při selhání a automatizovat navazování připojení mezi virtuální sítí pro obnovení a virtuální síť Azure, jak je znázorněno následujícím grafu.

![Připojení k Azure do Azure úplná oblasti převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

V závislosti na požadavcích aplikace může být připojení mezi dvěma virtuálními sítěmi v cílové oblasti zavedených před, během (jako přechodný krok při) nebo po převzetí služeb při selhání. Použití [plány obnovení](site-recovery-create-recovery-plans.md) přidat skripty a definovat pořadí převzetí služeb při selhání.

Firmy A má také na výběr mezi používáním partnerský vztah virtuální sítě nebo VPN typu Site-to-Site k navázání připojení mezi virtuální sítí pro obnovení a virtuální sítí VNet Azure. VNET Peering nepoužívá bránu sítě VPN a má jiná omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Pro převzetí služeb při selhání je obecně vhodné, aby napodoboval zdroje připojení, včetně připojení typu, chcete-li minimalizovat neočekávané incidenty vyplývající z změn v síti.

### <a name="isolated-application-failover"></a>Převzetí služeb při selhání izolované aplikace

Za určitých podmínek může být nutné uživatelů k různým částem převzetí služeb při selhání jejich aplikační infrastruktury. Jedním z takových příkladů přebírají konkrétní aplikaci nebo úroveň, která je umístěna ve vyhrazené podsíti. I podsítě převzetí služeb při selhání s uchováním IP je možné, není vhodné pro většinu situací, jak se podstatně zvyšuje nekonzistence připojení. Připojení k podsíti dojde ke ztrátě také do jiných podsítí v rámci stejné virtuální síti Azure.

Ještě lepší způsob, aby se zohlednily požadavky aplikací na úrovni podsítě převzetí služeb při selhání je pro převzetí služeb při selhání použít jiné cílové IP adresy (Pokud připojení se vyžaduje do jiných podsítí ve virtuální síti zdroje) a izolovat každou aplikaci v svůj vlastní vyhrazený virtuální síť ve zdroji. Druhý přístup můžete navázat připojení k síti mezi ve zdroji a stejné emulovat při převzetí služeb při selhání do cílové oblasti.

K jednotlivé žádosti o odolnosti proti chybám, se doporučuje organizace aplikace v svůj vlastní vyhrazený virtuální sítě a naváže připojení mezi těmito virtuálními sítěmi podle potřeby. To umožňuje převzetí služeb při selhání izolované aplikace při zachování původního privátních IP adres.

Konfigurace pre-převzetí služeb při selhání pak by měl vypadat takto:
- Aplikace virtuální počítače jsou hostované v Azure východní Asie, využívá virtuální sítě Azure s adres 10.1.0.0/16 místa pro první aplikaci a 10.2.0.0/16 pro druhou aplikaci. Virtuální sítě jsou pojmenované **zdroje ze sítě VNet1** a **zdroje ze sítě VNet2** první a druhé aplikace, v uvedeném pořadí.
- Každá virtuální síť je dále rozdělit do dvou podsítí.
- Azure jihovýchodní Asie cílové oblasti a má virtuální sítě pro zotavení obnovení ze sítě VNet1 a VNet2 obnovení.
- Uzly repliky, například oprávnění pro Always On, řadič domény, atd. jsou umístěny ve virtuální síti s adresa místa 10.3.0.0/16 uvnitř **podsítě 4** s 10.3.4.0/24 adresu. Virtuální síť s názvem virtuální sítě Azure a je na Azure jihovýchodní Asie.
- **Zdroje ze sítě VNet1** a **virtuální sítě Azure** připojeni pomocí připojení site-to-site VPN. Obdobně **zdroje ze sítě VNet2** a **virtuální sítě Azure** připojeni pomocí připojení site-to-site VPN.
- **Zdroje ze sítě VNet1** a **zdroje ze sítě VNet2** jsou také připojené prostřednictvím sítě VPN typu S2S v tomto příkladu. Vzhledem k tomu, že dvě virtuální sítě nacházejí ve stejné oblasti, VNet peering lze také místo sítě VPN typu S2S.
- **Obnovení ze sítě VNet1** a **obnovení ze sítě VNet2** nejsou připojené žádné virtuální sítě.
- Pokud chcete snížit plánovanou dobu obnovení (RTO), brány VPN jsou nakonfigurované na **obnovení ze sítě VNet1** a **obnovení ze sítě VNet2** před převzetí služeb při selhání.

![Připojení k Azure do Azure izolované aplikace před převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

V případě situaci po havárii, která ovlivní pouze jednu aplikaci (v tomto příkladu umístěnými v síti VNet2 zdroj) můžete firmy A obnovení příslušné aplikace následujícím způsobem:
- Připojení VPN mezi **zdroje ze sítě VNet1** a **zdroje ze sítě VNet2**a mezi **zdroje ze sítě VNet2** a **virtuální sítě Azure** jsou odpojené.
- Připojení k síti VPN se vytvoří mezi **zdroje ze sítě VNet1** a **obnovení ze sítě VNet2**a mezi **obnovení ze sítě VNet2** a **virtuální sítě Azure**.
- Virtuální počítače z **zdroje ze sítě VNet2** při selhání do **obnovení ze sítě VNet2**.

![Připojení k Azure do Azure izolovaná aplikace po převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

Výše izolované převzetí služeb při selhání se rozšířit příklad zahrnout další aplikace a připojení k síti. Doporučuje se dodržovat model podobné jako připojení, pokud je to možné, při převzetí služeb při selhání ze zdroje do cíle.

### <a name="further-considerations"></a>Další důležité informace

Brány VPN využívat veřejné IP adresy a směrování brány k navázání připojení. Pokud nechcete použít veřejné IP adresy nebo chcete vyhnout dalších segmentů směrování, můžete použít Azure [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) na vytvoření partnerského vztahu virtuálních sítí mezi [podporovaných oblastech Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="on-premises-to-azure-connectivity"></a>Připojení na místní – Azure

Druhý scénář považujeme **společnosti B** , která má část jeho infrastruktury aplikace běžící na Azure a zbytek spuštěné místně. Obchodní kontinuity a dodržování předpisů z důvodů **společnosti B** rozhodne chránit své aplikace běžící v Azure pomocí Azure Site Recovery.

Zde je, jak Síťová architektura vypadá před převzetí služeb při selhání:
- Aplikace virtuální počítače jsou hostované v Azure východní Asie, využívá virtuální sítě Azure s adres 10.1.0.0/16 místa. Tato virtuální síť jmenuje **zdrojová virtuální síť**.
- Aplikační úlohy jsou rozděleny mezi třemi podsítěmi – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, v uvedeném pořadí s názvem **podsíť 1**, **podsíť 2**, **podsítě 3**.
- Azure jihovýchodní Asie cílové oblasti a má virtuální síť pro obnovení, které imituje konfiguraci adresy prostoru a podsítě ve zdroji. Tato virtuální síť jmenuje **virtuální sítě pro zotavení**.
- Virtuální počítače v Azure východní Asie jsou připojené k místnímu datovému centru prostřednictvím ExpressRoute nebo VPN typu Site-to-Site.
- Pokud chcete snížit plánovanou dobu obnovení (RTO), B společnosti zřídí brány na virtuální síť pro obnovení v Azure jihovýchodní Asie před převzetí služeb při selhání.
- **Společnost B** přiřadí/ověří cílová IP adresa pro replikované položky. V tomto příkladu cílová IP adresa je stejná jako zdrojová IP adresa pro každý virtuální počítač

![Připojení na místní – Azure před převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Převzetí služeb při selhání celé oblasti

V případě regionálního výpadku **společnosti B** můžete obnovit jeho celé nasazení rychle a snadno pomocí Azure Site Recovery výkonné [plány obnovení](site-recovery-create-recovery-plans.md). Už nastavený cílová IP adresa pro každý virtuální počítač před převzetí služeb při selhání, **společnosti B** můžete orchestrovat převzetí služeb při selhání a automatizovat navazování připojení mezi virtuální sítí pro obnovení a místním datovým centrem, jak je znázorněno následujícím grafu.

Původní připojení mezi Azure východní Asie a místním datovým centrem by měl odpojen před navázáním připojení mezi Azure jihovýchodní Asii a v místním datovém centru. Místní směrování je také překonfigurovat tak, aby odkazoval na cílové oblasti a bran příspěvku převzetí služeb při selhání.

![Připojení na místní – Azure po převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>Převzetí služeb při selhání podsíť

Na rozdíl od scénáři Azure do Azure pro **firmy A**, úrovni podsítě převzetí služeb při selhání není možné v tomto případě pro **společnosti B**. Je to proto adresního prostoru na zdroj a obnovení virtuálních sítí je stejný a původní zdroj pro místní připojení není aktivní.

K zajištění odolnosti proti chybám aplikace, doporučujeme, že každá aplikace je umístěno v svůj vlastní vyhrazený virtuální síť Azure. Aplikace můžete potom převzetí služeb při selhání v izolaci a vyžaduje místní k připojení ke zdroji je možné směrovat do cílové oblasti, jak je popsáno výše.

## <a name="next-steps"></a>Další postup
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md).
