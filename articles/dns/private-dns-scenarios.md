---
title: Scénáře pro privátní zóny – Azure DNS
description: V tomto článku se dozvíte o běžných scénářích pro používání privátních zón Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939342"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scénáře prizemských zón Azure DNS

Privátní zóny Azure DNS poskytují překlad názvů v rámci virtuální sítě i mezi virtuálními sítěmi. V tomto článku se podíváme na některé běžné scénáře, které lze realizovat pomocí této funkce.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scénář: Překlad názvů s rozsahem jedné virtuální sítě
V tomto scénáři máte virtuální síť v Azure, která má řadu prostředků Azure v něm, včetně virtuálních počítačů (virtuálnípočítače). Chcete vyřešit prostředky z virtuální sítě prostřednictvím konkrétnínázev domény (zóna DNS) a potřebujete překlad názvů, které mají být soukromé a nejsou přístupné z Internetu. Kromě toho pro virtuální počítače v rámci virtuální sítě, budete potřebovat Azure automaticky zaregistrovat do zóny DNS. 

Tento scénář je znázorněn níže. Virtuální síť s názvem "A" obsahuje dva virtuální počítače (VNETA-VM1 a VNETA-VM2). Každý z nich má soukromé IP adresy přidružené. Jakmile vytvoříte privátní zónu s názvem contoso.com a propojíte tuto virtuální síť jako virtuální síť Registrace, Azure DNS automaticky vytvoří dva záznamy A v zóně, jak je znázorněno. Nyní dotazy DNS z VNETA-VM1 k vyřešení VNETA-VM2.contoso.com obdrží odpověď DNS, která obsahuje privátní IP vneta-VM2. Kromě toho reverzní dotaz DNS (PTR) pro privátní IP vneta-VM1 (10.0.0.1) vydané z VNETA-VM2 obdrží odpověď DNS, která obsahuje název VNETA-VM1, podle očekávání. 

![Rozlišení jedné virtuální sítě](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scénář: Překlad názvů napříč virtuálními sítěmi

Tento scénář je běžnější případ, kdy je třeba přidružit privátní zónu s více virtuálními sítěmi. Tento scénář se vejde architektury, jako je například hub-and-spoke model, kde je virtuální síť centrální rozbočovač, ke kterému jsou připojeny více dalších virtuálních sítí Spoke. Virtuální síť centrálního rozbočovače může být propojena jako virtuální síť Registrace do privátní zóny a virtuální sítě Spoke lze propojit jako virtuální sítě Řešení. 

Následující diagram znázorňuje jednoduchou verzi tohoto scénáře, kde existují pouze dvě virtuální sítě - A a B. A je označen jako registrace virtuální sítě a B je určen jako řešení virtuální sítě. Záměrem je, aby obě virtuální sítě sdílely společnou zónu contoso.com. Když je zóna vytvořena a virtuální sítě Řešení a registrace jsou propojeny se zónou, Azure automaticky zaregistruje záznamy DNS pro virtuální počítače (VNETA-VM1 a VNETA-VM2) z virtuální sítě A. Můžete také ručně přidat záznamy DNS do zóny pro virtuální počítače ve virtuální síti Rozlišení B. V tomto nastavení budete sledovat následující chování pro dotazy DNS dopředné a obrácené:
* Dotaz DNS z VNETB-VM1 ve virtuální síti Rozlišení B, pro VNETA-VM1.contoso.com, obdrží odpověď DNS obsahující privátní IP vneta-VM1.
* Reverzní DNS (PTR) dotaz z VNETB-VM2 ve virtuální síti Rozlišení B, pro 10.1.0.1, obdrží odpověď DNS obsahující VNETB-VM1.contoso.com.  
* Dotaz Reverse DNS (PTR) z VNETB-VM3 ve virtuální síti B řešení pro 10.0.0.1 obdrží NXDOMAIN. Důvodem je, že reverzní DNS dotazy jsou pouze obor na stejnou virtuální síť. 


![Více rozlišení virtuálnísítě](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scénář: Funkce Split-Horizon

V tomto scénáři máte případ použití, kde chcete realizovat různé chování rozlišení DNS v závislosti na tom, kde klient sedí (uvnitř Azure nebo na internetu), pro stejnou zónu DNS. Můžete mít například soukromou a veřejnou verzi aplikace, která má různé funkce nebo chování, ale chcete použít stejný název domény pro obě verze. Tento scénář lze realizovat pomocí Azure DNS vytvořením veřejné zóny DNS, stejně jako privátní zóny se stejným názvem.

Následující diagram znázorňuje tento scénář. Máte virtuální síť A, která má dva virtuální počítače (VNETA-VM1 a VNETA-VM2), které mají přidělené soukromé IP adresy i veřejné IP adresy. Vytvoříte veřejnou zónu DNS s názvem contoso.com a zaregistrujete veřejné IP adresy pro tyto virtuální servery jako záznamy DNS v rámci zóny. Můžete také vytvořit soukromou zónu DNS, která se také nazývá contoso.com zadáním a jako virtuální sítě Registrace. Azure automaticky zaregistruje virtuální počítače jako záznamy A do privátní zóny a ukáže na jejich privátní IP adresy.

Když teď internetový klient vydá dotaz DNS, aby vyhledal VNETA-VM1.contoso.com, Azure vrátí veřejný IP záznam z veřejné zóny. Pokud se stejný dotaz DNS vydává z jiného virtuálního počítače (například VNETA-VM2) ve stejné virtuální síti A, Azure vrátí soukromý IP záznam ze soukromé zóny. 

![Rozdělit Brian rozlišení](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Další kroky
Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md).

Přečtěte si, jak [vytvořit privátní zónu DNS](./private-dns-getstarted-powershell.md) v Azure DNS.

Informace o zónách a záznamech DNS naleznete na [stránce: DNS zones and records overview](dns-zones-records.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.

