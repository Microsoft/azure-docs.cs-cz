---
title: Co je Azure Sentinelu Preview? | Dokumentace Microsoftu
description: Přečtěte si o Azure Sentinelu, jejích klíčových funkcích a jak to funguje.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2c730c240ed5ff68a1d48ef17d15de1aaa73ab1e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619907"
---
# <a name="what-is-azure-sentinel-preview"></a>Co je Azure Sentinelu Preview?

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ověřovací aplikace Microsoft Azure je škálovatelná, nativních cloudových, **správu bezpečnostních informací událostí (SIEM)** a **automatizované Orchestrace zabezpečení odpovědi (SOAR)** řešení. Azure Sentinel poskytuje intelligent security intelligence analýz a analýz hrozeb napříč celým podnikem, poskytuje jednotné řešení pro výstrahy zjišťování, viditelnost před internetovými útoky, proaktivní typu a reakce na hrozby. 

Azure Sentinel je zobrazení ptačí perspektivy napříč celým podnikem boj zátěže stále důmyslnějšími útoky, zvýšit objem výstrah a časové rámce dlouho řešení.

- **Shromažďování dat v cloudovém měřítku** všech uživatelů, zařízení, aplikací a infrastruktury, v místním i v několika cloudy. 

- **Detekce hrozeb se předtím nepřišlo**a minimalizace falešně pozitivních výsledků pomocí analýzy a bezkonkurenční hrozeb od Microsoftu. 

- **Prošetření hrozeb s umělou inteligencí**a dost možná nejlepší na podezřelé aktivity, ve velkém měřítku, s výhodou využívejte let internetoví zabezpečení práce v Microsoftu. 

- **Reakce na incidenty rychle** s integrovanou Orchestrace a Automatizace běžných úkolů.


![Základní funkce pro Azure Sentinel](./media/overview/core-capabilities.png)

Staví na celou škálu stávající služby Azure, Azure Sentinelu nativně zahrnuje prověřené základy, jako je Log Analytics a Logic Apps. Azure Sentinel vylepšuje šetření a zjišťování s využitím AI a poskytuje datové proudy intelligence hrozeb od Microsoftu a umožňuje, abyste si přinesli vlastní analýza hrozeb. 

 
## <a name="connect-to-all-your-data"></a>Připojení k vašim datům

Chcete připojit Sentinelu Azure, budete nejdřív muset [připojení ke zdrojům zabezpečení](connect-data-sources.md). Azure Sentinel se dodává s celou řadou konektorů pro Microsoft solutions, k dispozici z pole a poskytuje v reálném čase, integrace, včetně řešení pro ochranu před internetovými útoky Microsoft a Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho jsou integrované konektory do širšího ekosystému zabezpečení pro řešení jiného subjektu než Microsoft. Můžete také použít běžný formát události Syslog nebo rozhraní REST API pro připojení zdroje dat pomocí Azure Sentinelu také.  

