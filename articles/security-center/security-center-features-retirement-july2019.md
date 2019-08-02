---
title: Vyřazení funkcí Security Center (červenec 2019) | Microsoft Docs
description: Tento článek popisuje funkce v Security Center, které budou vyřazení od 31. července 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 8e7456eff3062ef6667e7b0022ea9740c397a493
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679247"
---
> [!NOTE]
> Tento dokument obsahuje podrobný seznam funkcí, které byly vyřazeny z Azure Security Center 31. července 2019.
>
>


# <a name="retirement-of-security-center-features-july-2019"></a>Vyřazení funkcí Security Center (červenec 2019)

Provedli jsme několik [vylepšení](https://azure.microsoft.com/updates/?product=security-center) Azure Security Center za posledních šest měsíců.
S těmito vylepšenými možnostmi odebíráme některé redundantní funkce a související rozhraní API z Security Center 31. července 2019.  

Většina těchto funkcí pro vyřazení z provozu se dá nahradit novými funkcemi v Azure Security Center nebo Azure Log Analytics. Další funkce se dají implementovat pomocí [Azure Sentinel (Preview)](https://azure.microsoft.com/services/azure-sentinel/).

Security Center funkce k vyřazení zahrnují:

- [Řídicí panel události](#menu_events)
- [Položka nabídky Hledat](#menu_search)
- [Zobrazit přístup k klasické identitě & přístupu pro identitu a přístup (Preview)](#menu_classicidentity)
- [Tlačítko Mapa událostí zabezpečení na mapě výstrah zabezpečení (Preview)](#menu_securityeventsmap)
- [Vlastní pravidla výstrah (Preview)](#menu_customalerts)
- [Tlačítko prozkoumat v upozorněních zabezpečení ochrany před hrozbami](#menu_investigate)
- [Podmnožina řešení zabezpečení](#menu_solutions)
- [Úprava konfigurací zabezpečení pro zásady zabezpečení](#menu_securityconfigurations)
- [Řídicí panel zabezpečení a audit (původně použitý na portálu OMS) pro Log Analytics pracovní prostory](#menu_securityomsdashboard)

Tento článek obsahuje podrobné informace o jednotlivých vyřazených funkcích a krocích, které můžete provést při implementaci náhradních funkcí.

## Řídicí panel události<a name="menu_events"></a>

Security Center používá Microsoft Monitoring Agent ke shromažďování různých konfigurací a událostí souvisejících se zabezpečením z vašich počítačů. Tyto události se uloží do vašich pracovních prostorů. [Řídicí panel události](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umožňuje zobrazit tato data a poskytuje vstupní bod pro Log Analytics.

Vyřadíme řídicí panel události, který se zobrazí po výběru pracovního prostoru:

![Řídicí panel Události][2]

### <a name="events-dashboard---the-new-experience"></a>Řídicí panel události – nové prostředí

Doporučujeme, abyste k zobrazení důležitých událostí v pracovních prostorech používali nativní možnosti Azure Log Analytics.

Pokud jste v Security Center vytvořili vlastní významné události, budou k dispozici. V Log Analytics klikněte na **vybrat pracovní prostor** > **uložená hledání**. Vaše data se neztratí ani nezmění. Nativní významné události jsou k dispozici také ze stejné obrazovky Log Analytics.

![Hledání uložených pracovních prostorů][3]

## Položka nabídky Hledat<a name="menu_search"></a>

Azure Security Center aktuálně používá hledání Azure Monitor protokolů k načtení a analýze dat zabezpečení. Tato obrazovka slouží jako okno Log Analytics vyhledávání stránky a umožňuje uživatelům spouštět vyhledávací dotazy ve zvoleném pracovním prostoru. Další informace najdete v tématu [Azure Security Center Search](https://docs.microsoft.com/azure/security-center/security-center-search). Vyřazení tohoto okna hledání:

![Stránka hledání][4]

### <a name="search-menu-entry---the-new-experience"></a>Položka nabídky Hledat – nové prostředí

Doporučujeme, abyste k provádění vyhledávacích dotazů ve vašich pracovních prostorech použili nativní možnosti Azure Log Analytics. Přejít na Azure Log Analytics a vyberte **protokoly**.

![Stránka protokolů Log Analytics][5]

## Přístup k klasické identitě & (Preview)<a name="menu_classicidentity"></a>

Prostředí pro přístup k klasické identitě & v Security Center aktuálně zobrazuje řídicí panel identit a informací o přístupu v Log Analytics. Postup zobrazení tohoto řídicího panelu:

1. Vyberte **Zobrazit klasickou identitu & přístup**.

   ![Stránka identity][6]

1. Zobrazte **řídicí panel identita & přístupu**.

    ![Stránka identity – výběr pracovního prostoru][7]

1. Vyberte pracovní prostor, abyste otevřeli řídicí panel **identity & Access** v Log Analytics zobrazit identitu a přístup k informacím v pracovním prostoru.

   ![Stránka identity – řídicí panel][8]

Vyvyřazujeme všechny tři obrazovky uvedené v předchozích krocích. Vaše data zůstanou v řešení zabezpečení Log Analytics k dispozici a neupravují ani neodstraňují.

### <a name="classic-identity--access-preview---the-new-experience"></a>Přístup k klasické identitě & (Preview) – nové prostředí

Řídicí panel Log Analytics zobrazuje přehledy v jednom pracovním prostoru. Nativní funkce Security Center ale poskytují přehled o všech předplatných a všech pracovních prostorech, které jsou k nim přidružené. Můžete získat přístup k snadno použitelnému zobrazení, které vám umožní soustředit se na to, co je důležité pro doporučení podle jejich bezpečného skóre.

Pro všechny funkce řídicího panelu **přístup k identitě &** v Log Analytics můžete v Security Center vybrat **identity & Access (Preview)** .

![Stránka identity – vyřazení klasických zkušeností][9]

## Mapování událostí zabezpečení<a name="menu_securityeventsmap"></a>

Security Center poskytuje [mapu výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) , které vám pomůžou identifikovat bezpečnostní hrozby. Tlačítko **Přejít na mapu událostí zabezpečení** v této mapě otevře řídicí panel, který umožňuje zobrazit nezpracované události zabezpečení ve vybraném pracovním prostoru.

Odebíráme tlačítko **Přejít na mapu událostí zabezpečení** a řídicí panel pro jednotlivé pracovní prostory.

![Mapování výstrah zabezpečení – tlačítko][10]

Když vyberete tlačítko **Přejít na mapu událostí zabezpečení** , otevřete řídicí panel Analýza hrozeb. Vyhráváme řídicí panel Analýza hrozeb.  

![Řídicí panel Analýza hrozeb][11]

Když zvolíte pracovní prostor pro zobrazení řídicího panelu Analýza hrozeb, otevřete na Log Analytics obrazovku Mapa výstrah zabezpečení (Preview). Tuto obrazovku vyřazením z provozu dostaneme.

![Mapování výstrah zabezpečení v Log Analytics][12]

Stávající data zůstanou v řešení zabezpečení Log Analytics k dispozici a nebudou se upravovat ani odebírat.

### <a name="security-events-map---the-new-experience"></a>Mapování událostí zabezpečení – nové prostředí

Doporučujeme používat funkce mapy výstrah, které jsou integrované v Security Center: **Mapa výstrah zabezpečení (Preview)** . Tato funkce poskytuje optimalizované prostředí a funguje ve všech předplatných a přidružených pracovních prostorech. Poskytuje zobrazení na nejvyšší úrovni v celém prostředí a není zaměřené na jeden pracovní prostor.

## Vlastní pravidla výstrah (Preview)<a name="menu_customalerts"></a>

Vaše [vlastní upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) vyrovnáváme od 30. června 2019, protože vyřazování z jeho základní infrastruktury vychází z provozu. Do té doby můžete upravovat existující vlastní pravidla upozornění, ale nebudete moct přidávat nové. Po datu vyřazení se jakékoli vlastní definované výstrahy neprojeví a výstrahy zabezpečení založené na těchto pravidlech nebudou vygenerovány.
Doporučujeme povolit [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) a znovu vytvořit vlastní výstrahy. Alternativně můžete vytvořit upozornění pomocí Azure Monitor výstrahy protokolu.

Pokud chcete zachovat stávající výstrahy a vytvořit je pomocí Azure Sentinel:

1. [Otevřete Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) a vyberte pracovní prostor, ve kterém se vlastní výstrahy ukládají.
1. Výběr **analýzy** z nabídky
1. Podle pokynů v následujícím [kurzu](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) se naučíte vytvářet vlastní výstrahy v Azure Sentinel.

Pokud vás nejímá používání služby Azure Sentinel, můžete výstrahy vytvořit pomocí Azure Monitor výstrahy protokolu. Pokyny najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) a [výstrah protokolu v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Vlastní upozornění][13]

Další informace o vlastním vyřazení výstrah najdete v tématu [vlastní pravidla výstrah v Azure Security Center (Preview)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Šetření výstrah zabezpečení<a name="menu_investigate"></a>

[Funkce šetření](https://docs.microsoft.com/azure/security-center/security-center-investigation) v Security Center pomáhá určit potenciální bezpečnostní incident. Tato funkce vám umožní pochopit rozsah incidentu a sledovat jeho hlavní příčinu. Tuto funkci odebíráme z Security Center, protože byla nahrazena vylepšeným prostředím v rámci služby [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Incident zabezpečení][14]

Když vyberete tlačítko **prozkoumat** na obrazovce bezpečnostního **incidentu** , otevřete řídicí panel šetření (Preview) v Log Analytics. Vyřazování řídicího panelu šetření.  

Stávající data zůstanou v řešení zabezpečení Log Analytics k dispozici a nebudou se upravovat ani odebírat.

![Řídicí panel šetření v Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Šetření – nové prostředí

Doporučujeme, abyste se převedli na službu [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) pro bohatou vyšetřovací činnost. Azure Sentinel poskytuje výkonné nástroje pro vyhledávání a dotazování pro zajištění bezpečnostních hrozeb v rámci zdrojů dat vaší organizace.  

## Podmnožina řešení zabezpečení<a name="menu_solutions"></a>

Security Center můžou [v Azure povolit integrovaná řešení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Z Security Center vyřazování z následujících partnerských řešení vyhráváme. Tato řešení jsou povolená ve [službě Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) spolu s řadou dalších zdrojů dat.

- [Firewall nové generace a řešení Firewall webových aplikací](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrace řešení zabezpečení, která podporují formát Common Event Format (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Po vyřazení nebudete moct přidávat ani upravovat žádné typy řešení uvedené v předchozím seznamu, a to buď z uživatelského rozhraní, nebo z rozhraní API.

Pokud máte stávající připojená řešení, doporučujeme přejít na službu Azure Sentinel.

![Řešení Security Center][16]

## Úprava konfigurací zabezpečení pro zásady zabezpečení<a name="menu_securityconfigurations"></a>

Azure Security Center sleduje konfigurace zabezpečení použitím sady [více než 150 doporučených pravidel](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). pro posílení zabezpečení operačního systému. Tato pravidla se týkají bran firewall, auditování, zásad hesel a dalších. Pokud se na počítači zjistí zranitelná konfigurace, Security Center vygeneruje doporučení k zabezpečení. [Obrazovka upravit konfiguraci zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) umožňuje zákazníkům přizpůsobit výchozí konfiguraci zabezpečení operačního systému v Security Center.

Vyřazení této funkce ve verzi Preview. Pokud po datu vyřazení byste chtěli obnovit konfigurace zabezpečení zpátky na jejich výchozí hodnoty, můžete to udělat přes rozhraní API nebo PowerShell pomocí [následujících pokynů](https://aka.ms/ascresetsecurityconfigurations) .

![Upravit konfigurace zabezpečení][17]

### <a name="edit-security-configurations---the-new-experience"></a>Upravit konfigurace zabezpečení – nové prostředí

Chystáme se povolit Security Center podporu pro [agenta konfigurace hosta](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Tato aktualizace umožní mnohem rozsáhlejší sadu funkcí, včetně podpory pro více operačních systémů a integraci zásad Azure v rámci hosta pro konfigurace hostů. Až budou tyto změny povolené, budete mít také možnost řídit konfigurace ve velkém měřítku a automaticky je použít na nové prostředky.

## Řídicí panel zabezpečení a audit pro Log Analytics pracovní prostory<a name="menu_securityomsdashboard"></a>

Řídicí panel zabezpečení a audit byl původně použit na portálu OMS. V Log Analytics řídicí panel poskytuje přehled o významných událostech zabezpečení a hrozbách, mapě analýzy hrozeb a vyhodnocení událostí zabezpečení uložených v pracovním prostoru. Odebíráme řídicí panel. Jak už jsme to doporučili v uživatelském rozhraní řídicího panelu, doporučujeme, abyste přešli na Azure Security Center.

![Řídicí panel zabezpečení Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Řídicí panel zabezpečení a audit – nové prostředí

Doporučuje se přepnout na Azure Security Center. Poskytuje stejný Přehled zabezpečení napříč několika předplatnými a pracovními prostory, které jsou k nim přidruženy, a širší sadou funkcí.

Můžete získat původní Log Analytics dotazy, které naplní řídicí panel zabezpečení a audit v [úložišti GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) pro Security Center.

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Přečtěte si další informace o [Sentinel Azure](https://docs.microsoft.com/azure/sentinel).

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
