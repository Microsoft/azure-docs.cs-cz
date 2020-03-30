---
title: Partnerství se standardem Azure DDoS Protection Standard
description: Seznamte se s možnostmi partnerství, které umožňuje Azure DDoS Protection Standard.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849679"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Partnerství se standardem Azure DDoS Protection Standard
Tento článek popisuje možnosti partnerství povolené standardem Ochrany Před ním Azure DDoS. Tento článek je navržen tak, aby pomohl správcům produktů a rolím rozvoje podnikání porozumět investičním cestám a poskytnout přehled o partnerských hodnotových pozicích.

## <a name="background"></a>Pozadí
Distribuované útoky odmítnutí služby (DDoS) jsou jedním z hlavních problémů dostupnosti a zabezpečení vyjádřených zákazníky, kteří přesouvají své aplikace do cloudu. S vydírání a hacktivism je společné motivace za DDoS útoky, které byly neustále roste v typu, rozsahu a četnostvýskytu, protože jsou relativně snadné a levné zahájit.

Azure DDoS Protection poskytuje protiopatření proti nejsofistikovanějším hrozbám DDoS a využívá globální škálování sítí Azure. Služba poskytuje rozšířené možnosti zmírňování ddos pro aplikace a prostředky nasazené ve virtuálních sítích.

Technologičtí partneři mohou chránit prostředky svých zákazníků nativně pomocí azure ddos protection standard k řešení problémů s dostupností a spolehlivostí kvůli útokům DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Úvod do standardu ochrany Před dos azure
Azure DDoS Protection Standard poskytuje rozšířené možnosti zmírnění DDoS proti útokům DDoS vrstvy 3 a vrstvy 4. Níže jsou uvedeny klíčové funkce služby DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Adaptivní ladění v reálném čase
Pro každou chráněnou aplikaci Azure DDoS Protection Standard automaticky vyladí prahové hodnoty zásad zmírnění DDoS na základě vzorů profilu provozu aplikace. Služba provádí toto přizpůsobení pomocí dvou přehledů:

- Automatické učení o vzorcích provozu na zákazníka (na IP) pro vrstvu 3 a 4.
- Minimalizace falešných poplachů, vzhledem k tomu, že škálování Azure umožňuje absorbovat značné množství provozu.

