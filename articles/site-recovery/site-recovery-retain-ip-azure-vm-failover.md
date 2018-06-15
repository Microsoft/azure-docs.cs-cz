---
title: Zachovat IP adres při přebírání služeb při selhání virtuálních počítačích Azure jiné oblasti Azure | Microsoft Docs
description: Popisuje, jak chcete zachovat IP adresy pro scénáře převzetí služeb při selhání Azure do Azure s Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 9a9c18bfe9073e5af94c7bd8f0fbb91651387731
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071660"
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Uchování IP adresu pro převzetí služeb při selhání virtuálního počítače Azure

Azure Site Recovery umožňuje zotavení po havárii pro virtuální počítače Azure. Při selhání se z jedné oblasti Azure do druhého, zákazníci často potřebují uchování jejich konfigurace protokolu IP. Obnovení lokality ve výchozím nastavení, napodobuje zdrojové virtuální síť a podsíť struktury při vytváření tyto prostředky v oblasti cíl. Site Recovery pro virtuální počítače Azure nakonfigurovaný se statickou privátní IP adresy, také díky usilovně pokusí zřídit stejnou privátní IP Adresou na cílovém virtuálním počítači, pokud tento IP není již zablokován prostředek služby Azure nebo na replikované virtuální počítač.

Jednoduché v aplikacích pro výše uvedené výchozí konfigurace je všechno, co je potřeba. Pro složitější podnikové aplikace možná muset zákazníci zřízení dalších prostředků sítě zajistit post-převzetí služeb při selhání připojení s ostatními součástmi svoji infrastrukturu. Tento článek vysvětluje síťové požadavky pro selhání přes virtuální počítače Azure z jedné oblasti do druhého a přitom zachovat virtuálních počítačů pro IP adresy.

## <a name="azure-to-azure-connectivity"></a>Připojení k Azure do Azure

U prvního scénáře, jsme zvažte **společnosti A** má všechny jeho infrastrukturu aplikace běžící v Azure. Z pracovních důvodů kontinuitu a dodržování předpisů **společnosti A** rozhodne používat Azure Site Recovery k ochraně jeho aplikace.

Zadaný požadavek IP uchování (například pro vazby aplikace), společnost A má stejnou virtuální síť a podsíť strukturu na cílová oblast. Pokud chcete dál snížit cíli času obnovení (RTO), **společnosti A** využívá uzlech replik pro SQL Always ON, řadiče domény, atd. a tyto repliky uzly jsou umístěny v jinou virtuální síť na cílová oblast. Použití jiným adresním prostorem pro uzly repliky umožňuje **společnosti A** k navázání připojení site-to-site VPN mezi zdrojem a cílem oblastí, které by jinak možné v případě stejné adresní prostor se používá na obou koncích .

Zde je, jak síťovou architekturu vypadá před převzetí služeb při selhání:
- Virtuální počítače aplikace jsou hostované v Azure východní Asie, využívá virtuální síť Azure s 10.1.0.0/16 prostor adres. Tato virtuální síť je s názvem **zdroj VNet**.
- Úlohy aplikací jsou rozděleny mezi tři podsítě – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, v uvedeném pořadí s názvem **podsíť 1**, **podsíť 2**, **podsíť 3**.
- Azure jihovýchodní Asie je cílová oblast a má obnovení virtuální síť, která napodobuje konfiguraci adresy prostoru a podsítě zdroje. Tato virtuální síť je s názvem **obnovení VNet**.
- Uzlech replik, jako jsou ty, které vyžaduje pro Always On, řadič domény, atd. jsou umístěny ve virtuální síti s adresu 10.2.0.0/16 místo uvnitř 4 podsíť s 10.2.4.0/24 adresu. Název virtuální sítě **virtuální síť Azure** a je v Azure jihovýchodní Asie.
- **Zdroj VNet** a **virtuální síť Azure** jsou propojeny pomocí připojení site-to-site VPN.
- **Virtuální síť obnovení** není připojen k žádné virtuální síti.
- **Společnosti A** přiřadí/ověřuje cílová IP adresa pro replikované položky. V tomto příkladu cílová IP adresa je stejný jako zdrojové IP adresy pro každý virtuální počítač.

