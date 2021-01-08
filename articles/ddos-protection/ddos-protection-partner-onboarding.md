---
title: Partnerství s Azure DDoS Protection standardem
description: Principy partnerských příležitostí povolených Azure DDoS Protection Standard
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: f6cba0118a98ea50e8020b91b9c2064299ca4ccf
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019155"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Partnerství s Azure DDoS Protection standardem
Tento článek popisuje možnosti partnerských serverů, které jsou povolené standardem Azure DDoS Protection. Tento článek je navržený tak, aby pomohla manažerům produktu a obchodním vývojářům pochopení investičních cest a poskytoval přehled o propozicích partnerských funkcí.

## <a name="background"></a>Pozadí
DDoS (Distributed DOS Service) jsou jedním z nejdůležitějších potíží a zabezpečení, se kterými se zákazníci přesouvají do cloudu. Díky tomu, že extortion a hacktivismus jsou společné motivace v rámci DDoS útoků, se konzistentně zvyšovaly v typu, škále a četnosti výskytu, protože jejich spuštění je poměrně snadné a levné.

Azure DDoS Protection poskytuje protiopatření proti nejpropracovanějším hrozbám DDoS a využívá globální škálování sítí Azure. Služba poskytuje rozšířené funkce zmírnění DDoS pro aplikace a prostředky nasazené ve virtuálních sítích.

Technologická partneři můžou nativně chránit své prostředky zákazníků pomocí Azure DDoS Protection Standard, aby vyřešili problémy dostupnosti a spolehlivosti v důsledku útoků DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Úvod do Azure DDoS Protection Standard
Azure DDoS Protection Standard poskytuje rozšířené možnosti zmírnění DDoS proti útokům vrstvy 3 a 4 DDoS. Níže jsou uvedené klíčové funkce služby DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Adaptivní ladění v reálném čase
Pro každou chráněnou aplikaci Azure DDoS Protection Standard automaticky vyladí prahové hodnoty zásad zmírnění rizik DDoS na základě vzorů profilů přenosů aplikace. Služba provádí tyto úpravy pomocí dvou přehledů:

- Automatické učení vzorů přenosu podle zákazníka (podle IP adresy) pro vrstvy 3 a 4.
- Minimalizace falešně pozitivních hodnot, vzhledem k tomu, že škálování Azure umožňuje, aby mohla absorbovat významné množství provozu.

