---
title: Monitorování systému pro zabezpečení v Austrálii Azure
description: Pokyny ke konfiguraci monitorování systému v rámci australských oblastí pro splnění konkrétních požadavků na zásady australské vlády, předpisy a právní předpisy.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779324"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Monitorování systému pro zabezpečení v Austrálii Azure

Vysoce výkonné provozní zabezpečení vašich IT prostředí, včetně cloudu, je důležité mít robustní strategie zabezpečení, které zahrnují monitorování a běžné hodnocení zabezpečení v reálném čase.

Zabezpečení cloudu je společné úsilí mezi zákazníkem a poskytovatelem cloudu. Existují čtyři služby, které Microsoft Azure poskytují pro usnadnění těchto požadavků s ohledem na doporučení obsažená v [centru informující o ručních ovládacích prvcích zabezpečení (acsc) australského centra zabezpečení (](https://acsc.gov.au/infosec/ism/index.htm) ISM). konkrétně implementace centralizovaného protokolování událostí, auditování protokolu událostí a posouzení ohrožení zabezpečení a správy. Služba Microsoft Azure Services:

* Azure Security Center
* Azure Monitor
* Azure Advisor
* Azure Policy

ACSC doporučuje používat tyto služby k chráněným datům . Pomocí těchto služeb můžete aktivně monitorovat a analyzovat vaše IT prostředí a dělat kvalifikovaná rozhodnutí o tom, kde nejlépe přidělovat prostředky pro zvýšení zabezpečení. Každá z těchto služeb je součástí kombinovaného řešení, které vám poskytne nejlepší přehled, doporučení a možnost ochrany.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) poskytuje jednotnou konzolu pro správu zabezpečení, pomocí které můžete monitorovat a zdokonalovat zabezpečení prostředků Azure a hostovaných dat. Azure Security Center zahrnují zabezpečené skóre, skóre založené na analýze stavu konfigurace osvědčených postupů z Azure Advisor a celkovém dodržování předpisů Azure Policy.

Azure Security Center poskytuje zákazníkům Azure následující funkce:

* Zásady zabezpečení, vyhodnocení a doporučení
* Shromažďování a vyhledávání událostí zabezpečení
* Řízení přístupu a aplikací
* Pokročilá detekce hrozeb
* Řízení přístupu Virtual Machines za běhu
* Hybridní zabezpečení

Rozsah prostředků monitorovaných nástrojem Azure Security Center se dá rozšířit tak, aby zahrnoval podporované místní prostředky v prostředí hybridního cloudu. To zahrnuje místní prostředky, které jsou aktuálně monitorovány podporovanou verzí System Center Operations Manager.

Vrstva Security Center "Standard" poskytuje také cloudové kontroly zabezpečení, které vyžaduje ASD, který je nezbytný pro [8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm). Mezi ně patří přidávání aplikací do seznamu povolených aplikací a omezení oprávnění správce přes přístup za běhu.

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) je centralizované řešení protokolování pro všechny prostředky Azure a zahrnuje Log Analytics a Application Insights. Z prostředků Azure se shromažďují dva klíčové datové typy: protokoly a metriky. Po shromáždění v Azure Monitor můžou být informace o protokolování používány širokou škálou nástrojů a různými účely.

![Přehled služby Azure Monitor](media/overview.png)

Azure Monitor také obsahuje "protokol aktivit Azure". Protokol SActivity ukládá všechny události na úrovni předplatného, ke kterým došlo v rámci Azure. Umožňuje zákazníkům Azure zobrazit "kdo, co a kdy" za operací prováděných na svých prostředcích Azure. Protokolování na základě prostředků odeslaných do Azure Monitor a událostí protokolu aktivit Azure se dá analyzovat pomocí integrovaného dotazovacího jazyka Kusto. Tyto protokoly se pak dají exportovat, použít k vytvoření vlastních řídicích panelů a zobrazení a nakonfigurovat je tak, aby se aktivovaly výstrahy a oznámení.

### <a name="azure-advisor"></a>Azure Advisor

