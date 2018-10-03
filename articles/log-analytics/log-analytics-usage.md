---
title: Analýza využití dat v Log Analytics | Dokumentace Microsoftu
description: Použijte řídicí panel Využití a odhad nákladů v Log Analytics k vyhodnocení objemu dat odesílaných do Log Analytics a identifikaci možných příčin jejich neočekávaných nárůstů.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 8039a660a0f9fc94471e4b3e28b1b00bfb0d6b9e
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042441"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analýza využití dat v Log Analytics

> [!NOTE]
> Tento článek popisuje, jak analyzovat využití dat v Log Analytics.  Přečtěte si následující články související informace.
> - [Spravovat náklady pomocí řízení objemu dat a uchovávání dat v Log Analytics](log-analytics-manage-cost-storage.md) popisuje, jak řídit své náklady tak, že změníte vaše data uchovávají.
> - [Monitorování využití a odhadované náklady](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

Log Analytics obsahuje informace o objemu shromážděných dat, zdrojích odesílajících data a různých typech odesílaných dat.  Využití dat můžete zkontrolovat a analyzovat pomocí řídicího panelu **Využití Log Analytics**. Řídicí panel ukazuje, kolik dat shromažďují jednotlivá řešení a kolik dat odesílají vaše počítače.

## <a name="understand-the-usage-dashboard"></a>Vysvětlení řídicího panelu Využití
Řídicí panel **Využití Log Analytics** obsahuje tyto informace:

- Objem dat
    - Objem dat v průběhu času (v závislosti na vašem aktuálním časovém rozsahu)
    - Objem dat podle řešení
    - Data, která nejsou spojena s počítačem
- Počítače
    - Počítače odesílající data
    - Počítače, které během posledních 24 hodin neodeslaly žádná data
- Nabídky
    - Uzly Insight and Analytics
    - Uzly Automation and Control
    - Uzly zabezpečení  
- Výkon
    - Čas potřebný k shromáždění a indexaci dat  
- Seznam dotazů

![Řídicí panel Využití a náklady](./media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>
)

### <a name="to-work-with-usage-data"></a>Práce s daty o využití
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.<br><br> ![Azure Portal](./media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor.
4. V seznamu v levém podokně vyberte **Využití a odhad nákladů**.
5. Na řídicím panelu **Využití a odhad nákladů** můžete upravit časový rozsah výběrem možnosti **Čas: Posledních 24 hodin** a změnou časového intervalu.<br><br> ![časový interval](./media/log-analytics-usage/usage-time-filter-01.png)<br><br>
6. Zobrazte okna kategorií využití s oblastmi, které vás zajímají. Vyberte okno a pak v něm klikněte na některou položku, abyste zobrazili další podrobnosti v [Prohledávání protokolů](log-analytics-log-searches.md).<br><br> ![příklad klíčového indikátoru výkonnosti využití dat](media/log-analytics-usage/data-volume-kpi-01.png)<br><br>
7. Na řídicím panelu Prohledávání protokolů zkontrolujte vrácené výsledky hledání.<br><br> ![příklad prohledávání protokolů využití](./media/log-analytics-usage/usage-log-search-01.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Vytvoření upozornění při větším než očekávaném shromažďování dat
Tato část popisuje postup vytvoření upozornění v těchto případech:
- Objem dat překračuje zadanou velikost.
- Očekává se, že objem dat překročí zadanou velikost.

Upozornění Azure podporují [upozornění protokolu](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) využívající vyhledávací dotazy. 

Následující dotaz vrátí výsledek, pokud se za posledních 24 hodin shromáždilo více než 100 GB dat:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Následující dotaz pomocí jednoduchého vzorce předvídá, jestli dojde k odeslání více než 100 GB dat za den: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Pokud chcete upozornit na jiný objem dat, změňte v dotazech hodnotu 100 na počet GB, na který chcete upozornit.

Pokud chcete být upozorňováni při větším než očekávaném shromažďování dat, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

Při vytváření upozornění pro první dotaz (více než 100 GB dat během 24 hodin) nastavte:  

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** na *1440* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](../monitoring-and-diagnostics/monitoring-action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Při vytváření upozornění pro druhý dotaz (předpověď, že během 24 hodin bude shromážděno více než 100 GB dat) nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** na *180* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Očekávaný větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](../monitoring-and-diagnostics/monitoring-action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Pokud obdržíte upozornění, pomocí kroků v následující části můžete řešit potíže způsobující větší využití, než se čekalo.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Řešení potíží způsobujících větší využití, než se čekalo
Řídicí panel Využití vám pomůže určit, proč je využití (a tedy i náklady) větší, než očekáváte.

Větší využití je způsobeno jedním nebo obojím z těchto aspektů:
- Do Log Analytics se odesílá více dat, než se čekalo.
- Do Log Analytics odesílá data více uzlů, než se čekalo.

### <a name="check-if-there-is-more-data-than-expected"></a>Kontrola, jestli se shromažďuje více dat, než se čekalo 
Na stránce Využití jsou dvě klíčové části, které pomáhají určit, co způsobuje shromažďování většího objemu dat.

Graf *Objem dat v průběhu času* ukazuje celkový objem odeslaných dat a počítače odesílající nejvíce dat. V grafu v horní části můžete vidět, jestli vaše celkové využití dat roste, je konstantní nebo se snižuje. Seznam počítačů ukazuje prvních 10 počítačů odesílajících nejvíce dat.

Graf *Objem dat podle řešení* ukazuje objem dat, který odesílají jednotlivá řešení, a řešení odesílající nejvíce dat. Graf v horní části ukazuje celkový objem dat odesílaných jednotlivými řešeními v průběhu času. Pomocí těchto informací můžete určit, jestli řešení v průběhu času odesílá více dat, zhruba stejné množství dat nebo méně dat. Seznam řešení ukazuje prvních 10 řešení odesílajících nejvíce dat. 

Tyto dva grafy zobrazují veškerá data. Některá data jsou fakturovatelná, ostatní jsou zdarma. Pokud se chcete zaměřit pouze na data, která jsou fakturovatelná, změňte dotaz na vyhledávací stránce tak, aby zahrnoval `IsBillable=true`.  

![Grafy objemu dat](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Podívejte se na graf *Objem dat v průběhu času*. Pokud chcete zobrazit datové typy a řešení odesílající nejvíce dat z konkrétního počítače, klikněte na název počítače. Klikněte na název prvního počítače v seznamu.

Na následujícím snímku obrazovky odesílá z počítače nejvíce dat datový typ *Správa protokolů / Výkon*.<br><br> ![objem dat pro počítač](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

Potom se vraťte na řídicí panel *Využití* a podívejte se na graf *Objem dat podle řešení*. Pokud chcete zobrazit počítače odesílající nejvíce dat pro řešení, klikněte na název řešení v seznamu. Klikněte na název prvního řešení v seznamu. 

Na následujícím snímku obrazovky je potvrzení, že počítač *mycon* odesílá nejvíce dat pro řešení Správa protokolů.<br><br> ![objem dat pro řešení](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

V případě potřeby proveďte další analýzy k identifikaci velkých objemů v rámci řešení nebo datového typu. Ukázky dotazů:

+ Řešení **zabezpečení**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Řešení **pro správu protokolů**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Datový typ **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Datový typ **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Datový typ **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Datový typ **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

Pomocí následujících kroků snižte objem shromažďovaných protokolů:

| Zdroj velkého objemu dat | Postup snížení objemu dat |
| -------------------------- | ------------------------- |
| Události zabezpečení            | Vyberte [běžné nebo minimální události zabezpečení](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Změňte zásady auditu zabezpečení tak, aby se shromažďovaly jenom potřebné události. Zaměřte se hlavně na potřebu shromažďovat události pro <br> - [audit platformy Filtering Platform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [audit registru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [audit systému souborů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [audit objektu jádra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [audit manipulace s popisovačem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -audit vyměnitelného úložiště |
| Čítače výkonu       | Změňte [konfiguraci čítačů výkonu](log-analytics-data-sources-performance-counters.md) tak, aby se: <br> – Snížila četnost shromažďování dat <br> – Snížil počet čítačů výkonu |
| Protokoly událostí                 | Změňte [konfiguraci protokolů událostí](log-analytics-data-sources-windows-events.md) tak, aby se: <br> – Snížil počet shromažďovaných protokolů událostí <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace*. |
| Syslog                     | Změňte [konfiguraci syslogu](log-analytics-data-sources-syslog.md) tak, aby se: <br> – Snížil počet zařízení, ze kterých se shromažďují data <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace* a *Ladění*. |
| AzureDiagnostics           | Změňte shromažďování protokolů prostředků tak, aby se: <br> – Snížil počet prostředků, které odesílají protokoly do Log Analytics <br> – Shromažďovaly pouze požadované protokoly |
| Data řešení z počítačů, které řešení nepotřebují | Použijte [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Kontrola, jestli existuje více uzlů, než se čekalo
Pokud jste na cenové úrovni *za uzel (OMS)*, pak se vám bude účtovat v závislosti na počtu používaných uzlů a řešení. V části *nabídek* řídicího panelu využití můžete zobrazit, kolik uzlů každé nabídky se používá.<br><br> ![řídicí panel využití](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>

Kliknutím na **Zobrazit všechno...** zobrazte úplný seznam počítačů odesílajících data do vybrané nabídky.

Použijte [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů.

## <a name="next-steps"></a>Další postup
* V tématu [Prohledávání protokolů v Log Analytics](log-analytics-log-searches.md) zjistíte, jak používat jazyk vyhledávání. Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
* Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).
* Použijte [cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů.
* Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí zabezpečení, přečtěte si téma popisující [zásady filtrování v Azure Security Center](../security-center/security-center-enable-data-collection.md).
* Změňte [konfiguraci čítačů výkonu](log-analytics-data-sources-performance-counters.md).
* Pokud chcete upravit nastavení shromažďování událostí, přečtěte si téma popisující [konfiguraci protokolu událostí](log-analytics-data-sources-windows-events.md).
* Pokud chcete upravit nastavení shromažďování syslogu, přečtěte si téma popisující [konfiguraci syslogu](log-analytics-data-sources-syslog.md).
