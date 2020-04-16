---
title: Azure Security Control – zabezpečení sítě
description: Azure Security Control Network Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408305"
---
# <a name="security-control-network-security"></a>Řízení zabezpečení: Zabezpečení sítě

Doporučení zabezpečení sítě se zaměřují na určení, které síťové protokoly, porty TCP/UDP a služby připojené k síti mají povolený nebo zakázaný přístup ke službám Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Ochrana prostředků Azure ve virtuálních sítích

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Zákazník |

Ujistěte se, že všechna nasazení podsítě virtuální sítě mají skupinu zabezpečení sítě použitou s ovládacími prvky přístupu k síti specifickými pro důvěryhodné porty a zdroje vaší aplikace. Když je k dispozici, použijte privátní koncové body s privátním odkazem k zabezpečení prostředků služby Azure do vaší virtuální sítě rozšířením identity virtuální sítě na službu. Pokud soukromé koncové body a privátní propojení není k dispozici, použijte koncové body služby. Požadavky na konkrétní služby naleznete v doporučení zabezpečení pro tuto konkrétní službu. 

Případně pokud máte konkrétní případ použití, požadavek může být splněna implementací Azure Firewall.

- [Principy koncových bodů služby virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Principy privátního propojení Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Jak vytvořit virtuální síť](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Jak vytvořit skupinu zabezpečení zabezpečení](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Jak nasadit a nakonfigurovat Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu virtuálních sítí, podsítí a síťových rozhraní

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Zákazník |

Použijte Azure Security Center a postupujte podle doporučení pro ochranu sítě, abyste pomohli zabezpečit síťové prostředky v Azure. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště pro audit provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

- [Povolení protokolů toku nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Jak povolit a používat Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Principy zabezpečení sítě poskytované službou Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.3 | 9.5 | Zákazník |

Nasaďte bránu WAF (Azure Web Application Firewall) před kritické webové aplikace pro další kontrolu příchozího provozu. Povolte diagnostické nastavení pro WAF a ingestujte protokoly do pracovního prostoru účtu úložiště, centra událostí nebo analýzy protokolů.

- [Jak nasadit Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.4 | 12.3 | Zákazník |

Povolte ochranu DDoS Standard ve virtuálních sítích Azure, abyste se ochránili před útoky DDoS. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odmítnout komunikaci se známými škodlivými IP adresami.

Nasaďte bránu Azure Firewall na každé hranici sítě organizace s povolenou službou Threat Intelligence a nakonfigurovanou na "Výstrahy a odepření" pro škodlivý síťový provoz.

Pomocí Azure Security Center just in time network access nakonfigurujte skupiny zabezpečení sítě tak, aby omezily vystavení koncových bodů schváleným IP adresám po omezenou dobu.

Pomocí Azure Security Center Adaptive Network Hardening doporučujete konfigurace skupiny zabezpečení sítě, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

- [Jak nakonfigurovat ochranu Před sdos](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Jak nasadit Bránu Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Principy integrované analýzy hrozeb Centra zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Principy adaptivního posílení sítě Centra zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Principy Azure Security Center právě v řízení přístupu k síti Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Záznam síťových paketů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1,5 | 12.5 | Zákazník |

Povolit zachycení paketu Sledování sítě prozkoumat neobvyklé aktivity.

- [Jak povolit sledování sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.6 | 12.6, 12.7 | Zákazník |

Vyberte nabídku z Azure Marketplace, která podporuje funkce IDS/IPS s možnostmi kontroly datové části.  Pokud není vyžadováno zjišťování nebo prevence narušení na základě kontroly datové části, lze použít azure bránu firewall se službou Threat Intelligence. Filtrování založené na inteligenci hrozeb Azure Firewall může upozorňovat a odmítat provoz na známé škodlivé IP adresy a domény a z nich. IP adresy a domény jsou získávány z informačního kanálu Microsoft Threat Intelligence.

Nasadit řešení brány firewall podle vašeho výběru na každé z hranic sítě vaší organizace ke zjištění nebo odepření škodlivého provozu.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak nasadit Bránu Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Konfigurace výstrah pomocí Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.7 | 12.9, 12.10 | Zákazník |

Nasazení brány aplikací Azure pro webové aplikace s povoleným protokolem HTTPS/SSL pro důvěryhodné certifikáty.

- [Jak nasadit aplikační bránu](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Jak nakonfigurovat aplikační bránu pro použití protokolu HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Principy vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.8 | 1,5 | Zákazník |

Pomocí značek služby Virtuální síť definujte ovládací prvky přístupu k síti ve skupinách zabezpečení sítě nebo v bráně Azure Firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Můžete také použít skupiny zabezpečení aplikací ke zjednodušení složité konfigurace zabezpečení. Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě.

- [Principy a používání značek služeb](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Principy a používání skupin zabezpečení aplikací](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.9 | 11.1 | Zákazník |

Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky pomocí zásad Azure.

Azure Blueprints můžete také použít ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte artefakty prostředí klíčů, jako jsou šablony Azure Resources Manager, ovládací prvky RBAC a zásady, v jediné definici podrobného plánu. Podrobný plán můžete použít pro nová předplatná a doladit řízení a správu prostřednictvím správy verzí.

- [Jak nakonfigurovat a spravovat zásady Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Ukázky zásad Azure pro sítě](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Jak vytvořit Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.10 | 11.2 | Zákazník |

Značky slouží k sítím zabezpečení sítí a dalším prostředkům souvisejícím se zabezpečením sítě a tokem provozu. Pro jednotlivá pravidla sítě zabezpečení sítě použijte pole Popis k určení obchodní potřeby a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do nebo ze sítě.

Pomocí některé z předdefinovaných definic zásad Azure souvisejících s označováním, jako je například Vyžadovat značku a její hodnotu, můžete zajistit, aby se všechny prostředky vytvořily pomocí značek, a upozornit vás na existující neoznačené prostředky.

Azure PowerShell nebo Azure CLI můžete použít k vyhledávání nebo provádění akcí na prostředky na základě jejich značek.

- [Jak vytvořit a používat značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Jak vytvořit virtuální síť](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 1.11 | 11.3 | Zákazník |

Protokol aktivit Azure slouží ke sledování konfigurací prostředků a zjišťování změn prostředků Azure. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických prostředků.

- [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Jak vytvořit výstrahy ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [Protokolování a monitorování](security-control-logging-monitoring.md)
