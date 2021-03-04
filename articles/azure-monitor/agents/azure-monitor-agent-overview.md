---
title: Přehled agenta Azure Monitor
description: Přehled agenta Azure Monitor (AMA), který shromažďuje data monitorování z hostovaného operačního systému virtuálních počítačů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e4837de70e9f00308b440933e0cd433ad5b27cf9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711531"
---
# <a name="azure-monitor-agent-overview-preview"></a>Přehled agenta Azure Monitor (Preview)
Agent Azure Monitor (AMA) shromažďuje data monitorování z hostovaného operačního systému virtuálních počítačů a doručuje je do Azure Monitor. V tomto článku najdete přehled agenta Azure Monitor, včetně postupu jeho instalace a konfigurace shromažďování dat.

## <a name="relationship-to-other-agents"></a>Vztah k jiným agentům
Agent Azure Monitor nahrazuje následující agenty, které jsou aktuálně používány nástrojem Azure Monitor ke shromažďování dat hostů z virtuálních počítačů:

- [Agent Log Analytics](./log-analytics-agent.md) – odesílá data do pracovního prostoru Log Analytics a podporuje řešení pro přehled a monitorování virtuálních počítačů.
- [Diagnostické rozšíření](./diagnostics-extension-overview.md) – odesílá data do Azure monitor metrik (jenom Windows), Azure Event Hubs a Azure Storage.
- [Agent telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) – odesílá data do Azure monitor metrik (jenom Linux).

Kromě toho, že se tato funkce konsoliduje do jednoho agenta, přináší agent Azure Monitor v rámci stávajících agentů následující výhody:

- Rozsah monitorování. Centrálně konfigurujte kolekci pro různé sady dat z různých sad virtuálních počítačů.  
- Linux více domovských stránek: odesílat data z virtuálních počítačů se systémem Linux do několika pracovních prostorů.
- Filtrování událostí systému Windows: pomocí dotazů XPATH můžete filtrovat, které události systému Windows jsou shromažďovány.
- Vylepšená správa rozšíření: Agent Azure Monitor používá novou metodu pro zpracování rozšiřitelnosti, která je lépe transparentní a ovladatelné než sady Management Pack a moduly plug-in Linux v aktuálních agentech Log Analytics.

### <a name="changes-in-data-collection"></a>Změny v kolekci dat
Metody pro definování shromažďování dat pro stávající agenty se liší od sebe a každá z nich má problémy řešené agentem Azure Monitor.

- Agent Log Analytics získá svoji konfiguraci z pracovního prostoru Log Analytics. To je snadné – centrálně konfigurujte, ale obtížně definujte nezávislé definice pro různé virtuální počítače. Může odesílat data pouze do pracovního prostoru Log Analytics.

- Diagnostické rozšíření má konfiguraci pro každý virtuální počítač. Díky tomu je snadné definovat nezávislé definice pro různé virtuální počítače, ale obtížné centrálně spravovat. Data může posílat jenom Azure Monitor metriky, Azure Event Hubs nebo Azure Storage. Pro agenty Linux se k posílání dat Azure Monitorch metrik vyžaduje agent Open Source telegraf.

