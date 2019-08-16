---
title: Doporučení pro virtuální počítače v Azure Security Center | Microsoft Docs
description: Tento dokument vysvětluje Azure Security Center doporučení, která vám pomůžou chránit vaše virtuální počítače a počítače a vaše webové aplikace a App Service prostředí.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: 798e027ca611905766b1fb8bcdb89cba4aeaf9b2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531339"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Vysvětlení Azure Security Centerch doporučení k prostředkům


## <a name="recommendations"></a>Doporučení
Následující tabulky vám pomůžou pochopit dostupná doporučení výpočetních a aplikačních služeb a to, jak se používají.

### <a name="computers"></a>Počítače
| Doporučení | Popis |
| --- | --- |
| Povolit shromažďování dat pro předplatná|Doporučuje, abyste zapnuli shromažďování dat v zásadách zabezpečení pro každé ze svých předplatných a všechny virtuální počítače ve svých předplatných. |
| Povolit šifrování pro účet úložiště Azure| Doporučuje, abyste povolili šifrování služby Azure Storage pro neaktivní neaktivní data. Šifrování služby Storage (SSE) funguje tak, že šifruje data při zápisu do služby Azure Storage a před načtením je dešifruje. SSE je aktuálně dostupná jenom pro Azure Blob service a dá se použít pro objekty blob bloku, objekty blob stránky a doplňovací objekty blob. Další informace najdete v části [šifrování služby Storage pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.</br>SSE se podporuje jenom v Správce prostředků účtech úložiště. Klasické účty úložiště se v tuto chvíli nepodporují. Pro pochopení modelu nasazení Classic a Správce prostředků najdete informace v tématu modely nasazení Azure. |
| Opravit konfigurace zabezpečení|Doporučuje, abyste narovnali konfigurace operačního systému s doporučenými pravidly konfigurace zabezpečení, třeba nepovoluje ukládání hesel. |
| Nainstalovat aktualizace systému |Doporučuje nasazení chybějících aktualizací zabezpečení systému a kritických aktualizací do virtuálních počítačů. |
| Použití řízení přístupu k síti podle potřeby| Doporučuje, abyste použili přístup k virtuálnímu počítači přesně v čase. Funkce právě v čase je ve verzi Preview a dostupná na úrovni Standard Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md). |
| Restartovat po aktualizacích systému|Doporučuje, abyste restartovali virtuální počítač k dokončení procesu instalace aktualizací systému. |
| Nainstalovat Endpoint Protection|Doporučuje, abyste do virtuálních počítačů nainstalovali antimalwarové programy (platí pouze pro virtuální počítače s Windows). |
| Povolit agenta virtuálního počítače |Umožňuje vám zobrazit, které virtuální počítače vyžadují agenta virtuálního počítače. Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích, aby mohl poskytovat vyhledávání oprav, vyhledávání směrných plánů a antimalwarové programy. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat. |
| Použít šifrování disku|Doporučuje, abyste disky svých virtuálních počítačů zašifrovali pomocí služby Azure Disk Encryption (platí pro virtuální počítače s Windows a Linuxem). Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty. |
| Aktualizace verze operačního systému|Doporučuje aktualizovat verzi operačního systému (OS) pro vaši cloudovou službu na nejnovější verzi dostupnou pro vaši rodinu operačních systémů.  Další informace o Cloud Services najdete v tématu Cloud Services Overview. |
| Není nainstalováno posouzení ohrožení zabezpečení|Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| Opravit chyby zabezpečení) |Umožňuje vám zobrazit ohrožení zabezpečení systému a aplikací zjištěná řešením posouzení ohrožení zabezpečení nainstalovaným na vašem virtuálním počítači. |