![Připojení k Azure do Azure před převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Oblasti úplnou převzetí služeb při selhání

V případě výpadku regionální **společnosti A** můžete obnovit jeho celého nasazení snadno a rychle pomocí Azure Site Recovery výkonné [plány obnovení](site-recovery-create-recovery-plans.md). S již nastaven cílová IP adresa pro každý virtuální počítač před převzetí služeb při selhání, **společnosti A** může orchestraci převzetí služeb při selhání a automatizovat navázání připojení mezi virtuální sítí obnovení a virtuální síť Azure, jak je znázorněno v následujícím obrázku.

![Připojení k Azure do Azure oblasti úplnou převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

V závislosti na požadavcích aplikace, může být připojení mezi oběma virtuálními sítěmi na cílová oblast zavedené před, během (jako přechodný krok) nebo po převzetí služeb při selhání. Použití [plány obnovení](site-recovery-create-recovery-plans.md) přidat skripty a definovat pořadí převzetí služeb při selhání.

Společnosti A má také možnost použití partnerský vztah virtuální síť nebo síť Site-to-Site VPN k navázání připojení mezi virtuální sítí obnovení a virtuální síť Azure. VNET Peering nepoužívá bránu sítě VPN a má jiná omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Pro převzetí služeb při selhání je většinou vhodné tak, aby napodoboval zdroj připojení, včetně typ připojení, chcete-li minimalizovat nepředvídatelným incidenty vyplývající z změn v síti.

### <a name="isolated-application-failover"></a>Izolované aplikace převzetí služeb při selhání

Za určitých podmínek mohou uživatelé potřebovat pro převzetí služeb při selhání částí jejich infrastruktury aplikací. Jedním z těchto příkladů je přebírání služeb při selhání konkrétní aplikace nebo vrstvy, která je umístěna v rámci jedné vyhrazené podsítě. Při selhání podsíť s dobou uchování IP je možné, není vhodné pro většinu situace, jako je podstatně zvýší připojení nekonzistence. Připojení k podsíti dojde ke ztrátě také do jiných podsítí v rámci stejné virtuální síti Azure.

Lepší způsob, jak účet pro požadavky na úrovni podsítě aplikace převzetí služeb při selhání je použít jiné cílové IP adresy pro převzetí služeb při selhání (Pokud je připojení je vyžadována do jiných podsítí ve virtuální síti zdroj) nebo izolovat každou aplikaci v jeho vlastní vyhrazené virtuální síť na zdroji. S pozdější přístup můžete vytvořit připojení k síti mezi ve zdroji a emulovat stejné při přebírání služeb při selhání cílová oblast.

Do architektury jednotlivých žádostí o odolnost, by měl obsahovat aplikace svůj vlastní vyhrazený virtuální sítě a navázat připojení mezi těmito virtuálními sítěmi podle potřeby. To umožňuje izolované aplikační převzetí služeb při selhání a přitom zachovat původní privátní IP adresy.

Konfigurace pre-převzetí služeb při selhání pak vypadá takto:
- Virtuální počítače aplikace jsou hostované v Azure východní Asie, využívá virtuální síť Azure s 10.1.0.0/16 prostoru adres pro první aplikaci a 10.2.0.0/16 pro druhou aplikaci. Virtuální sítě jsou pojmenované **zdroj VNet1** a **zdroj VNet2** pro aplikaci první a druhý v uvedeném pořadí.
- Každý virtuální sítě je dále rozdělit na dvě podsítě.
- Azure jihovýchodní Asie je cílová oblast a má virtuální sítě obnovení VNet1 obnovení a obnovení VNet2.
- Uzlech replik, jako jsou ty, které vyžaduje pro Always On, řadič domény, atd. jsou umístěny ve virtuální síti s 10.3.0.0/16 prostoru adres uvnitř **podsíť 4** s 10.3.4.0/24 adresu. Virtuální sítě se označuje jako virtuální síť Azure a je v Azure jihovýchodní Asie.
- **Zdroj VNet1** a **virtuální síť Azure** jsou propojeny pomocí připojení site-to-site VPN. Podobně **zdroj VNet2** a **virtuální síť Azure** jsou také připojené prostřednictvím připojení VPN typu site-to-site.
- **Zdroj VNet1** a **VNet2 zdroje** jsou také připojené prostřednictvím S2S VPN v tomto příkladu. Vzhledem k tomu, že dvě virtuální sítě jsou ve stejné oblasti, partnerský vztah virtuální sítě lze také místo S2S VPN.
- **Obnovení VNet1** a **obnovení VNet2** není připojen k žádné virtuální síti.
- Pokud chcete zkrátit cíli času obnovení (RTO), jsou nakonfigurované brány sítě VPN na **obnovení VNet1** a **VNet2 obnovení** před převzetí služeb při selhání.

![Připojení k Azure do Azure izolované aplikace před převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

V případě situaci po havárii, která ovlivňuje pouze jednu aplikaci (v tomto příkladu, který byl součástí zdroje VNet2) můžete společnosti A obnovit příslušné takto:
- Připojení VPN mezi **zdroj VNet1** a **zdroj VNet2**a mezi **zdroj VNet2** a **virtuální síť Azure** jsou odpojené.
- Připojení k síti VPN se vytvoří mezi **zdroj VNet1** a **obnovení VNet2**a mezi **obnovení VNet2** a **virtuální síť Azure**.
- Virtuální počítače z **zdroj VNet2** jsou převzal **obnovení VNet2**.

![Připojení k Azure do Azure izolované aplikace po převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

Výše izolované převzetí služeb při selhání můžete příklad rozšířit tak, aby zahrnout další aplikace a síťové připojení. Doporučuje se podle modelu podobné jako připojení, pokud je to možné, při přebírání služeb při selhání ze zdroje k cíli.

### <a name="further-considerations"></a>Další informace

Brány sítě VPN využívat veřejné IP adresy a segmentů směrování brány k navázání připojení. Pokud nechcete použít veřejnou IP adresu, nebo chcete vyhnout další směrování, můžete použít Azure [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) rovnocenných počítačů virtuální sítě přes [podporované oblasti Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="on-premises-to-azure-connectivity"></a>Připojení na místní do Azure

Druhý scénář jsme zvažte **společnosti B** má součástí infrastruktury jeho aplikace spuštěné v Azure a zbytek spuštění místně. Z pracovních důvodů kontinuitu a dodržování předpisů **společnosti B** rozhodne používat Azure Site Recovery k ochraně jeho aplikace běžící v Azure.

Zde je, jak síťovou architekturu vypadá před převzetí služeb při selhání:
- Virtuální počítače aplikace jsou hostované v Azure východní Asie, využívá virtuální síť Azure s 10.1.0.0/16 prostor adres. Tato virtuální síť je s názvem **zdroj VNet**.
- Úlohy aplikací jsou rozděleny mezi tři podsítě – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, v uvedeném pořadí s názvem **podsíť 1**, **podsíť 2**, **podsíť 3**.
- Azure jihovýchodní Asie je cílová oblast a má obnovení virtuální síť, která napodobuje konfiguraci adresy prostoru a podsítě zdroje. Tato virtuální síť je s názvem **obnovení VNet**.
- Virtuální počítače v Azure východní Asie jsou připojené do místního datového centra prostřednictvím ExpressRoute nebo VPN typu Site-to-Site.
- Pokud chcete zkrátit cíli času obnovení (RTO), zřídí společnosti B brány o obnovení virtuální sítě v Azure jihovýchodní Asie před převzetí služeb při selhání.
- **Společnosti B** přiřadí/ověřuje cílová IP adresa pro replikované položky. V tomto příkladu cílová IP adresa je stejný jako zdrojové IP adresy pro každý virtuální počítač

![Připojení na místní do Azure před převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Oblasti úplnou převzetí služeb při selhání

V případě výpadku regionální **společnosti B** můžete obnovit jeho celého nasazení snadno a rychle pomocí Azure Site Recovery výkonné [plány obnovení](site-recovery-create-recovery-plans.md). S již nastaven cílová IP adresa pro každý virtuální počítač před převzetí služeb při selhání, **společnosti B** může orchestraci převzetí služeb při selhání a automatizovat navázání připojení mezi virtuální sítí obnovení a místního datového centra, jak je znázorněno v následujícím obrázku.

Původní připojení mezi východní Asie Azure a místního datového centra by měl být odpojen před navázáním připojení mezi jihovýchodní Asie Azure a místního datového centra. Místní směrování je také překonfigurovat tak, aby odkazoval cílová oblast a bran post převzetí služeb při selhání.

![Připojení na místní do Azure po převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>Podsíť převzetí služeb při selhání

Na rozdíl od Azure do Azure scénář popsaný pro **společnosti A**, selhání úrovni podsítě není možné v tomto případě pro **společnosti B**. Je to proto, že je adresní prostor na zdroj a obnovení virtuálních sítí stejná a původního zdroje místní připojení je aktivní.

Zajistit odolnost aplikace se doporučuje, aby každá aplikace je umístěna ve vlastní vyhrazené virtuální síť Azure. Aplikace můžete pak převzetí služeb při selhání v izolaci a vyžaduje místní k připojení ke zdroji můžete směrovat na cílová oblast, jak je popsáno výše.

## <a name="next-steps"></a>Další postup
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md).