Analýzy [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) v rámci předplatného Azure podporovaly prostředky Azure, soubory protokolů generované systémem a aktuální konfigurace prostředků. Analýza uvedená v Azure Advisor je vygenerována v reálném čase a založená na doporučených postupech společnosti Microsoft. Budou se analyzovat všechny podporované prostředky Azure přidané do vašeho prostředí a poskytnou se vhodná doporučení. Azure Advisor doporučení jsou rozdělená do čtyř kategorií osvědčených postupů:

* Zabezpečení
* Vysoká dostupnost
* Výkon
* Náklady

Doporučení zabezpečení vygenerovaná Azure Advisor tvoří část celkových analýz zabezpečení, kterou poskytuje Azure Security Center.

Informace shromažďované nástrojem Azure Advisor poskytují správcům nástroje:

* Přehled o konfiguraci prostředků, která nesplňuje Doporučené osvědčené postupy
* Doprovodné materiály týkající se konkrétních opravných akcí, které je potřeba provést
* Hodnocení udávající, které akce by se měly provádět s vysokou prioritou

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) poskytuje možnost použít pravidla, která řídí typy prostředků Azure a jejich povolené konfigurace. Zásady lze použít k řízení vytváření a konfigurace prostředků nebo k auditování nastavení konfigurace v rámci prostředí. Tyto výsledky auditu lze použít k vytvoření základu opravných aktivit. Azure Policy se liší od řízení přístupu na základě role (RBAC); Azure Policy slouží k omezení prostředků a jejich konfigurace, používá se RBAC k omezení privilegovaného přístupu k uživatelům Azure.

Bez ohledu na to, jestli se konkrétní zásady vynutily, nebo když se auditují zásady, dodržování předpisů se průběžně monitoruje a pro správce se poskytnou Obecné informace o dodržování předpisů pro konkrétní prostředky. Azure Policy údaje o dodržování předpisů jsou k dispozici pro Azure Security Center a Forms část zabezpečeného skóre.

## <a name="key-design-considerations"></a>Klíčové faktory návrhu

Při implementaci strategie protokolu událostí ACSC ISM zvýrazní následující požadavky:

* Centralizované Protokolovací zařízení
* Konkrétní události, které se mají protokolovat
* Ochrana protokolu událostí
* Uchování protokolu událostí
* Auditování protokolu událostí

Kromě shromažďování a správy protokolů společnost ISM také doporučuje rutinní posouzení ohrožení zabezpečení IT prostředí v organizaci.

### <a name="centralised-logging"></a>Centralizované protokolování

Jakékoli řešení protokolování by mělo všude, kde je to možné, konsolidovat zachycené protokoly do jediného úložiště dat. To nejen snižuje provozní složitost a brání vytvoření více dat, což umožňuje analyzovat data shromážděná z více zdrojů, aby bylo možné identifikovat jakékoli korelační události. To je důležité pro zjišťování a správu rozsahu všech případných incidentů zabezpečení.

Tento požadavek splní všechny zákazníky Azure s Azure Monitor. Tato nabídka poskytuje nejen centralizované úložiště protokolování v Azure pro všechny prostředky Azure, ale také umožňuje streamovat data do centra událostí Azure. Azure Event Hubs poskytuje plně spravovanou službu pro přijímání dat v reálném čase. Jakmile Azure Monitor dat streamování do centra událostí Azure, můžou být data taky snadno připojená k existujícím podporovaným úložištím zabezpečení a správě událostí (SIEM) a dalším nástrojům pro monitorování třetích stran.

