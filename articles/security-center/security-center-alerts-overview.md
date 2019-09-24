---
title: Výstrahy zabezpečení v Azure Security Center | Microsoft Docs
description: Toto téma vysvětluje, jaké jsou výstrahy zabezpečení, a různé typy, které jsou k dispozici v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 3b4b02574c028822d25d841376b127a718243b2e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202560"
---
# <a name="security-alerts-in-azure-security-center"></a>Výstrahy zabezpečení ve službě Azure Security Center

V Azure Security Center existuje celá řada výstrah pro mnoho různých typů prostředků. Security Center generuje výstrahy pro prostředky nasazené v Azure a také pro prostředky nasazené v místních i hybridních cloudových prostředích.

Rozšířená detekce je k dispozici v úrovni Standard služby Azure Security Center. K dispozici je bezplatná zkušební verze. Upgrade můžete provést z nabídky cenových úrovní v [Zásadách zabezpečení](security-center-pricing.md). Další informace o cenách naleznete na stránce [služby Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## Reakce na dnešní hrozby <a name="respond-threats"></a>

Za posledních 20 let došlo ve světě hrozeb k významným změnám. V minulosti se společnosti obvykle musely obávat pouze poškození vzhledu webu jednotlivými útočníky, které většinou jen zajímalo předvést, co dokážou udělat. Dnešní útočníci jsou mnohem sofistikovanější a organizovanější. Často mají konkrétní finanční a strategické cíle. Mají také k dispozici větší zdroje, protože mohou být financováni státy nebo organizovanou trestnou činností.

Tyto změny v realitách vedly k navýšení nebývalého profesionálního postavení v útočníkovi. Již je nezajímá pouhé poškození vzhledu webu. Nyní mají zájem ukrást informace, finanční účty a soukromá data – to vše, co můžou využít ke generování hotovosti na otevřeném trhu nebo k využití konkrétní obchodní, politické nebo vojenské pozice. Ještě znepokojivější než útočníci s finančními cíli jsou útočníci, kteří pronikají do sítí s cílem poškodit infrastrukturu a lidi.

V reakci na tyto hrozby společnosti často nasazují řadu jednotlivých řešení, která se zaměřují na ochranu firemní zóny nebo na koncové body tím, že hledají příznaky známých útoků. Tato řešení často generují velký objem málo specifických výstrah, které pak musí bezpečnostní analytik třídit a prošetřovat. Většina organizací nemá dostatek času a potřebné odborné znalosti, aby si s těmito výstrahami poradily, a mnoho z nich tak zůstane neprošetřených.  

Kromě toho útočníci vyvinuli své metody pro přepočet obrany na základě podpisu a [přizpůsobení cloudovým prostředím](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Je nutné zavést nové přístupy za účelem rychlejší identifikace nově se vynořujících hrozeb a urychlení detekce a reakce na ně.

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?

Výstrahy jsou oznámení, která Security Center generují při detekci hrozeb na vašich prostředcích. Security Center určí prioritu a seznam výstrah spolu s informacemi potřebnými k rychlému prozkoumání problému. Security Center také poskytuje doporučení, jak můžete útok napravit.

## Jak Security Center detekuje hrozby? <a name="detect-threats"></a>

Výzkumníci v oblasti zabezpečení ze společnosti Microsoft neustále vyhledávají nové hrozby. Z důvodu globální přítomnosti Microsoftu v cloudu i v místním prostředí mají přístup k obsáhlém sadě telemetrie. Souběžná a různorodá kolekce datových sad umožňuje zjistit nové vzory útoků a trendy v rámci svých místních zákaznických a podnikových produktů a také jejich online služby. Díky tomu dokáže Security Center rychle aktualizovat své algoritmy detekce spolu s tím, jak útočníci provádějí nové a stále sofistikovanější kousky. Tento přístup pomáhá udržet krok s rychle se rozvíjejícím prostředím hrozeb.

Security Center shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure a sítě, aby zjistila skutečné hrozby a snížila falešně pozitivní výsledky. Funguje taky s připojenými partnerskými řešeními, jako jsou brány firewall a řešení ochrany koncových bodů. Security Center analyzuje tyto informace a často koreluje informace z různých zdrojů k identifikaci hrozeb.

![Shromažďování a prezentace dat ve službě Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Využívá objevy v oblasti zpracování velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) k vyhodnocování událostí v rámci všech prostředků cloudové infrastruktury – a pomocí manuálních metod a předvídání vývoje útoků detekuje hrozby, které by jinak nebylo možné identifikovat. Do této analýzy zabezpečení patří:

* **Integrovaná analýza hrozeb**: Vyhledá známé chybné objekty actor využitím globální analýzy hrozeb z produktů a služeb společnosti Microsoft, jednotky digitálních zločinů společnosti Microsoft (DCU), centra MSRC (Microsoft Security Response Center) a externích informačních kanálů.
* **Analýza chování**: Použije známé vzorce pro zjištění škodlivého chování.
* **Detekce anomálií**: K sestavení historických standardních hodnot používá statistickou profilaci. Upozorní na odchylky od zavedených standardních hodnot, které mají potenciál útoku.

Následující témata podrobněji popisují každou z těchto analýz.

### <a name="integrated-threat-intelligence"></a>Integrovaná analýza hrozeb

Společnost Microsoft má k dispozici rozsáhlé zdroje globální analýzy hrozeb. Telemetrická data získává z různých zdrojů, například Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, tým Microsoft Digital Crimes Unit (DCU) nebo středisko Microsoft Security Response Center (MSRC). Výzkumní pracovníci také od třetích stran dostávají informace z analýzy hrozeb, které se sdílí mezi většinou poskytovatelů a předplatitelů cloudových služeb do kanálů analýzy hrozeb. Služba Azure Security Center vás pomocí těchto informací může upozornit na hrozby známých útočníků.

### <a name="behavioral-analytics"></a>Behaviorální analýza

Behaviorální analýza je technika, která analyzuje a porovnává data se sadou známých schémat. Tato schémata však nepředstavují jednoduché příznaky. Určují se prostřednictvím komplexních algoritmů strojového učení, které se aplikují na rozsáhlé datové sady. Určují se také prostřednictvím pečlivé analýzy škodlivého chování, kterou provádí zkušení analytici. Azure Security Center pomocí behaviorální analýzy identifikuje ohrožené prostředky na základě analýzy protokolů virtuálních počítačů, protokolů virtuálních síťových zařízení, protokolů prostředků infrastruktury, výpisů stavu systému a dalších zdrojů.

Kromě toho se pomocí vzájemné souvislosti s dalšími signály hledají podpůrné důkazy rozšířené kampaně. Tato korelace pomáhá identifikovat události, které jsou konzistentní se zavedenými ukazateli ohrožení zabezpečení. 

### <a name="anomaly-detection"></a>Zjištění anomálií

Služba Azure Security Center také identifikuje hrozby pomocí detekce anomálií. Oproti behaviorální analýze (která závisí na známých schématech odvozovaných z velkých datových sad), je detekce anomálií více „personalizovaná“ a zaměřuje se na standardní hodnoty specifické pro vaše nasazení. Pomocí strojového učení se určí běžné úrovně aktivity pro vaše nasazení a poté se vygenerují pravidla definující neobvyklé hodnoty, které by mohly představovat událost zabezpečení.

## <a name="continuous-monitoring-and-assessments"></a>Průběžné monitorování a posouzení

Azure Security Center výhody z oblasti výzkumu zabezpečení a týmů pro datové vědy v rámci Microsoftu, kteří neustále monitorují změny v rámci hrozby. To zahrnuje následující iniciativy:

* **Monitorování analýzy hrozeb**: Analýza hrozeb zahrnuje mechanismy, ukazatele, dopady a užitečná stanoviska k existujícím nebo nově vznikajícím hrozbám. Tyto informace se sdílí v bezpečnostní komunitě a společnost Microsoft kanály analýzy hrozeb z interních i externích zdrojů nepřetržitě monitoruje.
* **Sdílení signálu**: Přehledy z bezpečnostních týmů v rámci širokého portfolia cloudových a místních služeb, serverů a klientských koncových zařízení od Microsoftu jsou sdílené a analyzovatelné.
* **Odborníci na zabezpečení společnosti Microsoft**: Průběžná zapojení s týmy v Microsoftu, které fungují ve specializovaných polích zabezpečení, jako je forenzní a detekce útoků na web.
* **Ladění detekce**: Algoritmy se spouštějí proti reálným zákaznickým datovým sadám a výzkumníkům zabezpečení pracují se zákazníky, aby ověřili výsledky. Pravdivě a falešně pozitivní výsledky pak slouží ke zlepšování algoritmů strojového učení.

Toto celkové úsilí přineslo nové a vylepšené způsoby detekce, ze kterých budete mít okamžitý prospěch – a nemusíte sami nic dělat.

## Typy <a name="security-alert-types"></a> výstrah zabezpečení

Následující témata vás provedou různými výstrahami v závislosti na typech prostředků:

* [Výstrahy IaaS virtuální počítače a servery](security-center-alerts-iaas.md)
* [Nativní výpočetní výstrahy](security-center-alerts-compute.md)
* [Výstrahy datových služeb](security-center-alerts-data-services.md)

Následující témata vysvětlují, jak Security Center používá jinou telemetrii, kterou shromažďuje z integrace s infrastrukturou Azure, aby bylo možné použít další vrstvy ochrany pro prostředky nasazené v Azure:

* [Výstrahy vrstvy služeb](security-center-alerts-service-layer.md)
* [Integrace s produkty zabezpečení Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Co jsou incidenty zabezpečení?

Incident zabezpečení je kolekce souvisejících výstrah, místo aby se jednotlivé výstrahy zobrazovaly jednotlivě. Security Center používá [korelaci cloudových inteligentních výstrah](security-center-alerts-cloud-smart.md) ke korelaci různých výstrah a signálů s nízkou věrností do incidentů zabezpečení.

Pomocí incidentů vám Security Center poskytuje jediné zobrazení kampaně útoku a všech souvisejících výstrah. Toto zobrazení vám umožní rychle pochopit, jaké akce útočníka trvala, a jaké prostředky byly ovlivněny. Další informace najdete v tématu [korelace cloudové inteligentní výstrahy](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o různých typech výstrah, které jsou k dispozici v Security Center. Další informace naleznete v tématu:

* [Průvodce plánováním a provozem služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center – nejčastější dotazy](https://docs.microsoft.com/azure/security-center/security-center-faq)

