---
title: Plán připravenosti služby Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám poskytne plány připravenosti pro zavedení služby Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: ndicola
editor: ''
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 73ed6976450ef89fb87dd054147325c8b250d1a7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161505"
---
# <a name="azure-security-center-readiness-roadmap"></a>Plán připravenosti služby Azure Security Center
Tento dokument vám poskytne plány připravenosti, které vám pomůžou zahájit používání služby Azure Security Center.

## <a name="understanding-security-center"></a>Vysvětlení služby Security Center
Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami pro úlohy běžící v Azure, na místních systémech a v jiných cloudech. 

V začátcích používání služby Security Center vám pomůžou následující zdroje.

Články
* [Úvod do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Úvodní příručka ke službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Videa
* [Rychlé úvodní video](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Přehled možností prevence, detekce a reakce ve službě Security Center](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Plánování a provoz
Pokud chcete využívat všech výhod služby Security Center, je důležité pochopit, jak různí jednotlivci nebo týmy ve vaší organizaci používají tuto službu k tomu, aby vyhověli požadavkům na zabezpečený provoz, sledování, řízení a reakce na incidenty.

Následující zdroje vám pomůžou při plánování i provozu.


Článek
* [Průvodce plánováním a provozem služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Video
* [Ochrana úloh v hybridním cloudu se službou Security Center](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>Připojení počítačů ke službě Security Center
Security Center automaticky rozpozná veškerá předplatná a pracovní prostory Azure, které ještě nepřešly na úroveň Security Center Standard. To zahrnuje předplatná Azure využívající úroveň Security Center Free volné a pracovní prostory, které nemají povolené řešení zabezpečení.

Následující zdroje vám pomůžou při procesu onboardingu.

Článek
* [Připojení ke službě Azure Security Center úrovně Standard pro zvýšení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Video
* [Hybridní Azure Security Center – přehled](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Řešení problémů zabezpečení pomocí služby Security Center
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů.

Následující zdroje vám pomůžou při správě výstrah zabezpečení a ochraně vašich prostředků.

Články    
* [Monitorování stavu zabezpečení pomocí Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Ochrana virtuálních počítačů pomocí Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-recommendations)
* [Ochrana sítě pomocí Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Ochrana aplikací pomocí Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-application-recommendations)
* [Ochrana služby a dat Azure SQL pomocí Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Video   
* [Řešení problémů zabezpečení pomocí Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center a reakce na incidenty
Kvůli snížení nákladů a minimalizaci škod je důležité mít hotový plán reakce na incidenty ještě před tím, než k útoku dojde. Azure Security Center můžete využít v různých fázích reakce na incidenty.

Následující zdroje vám pomůžou porozumět tomu, jak zapojit Security Center do vašich procesů reakce na incidenty.

Videa  
* [Využití služby Azure Security Center při reakci na incidenty](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Rychlá reakce na hrozby s použitím bezpečnostních a vyšetřovacích nástrojů nové generace](https://youtu.be/e8iFCz5RM4g)

Články    
* [Využití Azure Security Center při reakci na incidenty](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatizace reakcí pomocí playbooku zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Pokročilá obrana cloudu

Virtuální počítače Azure můžou využít výhod pokročilé cloudové obrany, které Security Center nabízí. Mezi tyto možnosti patří zejména přístup k virtuálním počítačům metodou „Just-in-Time“ a adaptivní řízení aplikací.

V následujících zdrojích se dozvíte, jak tyto možnosti ve službě Security Center využít.

Videa  
* [Azure Security Center – Přístup k virtuálním počítačům metodou Just-in-Time](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center – Adaptivní řízení aplikací](https://youtu.be/wWWekI1Y9ck)

Články    
* [Správa přístupu k virtuálním počítačům pomocí metody Just-in-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Adaptivní řízení aplikací v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Praktické aktivity

* [Praktická cvičení pro Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook doporučených postupů pro Firewall webových aplikací (WAF) ve službě Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Playbook Azure Security Center: Výstrahy zabezpečení](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Další zdroje informací:
* [Stránky dokumentace služby Security Center](https://docs.microsoft.com/azure/security-center/)
* [Stránky dokumentace rozhraní Security Center REST API](https://msdn.microsoft.com/library/mt704034.aspx)
* [Nejčastější dotazy ke službě Azure Security Center (FAQ)](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Ceník služby Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Osvědčené postupy zabezpečení identity](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices)
* [Osvědčené postupy zabezpečení sítě](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)
* [Doporučení k PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Dodržování předpisů](https://www.microsoft.com/en-us/trustcenter/compliance/due-diligence-checklist)
* [Zákazníci OMS teď můžou pomocí služby Azure Security Center chránit své hybridní cloudové úlohy](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Komunitní zdroje

* [UserVoice ke službě Security Center](https://feedback.azure.com/forums/347535-azure-security-center)
* [Fórum komunity ke službě Security Center](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



