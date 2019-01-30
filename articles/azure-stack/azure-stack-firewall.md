---
title: Integrované systémy pro Azure Stack firewall plánování pro službu Azure Stack | Dokumentace Microsoftu
description: Popisuje aspekty brány firewall služby Azure Stack pro nasazení na víc uzlů Azure stacku Azure připojené.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: eff526118f6fd127ba720d28296baf86abd01393
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246429"
---
# <a name="azure-stack-firewall-integration"></a>Integrace brány firewall služby Azure Stack
Doporučuje se použít zařízení brány firewall umožňující zabezpečení Azure stacku. I když se třeba distribuovanými útoky s cílem odepření služeb (DDOS), zjišťování neoprávněných vniknutí a kontroly obsahu vám může pomoci brány firewall, jsou zároveň může stát kritickým bodem propustnost pro služby Azure storage jako objekty BLOB, tabulky a fronty.

Založené na modelu identity Azure Active Directory (Azure AD) nebo Windows Server Active Directory Federation Services (AD FS), budete možná muset publikovat koncový bod služby AD FS. Pokud použijete nasazení v odpojeném režimu, je nutné publikovat koncový bod služby AD FS. Další informace najdete v tématu [datacenter integrace identit článku](azure-stack-integrate-identity.md).

Azure Resource Manageru (správce), portál správce a koncové body služby Key Vault (správce) nutně nevyžadují externí publikování. Například jako poskytovatel služeb, můžete omezit útoku a pouze spravovat služby Azure Stack od uvnitř vaší sítě a nikoli z Internetu.

Pro organizace může být externí síť existující podnikové síti. V takové situaci je třeba publikovat tyto koncové body pro provoz služby Azure Stack od podnikové sítě.

### <a name="network-address-translation"></a>Překlad síťových adres
Překlad síťových adres (NAT) je doporučená metoda má povolit nasazení virtuálního počítače (DVM) pro přístup k externím prostředkům a Internetu během nasazování, jakož i virtuální počítače nouzovou obnovení konzoly (ERCS) nebo privilegovaného koncový bod (období) během registrace a řešení potíží.

NAT může být také o alternativu k veřejné IP adresy v externí síti nebo veřejné virtuální IP adresy. Ale nedoporučujeme to provést, protože omezuje klienta uživatelské prostředí a zvyšuje složitost. 1:1 NAT, která stále vyžaduje jedna veřejná IP adresa a uživatel IP adresu ve fondu nebo mnoho by měl dvě možnosti: 1 NAT, která vyžaduje pravidlo NAT na uživatele, který obsahuje přidružení pro všechny porty virtuálních IP adres může uživatel použít.

Zde jsou některé nevýhody použití NAT pro veřejných virtuálních IP adres:
- NAT přidá režie při správě pravidel brány firewall, protože uživatelé řídit své vlastní koncové body a jejich vlastní pravidla pro publikování v zásobníku softwarově definované sítě (SDN). Uživatelé musí kontaktovat operátory Azure stacku získat své virtuální IP adresy, publikovat a aktualizovat seznam portů.
- Při použití NAT limity uživatelské prostředí, poskytuje úplné řízení pro operátor prostřednictvím žádosti o publikování.
- U scénářů s hybridní cloud s Azure vezměte v úvahu, že Azure nepodporuje nastavení tunelového připojení sítě VPN do koncového bodu pomocí NAT.

### <a name="ssl-decryption"></a>Dešifrování SSL
Aktuálně se doporučuje zakázat dešifrování SSL na veškerý provoz služby Azure Stack. Pokud je podporovaná v budoucích aktualizacích, poskytujeme pokyny, jak povolit dešifrování SSL pro službu Azure Stack.

## <a name="edge-firewall-scenario"></a>Hraniční brána firewall scénář
V nasazení edge služby Azure Stack nasazuje přímo za hraniční směrovač nebo bránu firewall. V těchto scénářích platí pro bránu firewall, aby se nad ohraničení (scénář 1), kde podporuje konfiguraci aktivní aktivní a aktivní pasivní brány firewall nebo funguje jako hraniční zařízení (scénář 2), kde se podporuje jenom aktivní aktivní brána firewall Konfigurace spoléhat na stejné náklady s více cesta ECMP () pomocí protokolu BGP nebo statické směrování pro převzetí služeb při selhání.

Obvykle jsou veřejné IP adresy směrovatelné zadaný pro fond veřejných virtuálních IP adres z externí sítě v době nasazení. Ve scénáři edge se doporučuje použít veřejné IP adresy směrovatelné na jinou síť z bezpečnostních důvodů. Tento scénář umožňuje uživateli prostředí plně svým řízené cloudového prostředí jako veřejný cloud, jako je Azure.  

![Příklad edge brány firewall pomocí Azure Stack](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Podnikového intranetu nebo hraniční sítě brány firewall scénáře
V podnikovém intranetu nebo hraniční nasazení se nasadí Azure Stack na bráně firewall rozdělený zóny s více nebo mezi hraniční bráně firewall a brány firewall interní podnikové sítě. Jeho provoz je poté distribuován mezi zabezpečené, hraniční síti (nebo hraniční síti) a nezabezpečené zóny, jako je popsáno níže:

- **Zabezpečené zóně**: Toto je interní sítě, který používá interní nebo firemní směrovatelné IP adresy. Zabezpečené sítě je možné rozdělit, mít odchozí přístup k Internetu prostřednictvím překladu adres v bráně Firewall a je obvykle přístupné z kdekoli uvnitř vašeho datového centra prostřednictvím interní síti. Všechny služby Azure Stack sítě by měl být uložený v zabezpečené zóně s výjimkou veřejný fond VIP externí síť.
- **Zóna hraniční**. Hraniční síť je tam, kde je to externí nebo aplikace, jako jsou webové servery jsou obvykle implementovány směřujících do Internetu. Obvykle je monitorovaný bránou firewall, aby se zabránilo útokům DDoS a neoprávněného vniknutí (hacking) zároveň umožní zadané příchozí provoz z Internetu. Pouze externí síť veřejných virtuálních IP adres fondu služby Azure Stack by měl být umístěn v zóně DMZ.
- **Nezabezpečená zóny**. Jedná se o externí síť Internetu. To **není** doporučujeme nasadit Azure Stack v nezabezpečené zóny.

![Příklad hraniční sítě pomocí Azure Stack](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Další informace
Další informace o [portech a protokolech používaných koncové body služby Azure Stack](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Další postup
[Požadavky služby Azure Stack infrastruktury veřejných KLÍČŮ](azure-stack-pki-certs.md)

