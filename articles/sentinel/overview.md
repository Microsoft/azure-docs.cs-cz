---
title: Co je Azure Sentinel Preview? | Microsoft Docs
description: Přečtěte si o službě Azure Sentinel, jejích klíčových funkcích a o tom, jak to funguje.
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
ms.openlocfilehash: 15cf770a372c9a1386dd0293abeac01fd3cacf63
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779747"
---
# <a name="what-is-azure-sentinel-preview"></a>Co je Azure Sentinel Preview?

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft Azure Sentinel je škálovatelná, cloudová, nativní, **Siem (Security Information Management)** a **automatizované řešení pro orchestraci zabezpečení (společnosti)** . Služba Azure Sentinel nabízí inteligentní analýzu zabezpečení a analýzu hrozeb napříč podnikem a poskytuje jediné řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby. 

Azure Sentinel je pohled z ptačího oka v celém podniku, který řeší zátěž stále náročnějších útoků, zvyšuje množství výstrah a časové rámce dlouhého rozlišení.

- **Shromažďovat data v cloudovém měřítku** napříč všemi uživateli, zařízeními, aplikacemi a infrastrukturou, a to místně i v několika cloudech. 

- **Detekuje dříve**nezjištěné hrozby a minimalizuje falešně pozitivní výsledky pomocí analýzy Microsoftu a neparalelních analýz hrozeb. 

- **Prozkoumejte hrozby pomocí umělých inteligentních poznatků**a prošetřete podezřelé aktivity ve velkém měřítku, klepněte na roky zabezpečení na pracovišti v Microsoftu. 

- **Můžete rychle reagovat na incidenty** díky integrované orchestraci a automatizaci běžných úloh.


![Funkce Azure Sentinel Core](./media/overview/core-capabilities.png)

V celé škále stávajících služeb Azure se v systému Azure Sentinel nativně nepodnikou osvědčené základy, jako je Log Analytics a Logic Apps. Sentinel Azure vylepšuje vaše vyšetřování a detekci pomocí AI a poskytuje Stream Intelligence Microsoftu pro hrozby a umožňuje využít vlastní analýzu hrozeb. 

 
## <a name="connect-to-all-your-data"></a>Připojení ke všem datům

Aby bylo možné připojit se ke službě Azure Sentinel, musíte se nejdřív [připojit ke zdrojům zabezpečení](connect-data-sources.md). Služba Azure Sentinel se dodává s řadou konektorů pro řešení Microsoftu, které jsou dostupné předem a poskytuje integraci v reálném čase, včetně řešení ochrany před internetovými útoky Microsoftu a Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. Pro připojení zdrojů dat k Azure Sentinel taky můžete použít také běžné formáty událostí, syslog nebo REST API.  

