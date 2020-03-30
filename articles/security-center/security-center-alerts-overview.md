---
title: Výstrahy zabezpečení v Azure Security Center | Dokumenty společnosti Microsoft
description: Toto téma vysvětluje, co jsou výstrahy zabezpečení a různé typy dostupné v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 697c038a2fefdde8e488dad23a4e38e0b2b7b288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415855"
---
# <a name="security-alerts-in-azure-security-center"></a>Výstrahy zabezpečení ve službě Azure Security Center

V Azure Security Center existuje celá řada výstrah pro mnoho různých typů prostředků. Security Center generuje výstrahy pro prostředky nasazené v Azure a také pro prostředky nasazené v místním a hybridním cloudovém prostředí.

Výstrahy zabezpečení se aktivují pomocí pokročilých zjišťování a jsou k dispozici jenom ve standardní vrstvě Azure Security Center. K dispozici je bezplatná zkušební verze. Upgrade můžete provést z nabídky cenových úrovní v [Zásadách zabezpečení](security-center-pricing.md). Další informace o cenách naleznete na stránce [služby Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="responding-to-todays-threats"></a>Reakce na dnešní hrozby <a name="respond-threats"> </a>

Za posledních 20 let došlo ve světě hrozeb k významným změnám. V minulosti se společnosti obvykle musely starat pouze o znehodnocení webových stránek jednotlivými útočníky, kteří se většinou zajímali o to, "co by mohli udělat". Dnešní útočníci jsou mnohem sofistikovanější a organizovanější. Často mají konkrétní finanční a strategické cíle. Mají také k dispozici větší zdroje, protože mohou být financováni státy nebo organizovanou trestnou činností.

Tyto měnící se skutečnosti vedly k bezprecedentní úrovni profesionality v řadách útočníků. Již je nezajímá pouhé poškození vzhledu webu. Dnes mají zájem krást informace, finanční účty a soukromá data – to vše mohou využít k vytváření hotovosti na otevřeném trhu nebo k využití konkrétní obchodní, politické či vojenské pozice. Ještě znepokojivější než útočníci s finančními cíli jsou útočníci, kteří pronikají do sítí s cílem poškodit infrastrukturu a lidi.

V reakci na tyto hrozby společnosti často nasazují řadu jednotlivých řešení, která se zaměřují na ochranu firemní zóny nebo na koncové body tím, že hledají příznaky známých útoků. Tato řešení často generují velký objem málo specifických výstrah, které pak musí bezpečnostní analytik třídit a prošetřovat. Většina organizací nemá dostatek času a potřebné odborné znalosti, aby si s těmito výstrahami poradily, a mnoho z nich tak zůstane neprošetřených.  

Útočníci navíc vyvinuli své metody, aby rozvrátili mnoho obranných obránců založených na podpisech a [přizpůsobili se cloudovým prostředím](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Je nutné zavést nové přístupy za účelem rychlejší identifikace nově se vynořujících hrozeb a urychlení detekce a reakce na ně.

## <a name="what-are-security-alerts-and-security-incidents"></a>Co jsou výstrahy zabezpečení a bezpečnostní incidenty? 

**Výstrahy** jsou oznámení, která Centrum zabezpečení generuje, když detekuje hrozby vašich prostředků. Security Center upřednostňuje a uvádí výstrahy spolu s informacemi potřebnými k rychlému prošetření problému. Security Center také poskytuje doporučení pro nápravu útoku.

**Bezpečnostní incident** je kolekce souvisejících výstrah, namísto výpisu každé výstrahy jednotlivě. Security Center používá [Cloud Smart Alert Correlation](security-center-alerts-cloud-smart.md) ke korelaci různých výstrah a signálů s nízkou věrností do bezpečnostních incidentů.

Pomocí incidentů poskytuje Centrum zabezpečení jediné zobrazení útočné kampaně a všech souvisejících výstrah. Toto zobrazení umožňuje rychle pochopit, jaké akce útočník provedl a jaké prostředky byly ovlivněny. Další informace naleznete v [tématu Cloud smart alert correlation](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>Jak Security Center detekuje hrozby? <a name="detect-threats"> </a>

Výzkumníci v oblasti zabezpečení ze společnosti Microsoft neustále vyhledávají nové hrozby. Vzhledem k globální přítomnosti společnosti Microsoft v cloudu a v místním prostředí mají přístup k rozsáhlé sadě telemetrie. Rozsáhlá a různorodá kolekce datových sad umožňuje objevovat nové způsoby útoků a trendy napříč místními spotřebitelskými a podnikovými produkty a také online službami. Díky tomu dokáže Security Center rychle aktualizovat své algoritmy detekce spolu s tím, jak útočníci provádějí nové a stále sofistikovanější kousky. Tento přístup pomáhá udržet krok s rychle se rozvíjejícím prostředím hrozeb.

Centrum zabezpečení shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure a ze sítě, aby zjistilo skutečné hrozby a snížilo počet falešných poplachů. Spolupracuje také s připojenými partnerskými řešeními, jako je brána firewall a řešení ochrany koncových bodů. Security Center analyzuje tyto informace, často korelovat informace z více zdrojů, k identifikaci hrozeb.

![Shromažďování a prezentace dat ve službě Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Využívá objevy v oblasti zpracování velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) k vyhodnocování událostí v rámci všech prostředků cloudové infrastruktury – a pomocí manuálních metod a předvídání vývoje útoků detekuje hrozby, které by jinak nebylo možné identifikovat. Do této analýzy zabezpečení patří:

* **Integrovaná analýza hrozeb**: Microsoft má obrovské množství globálních informací o hrozbách. Telemetrie proudí z různých zdrojů, jako je Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) a Microsoft Security Response Center (MSRC). Výzkumní pracovníci také dostávají informace o informacích o hrozbách, které jsou sdíleny mezi hlavními poskytovateli cloudových služeb a kanály od jiných třetích stran. Služba Azure Security Center vás pomocí těchto informací může upozornit na hrozby známých útočníků.

* **Behaviorální analýza**: Behaviorální analýza je technika, která analyzuje a porovnává data s kolekcí známých vzorů. Tato schémata však nepředstavují jednoduché příznaky. Určují se prostřednictvím komplexních algoritmů strojového učení, které se aplikují na rozsáhlé datové sady. Určují se také prostřednictvím pečlivé analýzy škodlivého chování, kterou provádí zkušení analytici. Azure Security Center můžete použít behaviorální analýzy k identifikaci ohrožených prostředků na základě analýzy protokolů virtuálních strojů, protokoly virtuálních síťových zařízení, protokoly prostředků infrastruktury, výpisy stavu systému a další zdroje.

* **Detekce anomálií**: Azure Security Center také používá detekci anomálií k identifikaci hrozeb. Na rozdíl od behaviorální analýzy (která závisí na známých vzorcích odvozených z velkých datových sad) je detekce anomálií více "personalizovaná" a zaměřuje se na směrné plány, které jsou specifické pro vaše nasazení. Pomocí strojového učení se určí běžné úrovně aktivity pro vaše nasazení a poté se vygenerují pravidla definující neobvyklé hodnoty, které by mohly představovat událost zabezpečení.

## <a name="how-are-alerts-classified"></a>Jak jsou záznamy klasifikovány?

Centrum zabezpečení přiřazuje výstrahám závažnost, která vám pomůže určit pořadí, ve kterém se účastníte jednotlivých výstrah, takže když je prostředek ohrožen, můžete se k němu dostat ihned. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

> [!NOTE]
> Závažnost výstrahy se zobrazí odlišně na portálu a verze rozhraní REST API, které předcházejí 01-01-2019. Pokud používáte starší verzi rozhraní API, upgradujte pro konzistentní prostředí popsané níže.

- **Vysoká:** Existuje vysoká pravděpodobnost, že váš prostředek je ohrožena. Měl by ses na to hned podívat. Centrum zabezpečení má vysokou důvěru v škodlivý záměr a v zjištění použitá k vydání výstrahy. Například výstraha, která detekuje spuštění známého škodlivého nástroje, jako je Mimikatz, běžný nástroj používaný pro krádež pověření.
- **Střední:** Toto je pravděpodobně podezřelá aktivita může znamenat, že prostředek je ohrožena.
Důvěra Centra zabezpečení v analýzu nebo hledání je střední a důvěra škodlivého záměru je střední až vysoká. Obvykle se jedná o strojové učení nebo detekce založené na anomáliích. Například pokus o přihlášení z neobvyklého umístění.
- **Nízká:** Může to být neškodný pozitivní nebo blokovaný útok.
   * Centrum zabezpečení není dostatečně přesvědčeno, že záměr je škodlivý a aktivita může být nevinná. Například log clear je akce, která může nastat, když se útočník pokusí skrýt své stopy, ale v mnoha případech je rutinní operace prováděná správci.
   * Security Center obvykle neříká, kdy byly útoky zablokovány, pokud se nejedná o zajímavý případ, který doporučujeme, abyste se podívali. 
- **Informační:** Informační výstrahy se zobrazí pouze při přechodu k podrobnostem o incidentu zabezpečení nebo při použití rozhraní REST API s určitým ID výstrahy. Incident se obvykle skládá z několika výstrah, z nichž některé se mohou samy o sobě jevit jako pouze informační, ale v kontextu ostatních výstrah mohou být hodny bližšího pohledu. 

## <a name="continuous-monitoring-and-assessments"></a>Průběžné monitorování a hodnocení

Azure Security Center těží z toho, že má týmy pro výzkum zabezpečení a datové vědy v celém Microsoftu, které průběžně monitorují změny v prostředí hrozeb. To zahrnuje následující iniciativy:

* **Sledování analýzy hrozeb**: Analýza hrozeb zahrnuje mechanismy, ukazatele, důsledky a užitečné rady o stávajících nebo vznikajících hrozbách. Tyto informace se sdílí v bezpečnostní komunitě a společnost Microsoft kanály analýzy hrozeb z interních i externích zdrojů nepřetržitě monitoruje.
* **Sdílení signálu**: Přehledy od bezpečnostních týmů v širokém portfoliu cloudových a místních služeb, serverů a koncových zařízení klienta společnosti Microsoft jsou sdíleny a analyzovány.
* **Odborníci na zabezpečení společnosti Microsoft**: Průběžné zapojování týmů v rámci společnosti Microsoft, které pracují ve specializovaných oblastech zabezpečení, například forenzní analýza nebo detekce webových útoků.
* **Optimalizace detekce**: V datových sadách reálných zákazníků se spouští algoritmy a výzkumníci z oblasti bezpečnosti pracují se zákazníky na ověřování výsledků. Pravdivě a falešně pozitivní výsledky pak slouží ke zlepšování algoritmů strojového učení.

Toto společné úsilí vyvrcholí novými a vylepšenými detekcemi, které můžete okamžitě využít – není třeba je podniknout.


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o různých typech výstrah dostupných v Centru zabezpečení. Další informace naleznete v tématu:

* [Ochrana před hrozbami v Azure Security Center](threat-protection.md) – stručný popis zdrojů výstrah zabezpečení zobrazených službou Azure Security Center 
* **Výstrahy zabezpečení v protokolu aktivit Azure** – kromě toho, že jsou k dispozici na webu Azure Portal nebo programově, se výstrahy zabezpečení a incidenty auditují jako události v protokolu aktivit [Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Další informace o schématu událostí najdete [v tématu výstrahy zabezpečení v protokolu aktivit Azure](https://go.microsoft.com/fwlink/?linkid=2114113)

