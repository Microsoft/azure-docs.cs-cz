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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 023201d221ee5d7ec884c6a760407e8da8340d3f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207112"
---
# <a name="azure-stack-firewall-integration"></a>Integrace brány firewall služby Azure Stack
Doporučuje se použít zařízení brány firewall umožňující zabezpečení Azure stacku. Brány firewall můžete chránit proti věci jako jsou útoky distribuované útok na dostupnost služby (DDOS), zjišťování neoprávněných vniknutí a kontrolu obsahu. Ale zároveň může stát kritickým bodem propustnost pro služby Azure storage jako objekty BLOB, tabulky a fronty.

 Pokud použijete nasazení v odpojeném režimu, je nutné publikovat koncový bod služby AD FS. Další informace najdete v tématu [datacenter integrace identit článku](azure-stack-integrate-identity.md).

Azure Resource Manageru (správce), portál správce a koncové body služby Key Vault (správce) nutně nevyžadují externí publikování. Například jako poskytovatel služeb, můžete omezit pro možný útok prostřednictvím pouze správy služby Azure Stack od uvnitř vaší sítě a nikoli z Internetu.

Pro organizace může být externí síť existující podnikové síti. V tomto scénáři je nutné publikovat koncové body pro provoz služby Azure Stack od podnikové sítě.

### <a name="network-address-translation"></a>Překlad síťových adres
Překlad síťových adres (NAT) je doporučená metoda má povolit nasazení virtuálního počítače (DVM) pro přístup k externím prostředkům a Internetu během nasazení, a také virtuální počítače nouzovou obnovení konzoly (ERCS) nebo privilegovaného koncový bod (období) během registrace a řešení potíží.

NAT může být také o alternativu k veřejné IP adresy v externí síti nebo veřejné virtuální IP adresy. Ale nedoporučujeme to provést, protože omezuje klienta uživatelské prostředí a zvyšuje složitost. Jednou z možností by jedna ku jedné NAT, která se stále vyžaduje jedna veřejná IP adresa a uživatel IP adresu ve fondu. Další možností je mnoho k jedné NAT, která vyžaduje pravidlo NAT, za uživatele virtuálních IP adres pro všechny porty, které může uživatel použít.

Zde jsou některé nevýhody použití NAT pro veřejných virtuálních IP adres:
- NAT přidá režie při správě pravidel brány firewall, protože uživatelé řídit své vlastní koncové body a jejich vlastní pravidla pro publikování v zásobníku softwarově definované sítě (SDN). Uživatelé musí kontaktovat operátory Azure stacku získat své virtuální IP adresy, publikovat a aktualizovat seznam portů.
- Při použití NAT limity uživatelské prostředí, poskytuje úplné řízení pro operátor prostřednictvím žádosti o publikování.
- U scénářů s hybridní cloud s Azure vezměte v úvahu, že Azure nepodporuje nastavení tunelového připojení sítě VPN do koncového bodu pomocí NAT.

### <a name="ssl-decryption"></a>Dešifrování SSL
Aktuálně se doporučuje zakázat dešifrování SSL na veškerý provoz služby Azure Stack. Pokud je podporovaná v budoucích aktualizacích, poskytujeme pokyny, jak povolit dešifrování SSL pro službu Azure Stack.

## <a name="edge-firewall-scenario"></a>Hraniční brána firewall scénář
V nasazení edge služby Azure Stack nasazuje přímo za hraniční směrovač nebo bránu firewall. V těchto scénářích platí pro bránu firewall, aby se nad ohraničení (scénář 1), kde podporuje konfiguraci aktivní aktivní a aktivní pasivní brány firewall nebo funguje jako hraniční zařízení (scénář 2), kde se podporuje jenom aktivní aktivní brána firewall Konfigurace spoléhat na stejné náklady s více cesta ECMP () pomocí protokolu BGP nebo statické směrování pro převzetí služeb při selhání.

Veřejné IP adresy směrovatelné jsou určeny pro fond veřejných virtuálních IP adres z externí sítě v době nasazení. Ve scénáři edge se doporučuje použít veřejné IP adresy směrovatelné na jinou síť z bezpečnostních důvodů. Tento scénář umožňuje uživateli prostředí plně svým řízené cloudového prostředí jako veřejný cloud, jako je Azure.  

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