![Kolekce dat](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Řídicí panely

Po připojení zdroje dat můžete vybrat z Galerie [odborně vytvořit řídicí panely](quickstart-get-visibility.md#dashboards) ze zdrojů dat, která zařízení surface insights. Každý řídicí panel je plně přizpůsobitelná – můžete přidat vlastní logiku nebo upravit dotazy nebo řídicí panel můžete vytvořit úplně od začátku.

Řídicí panely poskytují interaktivní vizualizace pomocí pokročilé analýzy, které pomáhají vaše analytikům zabezpečení získat lépe pochopit, co se děje během útoku. Šetření nástroje umožňují vytvářet podrobné informace o jakékoli pole, všechna data, takže můžou rychle vytvářet kontextu před internetovými útoky. 

![Řídicí panely](./media/overview/dashboards.png)

## <a name="analytics"></a>Analýzy

Můžete omezit informační šum a minimalizovat počet výstrah, budete muset zkontrolovat a zjistit, Sentinelu Azure používá [analytics ke korelaci výstrahy do případů](tutorial-detect-threats.md). **Případy** jsou skupiny souvisejících výstrah, které vytvářejí společně možností reakce možné hrozbu, kterou můžete prozkoumat a vyřešit. Použít pravidla vestavěné korelace jako-je nebo je používat jako výchozí bod k vytvoření vlastních. Azure Sentinel také nabízí machine learning pravidla pro mapování chování vaší sítě a potom vyhledejte anomálie napříč vašimi prostředky. Tyto analýzy propojení významů, kombinací málo specifických výstrah o různé entity do potenciální přesné bezpečnostní incidenty v oblasti.

![Případy](./media/overview/cases.png)

## <a name="user-analytics"></a>Uživatelské analýzy

Díky nativní integraci rozhraní machine learning (ML) a [analýzy chování uživatelů](user-analytics.md), Sentinelu Azure může pomoct rychle detekovat hrozby. Azure Sentinel se hladce integrují s Azure Advanced Threat Protection k analýze chování uživatelů a stanovení priorit uživatelů, které byste měli prozkoumat nejdříve, na základě jejich výstrahy a vzory podezřelých aktivit v Azure Sentinelu a Microsoft 365.

![Uživatelské analýzy](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Zabezpečení automatizace a Orchestrace

Automatizaci běžných úkolů a [zjednodušit Orchestrace zabezpečení pomocí playbooků](tutorial-respond-threats-playbook.md) , která pomocí služby Azure a také svoje stávající nástroje pro integraci. Postaveno na základech služby Azure Logic Apps, Azure Sentinelu automatizace a Orchestrace řešení poskytuje vysoce rozšiřitelná architektura, která umožňuje škálovatelné automation jako nové technologie a tom hrozby. K vytvoření playbooky pomocí Azure Logic Apps, můžete si vybrat z rozrůstající se Galerie integrované playbooky. Patří mezi ně [více než 200 konektorů](https://docs.microsoft.com/azure/connectors/apis-list) pro služby, jako je služba Azure functions. Konektory umožňují aplikovat žádnou vlastní logiku v kódu, ServiceNow, Jira, Zendesk, HTTP požadavků, Microsoft Teams, Slack, ochrana ATP v programu Windows Defender a Cloud App Security.

Například pokud používáte systém lístků podpory ServiceNow, můžete použít nástroje poskytované pro pomocí Azure Logic Apps můžete automatizovat pracovní postupy a otevření lístku ve ServiceNow pokaždé, když je zjištěna určitá událost.

![Playbooky](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Šetření

Azure Sentinel [podrobné zkoumání](tutorial-investigate-cases.md) nástroje vám pomohou pochopit tak rozsah a najít původní příčinu možných ohrožení zabezpečení. Můžete zvolit entitu v grafu interaktivní zeptat zajímavé pro konkrétní entitu, nebo k podrobnostem dané entity a jeho připojení k původní příčinu možných ohrožení. 

![Šetření](./media/overview/investigation.png)


## <a name="hunting"></a>Vyhledávání

Použití Azure Sentinelu [myslivost výkonné nástroje hledání a dotazů](hunting.md)založená na rozhraní MITRE, které vám umožní aktivně dost možná nejlepší bezpečnostní hrozby ve vaší organizaci zdrojů dat, předtím, než se aktivuje upozornění. Po zjištění typu dotazu, který poskytuje cenné přehledy o možných útoků, můžete také vytvořit vlastní zjišťování pravidla založená na dotazu a zpracujte tyto poznatky jako oznámení na vaše respondéry incidentu zabezpečení. Při typu, můžete vytvořit záložky pro zajímavé události, která vám umožní vrátit se k nim později, je sdílet s ostatními a seskupit je pomocí jiných usnadňuje korelování událostem a vytvářet přesvědčivé případu pro zkoumání.

![Vyhledávání](./media/overview/hunting.png)

## <a name="community"></a>Komunita

Komunita Azure Sentinelu je výkonným prostředkem pro automatizaci a detekce hrozeb. Naše analytikům zabezpečení Microsoft neustále vytvořit a přidat nové řídicí panely, playbooky, typu dotazy a další, odesílání do komunity pro použití ve vašem prostředí. Ukázkový obsah si můžete stáhnout z Githubu soukromé komunity [úložiště](https://aka.ms/asicommunity) k vytvoření vlastních řídicích panelů, typu dotazů, poznámkové bloky a playbooky pro Sentinelu Azure. 

![Komunita](./media/overview/community.png)

## <a name="next-steps"></a>Další postup

- Abyste mohli začít s Azure Sentinelu, budete potřebovat předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Zjistěte, jak [připojit vaše data do Azure Sentinelu](quickstart-onboard.md), a [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
