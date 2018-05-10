---
title: Brány Azure zásobník plánování zásobník Azure integrované systémy | Microsoft Docs
description: Popisuje aspekty zásobník Azure brány firewall pro nasazení na víc uzlů připojená k Azure zásobník Azure.
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
ms.date: 05/09/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3d8a02cc540251d77b61cf0e5b7e2aa8292f1f5f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-firewall-integration"></a>Integrace brány firewall Azure zásobníku
Doporučuje se použít zařízení brány firewall ke zvýšení zabezpečení Azure zásobníku. I když se třeba distribuované útokům (DDOS) odmítnutí služby, zjišťování neoprávněných vniknutí a prověřování obsahu vám může pomoci brány firewall, budou také k propustnost úzkým místem pro služby Azure storage jako objekty BLOB, tabulek a front.

Podle modelu identity Azure Active Directory (Azure AD) nebo Windows Server Active Directory Federation Services (AD FS), může se budete muset publikovat koncový bod služby AD FS. Pokud se používá režim odpojené nasazení, musíte publikovat koncový bod služby AD FS. Další informace najdete v tématu [datacenter integraci identity článku](azure-stack-integrate-identity.md).

Azure Resource Manager (správce), portál správce a koncové body Key Vault (správce) nevyžadují nutně externí publikování. Například jako poskytovatele služeb, můžete chtít omezit prostor k útoku a jenom správu zásobník Azure z uvnitř vaší sítě a nikoli z Internetu.

Pro velké organizace externí síti může být existující podnikové síti. V takové situaci musíte publikovat těchto koncových bodů provoz zásobník Azure z podnikové sítě.

### <a name="network-address-translation"></a>Překlad síťových adres
Překlad adres (NAT) je doporučená metoda umožňující nasazení virtuálního počítače (DVM) pro přístup k externím prostředkům a Internetu během nasazení, jakož i virtuální počítače nouze obnovení konzoly (ERCS) nebo privilegovaného koncový bod období během registrace a řešení potíží.

NAT může být také alternativu k veřejné IP adresy v externí síti nebo veřejné virtuální IP adresy. Však není doporučeno to provést, protože se omezuje činnost koncového uživatele klienta a zvyšuje složitost. Dvě možnosti by NAT 1:1, který stále vyžaduje jednu veřejnou IP adresu na uživatele IP na fond nebo n: 1 NAT, která vyžaduje pravidlo NAT za uživatele VIP, který obsahuje přidružení na všechny porty, které může uživatel použít.

Některé z downsides použití NAT pro veřejný virtuální adresy IP jsou:
- NAT přidá zatížení při správě pravidel brány firewall, protože uživatelé řídit své vlastní koncové body a vlastní pravidla pro publikování v zásobníku softwarově definované sítě (SDN). Uživatelé musí kontaktovat operátor zásobník Azure získat jejich VIP publikována a k aktualizaci seznamu port.
- Při použití NAT limity činnost koncového uživatele, poskytuje úplné řízení ke operátor přes publikování požadavky.
- Pro hybridní scénáře cloudu s Azure zvažte, že Azure nepodporuje nastavení tunelového připojení sítě VPN pro koncový bod pomocí adres (NAT)

### <a name="ssl-decryption"></a>Dešifrování SSL
Aktuálně doporučujeme zakázat dešifrování SSL na veškerý provoz Azure zásobníku. Pokud je podporován v budoucí aktualizace, bude poskytnuta pokyny o tom, jak povolit dešifrování SSL pro Azure zásobníku.

## <a name="edge-firewall-scenario"></a>Scénář hraniční brány firewall
V nasazení okraj je zásobník Azure nasadit přímo za hraniční směrovač nebo bránu firewall. V těchto scénářích se podporuje pro bránu firewall, aby se výše ohraničení (scénář 1), které podporuje konfiguraci brány firewall aktivní aktivní a aktivní pasivní nebo funguje jako zařízení ohraničení (scénář 2), kde se podporuje jenom aktivní aktivní brány firewall Konfigurace spoléhat na stejné náklady více cesta (ECMP) pomocí protokolu BGP nebo statické směrování pro převzetí služeb při selhání.

Obvykle jsou veřejné směrovatelné IP adresy zadané pro veřejné fondu virtuálních IP adres z externí sítě v době nasazení. Ve scénáři s edge není doporučeno používat veřejné směrovatelné IP adresy v jiné síti z bezpečnostních důvodů. Tento scénář umožňuje uživatelům mít úplná samoobslužné řízené cloudové prostředí jako veřejný cloud, jako je například Azure.  

![Azure zásobníku hraniční brány firewall příklad](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Podnikového intranetu nebo hraniční síti firewall scénáře
V podnikovém intranetu nebo hraniční nasazení je nasadit Azure zásobníku na bráně firewall více zóně nebo mezi hraniční bráně firewall a brány firewall interní podnikové síti. Jeho provoz je poté distribuován mezi zabezpečený a hraniční síti (nebo DMZ) a nezabezpečená zóny jako popsané dál:

- **Zabezpečené zóny**: Jedná se o interní síť, která používá interní nebo podnikové směrovatelné IP adresy. Zabezpečené sítě je možné rozdělit, mají v bráně Firewall odchozí přístup k Internetu prostřednictvím NAT a je obvykle přístupné z kdekoli uvnitř datacentra prostřednictvím interní síti. Všechny Azure zásobník sítě by měl být umístěn v zabezpečené zóně s výjimkou veřejné fond VIP externí síť.
- **Hraniční zóny**. Hraniční síti je tam, kde je to externí nebo internetové aplikace, jako jsou obvykle nasazené webové servery. Obvykle je monitorovaný bránou firewall, aby se zabránilo útokům jako DDoS a neoprávněných vniknutí (útoky hackerů), přičemž zůstane zachován zadaný příchozí provoz z Internetu. Pouze externí síť veřejných virtuálních IP adres fondu Azure zásobníku by měl být umístěn v hraniční síti zóně.
- **Nezabezpečená zóny**. Toto je externí sítě internet. Ho **není** doporučujeme nasadit Azure zásobníku v nezabezpečená zóny.

![Azure zásobníku hraniční sítě příklad](.\media\azure-stack-firewall\perimeter-network-scenario.png)

## <a name="learn-more"></a>Další informace
Další informace o [porty a protokoly, které používají koncové body Azure zásobníku](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Další postup
[Požadavky pro Azure zásobníku infrastruktury veřejných KLÍČŮ](azure-stack-pki-certs.md)

