---
title: Plán připravenosti služby Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám poskytne plány připravenosti pro zavedení služby Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 38e1fc7f062908f8c4a4b9aa903ca09dd7fb9ea5
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342327"
---
# <a name="azure-security-center-readiness-roadmap"></a>Plán připravenosti na Azure Security Center
Tento dokument vám poskytne plány připravenosti, které vám pomůžou zahájit používání služby Azure Security Center.

## <a name="understanding-security-center"></a>Vysvětlení služby Security Center
Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami pro úlohy běžící v Azure, na místních systémech a v jiných cloudech. 

V začátcích používání služby Security Center vám pomůžou následující zdroje.

Články
- [Úvod do Azure Security Center](security-center-introduction.md)
- [Úvodní příručka ke službě Azure Security Center](security-center-get-started.md)

Videa
- [Rychlé úvodní video](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Přehled možností prevence, detekce a reakce ve službě Security Center](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Plánování a provoz

Pokud chcete využívat všech výhod služby Security Center, je důležité pochopit, jak různí jednotlivci nebo týmy ve vaší organizaci používají tuto službu k tomu, aby vyhověli požadavkům na zabezpečený provoz, sledování, řízení a reakce na incidenty.

Následující zdroje vám pomůžou při plánování i provozu.

- [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Připojení počítačů ke službě Security Center
Security Center automaticky detekuje všechna předplatná Azure nebo pracovní prostory, které nechrání Azure Defender. To zahrnuje předplatná Azure využívající Security Center volno a pracovní prostory, které nemají povolené řešení zabezpečení.

Následující zdroje vám pomůžou při procesu onboardingu.

- [Připojování počítačů mimo Azure](quickstart-onboard-machines.md)
- [Hybridní Azure Security Center – přehled](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Řešení problémů zabezpečení pomocí služby Security Center
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů.

Následující zdroje vám pomůžou při správě výstrah zabezpečení a ochraně vašich prostředků.

Články    
- [Sledování stavu zabezpečení v Azure Security Center](./security-center-monitoring.md)
- [Ochrana sítě pomocí Azure Security Center](./security-center-network-recommendations.md)
- [Ochrana služby a dat Azure SQL pomocí Azure Security Center](./security-center-remediate-recommendations.md)


Video    
- [Řešení problémů zabezpečení pomocí Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center a reakce na incidenty
Aby se snížily náklady a škody, je důležité mít na úmyslu plán reakce na incidenty, než dojde k útoku. Azure Security Center můžete využít v různých fázích reakce na incidenty.

Následující zdroje vám pomůžou porozumět tomu, jak zapojit Security Center do vašich procesů reakce na incidenty.

Videa    
* [Využití služby Azure Security Center při reakci na incidenty](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Rychlá reakce na hrozby s použitím bezpečnostních a vyšetřovacích nástrojů nové generace](https://youtu.be/e8iFCz5RM4g)

Články    
* [Využití Azure Security Center při reakci na incidenty](./tutorial-security-incident.md)
* [Automatizace reakce díky automatizaci pracovního postupu](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Pokročilá obrana cloudu

Virtuální počítače Azure můžou využít výhod pokročilé cloudové obrany, které Security Center nabízí. Tyto možnosti zahrnují přístup k virtuálnímu počítači (VM) za běhu a adaptivní řízení aplikací.

V následujících zdrojích se dozvíte, jak tyto možnosti ve službě Security Center využít.

Videa    
* [Azure Security Center – přístup k virtuálnímu počítači za běhu](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center – Adaptivní řízení aplikací](https://youtu.be/wWWekI1Y9ck)

Články    
* [Správa přístupu k virtuálnímu počítači pomocí za běhu](./security-center-just-in-time.md)
* [Adaptivní řízení aplikací v Azure Security Center](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Praktické aktivity

* [Praktická cvičení pro Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook doporučených postupů pro Firewall webových aplikací (WAF) ve službě Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Playbook Azure Security Center: Výstrahy zabezpečení](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Další zdroje informací
* [Stránky dokumentace služby Security Center](./index.yml)
* [Stránky dokumentace rozhraní Security Center REST API](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Nejčastější dotazy ke službě Azure Security Center (FAQ)](./faq-general.md)
* [Ceník služby Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Osvědčené postupy zabezpečení identity](../security/fundamentals/identity-management-best-practices.md)
* [Osvědčené postupy zabezpečení sítě](../security/fundamentals/network-best-practices.md)
* [Doporučení k PaaS](../security/fundamentals/paas-deployments.md)
* [Dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Zákazníci Log Analytics teď můžou použít Azure Security Center k ochraně svých hybridních cloudových úloh.](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Komunitní zdroje

* [UserVoice ke službě Security Center](https://feedback.azure.com/forums/347535-azure-security-center)
* [Otázka&stránky pro Security Center](/answers/topics/azure-security-center.html)