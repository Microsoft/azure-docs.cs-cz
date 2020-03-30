---
title: Azure Security Control – zabezpečení sítě
description: Zabezpečení zabezpečení sítě
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251868"
---
# <a name="security-control-network-security"></a>Řízení zabezpečení: Zabezpečení sítě

Doporučení zabezpečení sítě se zaměřují na určení, které síťové protokoly, porty TCP/UDP a služby připojené k síti mají povolený nebo zakázaný přístup ke službám Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Zákazník |

Ujistěte se, že všechna nasazení podsítě virtuální sítě mají skupinu zabezpečení sítě použitou s ovládacími prvky přístupu k síti specifickými pro důvěryhodné porty a zdroje vaší aplikace. Používejte Služby Azure s povolenou privátní linkou, nasaďte službu uvnitř vaší virtuální sítě nebo se připojujte soukromě pomocí privátních koncových bodů. Požadavky na konkrétní služby naleznete v doporučení zabezpečení pro tuto konkrétní službu.

Případně pokud máte konkrétní případ použití, požadavky lze splnit implementací Azure Firewall.

Obecné informace o soukromém spojení:

https://docs.microsoft.com/azure/private-link/private-link-overview

Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak vytvořit skupinu zabezpečení zabezpečení konfigurace:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak nasadit a nakonfigurovat Bránu Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.2 | 9.3, 12.2 | Zákazník |

Použijte Azure Security Center a postupujte podle doporučení pro ochranu sítě, abyste pomohli zabezpečit síťové prostředky v Azure. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště pro audit provozu.

Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Seznamte se se zabezpečením sítě poskytovaným službou Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.3 | 9.5 | Zákazník |

Nasaďte bránu WAF (Azure Web Application Firewall) před kritické webové aplikace pro další kontrolu příchozího provozu. Povolte diagnostické nastavení pro WAF a ingestujte protokoly do pracovního prostoru účtu úložiště, centra událostí nebo analýzy protokolů.

Jak nasadit Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.4 | 12.3 | Zákazník |

Povolte ochranu DDoS Standard ve virtuálních sítích Azure, abyste se ochránili před útoky DDoS. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odmítnout komunikaci se známými škodlivými IP adresami.

Nasaďte bránu Azure Firewall na každé hranici sítě &quot;organizace s&quot; povolenou službou Threat Intelligence a nakonfigurovanou tak, aby upozorňovala a odmítala škodlivý síťový provoz.

Pomocí Azure Security Center just in time network access nakonfigurujte skupiny zabezpečení sítě tak, aby omezily vystavení koncových bodů schváleným IP adresám po omezenou dobu.

Pomocí Azure Security Center Adaptive Network Hardening doporučujete konfigurace skupiny zabezpečení sítě, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

Jak nakonfigurovat ochranu Před sdos:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Jak nasadit Bránu Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Seznamte se s integrovanou analýzou hrozeb Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Principy adaptivního posílení sítě Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Principy Azure Security Center jenom v řízení přístupu k časové síti:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1,5 | 12.5, 15.8 | Zákazník |

Zaznamenejte protokoly toku nsg do účtu úložiště pro generování záznamů toku. Pokud je to nutné pro zkoumání neobvyklé aktivity, povolte zachytávání paketů Sledování sítě.

Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit sledování sítě:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.6 | 12.6, 12.7 | Zákazník |

Nasaďte bránu Azure Firewall na každé hranici sítě &quot;organizace s&quot; povolenou službou Threat Intelligence a nakonfigurovanou tak, aby upozorňovala a odmítala škodlivý síťový provoz.

Jak nasadit Bránu Azure Firewall:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak nakonfigurovat výstrahy pomocí Azure Firewall:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.7 | 12.9, 12.10 | Zákazník |

Nasazení brány aplikací Azure pro webové aplikace s povoleným protokolem HTTPS/SSL pro důvěryhodné certifikáty.

Jak nasadit aplikační bránu:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Jak nakonfigurovat aplikační bránu pro použití protokolu HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Principy vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.8 | 1,5 | Zákazník |

Pomocí značek služby Virtuální síť definujte ovládací prvky přístupu k síti ve skupinách zabezpečení sítě nebo v bráně Azure Firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Porozumět značkám služeb a používat je:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.9 | 11.1 | Zákazník |

Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky pomocí zásad Azure.

Azure Blueprints můžete také použít ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte artefakty prostředí klíčů, jako jsou šablony Azure Resource Manager, ovládací prvky RBAC a zásady, v jediné definici podrobného plánu. Podrobný plán můžete použít pro nová předplatná a doladit řízení a správu prostřednictvím správy verzí.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ukázky zásad Azure pro sítě:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Jak vytvořit Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.1 | 11.2 | Zákazník |

Značky slouží k sítím zabezpečení sítí a dalším prostředkům souvisejícím se zabezpečením sítě a tokem provozu. Pro jednotlivá pravidla sítě &quot;zabezpečení&quot; sítě použijte pole Popis k určení obchodní potřeby a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do nebo ze sítě.

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.11 | 11.3 | Zákazník |

Pomocí zásad Azure ověřte (nebo onápravné) konfiguraci síťových prostředků.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ukázky zásad Azure pro sítě:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [Protokolování a monitorování](security-control-logging-monitoring.md)
