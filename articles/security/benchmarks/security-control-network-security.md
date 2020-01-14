---
title: Řízení zabezpečení Azure – zabezpečení sítě
description: Zabezpečení sítě kontroly zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 376d7c1a9d2fe2ebce857362fd216e2047eb1f7b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934332"
---
# <a name="security-control-network-security"></a>Řízení zabezpečení: zabezpečení sítě

Doporučení zabezpečení sítě se zaměřují na určení, které síťové protokoly, porty TCP/UDP a služby připojené k síti mají povolený nebo odepřený přístup ke službám Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1 – 14.3 | Zákazník |

Zajistěte, aby všechna nasazení Virtual Network podsítí měla skupinu zabezpečení sítě použitou pro řízení přístupu k síti, která jsou specifická pro důvěryhodné porty a zdroje vaší aplikace. Používejte služby Azure s povoleným privátním odkazem, nasaďte službu do vaší virtuální sítě nebo soukromě připojte pomocí privátních koncových bodů. Požadavky na konkrétní služby najdete v doporučení zabezpečení této konkrétní služby.

Případně, pokud máte konkrétní případ použití, lze požadavky splnit implementací Azure Firewall.

Obecné informace o privátním odkazu:

https://docs.microsoft.com/azure/private-link/private-link-overview

Postup vytvoření Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Vytvoření NSG s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak nasadit a nakonfigurovat Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.2 | 9,3, 12,2 | Zákazník |

Použijte Azure Security Center a sledujte doporučení k ochraně síťových prostředků v Azure pomocí doporučení pro ochranu sítě. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Pochopení zabezpečení sítě, které poskytuje Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.3 | 9.5 | Zákazník |

Nasaďte Firewall webových aplikací Azure (WAF) před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Povolte nastavení diagnostiky pro protokoly WAF a ingestování do účtu úložiště, centra událostí nebo Log Analytics pracovního prostoru.

Jak nasadit Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.4 | 12,3 | Zákazník |

Povolte DDoS Standard Protection ve virtuálních sítích Azure, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Nasaďte Azure Firewall na každé úrovni sítě organizace s povoleným analýzou hrozeb a nakonfigurované tak, aby &quot;výstrahu a odepřela&quot; pro škodlivý síťový provoz.

Použijte Azure Security Center k přístupu k síti jenom v čase, abyste mohli nakonfigurovat skupin zabezpečení sítě tak, aby po omezenou dobu omezila počet vystavování koncových bodů na schválené IP adresy

Pomocí Azure Security Center adaptivního posílení zabezpečení sítě doporučujeme doporučit konfigurace NSG, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

Jak nakonfigurovat DDoS Protection:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Postup nasazení Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Pochopení Azure Security Center integrované analýzy hrozeb:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Pochopení Azure Security Center adaptivního posílení zabezpečení sítě:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Pochopení Azure Security Center k síťovému Access Control v čase:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1,5 | 12,5, 15,8 | Zákazník |

Zaznamenejte protokoly toku NSG do účtu úložiště, aby se generovaly záznamy toku. Pokud je to potřeba pro prošetření aktivity neobvyklé, povolte zachytávání paketů Network Watcher.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Postup povolení Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.6 | 12,6, 12,7 | Zákazník |

Nasaďte Azure Firewall na každé úrovni sítě organizace s povoleným analýzou hrozeb a nakonfigurované tak, aby &quot;výstrahu a odepřela&quot; pro škodlivý síťový provoz.

Postup nasazení Azure Firewall: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak nakonfigurovat výstrahy pomocí Azure Firewall: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.7 | 12,9, 12,10 | Zákazník |

Nasaďte Azure Application Gateway pro webové aplikace s povoleným protokolem HTTPS/SSL pro důvěryhodné certifikáty.

Postup nasazení Application Gateway:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Postup konfigurace Application Gateway pro použití protokolu HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Princip vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.8 | 1,5 | Zákazník |

Pomocí značek Virtual Network služby můžete definovat řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure Firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Pochopení a použití značek služeb:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.9 | 11,1 | Zákazník |

Definování a implementace standardních konfigurací zabezpečení pro síťové prostředky pomocí Azure Policy.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, ovládací prvky RBAC a zásady, a to v jediné definici podrobného plánu. Můžete použít podrobný plán na nová předplatná a vyladit řízení a správu prostřednictvím správy verzí.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy ukázky pro síťové služby:

https://docs.microsoft.com/azure/governance/policy/samples/#network

Postup vytvoření Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1.1 | 11.2 | Zákazník |

Používejte značky pro skupin zabezpečení sítě a další zdroje informací týkající se zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole &quot;popis&quot; k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Postup vytvoření Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Vytvoření NSG s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 1,11 | 11,3 | Zákazník |

Použijte Azure Policy k ověření (nebo nápravě) konfigurace síťových prostředků.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy ukázky pro síťové služby:

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [protokolování a monitorování](security-control-logging-monitoring.md) .