---
title: Plán připravenosti na Azure Security Center | Microsoft Docs
description: Tento dokument vám poskytne plán připravenosti na Azure Security Center.
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
ms.author: yurid
ms.openlocfilehash: b2b4dc1aa9486e432fb2fae48343c21fba620221
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996693"
---
# <a name="azure-security-center-readiness-roadmap"></a>Plán připravenosti na Azure Security Center
Tento dokument vám poskytne plán připravenosti, který vám pomůže začít s Azure Security Center.

## <a name="understanding-security-center"></a>Porozumění Security Center
Azure Security Center poskytuje jednotnou správu zabezpečení a pokročilou ochranu před internetovými útoky pro úlohy běžící v Azure, v místním prostředí i v jiných cloudech. 

Pomocí následujících zdrojů můžete začít s Security Center.

Zboží
* [Úvod do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Průvodce rychlým startem Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Videa
* [Rychlé úvodní video](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Přehled schopností Security Center prevence, detekce a reakce](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Plánování a provoz
Abyste mohli plně využít výhod Security Center, je důležité pochopit, jak různí jednotlivci nebo týmy ve vaší organizaci používají službu ke splnění požadavků na zabezpečené operace, monitorování, řízení a reakce na incidenty.

Následující zdroje vám pomůžou při plánování a operacích procesů.


Předmětu
* [Průvodce plánováním a provozem Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Obrazový
* [Ochrana úloh hybridního cloudu pomocí Security Center](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>Připojování počítačů k Security Center
Security Center automaticky detekuje všechna předplatná Azure nebo pracovní prostory, které nejsou povolené pro Security Center Standard. To zahrnuje předplatná Azure využívající Security Center volno a pracovní prostory, které nemají povolené řešení zabezpečení.

Následující zdroje vám pomůžou při připojování procesů.

Předmětu
* [Připojování k Azure Security Center Standard pro rozšířené zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Obrazový
* [Azure Security Center Hybrid – přehled](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Zmírnění potíží se zabezpečením pomocí Security Center
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby zjistila skutečné hrozby a snížila falešně pozitivní výsledky.

Následující zdroje vám pomohou při správě výstrah zabezpečení a ochraně prostředků.

Zboží    
* [Sledování stavu zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Ochrana počítačů a aplikací v Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrana sítě v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Ochrana dat a služby Azure SQL v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Obrazový   
* [Zmírnění potíží se zabezpečením pomocí Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center pro reakci na incidenty
Aby se snížily náklady a škody, je důležité mít na úmyslu plán reakce na incidenty, než dojde k útoku. Můžete použít Azure Security Center v různých fázích reakce na incidenty.

Následující zdroje vám pomohou pochopit, jak lze do procesu reakce na incidenty začlenit Security Center.

Videa  
* [Azure Security Center v reakci na incident](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Rychlá reakce na hrozby pomocí operace zabezpečení nové generace a šetření](https://youtu.be/e8iFCz5RM4g)

Zboží    
* [Použití Azure Security Center pro reakci na incidenty](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatizace odpovědí pomocí PlayBook zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Pokročilá obrana cloudu

Virtuální počítače Azure můžou využít výhod pokročilých možností ochrany cloudu v Security Center. Tyto možnosti zahrnují přístup k virtuálnímu počítači (VM) za běhu a adaptivní řízení aplikací.

Pomocí následujících zdrojů se naučíte používat tyto možnosti v Security Center.

Videa  
* [Azure Security Center – přístup k virtuálnímu počítači za běhu](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center – Adaptivní řízení aplikací](https://youtu.be/wWWekI1Y9ck)

Zboží    
* [Správa přístupu k virtuálnímu počítači pomocí za běhu](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Adaptivní řízení aplikací v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Praktické aktivity

* [Security Center praktické laboratorní prostředí](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Doporučení firewallu webových aplikací (WAF) PlayBook v Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center PlayBook: výstrahy zabezpečení](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Další zdroje
* [Stránka dokumentace Security Center](https://docs.microsoft.com/azure/security-center/)
* [Stránka dokumentace k Security Center REST API](https://msdn.microsoft.com/library/mt704034.aspx)
* [Nejčastější dotazy Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Stránka s cenami Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Osvědčené postupy zabezpečení identity](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Osvědčené postupy zabezpečení sítě](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [Doporučení pro PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Dodržování](https://www.microsoft.com/en-us/trustcenter/compliance/due-diligence-checklist)
* [Zákazníci Log Analytics teď můžou použít Azure Security Center k ochraně svých hybridních cloudových úloh.](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Komunitní zdroje

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Fórum komunity Security Center](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



