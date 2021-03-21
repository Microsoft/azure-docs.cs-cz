---
title: Co je Azure Sentinel? | Dokumentace Microsoftu
description: Přečtěte si o Azure Sentinel, škálovatelné, cloudové správě událostí zabezpečení (SIEM) a řešení automatizované reakce (společnosti) pro orchestraci zabezpečení.
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 4bc5732c1b2349f34297ecaacc7eb3176fdba611
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570478"
---
# <a name="what-is-azure-sentinel"></a>Co je Azure Sentinel?

Microsoft Azure Sentinel je škálovatelná, cloudová, nativní, **Siem (Security Information Management)** a **automatizované řešení pro orchestraci zabezpečení (společnosti)** . Služba Azure Sentinel nabízí inteligentní analýzu zabezpečení a analýzu hrozeb napříč podnikem a poskytuje jediné řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby. 

Azure Sentinel je pohled z ptačího oka v celém podniku, který řeší zátěž stále náročnějších útoků, zvyšuje množství výstrah a časové rámce dlouhého rozlišení.

- **Shromažďovat data v cloudovém měřítku** napříč všemi uživateli, zařízeními, aplikacemi a infrastrukturou, a to místně i v několika cloudech. 

- **Detekuje dříve nezjištěné hrozby** a minimalizuje falešně pozitivní výsledky pomocí analýzy Microsoftu a neparalelních analýz hrozeb. 

- **Prozkoumejte hrozby pomocí umělých inteligentních poznatků a prošetřete** podezřelé aktivity ve velkém měřítku, klepněte na roky zabezpečení na pracovišti v Microsoftu. 

- **Můžete rychle reagovat na incidenty** díky integrované orchestraci a automatizaci běžných úloh.

![Funkce Azure Sentinel Core](./media/overview/core-capabilities.png)

V celé škále stávajících služeb Azure se v systému Azure Sentinel nativně nepodnikou osvědčené základy, jako je Log Analytics a Logic Apps. Sentinel Azure vylepšuje vaše vyšetřování a detekci pomocí AI a poskytuje Stream Intelligence Microsoftu pro hrozby a umožňuje využít vlastní analýzu hrozeb. 

## <a name="connect-to-all-your-data"></a>Připojení ke všem datům

Aby bylo možné připojit se ke službě Azure Sentinel, musíte se nejdřív [připojit ke zdrojům zabezpečení](connect-data-sources.md). Služba Azure Sentinel je dodávána s řadou konektorů pro řešení Microsoftu, která je dostupná předem a poskytuje integraci v reálném čase, včetně Microsoft 365 Defender (dříve Microsoft Threat Protection) a Microsoft 365ch zdrojů, včetně Office 365, Azure AD, Microsoft Defenderu pro identitu (dřív Azure ATP) a Microsoft Cloud App Security a dalších. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. Pro připojení zdrojů dat k Azure Sentinel taky můžete použít také běžné formáty událostí, syslog nebo REST API. 

