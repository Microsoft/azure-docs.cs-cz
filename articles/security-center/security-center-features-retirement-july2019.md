---
title: Funkce Centra zabezpečení odchodu do důchodu (červenec 2019) | Dokumenty společnosti Microsoft
description: Tento článek popisuje funkce v Centru zabezpečení, které byly vyřazeny v červenci 31, 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: fda49ae31d7598497a3128a846a3f4e12e84ffe1
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435910"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Funkce Centra zabezpečení (Červenec 2019)

> [!NOTE]
> Tento dokument podrobně popisuje seznam funkcí, které byly vyřazeny z Centra zabezpečení Azure v červenci 31st, 2019.
>
>

Během šesti měsíců před červencem 2019 jsme v Centru zabezpečení Azure provedli několik [vylepšení.](https://azure.microsoft.com/updates/?product=security-center)
S těmito vylepšenými funkcemi jsme 31. července 2019 odstranili některé redundantní funkce a související api z Centra zabezpečení.

Většinu těchto vyřazených funkcí lze nahradit dalšími funkcemi v Azure Security Center nebo Azure Log Analytics. Další funkce lze implementovat pomocí [Azure Sentinelu (preview)](https://azure.microsoft.com/services/azure-sentinel/).

Mezi funkce Centra zabezpečení v důchodu patří:

- [Řídicí panel Události](#menu_events)
- [Hledání položky nabídky](#menu_search)
- [Zobrazit klasický odkaz Identity & Access na identitu a přístup (preview)](#menu_classicidentity)
- [Tlačítko Mapy událostí zabezpečení na mapě výstrah zabezpečení (náhled)](#menu_securityeventsmap)
- [Vlastní pravidla výstrah (náhled)](#menu_customalerts)
- [Tlačítko Prozkoumat v výstrahách zabezpečení ochrany před hrozbami](#menu_investigate)
- [Podmnožina bezpečnostních řešení](#menu_solutions)
- [Úprava konfigurací zabezpečení pro zásady zabezpečení](#menu_securityconfigurations)
- [Řídicí panel zabezpečení a auditu (původně použitý na portálu OMS) pro pracovní prostory Log Analytics](#menu_securityomsdashboard)

Tento článek obsahuje podrobné informace o jednotlivých vyřazených funkcích a kroky, které můžete provést k implementaci náhradních funkcí.

## <a name="events-dashboard"></a>Řídicí panel Události<a name="menu_events"></a>

Security Center používá agenta Log Analytics ke shromažďování různých konfigurací a událostí souvisejících se zabezpečením z vašich počítačů. Ukládá tyto události ve vašich pracovních prostorech. [Řídicí panel událostí](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umožňuje zobrazit tato data a poskytuje vstupní bod do analýzy protokolů.

Vyřazeni jsme z řídicího panelu událostí, který se objevil při výběru pracovního prostoru:

![Řídicí panel Události][2]

### <a name="events-dashboard---the-new-experience"></a>Řídicí panel událostí – nové prostředí

Doporučujeme vám, abyste pomocí nativních funkcí Služby Azure Log Analytics zobrazili významné události ve vašich pracovních prostorech.

Pokud jste v Centru zabezpečení vytvořili vlastní významné události, budou přístupné. V log analytics přejděte na Vybrat**hledání uložené** **v pracovním prostoru** > . Data nebudou ztracena ani změněna. Nativní pozoruhodné události jsou také k dispozici ze stejné obrazovky v Log Analytics.

![Hledání uložená v pracovním prostoru][3]

## <a name="search-menu-entry"></a>Hledání položky nabídky<a name="menu_search"></a>

Azure Security Center aktuálně používá azure monitor protokoly hledání k načtení a analýze dat zabezpečení. Tato obrazovka slouží jako okno pro vyhledávací stránku Log Analytics a umožňuje uživatelům spouštět vyhledávací dotazy na vybraném pracovním prostoru. Další informace najdete v [tématu Azure Security Center search](https://docs.microsoft.com/azure/security-center/security-center-search). Toto vyhledávací okno jsme vyřazeni:

![Hledat stránku][4]

### <a name="search-menu-entry---the-new-experience"></a>Hledání položky menu - nové prostředí

Doporučujeme vám používat nativní funkce Azure Log Analytics k provádění vyhledávacích dotazů ve vašich pracovních prostorech. Přejděte na Azure Log Analytics a vyberte **protokoly**.

![Stránka protokolů Log Analytics][5]

## <a name="classic-identity--access-preview"></a>Klasický přístup k identitě & (náhled)<a name="menu_classicidentity"></a>

Prostředí Classic Identity & Access v Centru zabezpečení aktuálně zobrazuje řídicí panel identity a přístupu k informacím v Log Analytics. Zobrazení tohoto řídicího panelu:

1. Vyberte **možnost Zobrazit klasický přístup k identitě & .**

   ![Stránka Identita][6]

1. Zobrazení **řídicího panelu Identity & Access**.

    ![Stránka Identity – výběr pracovního prostoru][7]

1. Vyberte pracovní prostor, který otevře řídicí panel **Identity & Access** v Log Analytics, abyste zobrazili identitu a přístup k informacím ve vašem pracovním prostoru.

   ![Stránka Identity - řídicí panel][8]

Vyřazeny jsme všechny tři obrazovky uvedené v předchozích krocích. Vaše data zůstanou k dispozici v řešení zabezpečení Log Analytics a nebyla změněna ani odebrána.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (Preview) – nové prostředí

Řídicí panel Log Analytics ukázal přehledy o jednom pracovním prostoru. Nativní funkce Security Center však poskytují přehled o všech předplatných a všech pracovních prostorech, které jsou s nimi spojené. Můžete přistupovat ke snadno použitelnému zobrazení, které vám umožní soustředit se na to, co je důležité, s doporučeními seřazenými podle jejich skóre Secure Score.

Všechny funkce řídicího panelu **Přístup u identity &** v Log Analytics lze dosáhnout výběrem **identity & přístup (Preview)** v centru zabezpečení.

![Identita stránka - klasické zkušenosti odchodu do důchodu][9]

## <a name="security-events-map"></a>Mapa událostí zabezpečení<a name="menu_securityeventsmap"></a>

Centrum zabezpečení poskytuje [mapu výstrah zabezpečení,](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) která pomáhá identifikovat bezpečnostní hrozby. Tlačítko **Přejít na mapy událostí zabezpečení** na této mapě otevře řídicí panel, který umožňuje zobrazit nezpracované události zabezpečení ve vybraném pracovním prostoru.

Odebrali jsme tlačítko **Přejít na mapy událostí zabezpečení** a řídicí panel pro každý pracovní prostor.

![Mapa výstrah zabezpečení - tlačítko][10]

Když vyberete tlačítko **Přejít na mapy událostí zabezpečení,** otevře se řídicí panel (nyní vyřazené) analýzy hrozeb.

![Řídicí panel Analýza hrozeb][11]

Když zvolíte pracovní prostor pro zobrazení jeho řídicího panelu analýzy hrozeb, otevřete obrazovku (nyní vyřazené) výstrahy zabezpečení (náhled) v Log Analytics.

![Mapa výstrah zabezpečení v log analytics][12]

Vaše stávající data zůstanou k dispozici v řešení zabezpečení Log Analytics a nebyly změněny ani odebrány.

### <a name="security-events-map---the-new-experience"></a>Mapa událostí zabezpečení - nové prostředí

Doporučujeme použít funkce mapy výstrah integrované do Centra zabezpečení: **Mapa výstrah zabezpečení (Preview).** Tato funkce poskytuje optimalizované prostředí a funguje ve všech předplatných a přidružených pracovních prostorech. Poskytuje zobrazení na vysoké úrovni napříč prostředím a není zaměřena na jeden pracovní prostor.

## <a name="custom-alert-rules-preview"></a>Vlastní pravidla výstrah (náhled)<a name="menu_customalerts"></a>

30. června 2019 jsme [vyřadili vlastní výstrahy,](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) protože jeho základní infrastruktura byla vyřazena. Po datu vyřazení se již negenerují vlastní výstrahy zabezpečení.
Doporučujeme povolit [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) a znovu vytvořit vlastní výstrahy tam. Případně můžete vytvořit výstrahy pomocí výstrah protokolu Azure Monitor.

Jak vytvořit vlastní výstrahy pomocí Azure Sentinelu:

1. [Otevřete Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) a vyberte pracovní prostor, kde jsou uloženy vlastní výstrahy
1. V nabídce vyberte **Analytics.**
1. Postupujte podle pokynů v následujícím [kurzu](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) o tom, jak vytvořit vlastní výstrahy v Azure Sentinelu

Pokud nemáte zájem o používání Azure Sentinelu, můžete vytvářet výstrahy pomocí výstrah protokolu Azure Monitor. Pokyny najdete v [tématu Vytváření, zobrazení a správa výstrah protokolu pomocí azure monitorování](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) a [protokolvýstrahy v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Vlastní výstrahy][13]

Další informace o vlastních výstrahách vyřazení, najdete [v tématu vlastní pravidla výstrah v Centru zabezpečení Azure (Preview)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Šetření výstrah zabezpečení<a name="menu_investigate"></a>

[Funkce Vyšetřování](https://docs.microsoft.com/azure/security-center/security-center-investigation) v Centru zabezpečení vám pomůže zprostředkovat potenciální bezpečnostní incident. Tato funkce umožňuje pochopit rozsah incidentu a sledovat jeho hlavní příčinu. Tuto funkci jsme odebrali z Centra zabezpečení, protože byla nahrazena vylepšeným prostředím v [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/).

![Incident zabezpečení][14]

Když vyberete tlačítko **Vyšetřit** z obrazovky **incidentu zabezpečení,** otevřete řídicí panel vyšetřování (preview) v Log Analytics. Stáhli jsme vyšetřovací přístroj.

Vaše stávající data zůstanou k dispozici v řešení zabezpečení Log Analytics a nebyly změněny ani odebrány.

![Řídicí panel šetření v analýze protokolů][15]

### <a name="investigation---the-new-experience"></a>Vyšetřování - nová zkušenost

Doporučujeme vám přejít na [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) pro bohaté prostředí vyšetřování. Azure Sentinel poskytuje výkonné vyhledávací a dotazovací nástroje pro hledání bezpečnostních hrozeb napříč zdroji dat vaší organizace.

## <a name="subset-of-security-solutions"></a>Podmnožina bezpečnostních řešení<a name="menu_solutions"></a>

Security Center může povolit [integrovaná řešení zabezpečení v Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Z Security Center jsme vyřazeni z následujících partnerských řešení. Tato řešení jsou povolená v [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/) spolu s řadou dalších zdrojů dat.

- [Řešení brány firewall nové generace a brány firewall webových aplikací](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrace bezpečnostních řešení, která podporují společný formát událostí (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Po vyřazení nelze přidat nebo upravit žádný z typů řešení uvedených v předchozím seznamu, z rozhraní ui nebo rozhraní API. Azure Security Center už nebude zjišťovat žádné nové instance těchto partnerských řešení.

Pokud máte existující připojená řešení, doporučujeme přejít na Azure Sentinel.

![Řešení bezpečnostních center][16]

## <a name="edit-security-configurations-for-security-policies"></a>Úprava konfigurací zabezpečení pro zásady zabezpečení<a name="menu_securityconfigurations"></a>

Azure Security Center monitoruje konfiguraci zabezpečení tím, že pro posílení operačního systému uplatní sadu [více než 150 doporučených pravidel](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Tato pravidla se týkající se brány firewall, auditování, zásad hesel a další. Pokud se na počítači zjistí zranitelná konfigurace, Security Center vygeneruje doporučení k zabezpečení. [Obrazovka Upravit konfiguraci zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) umožňuje zákazníkům přizpůsobit výchozí konfiguraci zabezpečení operačního systému v Centru zabezpečení.

Tuto funkci náhledu jsme vyřazeni. Chcete-li obnovit konfigurace zabezpečení zpět na výchozí hodnoty po datu vyřazení, učiňte tak prostřednictvím rozhraní API nebo prostředí Powershell [podle následujících pokynů](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization).

![Úpravy konfigurací zabezpečení][17]

### <a name="edit-security-configurations---the-new-experience"></a>Úpravy konfigurací zabezpečení – nové prostředí

Máme v úmyslu povolit Security Center pro podporu [hosta konfigurace agenta](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Taková aktualizace umožní mnohem bohatší sadu funkcí, včetně podpory pro další operační systémy a integrace zásad Azure pro hosta pro konfigurace hosta. Po povolení těchto změn budete mít také možnost řídit konfigurace ve velkém měřítku a automaticky je aplikovat na nové prostředky.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Řídicí panel zabezpečení a auditu pro pracovní prostory Analýzy protokolů<a name="menu_securityomsdashboard"></a>

Řídicí panel zabezpečení a auditu byl původně použit na portálu OMS. V Log Analytics, řídicí panel poskytuje přehled pro pracovní prostor významných událostí zabezpečení a hrozeb, mapa analýzy hrozeb a posouzení identity a přístupu k událostem zabezpečení uloženým v pracovním prostoru. Odstranili jsme palubní desku. Jak už jsme doporučili v uzdu řídicího panelu, doporučujeme přejít na Azure Security Center.

![Řídicí panel zabezpečení Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Řídicí panel zabezpečení a auditu – nové prostředí

Doporučujeme přepnout na Azure Security Center. Poskytuje stejný přehled zabezpečení napříč více předplatnými a pracovními prostory, které jsou s nimi spojeny, a také bohatší sadu funkcí.

Můžete získat původní log Analytics dotazy, které naplní zabezpečení a audit řídicí panel v [úložišti GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) pro Security Center.

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Další informace o [Azure Sentinelu](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
