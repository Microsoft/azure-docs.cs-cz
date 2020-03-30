---
title: Co je Azure Sentinel?| Dokumenty společnosti Microsoft
description: Přečtěte si o Azure Sentinelu, jeho klíčových možnostech a o tom, jak funguje.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 7d0f246a96072a6e433b0762256467f1c20a45fe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581714"
---
# <a name="what-is-azure-sentinel"></a>Co je Azure Sentinel?

Microsoft Azure Sentinel je škálovatelné řešení pro nativní pro cloud, **správu událostí zabezpečení (SIEM)** a **automatické odpovědi orchestrace zabezpečení (SOAR).** Azure Sentinel poskytuje inteligentní analýzy zabezpečení a analýzy hrozeb v celém podniku a poskytuje jediné řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby. 

Azure Sentinel je váš pohled z ptačí perspektivy napříč podnikem, který zmírňuje stres stále sofistikovanějších útoků, zvyšuje objemy výstrah a dlouhé časové rámce řešení.

- **Shromažďujte data v cloudovém měřítku** napříč všemi uživateli, zařízeními, aplikacemi a infrastrukturou, a to jak místně, tak ve více cloudech. 

- **Detekujte dříve nezjištěné hrozby**a minimalizujte falešně pozitivní výsledky pomocí analýzy společnosti Microsoft a bezkonkurenční analýzy hrozeb. 

- **Prozkoumejte hrozby pomocí umělé inteligence**a pátrejte po podezřelých aktivitách ve velkém měřítku a čerpete s mnohaletou prací v oblasti kybernetické bezpečnosti ve společnosti Microsoft. 

- **Rychle reagujte na incidenty** pomocí integrované orchestrace a automatizace běžných úloh.

![Základní možnosti Azure Sentinelu](./media/overview/core-capabilities.png)

Azure Sentinel nativně využívá celou řadu stávajících služeb Azure a nativně zahrnuje osvědčené základy, jako je Log Analytics a Logic Apps. Azure Sentinel obohacuje vaše vyšetřování a detekci pomocí umělé inteligence a poskytuje datový proud analýzy hrozeb společnosti Microsoft a umožňuje vám přinést vlastní analýzu hrozeb. 

## <a name="connect-to-all-your-data"></a>Připojení ke všem datům

Chcete-li na palubě Azure Sentinel, musíte [se nejprve připojit ke zdrojům zabezpečení](connect-data-sources.md). Azure Sentinel je dodáván s řadou konektorů pro řešení Microsoft, které jsou k dispozici iz v krabici a poskytují integraci v reálném čase, včetně řešení Microsoft Threat Protection, a zdrojů Microsoft 365, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho existují integrované konektory pro širší ekosystém zabezpečení pro řešení jiných společností než Microsoft. Můžete také použít běžný formát událostí, Syslog nebo REST-API pro připojení zdrojů dat s Azure Sentinel také.  

