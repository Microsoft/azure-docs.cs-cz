---
title: Řízení zabezpečení Azure – zabezpečení sítě
description: Zabezpečení sítě v Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dad01212be3589af7167082ff22c624fa776772a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82193118"
---
# <a name="security-control-network-security"></a>Řízení zabezpečení: zabezpečení sítě

Doporučení zabezpečení sítě se zaměřují na určení, které síťové protokoly, porty TCP/UDP a služby připojené k síti mají povolený nebo odepřený přístup ke službám Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1,1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Zákazník |

Zajistěte, aby všechna nasazení Virtual Network podsítí měla skupinu zabezpečení sítě použitou pro řízení přístupu k síti, která jsou specifická pro důvěryhodné porty a zdroje vaší aplikace. Pokud je k dispozici, pomocí privátních koncových bodů s privátním odkazem Zabezpečte prostředky služby Azure do vaší virtuální sítě rozšířením identity virtuální sítě na službu. Pokud privátní koncové body a soukromý odkaz nejsou k dispozici, použijte koncové body služby. Požadavky na konkrétní služby najdete v doporučení zabezpečení této konkrétní služby. 

Případně, pokud máte konkrétní případ použití, požadavek může být splněn implementací Azure Firewall.

- [Vysvětlení koncových bodů služby Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Principy privátního odkazu Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Vytvoření Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Vytvoření NSG s konfigurací zabezpečení](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Jak nasadit a nakonfigurovat Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1,2 | 9,3, 12,2, 12,8 | Zákazník |

Použijte Azure Security Center a sledujte doporučení k ochraně síťových prostředků v Azure pomocí doporučení pro ochranu sítě. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Postup povolení a použití Analýza provozu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1.3 | 9,5 | Zákazník |

Nasaďte Firewall webových aplikací Azure (WAF) před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Povolte nastavení diagnostiky pro protokoly WAF a ingestování do účtu úložiště, centra událostí nebo Log Analytics pracovního prostoru.

- [Jak nasadit Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1.4 | 12,3 | Zákazník |

Povolte DDoS Standard Protection ve virtuálních sítích Azure, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Nasaďte Azure Firewall na všech hranicích sítě organizace s povolenou funkcí Analýza hrozeb a nakonfigurované na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

Použijte Azure Security Center k přístupu k síti jenom v čase, abyste mohli nakonfigurovat skupin zabezpečení sítě tak, aby po omezenou dobu omezila počet vystavování koncových bodů na schválené IP adresy

Pomocí Azure Security Center adaptivního posílení zabezpečení sítě doporučujeme doporučit konfigurace NSG, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

- [Jak nakonfigurovat DDoS Protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Postup nasazení Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Pochopení Azure Security Center integrované analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Pochopení Azure Security Center k síťovému Access Control v čase](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1.5 | 12,5 | Zákazník |

Povolte zachytávání paketů Network Watcher pro zkoumání aktivit neobvyklé.

- [Postup povolení Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1.6 | 12,6, 12,7 | Zákazník |

Vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adres s možnostmi kontroly zatížení.  Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce nebo odepření škodlivého provozu.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Postup nasazení Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Postup konfigurace výstrah pomocí Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1,7 | 12,9, 12,10 | Zákazník |

Nasaďte Azure Application Gateway pro webové aplikace s povoleným protokolem HTTPS/TLS pro důvěryhodné certifikáty.

- [Postup nasazení Application Gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Postup konfigurace Application Gateway pro použití protokolu HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Princip vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1.8 | 1.5 | Zákazník |

Pomocí značek Virtual Network služby můžete definovat řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure Firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Skupiny zabezpečení aplikací můžete použít také ke zjednodušení složité konfigurace zabezpečení. Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě.

- [Pochopení a použití značek služeb](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Pochopení a použití skupin zabezpečení aplikací](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1,9 | 11,1 | Zákazník |

Definování a implementace standardních konfigurací zabezpečení pro síťové prostředky pomocí Azure Policy.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manageru, ovládací prvky RBAC a zásady, a to v jediné definici podrobného plánu. Můžete použít podrobný plán na nová předplatná a vyladit řízení a správu prostřednictvím správy verzí.

- [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Ukázky Azure Policy pro sítě](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Vytvoření Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1.10 | 11,2 | Zákazník |

Používejte značky pro skupin zabezpečení sítě a další zdroje informací týkající se zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Vytvoření Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Vytvoření NSG s konfigurací zabezpečení](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 1,11 | 11,3 | Zákazník |

Pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny vašich prostředků Azure. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [protokolování a monitorování](security-control-logging-monitoring.md) .