### Aplikační služby<a name="app-services"></a>
| Doporučení | Popis |
| --- | --- |
| App Service by měl být přístupný jenom přes HTTPS | Doporučuje, abyste omezili přístup k App Service jenom přes HTTPS. |
| Webové sokety by mělo být zakázáno pro webovou aplikaci| Doporučuje pečlivě zkontrolovat použití webových soketů v rámci webových aplikací.  Protokol webových soketů se dá ohrozit různými typy bezpečnostních hrozeb. |
| Použijte vlastní domény pro webovou aplikaci | Doporučuje používat vlastní domény k ochraně webové aplikace před běžnými útoky, jako jsou útoky phishing a další útoky související s DNS. |
| Nakonfigurovat omezení IP adres pro webovou aplikaci | Doporučuje definovat seznam IP adres, které mají povolený přístup k vaší aplikaci.  Použití omezení IP adres, ochrání webovou aplikaci před běžnými útoky. |
| Nejsou povoleny všechny ("*") prostředkům pro přístup k aplikaci | Doporučuje, abyste nastavili parametr WEBSITE_LOAD_CERTIFICATES na *. Nastavením parametru na*' ' znamená, že všechny certifikáty budou načteny do osobního úložiště certifikátů vaší webové aplikace.  To může vést k porušení principu nejnižší úrovně oprávnění, jak je pravděpodobné, že web potřebuje přístup ke všem certifikátům za běhu. |
| CORS by neměl umožňovat každému prostředku přístup k vaší aplikaci. | Doporučuje, abyste povolili interakci s webovou aplikací pouze požadovanými doménami. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat přístup k webové aplikaci všem doménám. |
| Použijte nejnovější podporované rozhraní .NET Framework pro webovou aplikaci | Doporučuje, abyste pro nejnovější třídy zabezpečení používali nejnovější verzi .NET Framework. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| Použít nejnovější podporovanou verzi Javy pro webovou aplikaci | Doporučuje, abyste pro nejnovější třídy zabezpečení používali nejnovější verzi Java. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| Použít nejnovější podporovanou verzi PHP pro webovou aplikaci | Doporučuje, abyste pro nejnovější třídy zabezpečení používali nejnovější verzi PHP. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| [Přidání brány firewall webových aplikací](security-center-add-web-application-firewall.md) |Doporučuje nasadit Firewall webových aplikací (WAF) pro koncové body webu. WAF doporučení se zobrazuje pro všechny veřejné IP adresy (buď IP adresa na úrovni instance nebo IP s vyrovnáváním zatížení), které mají přidruženou skupinu zabezpečení sítě s otevřenými vstupními webovými porty (80 443).</br></br>Security Center doporučuje zřídit WAF, které vám pomůžou chránit před útoky zaměřenými na vaše webové aplikace na virtuálních počítačích a v App Service Environment. App Service Environment (pomocným mechanismem) je možnost plánu [Premium](https://azure.microsoft.com/pricing/details/app-service/) Azure App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění Azure App Service aplikací. Další informace o pomocném mechanismu řízení najdete v [dokumentaci k App Service Environment](../app-service/environment/intro.md).</br></br>Můžete chránit více webových aplikací v Security Center přidáním těchto aplikací do stávajících nasazení WAF. |
| [Finalizace ochrany aplikací](security-center-add-web-application-firewall.md#finalize-application-protection) |Aby bylo možné dokončit konfiguraci WAF, musí být provoz přesměrován na zařízení WAF. Podle tohoto doporučení dokončíte potřebné změny instalačního programu. |
| Použít nejnovější podporovanou verzi Node.js pro webovou aplikaci | Doporučuje, abyste pro nejnovější třídy zabezpečení používali nejnovější verzi Node. js. Použití starší třídy a typy můžou ohrožovat zabezpečení aplikace. |
| CORS by nemělo umožňovat každý prostředek pro přístup k aplikaci Function App | Doporučuje, abyste povolili interakci s webovou aplikací pouze požadovanými doménami. Pro různé prostředků zdroji (CORS) pro sdílení obsahu by nemělo umožňovat všechny domény pro přístup k vaší aplikaci funkcí. |
| Použijte vlastní domény pro aplikaci Function App | Doporučuje používat vlastní domény k ochraně aplikace Function App před běžnými útoky, jako jsou útoky phishing a další útoky související s DNS. |
| Nakonfigurovat omezení IP adres pro aplikaci Function App | Doporučuje definovat seznam IP adres, které mají povolený přístup k vaší aplikaci. Použití omezení IP adres chrání před běžnými útoky aplikaci function app. |
| Funkce aplikace by měla být přístupná jen přes HTTPS | Doporučuje, abyste omezili přístup k aplikacím funkcí jenom přes protokol HTTPS. |
| Vzdálené ladění by mělo být pro aplikaci funkcí vypnuté | Doporučuje vypnout ladění pro Function App, pokud už je nepotřebujete používat. Vzdálené ladění vyžaduje příchozí porty potřeba otevřít v aplikaci Function App. |
| Webové sokety by mělo být zakázáno pro aplikaci Function App | Doporučuje pečlivě zkontrolovat použití webových soketů v aplikacích Function App. Protokol webových soketů se dá ohrozit různými typy bezpečnostních hrozeb. |


## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
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