![Adaptivní optimalizace v reálném čase](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Analýzy útoků, telemetrie, monitorování a upozorňování
Azure DDoS Protection identifikuje a zmírnit útoky DDoS bez zásahu uživatele.

- Pokud je chráněným prostředkem v předplatném, které je uvedené v části Azure Security Center, DDoS Protection Standard automaticky pošle výstrahu Security Center kdykoli se zjistí útok DDoS a sníží se na chráněnou aplikaci.
- Případně, pokud chcete dostávat upozornění, když dojde k aktivnímu zmírnění chráněné veřejné IP adresy, můžete [nakonfigurovat výstrahu](alerts.md) na základě DDoS útoku nebo ne.
- Můžete se také rozhodnout vytvořit výstrahy pro ostatní metriky DDoS a [nakonfigurovat telemetrii útoků](telemetry.md) , abyste porozuměli rozsahu útoku, zahození provozu, vektorům útoku, hlavním přispěvatelům a dalším podrobnostem.

![DDoS metriky](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS Rapid Response (DRR)
Zákazníci s DDoS Protection standard mají při aktivním útoku přístup k [týmu Rapid Response](ddos-rapid-response.md) . DRR může pomáhat s vyšetřováním útoků během útoku i analýz po útoku.

### <a name="sla-guarantee-and-cost-protection"></a>Záruka na smlouvu SLA a ochrana nákladů
Služba DDoS Protection Standard se zabývá smlouvou SLA 99,99% a nákladová ochrana poskytuje kredity prostředků pro horizontální navýšení kapacity během dokumentovaného útoku. Další informace najdete v tématu [SLA pro Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Doporučené partnerské scénáře
Níže jsou uvedené klíčové výhody, které můžete odvodit integrací s Azure DDoS Protection standardem:

- Přibraní partneři (Nástroj pro vyrovnávání zatížení, Firewall webových aplikací, firewall atd.) pro jejich zákazníky jsou automaticky chráněni (s bílým označením) Azure DDoS Protection standardem v back-endu.
- Partneři mají přístup k Azure DDoS Protection standardním analýzám útoků a telemetrie, které mohou integrovat s vlastními produkty, a nabízejí jednotné prostředí pro zákazníky.  
- Partneři mají přístup k podpoře DDoS Rapid Response, a to i v případě nepřítomnosti Azure Rapid Response, pro problémy související s DDoS.
- Chráněné aplikace partnerů jsou zajištěné DDoS smlouvou SLA a ochranou nákladů v případě DDoSch útoků.

## <a name="technical-integration-overview"></a>Přehled technické integrace
Azure DDoS Protection standardní možnosti partnerských partnerství jsou k dispozici prostřednictvím Azure Portal, rozhraní API a CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrace s DDoS Protection standardem
Pro partnery, kteří konfigurují integraci s Azure DDoS Protection Standard, je potřeba provést následující kroky:
1. Vytvořte plán DDoS Protection v požadovaném (partnerském) předplatném. Podrobné pokyny najdete v tématu [Vytvoření plánu DDoS Standard Protection](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Pro daného tenanta se musí vytvořit jenom 1 DDoS Protection plán. 
2. Nasaďte službu s veřejným koncovým bodem v předplatných (partnerských), jako je třeba nástroj pro vyrovnávání zatížení, brány firewall a firewall webových aplikací. 
3. Ve virtuální síti služby, která má veřejné koncové body pomocí plánu DDoS Protection vytvořeného v prvním kroku, povolte Azure DDoS Protection Standard. Podrobné pokyny najdete v tématu [povolení plánu DDoS Standard Protection](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network) .
   > [!IMPORTANT] 
   > Po povolení Azure DDoS Protection Standard ve virtuální síti se automaticky chrání všechny veřejné IP adresy v této virtuální síti. Původ těchto veřejných IP adres může být buď v rámci Azure (předplatné klienta), nebo mimo Azure. 
4. Volitelně můžete integrovat Azure DDoS Protection standardní telemetrie a analýzy útoků na řídicí panel pro konkrétního zákazníka. Další informace o používání telemetrie najdete v tématu [zobrazení a konfigurace telemetrie DDoS Protection](telemetry.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Příručky k registraci a technická dokumentace

- [Stránka Azure DDoS Protection produktu](https://azure.microsoft.com/services/ddos-protection/)
- [Dokumentace k Azure DDoS Protection](ddos-protection-overview.md)
- [Reference k rozhraní API Azure DDoS Protection](/rest/api/virtualnetwork/ddosprotectionplans)
- [Reference k rozhraní API virtuální sítě Azure](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Podpora

- Pokud máte dotazy týkající se integrace aplikací, služeb nebo produktů s Azure DDoS Protectionm standardem, je třeba se obrátit na [komunitu zabezpečení Azure](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Sledujte diskuzi o [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Získat na trh

- Primárním programem pro partnerství s Microsoftem je [Microsoft Partner Network](https://partner.microsoft.com/). – Microsoft Graph integrace zabezpečení spadají do sledování programu [MPN nezávislého výrobce softwaru (ISV)](https://partner.microsoft.com/saas-solution-guide) .
- [Přidružení inteligentního zabezpečení Microsoftu](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) je program určený speciálně pro partnery Microsoftu pro zabezpečení a pomáhá rozhloubit vaše integrace produktů zabezpečení a zdokonalit zjistitelnost vašich integrací produktů Microsoftu.

## <a name="next-steps"></a>Další kroky
Zobrazit existující integrace partnerů:

- [Barracuda WAF jako služba](https://www.barracuda.com/waf-as-a-service)