![Adaptivní ladění v reálném čase](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Analýza útoků, telemetrie, monitorování a upozorňování
Azure DDoS Protection identifikuje a zmírňuje útoky DDoS bez zásahu uživatele.

- Pokud je chráněný prostředek v předplatném, na které se vztahuje Centrum zabezpečení Azure, DDoS Protection Standard automaticky odešle výstrahu do Centra zabezpečení vždy, když je zjištěn útok DDoS a zmírnit proti chráněné aplikaci.
- Případně chcete-li získat upozornění, pokud je aktivní zmírnění pro chráněné veřejné IP adresy, můžete [nakonfigurovat výstrahu](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) na metriku Pod Útok DDoS nebo ne.
- Můžete navíc vytvořit výstrahy pro ostatní metriky DDoS a [nakonfigurovat analýzu útoků](manage-ddos-protection.md#configure-ddos-attack-analytics) tak, aby porozuměla rozsahu útoku, přerušování provozu, vektorům útoku, hlavním přispěvatelům a dalším podrobnostem.

![DDoS metriky](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>Rychlá odezva DDoS (DRR)
Zákazníci ddos protection standardu mají přístup k [týmu rychlé reakce](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) během aktivního útoku. DRR může pomoci s vyšetřováním útoku, vlastními skutečnosti snižující závažnostmi rizik během útoku a analýzou po útoku.

### <a name="sla-guarantee-and-cost-protection"></a>Záruka SLA a ochrana nákladů
Služba DDoS Protection Standard je pokryta 99,99% sla a ochrana nákladů poskytuje kredity prostředků pro horizontální navýšení kapacity během zdokumentovaného útoku. Další informace naleznete [v tématu SLA for Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Doporučené scénáře partnerů
Níže jsou uvedeny klíčové výhody, které můžete odvodit integrací se standardem Azure DDoS Protection Standard:

- Nabízené služby partnerů (balancer, brána firewall webových aplikací, brána firewall atd.) svým zákazníkům jsou automaticky chráněny (označeny bílým štítkem) standardem Azure DDoS Protection Standard v back-endu.
- Partneři mají přístup k analýzám útoků Azure DDoS Protection Standard a telemetrii, které mohou integrovat s vlastními produkty a které nabízejí jednotné prostředí pro zákazníky.  
- Partneři mají přístup k podpoře rychlé reakce DDoS i při absenci azure rychlé reakce, pro problémy související s DDoS.
- Chráněné aplikace partnerů jsou podporovány zárukou DDoS SLA a ochranou nákladů v případě útoků DDoS.

## <a name="technical-integration-overview"></a>Přehled technické integrace
Možnosti partnerství Azure DDoS Protection Standard jsou k dispozici prostřednictvím portálu Azure Portal, rozhraní API a rozhraní API/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrace se standardem DDoS Protection Standard
Pro partnery jsou vyžadovány následující kroky ke konfiguraci integrace se standardem Azure DDoS Protection Standard:
1. Vytvořte plán ochrany DDoS v požadovaném (partnerském) předplatném. Podrobné pokyny naleznete v [tématu Vytvoření plánu standardní ochrany DDoS](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Pro daného klienta je potřeba vytvořit pouze 1 plán ochrany DDoS. 
2. Nasaďte službu s veřejným koncovým bodem ve vašem (partnerském) předplatném, jako je nástroj pro vyrovnávání zatížení, brány firewall a brána firewall webových aplikací. 
3. Povolte Azure DDoS Protection Standard ve virtuální síti služby, která má veřejné koncové body pomocí plánu ochrany DDoS vytvořeného v prvním kroku. Pokyny stpe za krokem najdete v tématu [Povolení plánu standardní ochrany DDoS](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Po Azure DDoS Protection Standard je povolena ve virtuální síti, všechny veřejné IP adresy v rámci této virtuální sítě jsou automaticky chráněny. Původ těchto veřejných IP adresy může být buď v rámci Azure (klientpředplatné) nebo mimo Azure. 
4. Volitelně můžete integrovat telemetrii Azure DDoS Protection Standard a analýzu útoků do řídicího panelu orientovaného na zákazníka specifického pro aplikaci. Další informace o použití telemetrie naleznete [v tématu Použití telemetrie ochrany DDoS](manage-ddos-protection.md#use-ddos-protection-telemetry). Další informace o konfiguraci analýzy útoků naleznete v [tématu Konfigurace analýzy útoků DDoS](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>Onboarding průvodci a technická dokumentace

- [Stránka produktu Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
- [Dokumentace azure ddos ochrany](ddos-protection-overview.md)
- [Odkaz na rozhraní API ochrany Azure DDoS](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Odkaz na rozhraní API virtuální sítě Azure](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Podpora

- Pokud máte dotazy týkající se integrace aplikací, služeb nebo produktů se standardem Azure DDoS Protection Standard, oslovte [komunitu zabezpečení Azure](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Sledujte diskuse o [přetečení zásobníku](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Dostat se na trh

- Primárním programem pro partnerství se společností Microsoft je [program Microsoft Partner Network](https://partner.microsoft.com/). – Integrace microsoft graph security spadají do stopy [nezávislého dodavatele softwaru (ISV) programu MPN.](https://partner.microsoft.com/saas-solution-guide)
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) je program speciálně pro partnery microsoft security, který pomáhá obohatit vaše bezpečnostní produkty a zlepšit zjistitelnost vašich integrací se společností Microsoft Security.

## <a name="next-steps"></a>Další kroky
Zobrazení existujících integrací partnerů:

- [Barracuda WAF jako služba](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF od společnosti Radware](https://www.radware.com/resources/microsoft-azure/)
