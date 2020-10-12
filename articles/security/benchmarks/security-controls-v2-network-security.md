---
title: Azure Security benchmark v2 – zabezpečení sítě
description: Zabezpečení sítě Azure Security test v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9833f63d999ab7c24174853bd37f4e7a76f6dfbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329427"
---
# <a name="security-control-v2-network-security"></a>Řízení zabezpečení v2: zabezpečení sítě

Zabezpečení sítě pokrývá ovládací prvky pro zabezpečení a ochranu sítí Azure. To zahrnuje zabezpečení virtuálních sítí, vytváření privátních připojení, prevenci a zmírnění externích útoků a zabezpečení DNS.

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| NS-1 | 9,2, 9,4, 14,1, 14,2, 14,3 | AC-4, CA-3, SC-7 |

Zajistěte, aby všechny virtuální sítě Azure dodržovaly princip segmentace podniku, který se rovná obchodním rizikům. Každý systém, který by mohl organizaci zvýšit riziko, by se měl izolovat v rámci vlastní virtuální sítě a dostatečně zabezpečený pomocí skupiny zabezpečení sítě (NSG) a/nebo Azure Firewall. 

Na základě vašich aplikací a strategie segmentace segmentace můžete omezit nebo povolit provoz mezi interními prostředky na základě pravidel skupiny zabezpečení sítě. Pro konkrétní dobře definované aplikace (jako je například aplikace se třemi vrstvami) může to být vysoce zabezpečený odepřít ve výchozím nastavení, povolit výjimku "přístup". To se nemusí dobře škálovat, pokud máte mnoho aplikací a koncových bodů vzájemně pracujících. V případě potřeby můžete také použít Azure Firewall v situacích, kdy je centrální Správa nutná přes velký počet podnikových segmentů nebo paprsků (v topologii s rozbočovačem/paprsky). 

Pomocí Azure Security Center adaptivního posílení zabezpečení sítě můžete doporučit konfigurace skupin zabezpečení sítě, které omezují porty a zdrojové IP adresy na základě odkazu na pravidla pro přenos externích sítí.

Pomocí ověřování Azure můžete zjistit použití starších nezabezpečených protokolů, jako jsou SSL/TLSv1, SMBv1, LM/ověřovací NTLMv1, wDigest, nepodepsané vazby LDAP a slabá šifra v protokolu Kerberos.

- [Jak vytvořit skupinu zabezpečení sítě s pravidly zabezpečení](../../virtual-network/tutorial-filter-network-traffic.md)

- [Jak nasadit a nakonfigurovat Azure Firewall](../../firewall/tutorial-firewall-deploy-portal.md)

- [Adaptivní posílení zabezpečení sítě v Azure Security Center](../../security-center/security-center-adaptive-network-hardening.md)

- [Sešit nezabezpečených protokolů služby Azure Sentinel](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)     

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| NS – 2 | Není k dispozici | CA – 3, AC-17, MA-4 |

Pomocí Azure ExpressRoute nebo virtuální privátní sítě (VPN) Azure můžete vytvořit privátní připojení mezi datacentry Azure a místní infrastrukturou v prostředí s okolním umístěním. Připojení ExpressRoute nevyužívají veřejný Internet a nabízejí spolehlivější, rychlejší a nižší latenci než typická připojení k Internetu. Pro sítě VPN typu Point-to-site a VPN typu Site-to-site můžete připojit místní zařízení nebo sítě k virtuální síti pomocí libovolné kombinace těchto možností sítě VPN a Azure ExpressRoute.

Pokud chcete propojit dvě nebo víc virtuálních sítí v Azure společně, použijte partnerský vztah virtuální sítě nebo privátní odkaz. Síťový provoz mezi partnerskými virtuálními sítěmi je privátní a udržuje se v páteřní síti Azure. 

