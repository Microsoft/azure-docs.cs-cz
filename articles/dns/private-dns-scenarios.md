---
title: Scénáře pro zóny privátní Azure DNS | Microsoft Docs
description: Přehled běžných scénářů pro používání Azure DNS privátní zóny.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: de543913d4f8264fa8e5b3bca0c510c99c479cae
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="azure-dns-private-zones-scenarios"></a>Scénáře Azure privátní DNS zóny
Zóny DNS privátní Azure překladu názvů v rámci virtuální sítě i mezi virtuálními sítěmi. V tomto článku se podíváme na některé běžné scénáře, které může být dosaženo pomocí této funkce. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scénář: Překlad vymezeny do jedné virtuální sítě
V tomto scénáři máte virtuální síť v Azure, který má několik prostředků Azure v, včetně virtuálních počítačů (VM). Chcete vyřešit prostředky z v rámci virtuální sítě prostřednictvím název konkrétní domény (zóny DNS) a musíte překlad privátní a není přístupný z Internetu. Kromě toho pro virtuální počítače v rámci virtuální sítě, potřebujete Azure automaticky jejich registraci v zóně DNS. 

Tento scénář je znázorněn níže. Virtuální síť s názvem "A" obsahuje dva virtuální počítače (VNETA VM1 a VNETA virtuálního počítače 2). Každá z těchto mít privátní IP adresy přidružené. Jakmile vytvoříte privátní zónu s názvem contoso.com a propojíte této virtuální síti jako virtuální síť registrace, Azure DNS automaticky vytvoří dva záznamy A v zóně, jak je to znázorněno. Dotazy DNS z VNETA VM1 vyřešit VNETA VM2.contoso.com nyní, obdrží odpověď DNS, který obsahuje privátní IP VNETA virtuálního počítače 2. Kromě toho dotazu reverzním systému DNS (PTR) pro privátní IP VNETA-VM1 (10.0.0.1) vystavené VNETA virtuálního počítače 2 obdrží odpověď DNS, který obsahuje název VNETA-VM1 podle očekávání. 

![Jedna virtuální síť řešení](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scénář: Překlad mezi virtuálními sítěmi

Tento scénář je více častých případech, kdy potřebujete zónu privátní přidružit více virtuálních sítí. V tomto scénáři můžete začlenit architektury, jako je například model střed a paprsek níž se nachází centrální rozbočovač virtuální sítě do které více ramenem jsou připojené virtuální sítě. Centrální rozbočovač virtuální sítě může být propojený jako virtuální síť registrace na zónu, privátní a ramenem virtuální sítě může být propojený jako řešení virtuální sítě. 

Následující diagram znázorňuje jednoduché verzi tento scénář tam, kde jsou pouze dvě virtuální sítě - A a B. A je určený jako virtuální síť registrace a B je určený jako řešení virtuální sítě. Je pro obě virtuální sítě sdílet běžné zóně contoso.com. Při vytváření zóny a překlad IP adres a registrace virtuální sítě jsou propojené do zóny, Azure automaticky registrovat záznamy DNS pro virtuální počítače (VNETA VM1 a VNETA virtuálního počítače 2) z virtuální sítě A. Můžete také ručně přidat záznamy DNS do zóny pro virtuální počítače ve virtuální síti řešení B. V tomto nastavení bude sledovat následujícího chování pro dotazy DNS dopředného a zpětného:
* Dotaz DNS z VNETB VM1 ve virtuální síti řešení B, pro VNETA-VM1.contoso.com, obdrží odpověď DNS obsahující privátní IP VNETA VM1.
* Dotaz na zpětnou DNS (PTR) z VNETB virtuálního počítače 2 ve virtuální síti řešení B, pro 10.1.0.1, obdrží odpověď DNS obsahující VNETB-VM1.contoso.com plně kvalifikovaný název domény. Důvodem je, že dotazy Reverse DNS jsou omezená na stejné virtuální síti. 
* Dotaz na zpětnou DNS (PTR) z VNETB VM3 ve virtuální síti řešení B, pro 10.0.0.1, obdrží NXDOMAIN. Důvodem je, že jsou dotazy Reverse DNS pouze omezená na stejné virtuální síti. 


![Více možností rozlišení virtuální sítě](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scénář: Funkce rozdělení Horizon

V tomto scénáři máte případ použití, kam chcete různé chování překladu DNS v závislosti na tom, kde je umístěna klienta (v rámci Azure nebo odhlašování na Internetu), mějte na paměti pro stejné zóny DNS. Například můžete mít privátní a veřejné verze aplikace, který má jinou funkci nebo chování, ale chcete použít pro obě verze stejný název domény. Tento scénář může být dosaženo pomocí Azure DNS tak, že vytvoříte zónu DNS veřejné, jakož i privátní zóně, se stejným názvem.

Následující diagram znázorňuje tento scénář. Máte virtuální síť A, která má dva virtuální počítače (VNETA VM1 a VNETA virtuálního počítače 2) které mají obě IP adresy privátní a veřejné IP adresy přidělené. Vytvořte zónu veřejném DNS s názvem contoso.com a zaregistrovat veřejné IP adresy pro tyto virtuální počítače jako záznamy DNS v rámci zóny. Také vytvoříte zónu DNS privátní, označované taky jako contoso.com A zadáte jako virtuální síť registrace. Virtuální počítače Azure zaregistruje automaticky jako zaznamenává do zóny privátní, odkazující na jejich privátní IP adresy.

Nyní když internetového klienta vydá dotaz DNS k vyhledání VNETA VM1.contoso.com, Azure vrátí záznam veřejnou IP adresu z veřejné zóny. Pokud stejný dotaz DNS vydání z jiného virtuálního počítače (například: VNETA virtuálního počítače 2) ve stejné virtuální síti A, bude Azure vrátit záznam privátní IP od privátní zóny. 

![Rozdělení Brian řešení](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Další postup
Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md).

Zjistěte, jak [vytvořit zónu DNS privátní](./private-dns-getstarted-powershell.md) v Azure DNS.

Další informace o zóny DNS a záznamy, navštivte stránky: [DNS zóny a zaznamenává přehled](dns-zones-records.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.