![Sběrače dat](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Workbooks

Po [připojení zdrojů](quickstart-onboard.md) dat k Azure Sentinelu můžete data monitorovat pomocí integrace Azure Sentinelu se sešity Azure Monitor, které poskytují všestrannost při vytváření vlastních sešitů. Zatímco se sešity se v Azure Sentinelu zobrazují jinak, můžou být užitečné, když uvidíte, jak [vytvořit interaktivní sestavy pomocí sešitů Azure Monitor](../azure-monitor/app/usage-workbooks.md). Azure Sentinel umožňuje vytvářet vlastní sešity napříč daty a také obsahuje integrované šablony sešitu, které vám umožní rychle získat přehled o vašich datech, jakmile připojíte zdroj dat.

![Řídicí panely](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analýza

Aby chomáč snížit hluk a minimalizovat počet výstrah, které je třeba zkontrolovat a prozkoumat, Azure Sentinel používá [analýzy korelovat výstrahy do incidentů](tutorial-detect-threats-built-in.md). **Incidenty** jsou skupiny souvisejících výstrah, které společně vytvářejí možnou hrozbu, kterou můžete prozkoumat a vyřešit. Použijte předdefinovaná pravidla korelace tak, jak jsou, nebo je použijte jako výchozí bod k vytvoření vlastního. Azure Sentinel také poskytuje pravidla strojového učení pro mapování chování sítě a pak hledat anomálie napříč prostředky. Tyto analýzy spojují tečky tím, že kombinují výstrahy s nízkou věrností různých entit do potenciálních incidentů zabezpečení s vysokou věrností.

![Incidenty](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>& orchestrace automatizace zabezpečení

Automatizujte běžné úkoly a [zjednodušte orchestraci zabezpečení pomocí playbooků,](tutorial-respond-threats-playbook.md) které se integrují se službami Azure i s vašimi stávajícími nástroji. Řešení Azure Sentinel, postavené na základech Azure Logic Apps, poskytuje vysoce rozšiřitelnou architekturu, která umožňuje škálovatelnou automatizaci při růstu nových technologií a hrozeb. Pokud chcete vytvářet playbooky pomocí Azure Logic Apps, můžete si vybrat z rostoucí galerie předdefinovaných playbooků. Patří mezi ně [více než 200 konektorů](https://docs.microsoft.com/azure/connectors/apis-list) pro služby, jako jsou funkce Azure. Konektory umožňují použít libovolnou vlastní logiku v kódu ServiceNow, Jira, Zendesk, HTTP požadavky, Microsoft Teams, Slack, Windows Defender ATP a Cloud App Security.

Například pokud používáte ServiceNow ticketing systému, můžete použít nástroje k použití Azure Logic Apps k automatizaci pracovních postupů a otevřít lístek v ServiceNow pokaždé, když je zjištěna konkrétní událost.

![Playbooky](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Šetření

V současné době ve verzi Preview, Azure Sentinel [hloubkové šetření](tutorial-investigate-cases.md) nástroje vám pomohou pochopit obor a najít hlavní příčinu potenciální ohrožení zabezpečení. Můžete zvolit entitu v interaktivním grafu a položit zajímavé otázky pro konkrétní entitu a přejít k podrobnostem této entity a jejích připojení, abyste se dostali k hlavní příčině hrozby. 

![Šetření](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Vyhledávání

Používejte [výkonné nástroje azure sentinelu pro vyhledávání a dotazování](hunting.md)na základě architektury MITRE, které vám umožní aktivně vyhledávat bezpečnostní hrozby napříč zdroji dat vaší organizace před aktivací výstrahy. Poté, co zjistíte, který lovecký dotaz poskytuje vysoce hodnotné přehledy o možných útocích, můžete také vytvořit vlastní pravidla zjišťování na základě vašeho dotazu a zobrazit tyto přehledy jako výstrahy pro vaše respondenty incidentů zabezpečení. Při lovu můžete vytvořit záložky pro zajímavé události, které vám umožní vrátit se k nim později, sdílet je s ostatními a seskupit je s dalšími korelačními událostmi, abyste vytvořili přesvědčivý incident pro vyšetřování.

![Vyhledávání](./media/overview/hunting.png)

## <a name="community"></a>Komunita

Komunita Azure Sentinelu je výkonný prostředek pro detekci hrozeb a automatizaci. Naši analytici zabezpečení společnosti Microsoft neustále vytvářejí a přidávají nové sešity, playbooky, lovecké dotazy a další a zveřejňují je v komunitě, abyste je mohli používat ve svém prostředí. Ukázkový obsah si můžete stáhnout z úložiště GitHub v privátní [komunitě](https://aka.ms/asicommunity) a vytvořit si vlastní sešity, vyhledávací dotazy, poznámkové bloky a playbooky pro Azure Sentinel. 

![Komunita](./media/overview/community.png)

## <a name="next-steps"></a>Další kroky

- Abyste mohli začít pracovat s Azure Sentinelem, potřebujete předplatné Microsoft Azure. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Zjistěte, jak [založit data do Azure Sentinelu](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
