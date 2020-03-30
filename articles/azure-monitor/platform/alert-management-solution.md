---
title: Řešení správy výstrah v Azure Log Analytics | Dokumenty společnosti Microsoft
description: Řešení správy výstrah v Log Analytics vám pomůže analyzovat všechny výstrahy ve vašem prostředí.  Kromě konsolidace výstrah generovaných v rámci analýzy protokolů importuje výstrahy z připojených skupin správy Nástroje pro operations managery systému System Center do analýzy protokolů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668379"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Řešení správy výstrah v Azure Log Analytics

![Ikona Správa výstrah](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor teď podporuje rozšířené funkce pro [správu výstrah ve velkém měřítku](https://aka.ms/azure-alerts-overview), včetně těch, které generují [nástroje pro monitorování, jako je System Center Operations Manager, Zabbix nebo Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


Řešení správy výstrah vám pomůže analyzovat všechny výstrahy v úložišti Analýzy protokolů.  Tyto výstrahy mohou pocházet z různých zdrojů, včetně těch zdrojů [vytvořených Log Analytics](../../azure-monitor/platform/alerts-overview.md) nebo [importované z Nagios nebo Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). Řešení také importuje výstrahy ze všech [připojených skupin pro správu nástroje Operations Center Operations Manager](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Požadavky
Řešení pracuje s libovolnými záznamy v úložišti Log Analytics s typem **výstrahy**, takže je nutné provést libovolnou konfiguraci, která je požadována ke shromažďování těchto záznamů.

- Pro výstrahy Log Analytics [vytvořte pravidla výstrah](../../azure-monitor/platform/alerts-overview.md) pro vytvoření záznamů výstrah přímo v úložišti.
- Pro výstrahy Nagios a Zabbix [nakonfigurujte tyto servery](../../azure-monitor/learn/quick-collect-linux-computer.md) tak, aby odesílalo výstrahy do log analytics.
- U výstrah nástroje Operations Manager systémového centra [připojte skupinu pro správu nástroje Operations Manager k pracovnímu prostoru Analýzy protokolů](../../azure-monitor/platform/om-agents.md).  Všechny výstrahy vytvořené v nástroji System Center Operations Manager se importují do analýzy protokolů.  

## <a name="configuration"></a>Konfigurace
Přidejte řešení správy výstrah do pracovního prostoru Log Analytics pomocí procesu popsaného v části [Přidat řešení](../../azure-monitor/insights/solutions.md). Není nutná žádná další konfigurace.

## <a name="management-packs"></a>Sady Management Pack
Pokud je vaše skupina pro správu Nástroje pro správu operations manageru system center připojena k pracovnímu prostoru Analýzy protokolů, jsou při přidání tohoto řešení nainstalovány následující sady Management Pack v nástroji System Center Operations Manager.  Neexistuje žádná konfigurace nebo údržba sad management Pack.

* Správa výstrah poradce systémového centra společnosti Microsoft (Microsoft.IntelligencePacks.AlertManagement)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Shromažďování dat
### <a name="agents"></a>Agenti
Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|:--- |:--- |:--- |
| [Agenti systému Windows](agent-windows.md) | Ne |Přímí agenti systému Windows negenerují výstrahy.  Výstrahy Log Analytics lze vytvářet z událostí a údajů o výkonu shromážděných od agentů systému Windows. |
| [Agenti systému Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Ne |Agenti Direct Linux negenerují výstrahy.  Log Analytics výstrahy lze vytvořit z událostí a údaje o výkonu shromážděné z agentů Linuxu.  Výstrahy Nagios a Zabbix jsou shromažďovány z těch serverů, které vyžadují agenta Linuxu. |
| [Skupina pro správu nástroje System Center Operations Manager](../../azure-monitor/platform/om-agents.md) |Ano |Výstrahy, které jsou generovány na agenty Operations Manager jsou doručovány do skupiny pro správu a pak předány do Log Analytics.<br><br>Přímé připojení agentů Operations Manageru k log analytics není vyžadováno. Data výstrah se předávají ze skupiny pro správu do úložiště Log Analytics. |


### <a name="collection-frequency"></a>Četnost shromažďování dat
- Záznamy výstrah jsou k dispozici řešení, jakmile jsou uloženy v úložišti.
- Data výstrah se odesílají ze skupiny správy nástroje Operations Manager do služby Log Analytics každé tři minuty.  

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení správy výstrah do pracovního prostoru Analýzy protokolů, přidá se na řídicí panel dlaždice **Správa výstrah.**  Tato dlaždice zobrazuje počet a grafické znázornění počtu aktuálně aktivních výstrah, které byly vygenerovány během posledních 24 hodin.  Tento časový rozsah nelze změnit.

![Dlaždice Správa výstrah](media/alert-management-solution/tile.png)

Kliknutím na dlaždici **Správa výstrah** otevřete řídicí panel **Správa výstrah.**  Řídicí panel obsahuje sloupce v následující tabulce.  V každém sloupci je uvedeno prvních 10 výstrah podle počtu, který odpovídá kritériím tohoto sloupce pro zadaný rozsah a časový rozsah.  Hledání protokolu, které poskytuje celý seznam, můžete spustit kliknutím na **Zobrazit vše** v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--- |:--- |
| Kritická upozornění |Všechny výstrahy se závažností kritické seskupeny podle názvu výstrahy.  Kliknutím na název výstrahy spustíte vyhledávání v protokolu, které vrátí všechny záznamy pro tuto výstrahu. |
| Výstražná upozornění |Všechny výstrahy se závažností upozornění seskupené podle názvu výstrahy.  Kliknutím na název výstrahy spustíte vyhledávání v protokolu, které vrátí všechny záznamy pro tuto výstrahu. |
| Výstrahy nástroje Active System Center Operations Manager |Všechny výstrahy shromážděné z Operations Manager s libovolným státem než *Uzavřeno* seskupeny podle zdroje, který vygeneroval výstrahu. |
| Všechny aktivní výstrahy |Všechny výstrahy s jakoukoli závažností seskupené podle názvu výstrahy. Zahrnuje pouze výstrahy nástroje Operations Manager s jiným stavem než *Uzavřeno*. |

Pokud se posunete doprava, řídicí panel zobrazí několik běžných dotazů, na které můžete kliknout a provést vyhledávání dat výstrah v [protokolu.](../../azure-monitor/log-query/log-query-overview.md)

![Řídicí panel správy výstrah](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Řešení správy výstrah analyzuje všechny záznamy s typem **výstrahy**.  Výstrahy vytvořené službou Log Analytics nebo shromážděné z Nagios nebo Zabbix nejsou přímo shromažďovány řešením.

Řešení provádí import výstrahy z Nástroje pro operations manager system center a vytvoří odpovídající záznam pro každý s typem **výstrahy** a SourceSystem of **OpsManager**.  Tyto záznamy mají vlastnosti v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |*Výstraha* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Podrobnosti o datové položce, která způsobila generování výstrahy ve formátu XML. |
| `AlertDescription` |Podrobný popis výstrahy. |
| `AlertId` |IDENTIFIKÁTOR GUID výstrahy. |
| `AlertName` |Název výstrahy. |
| `AlertPriority` |Úroveň priority výstrahy. |
| `AlertSeverity` |Úroveň závažnosti výstrahy. |
| `AlertState` |Poslední stav rozlišení výstrahy. |
| `LastModifiedBy` |Jméno uživatele, který výstrahu naposledy upravil. |
| `ManagementGroupName` |Název skupiny pro správu, kde byla výstraha vygenerována. |
| `RepeatCount` |Počet, kolikrát byla pro stejný sledovaný objekt od vyřešení vygenerována stejná výstraha. |
| `ResolvedBy` |Jméno uživatele, který výstrahu vyřešil. Pokud výstraha ještě nebyla vyřešena, vyprázdněte ji. |
| `SourceDisplayName` |Zobrazovaný název objektu monitorování, který výstrahu vygeneroval. |
| `SourceFullName` |Úplný název objektu monitorování, který vygeneroval výstrahu. |
| `TicketId` |ID lístku pro výstrahu, pokud je prostředí System Center Operations Manager integrováno s procesem přiřazování lístků pro výstrahy.  Prázdné bez ID lístku je přiřazeno. |
| `TimeGenerated` |Datum a čas vytvoření výstrahy. |
| `TimeLastModified` |Datum a čas, kdy byla výstraha naposledy změněna. |
| `TimeRaised` |Datum a čas, kdy byla výstraha vygenerována. |
| `TimeResolved` |Datum a čas, kdy byla výstraha vyřešena. Pokud výstraha ještě nebyla vyřešena, vyprázdněte ji. |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
Následující tabulka obsahuje ukázkové hledání záznamů výstrah shromážděných tímto řešením: 

| Dotaz | Popis |
|:---|:---|
| Výstraha &#124; kde SourceSystem == "OpsManager" a AlertSeverity == "error" a TimeRaised > ago(24h) |Kritická upozornění vznesená během posledních 24 hodin |
| Výstraha &#124; kde AlertSverity == "upozornění" a TimeRaised > ago(24h) |Výstražná upozornění na upozornění získaná během posledních 24 hodin |
| Výstraha &#124; kde SourceSystem == "OpsManager" a AlertState != "Uzavřeno" a TimeRaised > ago(24h) &#124; shrnout Count = count() podle SourceDisplayName |Zdroje s aktivními výstrahami nazvanými během posledních 24 hodin |
| Výstraha &#124; kde SourceSystem == "OpsManager" a AlertSeverity == "error" a TimeRaised > ago(24h) a AlertState != "Uzavřeno" |Kritická upozornění vznesená během posledních 24 hodin, která jsou stále aktivní |
| Výstraha &#124; kde SourceSystem == "OpsManager" a TimeRaised > ago(24h) a AlertState == "Uzavřeno" |Upozornění vznesená během posledních 24 hodin, která jsou nyní uzavřena |
| Výstraha &#124; kde SourceSystem == "OpsManager" a TimeRaised > ago(1d) &#124; sumarizovat Count = count() podle AlertSeverity |Výstrahy vznesené během posledního 1 dne seskupené podle jejich závažnosti |
| Upozornění &#124; kde SourceSystem == "OpsManager" a TimeRaised > ago(1d) &#124; řazení podle RepeatCount desc |Výstrahy vyvolané během posledního 1 dne seřazené podle hodnoty počtu opakování |



## <a name="next-steps"></a>Další kroky
* Podrobnosti o generování upozornění ze služby Log Analytics najdete v tématu [Upozornění v Log Analytics](../../azure-monitor/platform/alerts-overview.md).