- [Jaké jsou modely připojení ExpressRoute](../../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN – přehled](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: vytvoření přístupu privátní sítě ke službám Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| NS – 3 | 14,1 | AC-4, CA-3, SC-7 |

Pomocí privátního odkazu Azure můžete z virtuálních sítí povolit privátní přístup ke službám Azure bez přechodu na Internet. V situacích, kdy privátní propojení Azure ještě není k dispozici, použijte koncové body služby Azure Virtual Network.  Koncové body služby Azure Virtual Network poskytují zabezpečený přístup ke službám prostřednictvím optimalizované trasy přes páteřní síť Azure.  

Soukromý přístup je kromě ověřování a zabezpečení provozu nabízených službami Azure další mírou důkladné ochrany. 

- [Principy privátního odkazu Azure](../../private-link/private-link-overview.md)

- [Vysvětlení koncových bodů služby Virtual Network](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| NS – 4 | 9,5, 12,3, 12,9 | SC-5, SC-7 |

Chraňte prostředky Azure před útoky z externích sítí, včetně útoků distribuovaných útoků na DDoS, specifických pro aplikace a nevyžádaných a potenciálně škodlivých internetových přenosů. Azure zahrnuje nativní možnosti:
-   Pomocí Azure Firewall můžete chránit aplikace a služby před potenciálně škodlivým provozem z Internetu a dalších externích umístění. 

-   Využijte možnosti firewallu webových aplikací (WAF) v Azure Application Gateway, přední dveře Azure a Azure Content Delivery Network (CDN) k ochraně aplikací, služeb a rozhraní API proti útokům na aplikační vrstvu. 

-   Chraňte své prostředky proti útokům DDoS tím, že ve svých virtuálních sítích Azure povolíte standardní ochranu DDoS. 
-   K detekci rizik s konfigurací, které souvisí s výše uvedeným, použijte Azure Security Center. 

- [Dokumentace k Azure Firewall](/azure/firewall/)

- [Jak nasadit Azure WAF](../../web-application-firewall/overview.md)

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](../../virtual-network/manage-ddos-protection.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

Žádné

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| NS-5 | 12,6, 12,7 | SI-4 |

Pomocí Azure Firewall filtrování založeného na kompatibilitě hrozeb můžete výstrahy zablokovat a zablokovat provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. Když je nutná kontrola datové části, můžete nasadit systém (identifikátory/IP adresy) třetí strany z Azure Marketplace s možnostmi kontroly zatížení. Alternativně můžete použít ID hostitele/IP adresy nebo řešení pro detekci a odpověď na základě hostitele (EDR) ve spojení s nebo místo síťových ID/IP adres.  

Poznámka: Pokud máte regulativní nebo jiný požadavek na použití IDENTIFIKÁTORů/IP adres, zajistěte, aby byla vždy vyladěna tak, aby poskytovala vysoce kvalitní výstrahy pro řešení SIEM. 

- [Postup nasazení Azure Firewall](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace zahrnuje možnosti ID třetích stran.](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [EDR schopnost služby Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| NS – 6 | 1.5 | IA-4 |

Zjednodušte pravidla zabezpečení sítě pomocí značek služeb a skupin zabezpečení aplikací (skupiny ASG). 

Pomocí značek Virtual Network služby můžete definovat řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure Firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby v poli zdroj nebo cíl pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Skupiny zabezpečení aplikací můžete použít také ke zjednodušení složité konfigurace zabezpečení. Místo definování zásad na základě explicitních IP adres ve skupinách zabezpečení sítě vám skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace, což vám umožní seskupovat virtuální počítače a definovat zásady zabezpečení sítě na základě těchto skupin.

- [Pochopení a použití značek služeb](../../virtual-network/service-tags-overview.md)

- [Pochopení a použití skupin zabezpečení aplikací](/azure/virtual-network/security-overview#application-security-groups)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Zabezpečená služba názvů domén (DNS)

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| NS – 7 | Není k dispozici | SC-20, SC-21 |

Dodržujte osvědčené postupy pro zabezpečení DNS, které vám umožní zmírnit časté útoky, jako je dangling DNS, útoky na servery DNS, poškození DNS a falšování identity atd. 

Pokud se jako autoritativní služba DNS používá Azure DNS, zajistěte, aby byly zóny a záznamy DNS chráněné před náhodnými nebo škodlivými úpravami pomocí Azure RBAC a zámků prostředků. 

- [Přehled Azure DNS](../../dns/dns-overview.md)

- [Příručka pro nasazení zabezpečení DNS (Domain Name System)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [Zabránit položkám DNS v dangling a vyhnout se převzetí subdomény](../fundamentals/subdomain-takeover.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

