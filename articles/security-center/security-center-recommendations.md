---
title: Správa doporučení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vás provede jak doporučení ve službě Azure Security Center pomoci chránit prostředky Azure a zůstat souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2018
ms.author: rkarlin
ms.openlocfilehash: 6aba94627d0676917664dc654507dce286a4e685
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304556"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Správa doporučení zabezpečení v Azure Security Center
Tento dokument vás provede postupy použijte doporučení ve službě Azure Security Center k ochraně vašich prostředků Azure.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není podrobný průvodce.
>
>

## <a name="what-are-security-recommendations"></a>Co jsou doporučení týkající se zabezpečení?
Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

## <a name="implementing-security-recommendations"></a>Implementace doporučení zabezpečení
### <a name="set-recommendations"></a>Sada doporučení
V [nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md), zjistíte, jak:

* Konfigurovat zásady zabezpečení.
* Shromažďování dat zapněte.
* Zvolte, které doporučení zobrazíte jako součást zásad zabezpečení.

Aktuální zásady doporučení center po aktualizacích systému, pravidla standardních hodnot, antimalwarové programy [skupiny zabezpečení sítě](../virtual-network/security-overview.md) podsítí a síťových rozhraní, auditování služby SQL database, SQL database transparentní šifrování dat, a brány firewall webových aplikací.  [Nastavení zásad zabezpečení](security-center-policies.md) obsahuje popis jednotlivých možností doporučení.

### <a name="monitor-recommendations"></a>Doporučení pro monitorování
Po nastavení zásad zabezpečení bude Security Center analyzovat stav zabezpečení vašich prostředků Azure za účelem identifikace potenciálních ohrožení zabezpečení. **Doporučení** dlaždici v části **přehled** vás informuje, celkový počet doporučení identifikované pomocí služby Security Center.

![Doporučení pro dlaždici][1]

Pokud chcete zobrazit podrobnosti o jednotlivých doporučení, vyberte **doporučení dlaždici** pod **přehled**. **Doporučení** otevře.

![Filtrovat doporučení][2]

Můžete filtrovat doporučení. Chcete-li filtrovat doporučení, vyberte **filtr** na **doporučení** okno. **Filtr** se otevře okno a vybrat závažnost a stavu hodnoty, které chcete zobrazit.

Doporučení jsou zobrazena ve formátu tabulky, kde každý řádek představuje jedno konkrétní doporučení. Jsou sloupce této tabulky:

* **Popis**: vysvětluje doporučení a co je potřeba provést k vyřešení ho.
* **PROSTŘEDEK**: seznam materiálů, u kterých bude použito toto doporučení.
* **Stav**: popisuje aktuální stav doporučení:
  * **Otevřít**: doporučení dosud nebylo řešeno.
  * **V průběhu**: doporučení se aktuálně zavádí prostředky a nevyžaduje žádné akce se.
  * **Vyřešené**: doporučení již byla dokončena (v tomto případě řádku je zobrazena šedě).
* **SEVERITY** (Závažnost): Popisuje závažnost tohoto konkrétního doporučení:
  * **Vysoká**: ohrožení zabezpečení existuje u významného prostředku (například aplikace, virtuálního počítače nebo skupiny zabezpečení sítě) a vyžaduje pozornost.
  * **Střední**: existuje ohrožení zabezpečení a nekritické nebo další kroky jsou požadovány k jeho odstranění nebo k dokončení procesu.
  * **Nízká**: existuje ohrožení zabezpečení, které mělo by se řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení, s nízkou doporučení nejsou k dispozici, ale můžete filtrovat podle doporučení s nízkou, pokud chcete zobrazit.)

V následující tabulce použijte jako odkaz vám pomůžou pochopit dostupné doporučení a každý z nich, co dělá když je použijete.

