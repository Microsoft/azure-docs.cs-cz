---
title: Scénáře pro privátní zóny – Azure DNS
description: V tomto článku se dozvíte o běžných scénářích použití Azure DNS Private Zones.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "76939342"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scénáře Azure DNS privátních zón

Azure DNS Private Zones poskytovat překlad názvů v rámci virtuální sítě i mezi virtuálními sítěmi. V tomto článku se podíváme na některé běžné scénáře, které se dají realizovat pomocí této funkce.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scénář: rozlišení názvů v oboru pro jednu virtuální síť
V tomto scénáři máte virtuální síť v Azure, která obsahuje řadu prostředků Azure, včetně virtuálních počítačů (VM). Chcete vyřešit prostředky z virtuální sítě pomocí konkrétního názvu domény (zóny DNS) a potřebujete, aby překlad IP adres byl privátní a nebyl přístupný z Internetu. Pro virtuální počítače v rámci virtuální sítě navíc potřebujete Azure, aby je automaticky zaregistrovali do zóny DNS. 

Tento scénář je znázorněný níže. Virtual Network s názvem A obsahuje dva virtuální počítače (PARTNERSKÉM-VM1 a PARTNERSKÉM-VM2). Každá z nich má přidruženou privátní IP adresy. Jakmile vytvoříte privátní zónu s názvem contoso.com a propojíte tuto virtuální síť jako registrační virtuální síť, Azure DNS se v zóně automaticky vytvoří dva záznamy, jak je znázorněno na tomto seznamu. Nyní budou dotazy DNS z PARTNERSKÉM-VM1 na řešení VNETA-VM2.contoso.com přijímat odpověď DNS, která obsahuje soukromou IP adresu PARTNERSKÉM-VM2. Kromě toho obdrží reverzní dotaz DNS (PTR) pro soukromou IP adresu PARTNERSKÉM-VM1 (10.0.0.1) vydanou v PARTNERSKÉM-VM2 odpověď DNS, která obsahuje název PARTNERSKÉM-VM1, jak se očekávalo. 

![Řešení jedné virtuální sítě](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scénář: rozlišení názvů napříč virtuálními sítěmi

Tento scénář je nejběžnějším případem, kdy potřebujete přidružit privátní zónu k více virtuálním sítím. Tento scénář se může vejít do architektury, jako je model hvězdicové sítě, kde je k dispozici virtuální síť centrálního rozbočovače, ke které se připojují víc virtuálních sítí s paprsky. Virtuální síť centrálního centra se dá propojit jako registrační virtuální síť se soukromou zónou a virtuální sítě paprsků se dají propojit jako virtuální sítě pro překlad. 

Následující diagram znázorňuje jednoduchou verzi tohoto scénáře, kdy jsou k dispozici pouze dvě virtuální sítě – a a B. Je určený jako registrační virtuální síť a B je určený jako virtuální síť pro překlad. Účelem je, aby obě virtuální sítě sdílely společné zóny contoso.com. Když se zóna vytvoří a virtuální sítě pro překlad a registraci jsou propojené s zónou, Azure bude automaticky registrovat záznamy DNS pro virtuální počítače (PARTNERSKÉM-VM1 a PARTNERSKÉM-VM2) z virtuální sítě A. Do zóny můžete také ručně přidat záznamy DNS pro virtuální počítače ve virtuální síti řešení B. V této instalaci se zobrazí následující chování pro předávací a reverzní dotazy DNS:
* Dotaz DNS z VNETB-VM1 ve virtuální síti překladu B pro VNETA-VM1.contoso.com obdrží odpověď DNS obsahující soukromou IP adresu PARTNERSKÉM-VM1.
* Dotaz reverzního DNS (PTR) z VNETB-VM2 ve virtuální síti překladu B pro 10.1.0.1 dostane odpověď DNS obsahující plně kvalifikovaný název domény VNETB-VM1.contoso.com.  
* Dotaz na reverzní DNS (PTR) z VNETB-VM3 ve virtuální síti překladu B, který bude pro 10.0.0.1, obdrží NXDOMAIN. Důvodem je, že reverzní dotazy DNS jsou v oboru pouze stejné virtuální síti. 


![Více rozlišení virtuální sítě](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scénář: funkce Split-Horizon

V tomto scénáři máte případ použití, ve kterém chcete využít jiné chování překladu DNS v závislosti na tom, kde je klient umístěný (uvnitř Azure nebo na internetu) pro stejnou zónu DNS. Můžete mít například soukromou a veřejnou verzi aplikace, která má jiné funkce nebo chování, ale chcete pro obě verze použít stejný název domény. Tento scénář je možné vytvořit pomocí Azure DNS vytvořením veřejné zóny DNS i privátní zóny se stejným názvem.

Následující diagram znázorňuje tento scénář. Máte virtuální síť A, která má dva virtuální počítače (PARTNERSKÉM-VM1 a PARTNERSKÉM-VM2), které mají přidělené soukromé IP adresy i veřejné IP adresy. Vytvoříte veřejnou zónu DNS s názvem contoso.com a zaregistrujete veřejné IP adresy pro tyto virtuální počítače jako záznamy DNS v rámci zóny. Také se vytvoří zóna Privátní DNS označovaná také jako contoso.com určující registrační virtuální síť. Azure automaticky registruje virtuální počítače jako záznamy do privátní zóny a odkazuje na jejich privátní IP adresy.

Když teď internetový klient vydá dotaz DNS, aby vyhledal VNETA-VM1.contoso.com, Azure vrátí záznam veřejné IP adresy z veřejné zóny. Pokud je stejný dotaz DNS vydaný z jiného virtuálního počítače (například: PARTNERSKÉM-VM2) ve stejné virtuální síti A, Azure vrátí záznam privátní IP adresy z privátní zóny. 

![Rozdělit Brian rozlišení](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Další kroky
Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md).

Naučte se [vytvořit privátní ZÓNU DNS](./private-dns-getstarted-powershell.md) v Azure DNS.

Další informace o zónách a záznamech DNS najdete v tématu [Přehled zón a záznamů DNS](dns-zones-records.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.

