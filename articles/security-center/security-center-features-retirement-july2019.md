---
title: Vyřazení služby Security Center nabízí (červenec 2019) | Dokumentace Microsoftu
description: Tento článek popisuje funkce ve službě Security Center, které se vyřadí dne 31. května 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 069345f9c2d0fff0b580365153d8be13bb4ba204
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65952134"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Vyřazení služby Security Center nabízí (2019 dne)

Provedli jsme několik [vylepšení](https://azure.microsoft.com/updates/?product=security-center) ke službě Azure Security Center za posledních šest měsíců.
Tyto vylepšené možnosti jsme se odebírá některé redundantní funkce a související rozhraní API ze služby Security Center na dne 31. května 2019.  

Většina těchto funkcí retiring lze nahradit s novými funkcemi v Azure Security Center a Azure Log Analytics. Další funkce, je možné implementovat pomocí [Sentinelu Azure (preview)](https://azure.microsoft.com/services/azure-sentinel/).

Funkce Security Center k vyřadí z provozu:

- [Řídicí panel události](#menu_events)
- [Položka nabídky vyhledávání](#menu_search)
- [Zobrazení classic identita a přístup odkaz na identitu a přístup (preview)](#menu_classicidentity)
- [Události zabezpečení mapování tlačítka na mapě výstrahy zabezpečení (preview)](#menu_securityeventsmap)
- [Vlastní pravidla výstrah (preview)](#menu_customalerts)
- [Prozkoumat tlačítko ve výstrahách zabezpečení ochrany před internetovými útoky](#menu_investigate)
- [Podmnožinu řešení zabezpečení](#menu_solutions)
- [Upravit konfigurace zabezpečení pro zásady zabezpečení](#menu_securityconfigurations)
- [Zabezpečení a audit v řídicím panelu (původně byl použit v portálu OMS) pro pracovní prostory Log Analytics](#menu_securityomsdashboard)

Tento článek obsahuje podrobné informace o jednotlivých vyřazené funkce a kroky, které můžete provést k implementaci funkcí nahrazení.

## Řídicí panel události<a name="menu_events"></a>

Security Center používá agenta Microsoft Monitoring Agent shromažďovat různé konfigurace týkající se zabezpečení a událostí z vašich počítačů. Ukládají se tyto události ve vašich pracovních prostorů. [Řídicí panel události](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umožňuje zobrazit tato data a poskytuje vstupní bod do Log Analytics.

Jsme se vyřazování z provozu řídicí panel události, které se zobrazí po výběru pracovního prostoru:

![Řídicí panel události][2]

### <a name="events-dashboard---the-new-experience"></a>Řídicí panel události – nové prostředí

Budeme podporovat zobrazíte významné události ve vašich pracovních prostorů pomocí nativních možností Azure Log Analytics.

Pokud vytvoříte vlastní významné události ve službě Security Center budou dostupné. V Log Analytics, přejděte na **vyberte pracovní prostor** > **uložená hledání**. Vaše data nebudou ztracena ani upravit. Nativní významné události jsou také k dispozici na stejné obrazovce ve službě Log Analytics.

![Pracovní prostor uložená hledání][3]

## Položka nabídky vyhledávání<a name="menu_search"></a>

Azure Security Center aktuálně používá službu search protokoly Azure monitoru načíst a analyzovat data zabezpečení. Tato obrazovka slouží jako okno stránka hledání Log Analytics a umožňuje uživatelům umožňuje spustit vyhledávací dotazy v jejich vybraném pracovním prostoru. Další informace najdete v tématu [vyhledávání Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-search). Jsme se vyřazování z provozu toto okno hledání:

![Stránka hledání][4]

### <a name="search-menu-entry---the-new-experience"></a>Položka nabídky Search – nové prostředí

Doporučujeme vám využít možnosti nativní Azure Log Analytics k provedení vyhledávacích dotazů na pracovní prostory. Přejděte na Azure Log Analytics a vyberte **protokoly**.

![Log Analytics zaznamená stránky][5]

## Klasické identita a přístup (Preview)<a name="menu_classicidentity"></a>

Prostředí Classic identita a přístup ve službě Security Center aktuálně znázorňuje řídicí panel identita a přístup informace ve službě Log Analytics. Chcete-li zobrazit tento řídicí panel:

1. Vyberte **Zobrazit klasickou identitu a přístup**.

   ![Stránka identity][6]

1. Zobrazení **řídicí panel identita a přístup**.

    ![Stránka identity – výběr pracovního prostoru][7]

1. Vyberte pracovní prostor otevřít **identita a přístup** řídicího panelu ve službě Log Analytics k zobrazení identity a přístup k informacím v pracovním prostoru.

   ![Stránka identity – řídicí panel][8]

Jsme se vyřazování z provozu všechny tři obrazovky je znázorněno v předchozích krocích. Vaše data zůstanou k dispozici v Log Analytics řešení zabezpečení a nebude upravovat nebo je odebrat.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klasickou identitu a přístup (Preview) – nové prostředí

Log Analytics řídicího panelu ukazuje insights na jeden pracovní prostor. Nativní funkce služby Security Center však poskytují přehled o všech předplatných a všechny pracovní prostory k nim má přiřazené. Můžete přistupovat snadno – použití zobrazení, která vám umožní zaměřit se na to, co je důležité s doporučeními seřazené podle jejich zabezpečené skóre.

Všechny funkce **identita a přístup** řídicího panelu ve službě Log Analytics můžete dosáhnout tak, že vyberete **identita a přístup (Preview)** v Security Center.

![Stránka identity – klasické dojít k vyřazení z provozu][9]

## Mapy událostí zabezpečení<a name="menu_securityeventsmap"></a>

Security Center vám nabízí [mapy výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) vám pomůže identifikovat bezpečnostní hrozby. **Přejít do mapy událostí zabezpečení** tlačítka, která je namapována otevře řídicí panel, který vám umožní zobrazit nezpracované bezpečnostních událostí ve vybraném pracovním prostoru.

Můžeme se odebírá **přejít do mapy událostí zabezpečení** tlačítko a řídicí panel za pracovní prostor.

![Výstrahy zabezpečení mapy – tlačítko][10]

Když vyberete **přejít do mapy událostí zabezpečení** tlačítko, otevřete řídicí panel analýza hrozeb. Jsme se vyřazování z provozu řídicí panel analýza hrozeb.  

![Řídicí panel Analýza hrozeb][11]

Když vyberete pracovní prostor, chcete-li zobrazit jeho řídicí panel analýza hrozeb, otevřete dialogovém okně Mapa (Náhled) výstrahy zabezpečení ve službě Log Analytics. Jsme se vyřazování z provozu tuto obrazovku.

![Mapování výstrah zabezpečení ve službě Log Analytics][12]

Existující data zůstanou k dispozici v Log Analytics řešení zabezpečení a nebude být změnit ani odebrat.

### <a name="security-events-map---the-new-experience"></a>Mapy událostí zabezpečení – nové prostředí

Doporučujeme vám používat funkce mapy výstrahy, které jsou integrované do Security Center: **Mapa upozornění zabezpečení (Preview)** . Tato funkce poskytuje optimalizované a funguje ve všech předplatných a přidružených pracovních prostorů. Poskytuje podrobný přehled napříč vaším prostředím a se zaměřuje na jeden pracovní prostor.

## Vlastní pravidla výstrah (Preview)<a name="menu_customalerts"></a>

Máme [vyřazení z provozu vlastní výstrahy prostředí](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na 30. června 2019 protože vyřazení její podpůrnou infrastrukturou. Dokud to neuděláte můžete upravit existující vlastní pravidla upozornění, ale nebudete moct přidat nové. Doporučujeme, abyste povolili [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/) automaticky migrovat existující výstrahy a vytvořit nové. Upozornění můžete případně vytvořit s využitím upozornění log Azure Monitor.

Zachovat existující výstrahy a migrací do Azure Sentinelu:

1. Otevřete Azure Sentinelu a vyberte pracovní prostor, kde jsou uložené vaše vlastní upozornění.
1. Vyberte **Analytics** z nabídky automaticky migrovat vaše upozornění.

![Vlastní upozornění][13]

Pokud si nejste zájem o přechod na Azure Sentinelu, doporučujeme vám vytvořit upozornění s využitím upozornění log Azure Monitor. Pokyny najdete v tématu [vytvoření, zobrazení a Správa upozornění protokolů pomocí Azure monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) a [upozornění protokolů ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Další informace o vyřazení z provozu vlastní výstrahy, najdete v části [vlastní pravidla výstrah ve službě Azure Security Center (Preview)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Šetření výstrahy zabezpečení<a name="menu_investigate"></a>

[Funkce šetření](https://docs.microsoft.com/azure/security-center/security-center-investigation) v Security Center pomáhá posuzovat potenciální bezpečnostní incident. Tato funkce umožňuje pochopit tak rozsah incidentu a sledovat jejich hlavní příčiny. Můžeme se vzhledem k tomu, že je byla nahrazena vylepšení v odstranění této funkce ze služby Security Center [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/).

![Incident zabezpečení][14]

Když vyberete **prošetření** tlačítko **bezpečnostní incident** obrazovky, otevřete řídicí panel šetření (Preview) ve službě Log Analytics. Jsme se vyřazování z provozu řídicí panel šetření.  

Existující data zůstanou k dispozici v Log Analytics řešení zabezpečení a nebude být změnit ani odebrat.

![Řídicí panel šetření ve službě Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Šetření – nové prostředí

Doporučujeme vám přejít na [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/) zajišťuje bohaté šetření. Azure Sentinel poskytuje výkonné nástroje pro hledání a dotaz na hunt bezpečnostních hrozeb napříč zdroji dat vaší organizace.  

## Podmnožina řešení zabezpečení<a name="menu_solutions"></a>

Security Center můžete povolit [integrovaných řešení zabezpečení v Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Jsme se vyřazování následující partnerská řešení ze služby Security Center. Tato řešení jsou povolené v [Azure Sentinelu](https://azure.microsoft.com/services/azure-sentinel/) spolu s celou řadou dalších zdrojů.

- [Další generace brány firewall a webových aplikací brány firewall řešení](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrace řešení zabezpečení, které podporují formát cef (Common Event Format)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Po vyřazení nebudete moci přidat nebo změnit libovolné typy řešení, které jsou uvedené v předchozím seznamu, buď z uživatelského rozhraní nebo rozhraní API.

Pokud máte existující propojená řešení, doporučujeme vám přejít na Azure Sentinelu.

![Security Center řešení][16]

## Upravit konfigurace zabezpečení pro zásady zabezpečení<a name="menu_securityconfigurations"></a>

Azure Security Center monitoruje konfigurace zabezpečení s použitím sady [víc než 150 doporučená pravidla](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). pro posílení zabezpečení operačního systému. Tato pravidla se týkají brány firewall, auditování, zásady pro hesla a další. Pokud se na počítači zjistí zranitelná konfigurace, Security Center vygeneruje doporučení k zabezpečení. [Konfigurační obrazovce pro úpravy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) umožňuje zákazníkům přizpůsobit výchozí konfigurace zabezpečení operačního systému ve službě Security Center.

Tuto funkci ve verzi preview jsme už po vyřazení. Pokud po datu vyřazení chcete resetovat konfiguraci zabezpečení zpět na výchozí hodnoty, můžete tak učinit pomocí rozhraní API nebo Powershellu pomocí [postupujte podle pokynů](https://aka.ms/ascresetsecurityconfigurations)

![Upravit konfigurace zabezpečení][17]

### <a name="edit-security-configurations---the-new-experience"></a>Upravit konfigurace zabezpečení – nové prostředí

Chceme povolit službu Security Center pro podporu [konfigurace agenta hosta](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Takové aktualizace vám umožní mnohem širší nabídku sad funkcí, včetně podpory pro další operační systémy a integrace Azure v rámci hostů zásad pro konfigurace hosta. Jakmile tyto změny jsou povolené, budete mít také možnost řídit konfigurací ve velkém měřítku a použít je u nových prostředků automaticky.

## Řídicí panel zabezpečení a audit pro pracovní prostory Log Analytics<a name="menu_securityomsdashboard"></a>

Řídicí panel zabezpečení a audit použil původně portálu OMS. Ve službě Log Analytics poskytuje řídicí panel za pracovní prostor Přehled události významné zabezpečení a hrozby, mapa analýzy hrozeb a identit a přístupu posouzení událostí zabezpečení, které jsou uložené v pracovním prostoru. Už jsme odebráním řídicího panelu. Jak už je vhodné na řídicím panelu uživatelského rozhraní, doporučujeme, abyste přechod ke službě Azure Security Center.

![Řídicí panel security log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Zabezpečení a audit pro řídicí panel – nové prostředí

Doporučujeme vám pro přepnutí do služby Azure Security Center. Poskytuje stejné zabezpečení přehled napříč několika předplatnými a k nim má přiřazené pracovní prostory a nastavit bohatší funkce.

Můžete získat původní dotazy Log Analytics, která naplní řídicí panel zabezpečení a audit v [úložiště GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) pro Security Center.

## <a name="next-steps"></a>Další postup

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
