---
title: Funkce detekce ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže porozumět tomu, jak ve službě Azure Security Center funguje detekce.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 4c5599cc-99a1-430f-895f-601615ef12a0
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 699903ff874ace4991c5c4b0584d4661d3e45f33
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-security-center-detection-capabilities"></a>Funkce detekce ve službě Azure Security Center
Tento dokument popisuje pokročilé schopnosti detekce služby Azure Security Center, které pomáhají při identifikaci aktivních hrozeb cílených na prostředky Microsoft Azure, a poskytuje vám informace potřebné pro rychlou reakci na tyto hrozby.

Rozšířená detekce je k dispozici v úrovni Standard služby Azure Security Center. K dispozici je bezplatná 60denní zkušební verze. Upgrade můžete provést z nabídky cenových úrovní v [Zásadách zabezpečení](security-center-policies.md). Další informace o cenách naleznete na stránce [služby Security Center](https://azure.microsoft.com/pricing/details/security-center/). 

> [!NOTE]
> Ve službě Security Center byla vydána ve verzi Limited Preview nová sada detekcí, které využívají záznamy auditu, což je běžný auditní rámec, k detekci škodlivého chování na počítačích s Linuxem. Pokud se chcete připojit k verzi Preview, zašlete [nám](mailto:ASC_linuxdetections@microsoft.com) e-mail s ID vašich předplatných.

## <a name="responding-to-todays-threats"></a>Reakce na současné hrozby
Za posledních 20 let došlo ve světě hrozeb k významným změnám. V minulosti se společnosti obvykle musely obávat pouze poškození vzhledu webu jednotlivými útočníky, které většinou jen zajímalo předvést, co dokážou udělat. Dnešní útočníci jsou mnohem sofistikovanější a organizovanější. Často mají konkrétní finanční a strategické cíle. Mají také k dispozici větší zdroje, protože mohou být financováni státy nebo organizovanou trestnou činností.

Tento přístup v řadách útočníků vedl k nebývalému růstu úrovně profesionality. Již je nezajímá pouhé poškození vzhledu webu. Dnes se snaží odcizit informace, finanční účty a osobní údaje – toto vše mohou použít k získání hotovosti na otevřeném trhu nebo k zajištění určité obchodní, politické či vojenské výhody. Ještě znepokojivější než útočníci s finančními cíli jsou útočníci, kteří pronikají do sítí s cílem poškodit infrastrukturu a lidi.

V reakci na tyto hrozby společnosti často nasazují řadu jednotlivých řešení, která se zaměřují na ochranu firemní zóny nebo na koncové body tím, že hledají příznaky známých útoků. Tato řešení často generují velký objem málo specifických výstrah, které pak musí bezpečnostní analytik třídit a prošetřovat. Většina organizací nemá dostatek času a potřebné odborné znalosti, aby si s těmito výstrahami poradily, a mnoho z nich tak zůstane neprošetřených.  Útočníci na druhé straně rozvinuli své metody narušování celé řady typů ochrany založené na detekci příznaků a [přizpůsobili se cloudovým prostředím](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Je nutné zavést nové přístupy za účelem rychlejší identifikace nově se vynořujících hrozeb a urychlení detekce a reakce na ně. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Jak služba Azure Security Center detekuje hrozby a reaguje na ně
Výzkumníci v oblasti zabezpečení ze společnosti Microsoft neustále vyhledávají nové hrozby. Mají přístup k rozsáhlé sadě telemetrických údajů získávaných díky globálního prezenci společnosti Microsoft v cloudových i místních prostředích. Tento rozsáhlý a různorodý soubor datových sad umožňuje společnosti Microsoft objevovat nová schémata a trendy útoků v rámci jejích místních produktů pro zákazníky a podniky a rovněž i v rámci online služeb. Díky tomu dokáže Security Center rychle aktualizovat své algoritmy detekce spolu s tím, jak útočníci provádějí nové a stále sofistikovanější kousky. Tento přístup pomáhá udržet krok s rychle se rozvíjejícím prostředím hrozeb. 

Detekce hrozeb ve službě Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení ze všech prostředků Azure, ze sítě a připojených partnerských řešení. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

![Shromažďování a prezentace dat ve službě Security Center](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Využívá objevy v oblasti zpracování velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) k vyhodnocování událostí v rámci všech prostředků cloudové infrastruktury – a pomocí manuálních metod a předvídání vývoje útoků detekuje hrozby, které by jinak nebylo možné identifikovat. Do této analýzy zabezpečení patří: 

* **Integrovaná analýza hrozeb**: Využívá globální analýzu hrozeb z produktů a služeb Microsoftu, od týmu DCU (Microsoft Digital Crimes Unit), ze střediska MSRC (Microsoft Security Response Center) a z externích informačních kanálů.
* **Behaviorální analýza**: Používá známá schémata k odhalování škodlivého chování. 
* **Detekce anomálií**: Pomocí statistické profilace vytváří historické standardní hodnoty. Upozorní na odchylky od zavedených standardních hodnot, které mají potenciál útoku.

### <a name="threat-intelligence"></a>Analýza hrozeb
Společnost Microsoft má k dispozici rozsáhlé zdroje globální analýzy hrozeb. Telemetrická data získává z různých zdrojů, například Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, tým Microsoft Digital Crimes Unit (DCU) nebo středisko Microsoft Security Response Center (MSRC). Výzkumní pracovníci také od třetích stran dostávají informace z analýzy hrozeb, které se sdílí mezi většinou poskytovatelů a předplatitelů cloudových služeb do kanálů analýzy hrozeb. Služba Azure Security Center vás pomocí těchto informací může upozornit na hrozby známých útočníků. Možné příklady:

* **Odchozí komunikace na škodlivé IP adresy**: Odchozí přenosy na známé botnety nebo darknety pravděpodobně naznačují, že došlo k napadení vašich prostředků a útočník se v daném systému pokouší spustit příkazy nebo stáhnout data. Služba Azure Security Center porovná síťové přenosy s globální databází hrozeb společnosti Microsoft a upozorní vás, pokud zjistí komunikaci na škodlivou IP adresu.

## <a name="behavioral-analytics"></a>Behaviorální analýza
Behaviorální analýza je technika, která analyzuje a porovnává data se sadou známých schémat. Tato schémata však nepředstavují jednoduché příznaky. Určují se prostřednictvím komplexních algoritmů strojového učení, které se aplikují na rozsáhlé datové sady. Určují se také prostřednictvím pečlivé analýzy škodlivého chování, kterou provádí zkušení analytici. Azure Security Center pomocí behaviorální analýzy identifikuje ohrožené prostředky na základě analýzy protokolů virtuálních počítačů, protokolů virtuálních síťových zařízení, protokolů prostředků infrastruktury, výpisů stavu systému a dalších zdrojů. 

Kromě toho se pomocí vzájemné souvislosti s dalšími signály hledají podpůrné důkazy rozšířené kampaně. Tato korelace pomáhá identifikovat události, které jsou konzistentní se zavedenými ukazateli ohrožení zabezpečení. Možné příklady:

* **Podezřelé spouštění procesů:** Útočníci používají řadu technik k tajnému spouštění škodlivého softwaru. Útočník může například malware pojmenovat stejně jako legitimní systémové soubory, ale umístí tyto souboru do alternativních umístění, nebo použije název velmi podobný neškodnému souboru nebo zamaskuje skutečnou příponu souboru. Modely služby Security Center zpracovávají chování a monitorují spouštění procesů s cílem odhalit právě takovéto mimořádné hodnoty.  
* **Skrytý malware a pokusy o zneužití:** Sofistikovaný malware dokáže obejít tradiční antimalwarové produkty tím, že nikdy nezapisuje na disk nebo softwarové komponenty ukládané na disk šifruje.  Takový malware lze ale zjistit pomocí analýzy paměti, protože aby malware mohl fungovat, musí v paměti zanechat stopy. Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání.  Díky analýze paměti ve výpisu stavu systému dokáže služba Azure Security Center detekovat techniky, které využívají zranitelností softwaru, získávají přístup k důvěrným datům a nenápadně přetrvávají v napadeném počítači, aniž by to mělo dopad na jeho výkon.
* **Laterální přesuny a interní sondování:** S cílem udržet se v napadené síti a najít/shromáždit hodnotná data se útočníci často pokouší přesunout z jednoho napadeného počítače do dalších zařízení ve stejné síti. Služba Security Center monitoruje aktivity procesů a přihlašování s cílem objevit pokusy útočníka o rozšíření základny v rámci sítě, například vzdálené sondování sítě spouštěním příkazů nebo výčet účtu.
* **Škodlivé skripty prostředí PowerShell**: Pomocí prostředí PowerShell spouští útočníci v cílových virtuálních počítačích škodlivé kódy, které slouží pro různé účely. Služba Security Center kontroluje aktivitu prostředí PowerShell a hledá známky podezřelé aktivity. 
* **Odchozí útoky:** Útočníci často cílí na cloudové prostředky s cílem využít je k dalším útokům. Napadené virtuální počítače je například možné použít pro spouštění útoků hrubou silou proti jiným virtuálním počítačům, odesílání nevyžádané pošty nebo skenování otevřených portů a dalších zařízení na internetu. Služba Security Center pomocí strojového učení, které uplatňuje na síťový provoz, dokáže detekovat odchozí síťovou komunikaci vybočující z normy. V případě nevyžádané pošty služba Security Center také koreluje neobvyklý e-mailový provoz pomocí informací ze služby Office 365 a snaží se zjistit, jestli pošta neslouží pro nekalé účely nebo jestli je výsledkem legitimní e-mailové kampaně.  

### <a name="anomaly-detection"></a>Detekce anomálií
Služba Azure Security Center také identifikuje hrozby pomocí detekce anomálií. Oproti behaviorální analýze (která závisí na známých schématech odvozovaných z velkých datových sad), je detekce anomálií více „personalizovaná“ a zaměřuje se na standardní hodnoty specifické pro vaše nasazení. Pomocí strojového učení se určí běžné úrovně aktivity pro vaše nasazení a poté se vygenerují pravidla definující neobvyklé hodnoty, které by mohly představovat událost zabezpečení. Zde naleznete příklad:

* **Příchozí útoky hrubou silou RDP/SSH**: Vaše nasazení mohou obsahovat velmi vytížené virtuální počítače s velkým množstvím přihlášení každý den a další virtuální počítače, které mají velmi málo nebo žádná přihlášení. Azure Security Center dokáže určit standardní hodnoty pro přihlašovací aktivitu pro tyto virtuální počítače a pomocí strojového učení definovat, co je mimo rámec běžné přihlašovací aktivity. Pokud se počet přihlášení, čas přihlášení nebo místo, ze kterého žádost o přihlášení pochází, případně další charakteristiky související s přihlášením výrazně liší od standardních hodnot, vygeneruje se výstraha. A strojové učení tu zase určuje, co je významné.

## <a name="continuous-threat-intelligence-monitoring"></a>Průběžné monitorování analýzy hrozeb
Služba Azure Security Center zaměstnává týmy pro výzkum zabezpečení a datové vědy, který neustále monitorují změny ve světě hrozeb. To zahrnuje následující iniciativy:

* **Monitorování analýzy hrozeb**: Analýza hrozeb zahrnuje mechanismy, ukazatele, důsledky a praktické rady týkající se stávajících nebo nově vznikajících hrozeb. Tyto informace se sdílí v bezpečnostní komunitě a společnost Microsoft kanály analýzy hrozeb z interních i externích zdrojů nepřetržitě monitoruje.
* **Sdílení signálu**: Informace od týmu zabezpečení z širokého portfolia cloudových a místních služeb a serverů společnosti Microsoft a z klientských koncových zařízení se sdílí a analyzují. 
* **Odborníci na zabezpečení společnosti Microsoft**: Průběžné zapojování týmů v rámci společnosti Microsoft, které pracují ve specializovaných oblastech zabezpečení, například forenzní analýza nebo detekce webových útoků.
* **Optimalizace detekce**: V datových sadách reálných zákazníků se spouští algoritmy a výzkumníci z oblasti bezpečnosti pracují se zákazníky na ověřování výsledků. Pravdivě a falešně pozitivní výsledky pak slouží ke zlepšování algoritmů strojového učení.

Toto celkové úsilí přineslo nové a vylepšené způsoby detekce, ze kterých budete mít okamžitý prospěch – a nemusíte sami nic dělat.

## <a name="see-also"></a>Viz také
V tomto dokumentu jste zjistili, jak ve službě Azure Security Center funguje detekce. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Výstrahy zabezpečení podle typu ve službě Azure Security Center](security-center-alerts-type.md)
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

