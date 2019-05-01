---
title: Vyřazení služby Security Center nabízí (červenec 2019) | Dokumentace Microsoftu
description: Tento článek podrobně popisuje funkce ve službě Security Center, který skončí 31. července 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 392782310d8bc3b38a3dd1349cb1760ca287acd1
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875588"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Vyřazení služby Security Center nabízí (2019 dne)

Provedli jsme několik [vylepšení](https://azure.microsoft.com/updates/?product=security-center) ke službě Azure Security Center za posledních 6 měsíců.  
Vylepšené možnosti odebíráme počet redundantní funkce, jakož i související rozhraní API, ze služby Security Center na dne 31. května 2019.  

Většina vyřazeno funkcí se dá nahradit výrazem nové funkce v Azure Security Center nebo Log Analytics; a několik funkcí je možné implementovat pomocí [Sentinelu Azure (preview)](https://azure.microsoft.com/services/azure-sentinel/), nedávno zveřejněné.

Seznam funkcí vyřazuje ze služby Security Center obsahuje:

- [Řídicí panel události](#menu_events)
- [Položka nabídky vyhledávání](#menu_search)
- [Zobrazení classic identita a přístup odkaz na identitu a přístup (preview)](#menu_classicidentity)
- [Události zabezpečení mapování tlačítka na mapě výstrahy zabezpečení (preview)](#menu_securityeventsmap)
- [Vlastní pravidla výstrah (preview)](#menu_customalerts)
- [Prozkoumat tlačítko ve výstrahách zabezpečení ochrany před internetovými útoky](#menu_investigate)
- [Podmnožinu řešení zabezpečení](#menu_solutions)
- [Upravit konfigurace zabezpečení pro zásady zabezpečení](#menu_securityconfigurations)
- [Zabezpečení a audit řídicího panelu (původně byl použit v portálu OMS) pro pracovní prostory Log Analytics.](#menu_securityomsdashboard)

Dál poskytuje podrobné informace pro jednotlivé vyřazené funkce a kroky můžete provést pomocí funkcí pro nahrazení.

## Řídicí panel události<a name="menu_events"></a>
Security Center používá agenta Microsoft Monitoring Agent shromažďovat různé zabezpečení související s konfigurací a události z vašeho počítače a uloží tyto události v pracovních prostorech. [Řídicí panel události](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umožňuje zobrazení těchto dat a v podstatě představuje jinou vstupní bod do Log Analytics.

Od této chvíle řídicí panel události se vyřadí z provozu:

![Události na obrazovce pro výběr pracovního prostoru][1]

Řídicí panel události, které se zobrazí, jakmile uživatel klikne v pracovním prostoru, budou také vyřadí z provozu:

![Řídicí panel Události][2]

### <a name="events-dashboard---new-experience"></a>Řídicí panel události – nové prostředí

Nepodnikovým zákazníkům využít možnosti nativní Log Analytics Chcete-li zobrazit významné události na svých pracovních prostorech.
Pokud jste již vytvořili vlastní významné události ze služby Security Center, budou přístupné přes protokol analýzy -> vyberte pracovní prostor -> uložená hledání. Vaše data nebudou ztráty ani upravit. Nativní významné události jsou také k dispozici na stejné obrazovce.

![Pracovní prostor uložená hledání][3]

## Položka nabídky vyhledávání<a name="menu_search"></a>
Azure Security Center aktuálně používá službu search protokoly Azure monitoru načíst a analyzovat data zabezpečení. Tato obrazovka v podstatě slouží jako adaptační vrstva ke službě Log Analytics "[hledání](https://docs.microsoft.com/azure/security-center/security-center-search)" stránku – umožňuje uživatelům umožňuje spustit vyhledávací dotazy v jejich vybraném pracovním prostoru. Od této chvíle toto okno hledání se vyřadí z provozu:

![Stránka hledání][4]

### <a name="search-menu-entry---new-experience"></a>Položka nabídky Search – nové prostředí

Nepodnikovým zákazníkům používat **Log Analytics** nativní možnosti provádět dotazy prohledávání na své pracovní prostory. Uděláte to tak, že přejděte ke službě Log analytics v Azure a vyberte: "Protokoly":

![Log Analytics zaznamená stránky][5]

## Klasické identita a přístup (Preview)<a name="menu_classicidentity"></a>
Prostředí "Klasickém" identita a přístup ve službě Security Center má k dispozici způsob, jak zákazníci mohli zobrazit svou identitu a přístup související informace ve službě log analytics. To se provádí po kliknutí dole:

Klikněte na "Klasické zobrazení identita a přístup"

![Stránka identity][6]

Spolu s obrazovkou, tato stránka by se otevřely "řídicí panel identita a přístup":

![Stránka identity – výběr pracovního prostoru][7]

Klikněte na pracovním prostoru otevře "Identit a přístupu" log analytics řídicí panel ve kterém může zákazníkům zobrazení identity a přístup k informacím v jejich pracovním prostoru:

![Stránka identity – řídicí panel][8]

Od této chvíle všechny tři obrazovky výše se vyřadí z provozu. Vaše data zůstanou k dispozici v řešení zabezpečení log analytics a nebude změnit ani je odebrat.

### <a name="classic-identity--access-preview---new-experience"></a>Klasickou identitu a přístup (Preview) – nové prostředí
Zatímco řídicí panel Log analytics poskytuje přehledy v daném pracovním prostoru pouze, nativní funkce Security Center poskytuje přehled o všech předplatných a všechny pracovní prostory, které jsou spojené s nimi, v přehledném – použití zobrazení, která vám umožní zaměřit se na co společnosti důležité podle zabezpečené skóre recommendation(s) vaše identita a přístup.
Všechny funkce identity a přístupu k Log analytics řídicího panelu lze dosáhnout tak, že vyberete "Identita a přístup (Preview)" v Security Center:

![Stránka identity – klasické dojít k vyřazení z provozu][9]

## Mapy událostí zabezpečení<a name="menu_securityeventsmap"></a>
Security Center vám nabízí [mapy](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) , který pomáhá identifikovat bezpečnostní hrozby pro prostředí. Vede na tlačítko 'Přejít na mapě událostí zabezpečení' v, která je namapována na řídicí panel, který umožňuje zobrazit nezpracované bezpečnostních událostí ve vybraném pracovním prostoru.
Tlačítko, spolu s řídicím panelu na pracovní prostor se odebere po vyřazení.

![Výstrahy zabezpečení mapy – tlačítko][10]

Dnes po kliknutí na "Přejít na mapě událostí zabezpečení" se otevře řídicí panel analýza hrozeb. Řídicí panel analýza hrozeb se vyřadí z provozu.  

![Řídicí panel Analýza hrozeb][11]

Když vyberete pracovní prostor, chcete-li zobrazit jeho řídicí panel analýza hrozeb, výstrahy zabezpečení map(Preview) obrazovky *v Log Analytics* je otevřen. Tato obrazovka se vyřadí z provozu.

![Mapování výstrah zabezpečení ve službě Log Analytics][12]

Existující data zůstanou k dispozici v řešení zabezpečení log analytics a nebude být změnit ani odebrat.

### <a name="security-events-map---new-experience"></a>Mapy událostí zabezpečení – nové prostředí
Doporučujeme, aby naše zákazníky, pomocí funkce mapy výstrahy, které jsou integrované do Security Center – "mapování výstrah zabezpečení (Preview)". To poskytuje optimalizované a funguje ve všech předplatných a přidružených pracovních prostorů, povolení makra zobrazení napříč vaším prostředím a ne zaměřit na jeden pracovní prostor.

## Vlastní pravidla výstrah (Preview)<a name="menu_customalerts"></a>
Vlastní upozornění prostředí bude [vyřazeno](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30. června 2019 kvůli vyřazení základní infrastruktury orchard je založen na. V časovém rámci až do vyřazení uživatelé budou moci upravit existující vlastní pravidla výstrah ale nebudete moct přidat nové. Uživatelé by měli povolit [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/) s připojováním jedním kliknutím automaticky migrovat své existující výstrahy a vytvářet nové nebo případně znovu vytvořit jejich výstrahy se službou Azure Monitor upozornění protokolů.

Chcete-li zachovat existující výstrahy a migrací do Azure Sentinelu, Sentinelu Azure, spusťte. Jako první krok vyberte pracovní prostor, kde jsou uložené vaše vlastní výstrahy a poté vyberte položku nabídky "Analytics" automaticky migrovat vaše upozornění.

![Vlastní výstrahy][13]

Nezajímá vás připojení ke službě Azure Sentinelu nepodnikovým zákazníkům znovu vytvořit s využitím upozornění log Azure Monitor výstrah. Pokyny najdete v tématu: [vytvoření, zobrazení a Správa upozornění protokolů pomocí Azure monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Návod, jak vytvořit upozornění protokolů najdete v tématu: [Upozornění protokolů ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Další informace o vyřazení z provozu vlastní výstrahy, najdete v tématu [výstrahy dokumentace ke službě Security Center vlastní](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Šetření výstrahy zabezpečení<a name="menu_investigate"></a>
[Funkce šetření](https://docs.microsoft.com/azure/security-center/security-center-investigation) ve službě Security Center umožňuje třídit, pochopit tak rozsah a vysledovat původní příčinu možných bezpečnostních incidentů. Tato funkce se odebere ze služby Security Center jako bylo nahrazeno tématem vylepšení v [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/).

![Incident zabezpečení][14]

Po kliknutí na tlačítko "Prošetření" výše se otevře "řídicí panel šetření (Preview)" v Log Analytics. Řídicí panel šetření se vyřadí z provozu.  
Existující data zůstanou k dispozici v Log Analytics řešení zabezpečení a nebude být změnit ani odebrat.

![Řídicí panel šetření ve službě Log Analytics][15]

### <a name="investigation---new-experience"></a>Šetření – nové prostředí

Doporučujeme, aby naše zákazníky na připojení [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/) šetření bohaté prostředí, používá technologii výstrahy myslivost funkce. Azure Sentinel poskytuje výkonné typu vyhledávání a dotaz nástroje, které hunt bezpečnostních hrozeb napříč zdroji dat vaší organizace.  

## Podmnožina řešení zabezpečení<a name="menu_solutions"></a>

Má služba Security Center umožňuje uskutečnit [integrovaných řešení zabezpečení v Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Následující partnerská řešení by odebrat a v podporované [Sentinelu Azure](https://azure.microsoft.com/services/azure-sentinel/), spolu s ještě více zdrojů.

- Firewall nové generace a Web application Firewall řešení (Sentinelu Azure [dokumentaci](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- Integrace řešení zabezpečení, které podporují společný formát událostí (CEF) (Sentinelu Azure [dokumentaci](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure Sentinel [dokumentaci](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure Sentinel [dokumentaci](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

Po vyřazení uživatelé nebudou moct přidat nové nebo upravovat stávající propojená řešení z typů uvedených výše, z uživatelského rozhraní a rozhraní API.
Stávající propojená řešení můžou pro přesun do Azure Sentinelu na konci tohoto období.

![Security Center řešení][16]

## Upravit konfigurace zabezpečení pro zásady zabezpečení<a name="menu_securityconfigurations"></a>
Azure Security Center monitoruje konfigurace zabezpečení s použitím sady [víc než 150 doporučená pravidla](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pro posílení zabezpečení operačního systému, včetně pravidel souvisejících s branami firewall, auditování, zásady pro hesla a další. Pokud na počítači se zjistilo, že zranitelné konfigurace jim, Security Center vygeneruje doporučení k zabezpečení. [Konfigurační obrazovce pro úpravy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) umožňuje zákazníkům přizpůsobit výchozí konfigurace zabezpečení operačního systému ve službě Security Center.

Tato funkce byla ve verzi preview a skončí.

![Upravit konfigurace zabezpečení][17]

### <a name="edit-security-configurations---new-experience"></a>Upravit konfigurace zabezpečení – nové prostředí

Security Center bude podporovat [agenta v hostovi](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) v blízké budoucnosti umožňuje mnohem širší nabídku sad funkcí – včetně podpory pro další operační systémy a integraci s konfigurací typu Host pomocí zásad Azure (v hostovi zásady). To se také poskytují možnost řídit ve velkém měřítku a použít na nové prostředky automaticky.

## Zabezpečení a audit řídicího panelu (původně byl použit v portálu OMS) pro pracovní prostory Log Analytics<a name="menu_securityomsdashboard"></a>

Řídicí panel zabezpečení ve službě Log analytics poskytuje za pracovní prostor Přehled událostí významné zabezpečení a hrozby, mapa analýzy hrozeb a identita a přístup k vyhodnocování událostí zabezpečení, které jsou uložené v pracovním prostoru. Řídicí panel se odeberou do budoucna. Jak už je vhodné na řídicím panelu uživatelského rozhraní, naši uživatelé doporučujeme používat Azure Security Center do budoucna.

![Řídicí panel security log analytics][18]

### <a name="security--audit-dashboard---new-experience"></a>Zabezpečení a audit řídicí panel – nové prostředí
Našim zákazníkům doporučujeme používat Azure Security Center, která poskytuje stejné zabezpečení přehled napříč více předplatná a pracovní prostory, které jsou spojené s nimi, spolu s širší nabídku sad funkcí.

## <a name="next-steps"></a>Další postup
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- Další informace o [Azure Sentinelu](https://docs.microsoft.com/azure/sentinel)

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
