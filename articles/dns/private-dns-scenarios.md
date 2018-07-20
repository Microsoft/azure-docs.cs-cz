---
title: Scénáře pro privátní zóny Azure DNS
description: Přehled běžných scénářů použití Azure DNS Private Zones.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: d84da36ad6b1ef3e2a507a0944aac583861d5ccb
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162163"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS Private Zones scénáře
Azure DNS Private Zones překladu názvů ve virtuální síti a jde o vztah mezi virtuálními sítěmi. V tomto článku se podíváme na některé běžné scénáře, které se dají realizovat díky této funkci. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scénář: Překlad názvů s rozsahem v jedné virtuální sítě
V tomto scénáři máte virtuální síť v Azure, který má několik prostředků Azure, včetně virtual machines (VM). Chcete vyřešit prostředky z v rámci virtuální sítě pomocí názvu konkrétní domény (zóna DNS) a je třeba na název řešení jako privátní a nejsou přístupné z Internetu. Kromě toho pro virtuální počítače v rámci virtuální sítě, budete potřebovat Azure a automaticky je registrovat do zóny DNS. 

Tento scénář je vidět na obrázku níže. Virtuální síť s názvem "A" obsahuje dvou virtuálních počítačů (VM1 virtuální síť a síť VNETA VM2). Každý z nich mít privátní IP adresy přidružené. Jakmile vytvoříte privátní zónu s názvem contoso.com a propojení této virtuální sítě jako registrační virtuální síť, Azure DNS automaticky vytvoří dva záznamy A v zóně, jak je znázorněno. Nyní dotazy DNS z síť VNETA VM1 vyřešit síť VNETA VM2.contoso.com přijetí odpovědi DNS, který obsahuje privátní IP VM2 virtuální síť. Kromě toho dotazu reverzním systému DNS (PTR) pro privátní IP (10.0.0.1) síť VNETA-VM1, VM2 síť VNETA vydávány přijetí odpovědi DNS, který obsahuje název síť VNETA-VM1 podle očekávání. 

![Jednotné řešení virtuální sítě](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scénář: Překlad mezi virtuálními sítěmi

Tento scénář je častější případ, kdy je potřeba přidružit privátních zón s několika virtuálními sítěmi. Tento scénář můžete přizpůsobit architektury, jako je model střed a paprsek níž se nachází centrální virtuální sítě centra ke které více paprsků jsou připojené virtuální sítě. Centrální virtuální síti centra lze propojit jako registrační virtuální síť k privátní zónu a virtuálními sítěmi paprsků lze propojit jako virtuální sítě pro překlad. 

Následující diagram znázorňuje jednoduchá verze tohoto scénáře, kde jsou jenom dvě virtuální sítě - A a B. A je určený jako registrační virtuální síť a B je označena jako virtuální síť pro překlad. Naším záměrem je pro obě virtuální sítě sdílet společný zóně contoso.com. Při vytváření zóny a překlad IP adres a registrační virtuální sítě jsou propojeny do zóny, Azure automaticky registrovat záznamy DNS u virtuálních počítačů (VM1 virtuální síť a síť VNETA VM2) z virtuální sítě A. Můžete také ručně přidat záznamy DNS do zóny pro virtuální počítače ve virtuální síti rozlišení B. S tímto nastavením můžete všimnout následujícího chování pro dotazy DNS dopředného a zpětného:
* Dotaz DNS ze sítí VNETB VM1 ve virtuální síti rozlišení B, pro virtuální síť-VM1.contoso.com, obdrží odpověď obsahující privátní IP VM1 virtuální síť.
* Reverzní DNS (PTR) dotaz z VNETB VM2 ve virtuální síti rozlišení B, pro 10.1.0.1, obdrží odpověď obsahující VNETB-VM1.contoso.com plně kvalifikovaný název domény. Důvodem je, že dotazy reverzním systému DNS jsou vymezené ke stejné virtuální síti. 
* Reverzní DNS (PTR) dotaz z VNETB VM3 ve virtuální síti rozlišení B, 10.0.0.1, obdrží NXDOMAIN. Důvodem je, že dotazy reverzním systému DNS jsou pouze vymezené ke stejné virtuální síti. 


![Více možností rozlišení virtuální sítě](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scénář: Funkce zobrazením Split-Horizon

V tomto scénáři máte případ použití, ve které chcete využít různé chování překladu DNS v závislosti na tom, kde je umístěna klienta (v rámci služby Azure nebo oddálení v síti internet), pro stejnou zónu DNS. Například můžete mít privátní a veřejné verze aplikace, která má jinou funkci nebo chování, ale chcete použít pro obě verze se stejným názvem domény. Tento scénář se dají realizovat s využitím Azure DNS tak, že vytvoříte veřejná služba DNS zóny, jakož i privátních zón se stejným názvem.

Následující diagram znázorňuje tento scénář. Máte virtuální síť A, který má dva virtuálních počítačů (VM1 virtuální síť a síť VNETA VM2) návratovými hodnotami typu obě privátní IP adresy a přidělovat veřejné IP adresy. Vytvořte zónu DNS veřejné s názvem contoso.com a zaregistrujte veřejné IP adresy pro tyto virtuální počítače jako záznamy DNS v zóně. Můžete také vytvořit privátní zónu DNS taky contoso.com A zadáte jako registrační virtuální síť. Azure automaticky zaregistruje virtuální počítače jako zaznamenává do privátních zón, odkazuje na jejich privátní IP adresy.

Nyní když internetového klienta vydá dotaz DNS k vyhledání VM1.contoso.com virtuální síť, Azure vrátí veřejnou IP adresu záznamu z veřejné zóny. Pokud stejný dotaz DNS pochází z jiného virtuálního počítače (například: síť VNETA VM2) ve stejné virtuální síti A Azure vrátí záznam privátní IP z privátní zónu. 

![Brian rozdělení řešení](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Další postup
Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md).

Zjistěte, jak [vytvoření privátní zóny DNS](./private-dns-getstarted-powershell.md) v Azure DNS.

Další informace o záznamy a zóny DNS najdete: [Přehled záznamů a zón DNS](dns-zones-records.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.