![Kolektory dat](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Workbooks

Po [připojení zdrojů dat](quickstart-onboard.md) ke službě Azure Sentinel můžete monitorovat data pomocí integrace služby Azure sentinel s Azure monitor sešity, které poskytují univerzálnost při vytváření vlastních sešitů. Přestože se sešity ve službě Azure Sentinel zobrazují odlišně, může být užitečné, abyste viděli, jak [vytvářet interaktivní sestavy pomocí Azure monitor sešity](../azure-monitor/visualize/workbooks-overview.md). Azure Sentinel umožňuje vytvářet vlastní sešity napříč vašimi daty a také nabízí předdefinované šablony sešitu, které vám umožní rychle získat přehled o vašich datech, jakmile připojíte zdroj dat.

![Řídicí panely](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analýzy

K tomu, abyste snížili šum a minimalizovali počet výstrah, které je třeba zkontrolovat a prozkoumat, používá Azure Sentinel [analýzy ke korelaci upozornění na incidenty](tutorial-detect-threats-built-in.md). **Incidenty** jsou skupiny souvisejících výstrah, které dohromady vytvářejí možnou možné hrozby, kterou můžete prozkoumat a vyřešit. Použijte předdefinovaná pravidla korelace, jak jsou, nebo je použijte jako výchozí bod a sestavte si vlastní. Funkce Azure Sentinel taky poskytuje pravidla pro strojové učení pro mapování chování sítě a hledání anomálií napříč prostředky. Tyto analýzy spojují tečky kombinací upozornění s nízkou věrností o různých entitách na potenciální incidenty zabezpečení s vysokou přesností.

![Incidenty](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Orchestrace & automatizace zabezpečení

Automatizujte své běžné úlohy a [Zjednodušte orchestraci zabezpečení pomocí playbooky](tutorial-respond-threats-playbook.md) , která se integruje se službami Azure, i s vašimi stávajícími nástroji. Řešení automatizace a orchestrace v Azure, které je postavené na základu Azure Logic Apps, nabízí vysoce rozšiřitelnou architekturu, která umožňuje škálovatelnou automatizaci jako nové technologie a hrozby. Pokud chcete sestavit playbooky s Azure Logic Apps, můžete si vybrat z rostoucí Galerie předdefinovaných playbooky. Mezi ně patří [200 konektorů](../connectors/apis-list.md) pro služby, jako je Azure Functions. Konektory umožňují použití libovolné vlastní logiky v kódu, ServiceNow, JIRA, Zendesk, požadavcích HTTP, Microsoft teams, dislogic, Windows Defender ATP a Cloud App Security.

Pokud například používáte systém lístků ServiceNow, můžete použít nástroje, které jsou k dispozici pro použití Azure Logic Apps k automatizaci pracovních postupů a otevření lístku v ServiceNow pokaždé, když se zjistí konkrétní událost.

![Playbooky](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Šetření

V současnosti ve verzi Preview vám Azure Sentinel – [podrobné vyšetřovací](tutorial-investigate-cases.md) nástroje pomůžou pochopit rozsah a najít hlavní příčinu potenciální bezpečnostní hrozby. V interaktivním grafu můžete zvolit entitu, která bude klást zajímavé otázky konkrétní entity, a přejít k podrobnostem o této entitě a jejím připojení, abyste se dostali k hlavní příčině hrozby. 

![Šetření](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Vyhledávání

Využijte [výkonné nástroje pro hledání a dotazování](hunting.md)v rámci Azure Sentinel na základě Mitre Frameworku, které vám umožní proaktivní prokládat bezpečnostní hrozby ve zdrojích dat vaší organizace, než se aktivuje výstraha. Až zjistíte, který lovecký dotaz poskytuje přehledy o možných útokech, můžete také vytvořit vlastní pravidla detekce na základě dotazu a tyto poznatky obdržíte jako výstrahy na vaše reakce na incidenty zabezpečení. Při lovu můžete vytvořit záložky pro zajímavé události, které vám umožní vrátit se k nim později, sdílet je s ostatními a seskupovat je s ostatními korelačními událostmi a vytvořit tak přesvědčivý incident pro šetření.

![Přehled funkce pro lov](./media/overview/hunting.png)

## <a name="community"></a>Komunita

Komunita Sentinel Azure je výkonným prostředkem pro detekci a automatizaci hrozeb. Naši analytici Microsoftu v oblasti zabezpečení neustále vytvářejí a přidávají nové sešity, playbooky, lovecké dotazy a další, jejich publikování do komunity, abyste je mohli používat ve svém prostředí. Můžete si stáhnout ukázkový obsah z [úložiště](https://aka.ms/asicommunity) GitHub privátní komunity a vytvořit vlastní sešity, lovecké dotazy, poznámkové bloky a playbooky pro službu Azure Sentinel. 

![Prozkoumejte komunitu uživatelů](./media/overview/community.png)

## <a name="next-steps"></a>Další kroky

- Abyste mohli začít používat službu Azure Sentinel, potřebujete Microsoft Azure předplatné. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Naučte se, jak začlenit [data do Azure Sentinel](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).