> [!NOTE]
> Chcete se dozvědět [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
>
>

| Doporučení | Popis |
| --- | --- |
| [Povolení shromažďování dat pro předplatná](security-center-enable-data-collection.md) |Doporučuje zapnout shromažďování dat v zásadách zabezpečení pro každý z vašich předplatných a všechny virtuální počítače Azure (virtuální počítače) a počítače mimo Azure. |
| [Náprava konfigurací zabezpečení](security-center-remediate-os-vulnerabilities.md) |Doporučuje konfiguraci operačního systému se konfigurace pravidla doporučené zabezpečení, například zarovnání, není povoleno ukládání hesel. |
| [Instalace aktualizací systému](security-center-apply-system-updates.md) |Doporučuje nasazení chybějících zabezpečení systému a kritických aktualizací do Windows a virtuální počítače s Linuxem a počítačích. |
| [Použít Just-In-Time řízení přístupu k síti](security-center-just-in-time.md) | Doporučuje se použít dočasný přístup virtuálních počítačů v. Podle potřeby v čase funkce je dostupná na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md). |
| [Restartování po aktualizacích systému](security-center-apply-system-updates.md#reboot-after-system-updates) |Doporučuje, abyste restartovali virtuální počítač k dokončení procesu instalace aktualizací systému. |
| [Přidání brány firewall webových aplikací](security-center-add-web-application-firewall.md) |Doporučuje, firewall webových aplikací (WAF) pro koncové body webové nasazení. Doporučení WAF se zobrazí pro jakékoli veřejnou IP adresu (IP adresa na úrovni Instance nebo IP adresu s vyrovnáváním zatížení), který má skupinu zabezpečení sítě spojenou s otevřenými vstupními webovými porty (80,443). </br>Security Center doporučuje zřízení WAF, které pomáhají bránit útokům, které cílí na vaše webové aplikace na virtuálních počítačích a ve službě App Service Environment. App Service Environment (ASE) je [Premium](https://azure.microsoft.com/pricing/details/app-service/) služeb možnost plánu služby Azure App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací Azure App Service. Další informace o službě ASE, najdete v článku [dokumentace pro App Service Environment](../app-service/environment/intro.md).</br>Přidáním těchto aplikací na vašich stávajících nasazení WAF může chránit několik webových aplikací ve službě Security Center. |
| [Finalizace ochrany aplikací](security-center-add-web-application-firewall.md#finalize-application-protection) |K dokončení konfigurace brány WAF, musí přesměrovat provoz do zařízení WAF. Toto doporučení dokončení změny nezbytné instalační program. |
| [Přidání brány firewall příští generace](security-center-add-next-generation-firewall.md) |Doporučuje se přidání brány Firewall pro další generace (NGFW) od partnera Microsoftu o navýšení vaší ochrany zabezpečení. |
| [Směrování provozu jenom přes NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Doporučuje konfiguraci sítě pravidla skupiny zabezpečení (NSG), které vynucují příchozí provoz k vašemu virtuálnímu počítači přes vaše NGFW. |
| [Instalace Endpoint Protection](security-center-install-endpoint-protection.md) |Doporučuje, abyste do virtuálních počítačů nainstalovali antimalwarové programy (platí pouze pro virtuální počítače s Windows). |
| [Povolit skupiny zabezpečení sítě u podsítí nebo virtuálních počítačů](security-center-enable-network-security-groups.md) |Doporučuje povolit skupiny Nsg na podsítě nebo virtuálních počítačů. |
| [Omezit přístup přes internetový koncový bod](security-center-restrict-access-through-internet-facing-endpoints.md) |Doporučuje nakonfigurovat pravidla pro příchozí provoz pro skupiny zabezpečení sítě. |
| [Povolení auditování a detekce hrozeb na SQL serverech](security-center-enable-auditing-on-sql-servers.md) |Doporučuje, zapněte auditování a detekce hrozeb pro servery Azure SQL. (Pouze služby azure SQL. Nezahrnuje běžící na virtuálních počítačích SQL.) |
| [Povolení auditování a detekce hrozeb v databázích SQL](security-center-enable-auditing-on-sql-databases.md) |Doporučuje, zapněte auditování a detekci hrozeb u databází Azure SQL. (Pouze služby azure SQL. Nezahrnuje běžící na virtuálních počítačích SQL.) |
| [Povolit transparentní šifrování dat v databázích SQL](security-center-enable-transparent-data-encryption.md) |Doporučuje povolit šifrování pro databáze SQL. (Azure SQL služby pouze.) |
| [Povolení agenta virtuálního počítače](security-center-enable-vm-agent.md) |Umožňuje vám zobrazit, které virtuální počítače vyžadují agenta virtuálního počítače. O zřízení opravu, vyhledávání směrných plánů a antimalwarové programy musí být Agent virtuálního počítače nainstalovaný na virtuálních počítačích. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat. |
| [Použití šifrování disku](security-center-apply-disk-encryption.md) |Doporučuje, abyste disky svých virtuálních počítačů zašifrovali pomocí služby Azure Disk Encryption (platí pro virtuální počítače s Windows a Linuxem). Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty. |
| [Poskytnutí podrobností kontaktů zabezpečení](security-center-provide-security-contact-details.md) |Doporučuje se, že zadáte zabezpečení kontaktní informace na každé z vašich předplatných. Kontaktní údaje je e-mailovou adresu a telefonní číslo. Tyto informace slouží k vás kontaktovat, pokud náš tým zabezpečení zjistí ohrožení vašich prostředků. |
| [Aktualizace verze operačního systému](security-center-update-os-version.md) |Doporučuje se aktualizovat verzi operačního systému (OS) pro cloudové služby na nejnovější verzi k dispozici pro váš operační systém řady.  Další informace o službách Cloud Services, najdete v článku [Přehled služby Cloud Services](../cloud-services/cloud-services-choose-me.md). |
| [Není nainstalováno posouzení ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md) |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| [Náprava ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umožňuje vám zobrazit ohrožení zabezpečení systému a aplikací zjištěná řešením posouzení ohrožení zabezpečení nainstalovaným na vašem virtuálním počítači. |
| [Povolit šifrování pro účet služby Azure Storage](security-center-enable-encryption-for-storage-account.md) | Doporučuje povolit šifrování služby Azure Storage pro neaktivní uložená data. Šifrování služby Storage (SSE) funguje tak, že šifrování dat, když se zapisují do úložiště Azure a dešifruje před načítání. SSE je momentálně dostupná jenom pro službu Azure Blob service a lze použít pro objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB. Další informace najdete v tématu [šifrování služby Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md).</br>SSE je podporována pouze pro účty úložiště Resource Manageru. |
| [Povolit aplikacím adaptivní ovládací prvky](security-center-adaptive-application.md) | Doporučuje se použít adaptivní řízení aplikací na virtuálních počítačích Windows. Tato funkce je dostupná na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md). |
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
| Určení více než jednoho vlastníka předplatného | Doporučuje se určit více než jednoho vlastníka předplatného abyste měli redundance přístup správce. |
| Určit vlastníky až 3 v rámci předplatného | Doporučuje se určit méně než 3 na vlastníky těchto předplatných za účelem snížení riziko porušení zabezpečení ohroženým uživatelem. |
| Povolte MFA pro účty s oprávněními vlastníka v rámci předplatného | Doporučuje povolit Vícefaktorové ověřování (MFA) pro všechny účty předplatných s oprávněními správce nedošlo k porušení zabezpečení účtů nebo prostředků. |
| Povolte MFA pro účty s oprávněním pro zápis v předplatném | Doporučuje povolit Vícefaktorové ověřování (MFA) pro všechny účty předplatných s oprávněními pro zápis do nedošlo k porušení zabezpečení účtů nebo prostředků. |
| Povolte MFA pro účty s oprávněním pro čtení v rámci předplatného | Doporučuje povolit Vícefaktorové ověřování (MFA) pro všechny účty předplatných s oprávněními pro čtení k nedošlo k porušení zabezpečení účtů nebo prostředků. |
| Z předplatného odebrat externí účty s oprávněním pro čtení | Doporučuje, aby nedocházelo k nemonitorovanému přístupu odebrat externí účty s oprávněními pro čtení z předplatného. |
| Odeberte z předplatného externí účty s oprávněním pro zápis | Doporučuje, aby nedocházelo k nemonitorovanému přístupu odebrat externí účty s oprávněními pro zápis z předplatného. |
| Z předplatného odebrat externí účty s oprávněními vlastníka | Doporučuje, aby nedocházelo k nemonitorovanému přístupu odebrat externí účty s oprávněními vlastníka ze svého předplatného. |
| Odeberte zastaralé účty z předplatného | Doporučuje odebrat zastaralé účty z vašich předplatných. |
| Z předplatného odebrat zastaralé účty s oprávněními vlastníka | Doporučuje odebrat zastaralé účty s oprávněními vlastníka z vašich předplatných. |

### <a name="apply-recommendations"></a>Použití doporučení
Po kontrole všech doporučení, rozhodněte, které ten, který jste měli nejdřív použít. Doporučujeme používat hodnocení závažnosti, protože parametr hlavní k vyhodnocení, které doporučení bude použito první.

V tabulce výše uvedených doporučení vyberte doporučení a projděte jej jako příklad toho, jak chcete doporučení použít.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste se seznámili doporučení zabezpečení v Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