Agent Azure Monitor používá [pravidla shromažďování dat (DCR)](data-collection-rule-overview.md) ke konfiguraci dat, která se mají shromažďovat od každého agenta. Pravidla shromažďování dat umožňují spravovatelnost nastavení kolekce ve velkém měřítku a zároveň přitom povolují jedinečné a vymezené konfigurace pro podmnožiny počítačů. Jsou nezávislé na pracovním prostoru a nezávisle na virtuálním počítači, což umožňuje jejich definování a jejich použití v různých počítačích a prostředích. Viz téma [Konfigurace shromažďování dat pro agenta Azure monitor (Preview)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Mám přepnout na agenta Azure Monitor?
Agent Azure Monitor spolupracuje s [všeobecně dostupnými agenty pro Azure monitor](agents-overview.md), ale můžete zvážit přechod virtuálních počítačů z aktuálních agentů během období verze preview agenta Azure monitor. Při provádění tohoto rozhodnutí Vezměte v úvahu následující faktory.

- **Požadavky prostředí.** Agent Azure Monitor má více omezené sady podporovaných operačních systémů, prostředí a požadavků na síť než aktuální agenti. Budoucí podpora prostředí, jako jsou například nové verze operačního systému a typy požadavků na síť, budou pravděpodobně poskytovány pouze v agentovi Azure Monitor. Azure Monitor Agent byste měli vyhodnotit, jestli je vaše prostředí podporované. Pokud ne, budete muset zůstat s aktuálním agentem. Pokud agent Azure Monitor podporuje vaše současné prostředí, měli byste zvážit přechod na něj.
- **Tolerance rizika veřejné verze Preview.** I když byl agent Azure Monitor důkladně testován pro aktuálně podporované scénáře, je agent stále ve verzi Public Preview. Aktualizace verzí a vylepšení funkcí se často projeví a můžou vést k chybám. Měli byste vyhodnotit riziko chyby v agentovi na virtuálních počítačích, které by mohly zastavit shromažďování dat. Pokud by mezera v kolekci dat nevýznamně ovlivnila vaše služby, pokračujte Azure Monitor agenta. Pokud máte nízkou toleranci pro jakoukoli nestabilitu, měli byste zůstat s obecně dostupnými agenty, dokud agent Azure Monitor nedosáhne tohoto stavu.
- **Aktuální a nové požadavky na funkce.** Agent Azure Monitor přináší několik nových funkcí, jako je filtrování, rozsah a vícenásobné navýšení, ale u současných agentů ještě není paritou pro jiné funkce, jako je například vlastní shromažďování protokolů a integrace s řešeními. Většina nových funkcí v Azure Monitor bude k dispozici pouze u agenta Azure Monitor, takže v průběhu času budou více funkcí k dispozici pouze v novém agentovi. Měli byste zvážit, zda má Azure Monitor Agent funkce, které požadujete, a pokud jsou k dispozici některé funkce, které můžete dočasně provádět bez dalších důležitých funkcí v novém agentovi. Pokud Azure Monitor Agent má všechny základní možnosti, které požadujete, zvažte přechod na něj. Pokud jsou důležité funkce, které požadujete, pokračujte v aktuálním agentovi, dokud agent nedosáhne Azure Monitor.
- **Tolerance pro repráci** Pokud vytváříte nové prostředí s prostředky, jako jsou skripty pro nasazení a šablony zprovoznění, měli byste zvážit, jestli budete moct tyto informace přepracovat, když se agent Azure Monitor stane všeobecně dostupným. Pokud bude úsilí pro tuto repráci minimální, zajistěte si aktuální agenty v současnosti. Pokud bude trvat hodně práce, zvažte nastavení nového prostředí pomocí nového agenta. Očekává se, že agent Azure Monitor je všeobecně dostupný a datum vyřazení je publikované pro agenty Log Analytics v 2021. Po zahájení vyřazení budou aktuální agenti podporováni po dobu několika let.



## <a name="current-limitations"></a>Aktuální omezení
Během veřejné verze Preview agenta Azure Monitor platí následující omezení:

- Agent Azure Monitor nepodporuje řešení a přehledy, jako jsou třeba přehledy virtuálních počítačů a Azure Security Center. Jediným scénářem, který se aktuálně podporuje, je shromažďování dat pomocí pravidel shromažďování dat, která nakonfigurujete. 
- Pravidla shromažďování dat musí být vytvořena ve stejné oblasti jako kterýkoli Log Analytics pracovní prostor, který se používá jako cíl.
- Virtuální počítače Azure, služba Virtual Machine Scale Sets a servery s podporou ARC Azure jsou aktuálně podporované. Služba Azure Kubernetes a jiné typy výpočetních prostředků se aktuálně nepodporují.
- Virtuální počítač musí mít přístup k následujícím koncovým bodům HTTPS:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Koexistence s ostatními agenty
Agent Azure Monitor může společně s existujícími agenty fungovat, abyste mohli nadále používat stávající funkce během hodnocení nebo migrace. To je důležité hlavně z důvodu omezení ve verzi Public Preview v části Podpora stávajících řešení. Měli byste být opatrní při shromažďování duplicitních dat, protože by to mohlo zkosit výsledky dotazu a mít za následek další poplatky za přijímání a uchovávání dat.

Například virtuální počítač Insights používá agenta Log Analytics k posílání údajů o výkonu do pracovního prostoru Log Analytics. Můžete také nakonfigurovat pracovní prostor pro shromažďování událostí systému Windows a událostí protokolu syslog od agentů. Pokud nainstalujete agenta Azure Monitor a vytvoříte pravidlo shromažďování dat pro tyto stejné události a data o výkonu, bude výsledkem duplicitní data.


## <a name="costs"></a>Náklady
Pro agenta Azure Monitor se neúčtují žádné náklady, ale za ingestování dat vám můžou být účtovány poplatky. Podrobnosti o Log Analytics Shromažďování a uchovávání dat a zákaznických metrikách najdete v článku o [cenách Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="data-sources-and-destinations"></a>Zdroje dat a cíle
V následující tabulce jsou uvedeny typy dat, které můžete v současnosti shromažďovat s agentem Azure Monitor pomocí pravidel shromažďování dat a kam můžete tato data odeslat. Podívejte se na téma [co je monitorované pomocí Azure monitor?](../monitor-reference.md) v seznamu přehledů, řešení a dalších řešení, která používají agenta Azure monitor ke shromažďování dalších typů dat.


Agent Azure Monitor odesílá data do Azure Monitor metrik nebo Log Analytics pracovní prostor podporující protokoly Azure Monitor.

| Zdroj dat | Cíle | Popis |
|:---|:---|:---|
| Výkon        | Metriky Azure Monitoru<br>Pracovní prostor služby Log Analytics | Číselné hodnoty, které měří výkon různých aspektů operačního systému a zatížení. |
| Protokoly událostí systému Windows | Pracovní prostor služby Log Analytics | Informace odesílané systému protokolování událostí systému Windows. |
| Syslog             | Pracovní prostor služby Log Analytics | Informace odesílané systému protokolování událostí pro Linux. |


## <a name="supported-operating-systems"></a>Podporované operační systémy
Seznam verzí operačních systémů Windows a Linux, které jsou aktuálně podporované agentem Azure Monitor, najdete v části [podporované operační systémy](agents-overview.md#supported-operating-systems) .



## <a name="security"></a>Zabezpečení
Agent Azure Monitor nepotřebuje žádné klíče, ale místo toho vyžaduje [spravovanou identitu přiřazenou systémem](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Před nasazením agenta musíte mít na každém virtuálním počítači povolenou spravovanou identitu přiřazenou systémem.

## <a name="networking"></a>Sítě
Agent Azure Monitor podporuje značky služeb Azure (vyžadují se značky AzureMonitor a AzureResourceManager), ale ještě nepracuje s Azure Monitormi obory privátních odkazů nebo přímými proxy servery.


## <a name="next-steps"></a>Další kroky

- [Nainstalujte agenta Azure monitor](azure-monitor-agent-install.md) na virtuální počítače se systémem Windows a Linux.
- [Vytvořte pravidlo shromažďování dat](data-collection-rule-azure-monitor-agent.md) pro shromažďování dat z agenta a jeho odeslání na Azure monitor.