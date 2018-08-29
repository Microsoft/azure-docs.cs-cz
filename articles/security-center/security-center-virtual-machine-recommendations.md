---
title: Doporučení pro virtuální počítače v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje doporučení Azure Security Center k ochraně virtuálních počítačů a počítačů a vaše webové aplikace a služby App Service Environment.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: rkarlin
ms.openlocfilehash: 7e73d6236f76c58307bb552aeee03bafe66addcc
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43133023"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Vysvětlení doporučení prostředků služby Azure Security Center


## <a name="recommendations"></a>Doporučení
V následujících tabulkách použijte jako vám pomůžou pochopit dostupné výpočetní prostředky a služeb App services, doporučení a co každý z nich dělá, když je použijete.

### <a name="computers"></a>Počítače
| Doporučení | Popis |
| --- | --- |
| [Povolení shromažďování dat pro předplatná](security-center-enable-data-collection.md) |Doporučuje, abyste zapnuli shromažďování dat v zásadách zabezpečení pro každé ze svých předplatných a všechny virtuální počítače ve svých předplatných. |
| [Povolit šifrování pro účet služby Azure Storage](security-center-enable-encryption-for-storage-account.md) | Doporučuje povolit šifrování služby Azure Storage pro neaktivní uložená data. Šifrování služby Storage (SSE) funguje tak, že šifrování dat, když se zapisují do úložiště Azure a dešifruje před načítání. SSE je momentálně dostupná jenom pro službu Azure Blob service a lze použít pro objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB. Další informace najdete v tématu [šifrování služby Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md).</br>SSE je podporována pouze pro účty úložiště Resource Manageru. Účty úložiště Classic se momentálně nepodporují. Klasické modely nasazení a modely nasazení Resource Manageru najdete v tématu [modelech nasazení Azure](../azure-classic-rm.md). |
| [Náprava konfigurací zabezpečení](security-center-remediate-os-vulnerabilities.md) |Doporučuje, abyste zakázali konfiguraci operačního systému se konfigurace pravidla doporučené zabezpečení, například neumožňují ukládání hesel. |
| [Instalace aktualizací systému](security-center-apply-system-updates.md) |Doporučuje nasazení chybějících aktualizací zabezpečení systému a kritických aktualizací do virtuálních počítačů. |
| [Použít Just-In-Time řízení přístupu k síti](security-center-just-in-time.md) | Doporučuje se použít dočasný přístup virtuálních počítačů v. Jenom v čase je funkce ve verzi preview a dostupné na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md). |
| [Restartování po aktualizacích systému](security-center-apply-system-updates.md#reboot-after-system-updates) |Doporučuje, abyste restartovali virtuální počítač k dokončení procesu instalace aktualizací systému. |
| [Instalace Endpoint Protection](security-center-install-endpoint-protection.md) |Doporučuje, abyste do virtuálních počítačů nainstalovali antimalwarové programy (platí pouze pro virtuální počítače s Windows). |
| [Povolení agenta virtuálního počítače](security-center-enable-vm-agent.md) |Umožňuje vám zobrazit, které virtuální počítače vyžadují agenta virtuálního počítače. Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích, aby mohl poskytovat vyhledávání oprav, vyhledávání směrných plánů a antimalwarové programy. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat. |
| [Použití šifrování disku](security-center-apply-disk-encryption.md) |Doporučuje, abyste disky svých virtuálních počítačů zašifrovali pomocí služby Azure Disk Encryption (platí pro virtuální počítače s Windows a Linuxem). Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty. |
| [Aktualizace verze operačního systému](security-center-update-os-version.md) |Doporučuje se aktualizovat verzi operačního systému (OS) pro cloudové služby na nejnovější verzi k dispozici pro váš operační systém řady.  Další informace o službách Cloud Services, najdete v článku [Přehled služby Cloud Services](../cloud-services/cloud-services-choose-me.md). |
| [Není nainstalováno posouzení ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md) |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| [Náprava ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umožňuje vám zobrazit ohrožení zabezpečení systému a aplikací zjištěná řešením posouzení ohrožení zabezpečení nainstalovaným na vašem virtuálním počítači. |

### <a name="app-services"></a>Aplikační služby
| Doporučení | Popis |
| --- | --- |
| App Service by měly být dostupné jenom přes protokol HTTPS | Doporučuje se pouze omezit přístup služby App Service přes protokol HTTPS. |
| Webové sokety by mělo být zakázáno pro webovou aplikaci| Doporučuje, abyste si pečlivě používání webových soketů ve webových aplikacích.  Protokol webových soketů se dá ohrozit různými typy bezpečnostních hrozeb. |
| Použijte vlastní domény pro webovou aplikaci | Doporučuje, použijte vlastní domény k ochraně webové aplikace před běžnými útoky, jako je útok phishing a další útoky související s DNS. |
| Nakonfigurovat omezení IP adres pro webovou aplikaci | Doporučuje se, že můžete definovat seznam IP adres, které můžou přistupovat k vaší aplikace.  Použití omezení IP adres, ochrání webovou aplikaci před běžnými útoky. |
| Nejsou povoleny všechny ("*") prostředkům pro přístup k aplikaci | Doporučuje, nenastavujte parametr WEBSITE_LOAD_CERTIFICATES na "*". Nastavení parametru na "*" znamená, že všechny certifikáty se načtou do úložiště osobních certifikátů webové aplikace.  To může vést k porušení principu nejnižší úrovně oprávnění, jak je pravděpodobné, že web potřebuje přístup ke všem certifikátům za běhu. |
| CORS by nemělo umožňovat každý prostředek pro přístup k aplikaci | Doporučuje se, že povolíte jenom požadované domén pro interakci s vaší webovou aplikací. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat přístup k webové aplikaci všem doménám. |
| Použijte nejnovější podporované rozhraní .NET Framework pro webovou aplikaci | Doporučuje se, že používáte nejnovější verzi rozhraní .NET Framework pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| Použít nejnovější podporovanou verzi Javy pro webovou aplikaci | Doporučuje se, že používáte nejnovější verzi Javy pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| Použít nejnovější podporovanou verzi PHP pro webovou aplikaci | Doporučuje se, že používáte nejnovější verzi PHP pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| [Přidání brány firewall webových aplikací](security-center-add-web-application-firewall.md) |Doporučuje, firewall webových aplikací (WAF) pro koncové body webové nasazení. Doporučení WAF se zobrazí pro jakékoli veřejnou IP adresu (IP adresa na úrovni Instance nebo IP adresu s vyrovnáváním zatížení), který má skupinu zabezpečení sítě spojenou s otevřenými vstupními webovými porty (80,443).</br></br>Security Center doporučuje zřízení WAF, které pomáhají bránit útokům, které cílí na vaše webové aplikace na virtuálních počítačích a ve službě App Service Environment. App Service Environment (ASE) je [Premium](https://azure.microsoft.com/pricing/details/app-service/) služeb možnost plánu služby Azure App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací Azure App Service. Další informace o službě ASE, najdete v článku [dokumentace pro App Service Environment](../app-service/environment/intro.md).</br></br>Přidáním těchto aplikací na vašich stávajících nasazení WAF může chránit několik webových aplikací ve službě Security Center. |
| [Finalizace ochrany aplikací](security-center-add-web-application-firewall.md#finalize-application-protection) |K dokončení konfigurace brány WAF, musí přesměrovat provoz do zařízení WAF. Toto doporučení dokončení změny nezbytné instalační program. |
| Použít nejnovější podporovanou verzi Node.js pro webovou aplikaci | Doporučuje se, že používáte nejnovější verzi Node.js pro nejnovější třídy zabezpečení. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| CORS by nemělo umožňovat každý prostředek pro přístup k aplikaci Function App | Doporučuje se, že povolíte jenom požadované domén pro interakci s vaší webovou aplikací. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat všechny domény pro přístup k vaší aplikaci funkcí. |
| Použijte vlastní domény pro aplikaci Function App | Doporučuje, použijte vlastní domény k ochraně aplikace function app před běžnými útoky, jako je útok phishing a další útoky související s DNS. |
| Nakonfigurovat omezení IP adres pro aplikaci Function App | Doporučuje se, že můžete definovat seznam IP adres, které můžou přistupovat k vaší aplikace. Použití omezení IP adres chrání před běžnými útoky aplikaci function app. |
| Funkce aplikace by měla být přístupná jen přes HTTPS | Doporučuje se pouze omezit přístup z aplikace Function App přes protokol HTTPS. |
| Vzdálené ladění by měl být vypnuté pro aplikaci Function App | Doporučuje vypnout ladění pro aplikace Function App, pokud už nepotřebujete ho používat. Vzdálené ladění vyžaduje příchozí porty potřeba otevřít v aplikaci Function App. |
| Webové sokety by mělo být zakázáno pro aplikaci Function App | Doporučuje, abyste pečlivě zkontrolujte použití procedury webové sokety v rámci aplikace Function App. Protokol webových soketů se dá ohrozit různými typy bezpečnostních hrozeb. |


## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Ochrana vašich počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