Microsoft taky nabízí své vlastní řešení Azure s nativním SIEM, Azure Sentinel. Sentinel Azure podporuje širokou škálu datových konektorů a je možné ji použít k monitorování událostí zabezpečení napříč celým podnikem. Kombinací dat z podporovaných [datových konektorů](https://docs.microsoft.com/azure/sentinel/connect-data-sources), integrovaného strojového učení Azure Sentinel a dotazovacího jazyka Kusto jsou správci zabezpečení k dispozici s jediným řešením pro detekci výstrah, viditelnost hrozeb, proaktivní lov, a odpověď na hrozbu. Sentinel taky poskytuje funkci pro lovecké a poznámkové bloky, která správcům zabezpečení umožňuje zaznamenávat všechny kroky podniknuté jako součást šetření zabezpečení v opakovaně používaném PlayBook, které je možné sdílet v rámci organizace. Správci zabezpečení můžou dokonce pomocí integrovaného uživatelsky [Analytics](https://docs.microsoft.com/azure/sentinel/user-analytics) prozkoumat akce jednoho navrženého uživatele.

### <a name="logged-events-and-log-detail"></a>Protokolované události a podrobnosti protokolu

ISM poskytuje podrobný seznam typů protokolu událostí, které by měly být zahrnuty v jakékoli strategii protokolování. Všechny zachycené protokoly musí obsahovat dostatek podrobných údajů, které by měly být v provozu při analýze a vyšetřování.

Protokoly shromážděné v Azure spadají do jedné z následujících tří kategorií:

* **Protokoly řízení a správy**: Tyto protokoly poskytují informace o Azure Resource Manager operacích vytváření, aktualizace a odstraňování.

* **Protokoly roviny dat**: Ty obsahují události, které jsou vyvolány jako součást využití prostředků Azure. Zahrnuje zdroje, jako jsou protokoly událostí systému Windows, včetně systémů, zabezpečení a protokolů aplikací.

* **Zpracované události**: Tyto události obsahují informace o událostech a výstrahách, které byly automaticky zpracovány jménem zákazníka v rámci Azure. Příkladem zpracovávané události je Azure Security Center výstraha.

Monitorování virtuálních počítačů Azure je vylepšené nasazením agenta virtuálního počítače pro systémy Windows i Linux. Tato značka velmi zvyšuje množství shromážděných informací o protokolování. Nasazení tohoto agenta se dá nakonfigurovat tak, aby se automaticky probíhat prostřednictvím Azure Security Center.

Společnost Microsoft poskytuje podrobné informace o protokolech specifických pro prostředky Azure [](https://docs.microsoft.com/azure/security/fundamentals/log-audit)a jejich schématech.

### <a name="log-retention-and-protection"></a>Uchovávání protokolů a ochrana

 Protokoly událostí musí být uloženy zabezpečeně pro požadovanou dobu uchování. ISM doporučuje, aby se protokoly zachovaly minimálně sedm let. Azure poskytuje řadu způsobů, jak zajistit dlouhou životnost vašich shromážděných protokolů. Ve výchozím nastavení se události protokolu Azure ukládají po dobu 90 dnů. Data protokolu zachycená Azure Monitor lze přesunout a uložit na účet Azure Storage, jak je vyžadováno pro dlouhodobé uchovávání. Protokoly aktivit uložené v účtu Azure Storage se můžou uchovávat v nastaveném počtu dnů nebo v případě potřeby neomezeně.

Účty Azure Storage používané k ukládání událostí protokolu Azure můžou být geograficky redundantní a dají se zálohovat pomocí Azure Backup. Po zachycení Azure Backup bude jakékoli odstranění záloh obsahujících protokoly vyžadovat schválení správcem a zálohy označené k odstranění se pořád uchovávají po dobu 14 dní, která umožňuje obnovení. Azure Backup umožňuje pro 9999 kopie chráněné instance, která poskytuje více než 27 let denních záloh.

Pro řízení přístupu k prostředkům používaným pro protokolování Azure by se měla použít Access Control na základě rolí. Účty Azure Monitor, Azure Storage a zálohování Azure by měly být nakonfigurované pomocí řízení přístupu na základě rolí, aby se zajistilo zabezpečení dat obsažených v protokolech.

### <a name="log-auditing"></a>Auditování protokolu

Hodnota true protokolu je po analýze realizována. Pomocí automatizovaných i ručních analýz a znalostí dostupných nástrojů vám pomůže detekovat a spravovat porušení organizačních zásad zabezpečení a incidentů zabezpečení na Internet. Azure Monitor poskytuje bohatou sadu nástrojů pro analýzu shromážděných protokolů. Výsledek této analýzy se pak může sdílet mezi systémy, visualised nebo šíří v několika formátech.

Data protokolu uložená v Azure Monitor se uchovávají v pracovním prostoru Log Analytics. Veškerá analýza začíná dotazem. Do dotazovacího jazyka Kusto se napíší dotazy Azure Monitor. Dotazy tvoří základ všech výstupů z Azure Monitor z řídicích panelů Azure až po pravidla výstrah.

![Přehled dotazů protokolu Azure](media/queries-overview.png)

Auditování protokolů se dá pomocí řešení monitorování rozšířit. Jedná se o předbalená řešení, která obsahují logiku shromažďování, dotazy a zobrazení dat Visualisation. Společnost Microsoft [poskytuje](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) řadu řešení monitorování a další řešení od dodavatelů produktů najdete v Azure Marketplace.

### <a name="vulnerability-assessment-and-management"></a>Posouzení ohrožení zabezpečení a Správa

V této části jsou poznámení s běžným posouzením ohrožení zabezpečení a správou. Vaše IT prostředí se neustále vyvíjí a externí bezpečnostní hrozba se nekonečně mění. Pomocí Azure Security Center můžete provádět automatizované posuzování ohrožení zabezpečení a získat pokyny k plánování a provádění opravných aktivit.

V části zabezpečené skóre v Azure Security Center získáte seznam doporučení, která při použití budou zlepšit zabezpečení vašeho prostředí. Seznam je seřazený podle dopadu na celkové zabezpečené skóre od nejvyšší po nejnižší. Řazení seznamu podle dopadu vám umožní soustředit se na doporučení s nejvyšší prioritou, která při zvyšování zabezpečení prezentují největší hodnotu.

Azure Policy také hraje klíčovou část při probíhajícím posouzení ohrožení zabezpečení. Typy zásad, které jsou k dispozici v Azure Policy rozsahu od vynucování značek prostředků a hodnot, k omezení oblastí Azure, ve kterých je možné vytvořit prostředky, k tomu, aby se zcela blokovaly vytváření konkrétních typů prostředků. Sadu zásad Azure je možné seskupit do iniciativ. Iniciativa se používá k aplikování souvisejících zásad Azure, které se při použití společně jako skupiny tvoří základem konkrétního cíle zabezpečení nebo dodržování předpisů.

Azure Policy má knihovnu předdefinovaných definic zásad, které se neustále zvětšují. Azure Portal také nabízí možnost vytvářet vlastní definice Azure Policy. Po vyhledání zásady v existující knihovně nebo vytvoření nové můžete tuto zásadu přiřadit k prostředkům Azure. Tato přiřazení můžou být [vymezená](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) na různých úrovních v hierarchii správy prostředků. Přiřazení zásad je zděděné, což znamená, že všechny podřízené prostředky v rámci oboru obdrží stejné přiřazení zásad. Prostředky je taky možné z přiřazení vymezených zásad vyloučit, jak je potřeba.

Všechny nasazené zásady Azure přispívají k bezpečnostnímu skóre organizace. Ve vysoce Bespoke prostředí je možné vytvořit a nasadit vlastní definice Azure Policy a poskytnout tak informace o auditu na konkrétní úlohy.

## <a name="getting-started"></a>Začínáme

Aby bylo možné začít používat Azure Security Center a plně využívat Azure Monitor, poradce a zásad, společnost Microsoft doporučuje následující úvodní kroky:

* Povolit Azure Security Center
* Upgrade na úroveň Standard
* Povolení automatického zřizování Microsoft Monitoring Agent pro podporu Azure Virtual Machines
* Přečtěte si, prioritise a zmírnit doporučení a výstrahy zabezpečení na řídicím panelu Security Center.

## <a name="next-steps"></a>Další kroky

Podrobné informace o implementaci zásad správného řízení a řízení prostředků v Austrálii Azure pro zajištění dodržování předpisů a dodržování předpisů najdete v seAzure Policych [a Azure modrotisky](azure-policy.md) .