![Sběrače dat](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Řídicí panely

Po připojení zdrojů dat si můžete vybrat z Galerie expertů [vytvořených řídicích panelů](quickstart-get-visibility.md#dashboards) , které jsou z vašich zdrojů dat Surface. Každý řídicí panel je plně přizpůsobitelný – můžete přidat vlastní logiku nebo upravit dotazy, nebo můžete vytvořit řídicí panel úplně od začátku.

Řídicí panely poskytují interaktivní vizualizaci pomocí pokročilých analýz, které vašim analytikům zabezpečení pomůžou lépe pochopit, co se v průběhu útoku vychází. Nástroje pro šetření vám umožňují hluboko podrobně na jakémkoli poli, od všech dat až po rychlé vývoj kontextu hrozeb. 

![Řídicí panely](./media/overview/dashboards.png)

## <a name="analytics"></a>Analýzy

K tomu, abyste snížili šum a minimalizovali počet výstrah, které je třeba zkontrolovat a prozkoumat, používá Azure Sentinel [analýzy ke korelaci upozornění na incidenty](tutorial-detect-threats.md). **Incidenty** jsou skupiny souvisejících výstrah, které dohromady vytvářejí možnou možné hrozby, kterou můžete prozkoumat a vyřešit. Použijte předdefinovaná pravidla korelace, jak jsou, nebo je použijte jako výchozí bod a sestavte si vlastní. Funkce Azure Sentinel taky poskytuje pravidla pro strojové učení pro mapování chování sítě a hledání anomálií napříč prostředky. Tyto analýzy spojují tečky kombinací upozornění s nízkou věrností o různých entitách na potenciální incidenty zabezpečení s vysokou přesností.

![Věcech](./media/overview/cases.png)

## <a name="user-analytics"></a>Uživatelské analýzy

Díky nativní integraci služby Machine Learning (ML) a [analýzám uživatelů](user-analytics.md)může Azure Sentinel pomáhat rychle detekovat hrozby. Funkce Sentinel Azure se bezproblémově integruje s rozšířenou ochranou před internetovými útoky Azure a analyzuje chování uživatelů a určí prioritu uživatelů, které byste měli prozkoumat, na základě jejich výstrah a vzorců podezřelých aktivit napříč službou Azure Sentinel a Microsoft 365.

![Uživatelské analýzy](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Orchestrace & automatizace zabezpečení

Automatizujte své běžné úlohy a [Zjednodušte orchestraci zabezpečení pomocí playbooky](tutorial-respond-threats-playbook.md) , která se integruje se službami Azure, i s vašimi stávajícími nástroji. Řešení automatizace a orchestrace v Azure, které je postavené na základu Azure Logic Apps, nabízí vysoce rozšiřitelnou architekturu, která umožňuje škálovatelnou automatizaci jako nové technologie a hrozby. Pokud chcete sestavit playbooky s Azure Logic Apps, můžete si vybrat z rostoucí Galerie předdefinovaných playbooky. Mezi ně patří [200 konektorů](https://docs.microsoft.com/azure/connectors/apis-list) pro služby, jako je Azure Functions. Konektory umožňují použití libovolné vlastní logiky v kódu, ServiceNow, JIRA, Zendesk, požadavcích HTTP, Microsoft teams, dislogic, Windows Defender ATP a Cloud App Security.

Pokud například používáte systém lístků ServiceNow, můžete použít nástroje, které jsou k dispozici pro použití Azure Logic Apps k automatizaci pracovních postupů a otevření lístku v ServiceNow pokaždé, když se zjistí konkrétní událost.

![Playbooky](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Šetření

Podrobné nástroje pro [zkoumání](tutorial-investigate-cases.md) Azure Sentinel vám pomůžou pochopit rozsah a najít hlavní příčinu potenciální bezpečnostní hrozby. V interaktivním grafu můžete zvolit entitu, která bude klást zajímavé otázky konkrétní entity, a přejít k podrobnostem o této entitě a jejím připojení, abyste se dostali k hlavní příčině hrozby. 

![Šetření](./media/overview/investigation.png)


## <a name="hunting"></a>Vyhledávání

Využijte [výkonné nástroje pro hledání a dotazování](hunting.md)v rámci Azure Sentinel na základě Mitre Frameworku, které vám umožní proaktivní prokládat bezpečnostní hrozby ve zdrojích dat vaší organizace, než se aktivuje výstraha. Až zjistíte, který lovecký dotaz poskytuje přehledy o možných útokech, můžete také vytvořit vlastní pravidla detekce na základě dotazu a tyto poznatky obdržíte jako výstrahy na vaše reakce na incidenty zabezpečení. Při lovu můžete vytvořit záložky pro zajímavé události, které vám umožní vrátit se k nim později, sdílet je s ostatními a seskupovat je s jinými událostmi korelace a vytvořit tak přesvědčivý případ pro šetření.

![Vyhledávání](./media/overview/hunting.png)

## <a name="community"></a>Komunita

Komunita Sentinel Azure je výkonným prostředkem pro detekci a automatizaci hrozeb. Naši analytiky Microsoftu pro zabezpečení neustále vytvářejí a přidávají nové řídicí panely, playbooky, lovecké dotazy a další, jejich publikování do komunity, abyste je mohli používat ve svém prostředí. Můžete si stáhnout ukázkový obsah z [úložiště](https://aka.ms/asicommunity) GitHub privátní komunity a vytvořit vlastní řídicí panely, lovecké dotazy, poznámkové bloky a playbooky pro službu Azure Sentinel. 

![Komunita](./media/overview/community.png)

## <a name="next-steps"></a>Další postup

- Abyste mohli začít používat službu Azure Sentinel, potřebujete Microsoft Azure předplatné. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Naučte se, jak začlenit [data do Azure Sentinel](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
