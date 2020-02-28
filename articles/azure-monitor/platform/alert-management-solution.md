---
title: Řešení alert managementu ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Řešení pro správu upozornění v Log Analytics pomáhá analyzovat všechny výstrahy ve vašem prostředí.  Kromě konsolidace výstrahy generované v Log Analytics importuje výstrahy z připojených skupin pro správu System Center Operations Manager do Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668379"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Řešení alert managementu ve službě Azure Log Analytics

![Ikona upozornění správy](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor teď podporuje vylepšené funkce pro [správu výstrah ve velkém měřítku](https://aka.ms/azure-alerts-overview), včetně těch, které vygenerovaly [Nástroje pro monitorování, jako je System Center Operations Manager, Zabbix nebo Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


Správa výstrah řešení pomáhá analyzovat všechny výstrahy v úložišti Log Analytics.  Tyto výstrahy můžou pocházet z nejrůznějších zdrojů, včetně těchto zdrojů [vytvořených Log Analytics](../../azure-monitor/platform/alerts-overview.md) nebo [importovaných z Nagios nebo Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). Řešení také importuje výstrahy ze všech [připojených skupin pro správu System Center Operations Manager](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Požadavky
Řešení funguje se všemi záznamy v úložišti Log Analytics s typem **výstrahy**, takže musíte provést jakoukoli konfiguraci, která je nutná ke shromáždění těchto záznamů.

- Pro výstrahy Log Analytics [vytvořte pravidla upozornění](../../azure-monitor/platform/alerts-overview.md) pro vytváření záznamů výstrah přímo v úložišti.
- U upozornění Nagios a Zabbix [nakonfigurujte tyto servery](../../azure-monitor/learn/quick-collect-linux-computer.md) tak, aby odesílaly výstrahy Log Analytics.
- Pro výstrahy System Center Operations Manager [Připojte skupinu pro správu Operations Manager k vašemu pracovnímu prostoru Log Analytics](../../azure-monitor/platform/om-agents.md).  Všechny výstrahy vytvořené v nástroji System Center Operations Manager jsou importovány do Log Analytics.  

## <a name="configuration"></a>Konfigurace
Přidejte Alert Management řešení do svého pracovního prostoru Log Analytics pomocí postupu popsaného v tématu [Přidání řešení](../../azure-monitor/insights/solutions.md). Není nutná žádná další konfigurace.

## <a name="management-packs"></a>Sady Management Pack
Pokud vaší skupině pro správu System Center Operations Manageru je připojený k pracovnímu prostoru Log Analytics, jsou při přidání tohoto řešení následující sady management Pack nainstalované v nástroji System Center Operations Manager.  Neexistuje žádná konfigurace ani Údržba požadované sady management Pack.

* Microsoft System Center Advisor Správa výstrah (Microsoft.IntelligencePacks.AlertManagement)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Shromažďování dat
### <a name="agents"></a>Agenti
Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|:--- |:--- |:--- |
| [Agenti systému Windows](agent-windows.md) | Ne |Přímí agenti Windows nejsou generovány výstrahy.  Upozornění log Analytics je možné vytvořit z události a data o výkonu shromáždí z Windows agentů. |
| [Agenti systému Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Ne |Přímí agenti systému Linux negenerují výstrahy.  Upozornění log Analytics můžete vytvořit z události a údaje o výkonu shromážděná z agentů Linuxu.  Výstrahy Nagios a Zabbix se shromažďují z těchto serverů, které vyžadují agenta pro Linux. |
| [System Center Operations Manager skupina pro správu](../../azure-monitor/platform/om-agents.md) |Ano |Výstrahy, které jsou vytvořeny na agenty nástroje Operations Manager se doručí do skupiny pro správu a pak se předávají do Log Analytics.<br><br>Přímé připojení z agentů nástroje Operations Manager ke službě Log Analytics se nevyžaduje. Upozornění data se přesměrovávají ze skupiny pro správu do úložiště Log Analytics. |


### <a name="collection-frequency"></a>Četnost shromažďování dat
- Záznamy upozornění jsou k dispozici řešení, jako jsou uloženy v úložišti.
- Oznámení se odešlou ze skupiny pro správu Operations Manageru ke službě Log Analytics každé 3 minuty.  

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení Alert Management do pracovního prostoru Log Analytics, do řídicího panelu se přidá dlaždice **Alert Management** .  Na této dlaždici se zobrazuje počet a grafická reprezentace počet aktuálně aktivních výstrah, které byly generovány během posledních 24 hodin.  Nelze změnit tomto časovém rozsahu.

![Dlaždice výstrah správy](media/alert-management-solution/tile.png)

Kliknutím na dlaždici **Alert Management** otevřete řídicí panel **Alert Management** .  Řídicí panel obsahuje sloupce v následující tabulce.  Každý sloupec uvádí hlavní 10 upozornění podle počtu odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah.  Hledání v protokolu, které poskytuje celý seznam, můžete spustit kliknutím na **Zobrazit vše** v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--- |:--- |
| Důležitá upozornění |Všechny výstrahy se závažností kritický seskupené podle názvu upozornění.  Klikněte na název výstrahy. ke spuštění hledání v protokolu všechny záznamy pro tuto výstrahu. |
| Upozorňující výstrahy |Všechny výstrahy se závažností upozornění seskupené podle názvu upozornění.  Klikněte na název výstrahy. ke spuštění hledání v protokolu všechny záznamy pro tuto výstrahu. |
| Výstrahy Active System Center Operations Manager |Všechny výstrahy shromážděné z Operations Manager s jakýmkoli jiným stavem než *uzavřeným* seskupeným podle zdroje, který výstrahu vygeneroval. |
| Všechny aktivní výstrahy |Všechny výstrahy s všechny závažnosti seskupené podle názvu upozornění. Zahrnuje jenom Operations Manager výstrahy se stavem jiný než *Uzavřeno*. |

Pokud se posunete napravo, řídicí panel obsahuje několik běžných dotazů, na které můžete kliknout a provést [prohledávání protokolu](../../azure-monitor/log-query/log-query-overview.md) pro data výstrah.

![Řídicí panel pro správu výstrah](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Řešení Alert Management analyzuje libovolný záznam s typem **výstrahy**.  Výstrahy vytvořené službou Log Analytics nebo odebrané Nagios a Zabbix přímo neshromáždí řešení.

Řešení importuje výstrahy z System Center Operations Manager a vytvoří odpovídající záznam pro každý s typem **výstrahy** a SourceSystem **OpsManager**.  Tyto záznamy mají vlastnosti v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |*Upozornění* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Podrobnosti o datová položka, která způsobila výstrahu, kterou chcete vygenerovat ve formátu XML. |
| `AlertDescription` |Podrobný popis výstrahy. |
| `AlertId` |Identifikátor GUID výstrahy. |
| `AlertName` |Název výstrahy. |
| `AlertPriority` |Úroveň priority výstrahy. |
| `AlertSeverity` |Úroveň závažnosti výstrahy. |
| `AlertState` |Nejnovější stav řešení výstrahy. |
| `LastModifiedBy` |Jméno uživatele, kteří poslední změny výstrahy. |
| `ManagementGroupName` |Název skupiny pro správu ve kterém byla výstraha vygenerována. |
| `RepeatCount` |Počet pokusů, které je stejná výstraha generovaná pro dané monitorují objekt od přeloženy. |
| `ResolvedBy` |Jméno uživatele, kteří vyřešené výstrahy. Prázdný, pokud výstraha dosud nebyl vyřešen. |
| `SourceDisplayName` |Zobrazovaný název objektu monitorování, které upozornění vygenerovalo. |
| `SourceFullName` |Celý název objektu monitorování, které upozornění vygenerovalo. |
| `TicketId` |ID lístku pro upozornění, pokud prostředí System Center Operations Manageru je integrovaná s procesem pro přidělování lístků pro výstrahy.  Prázdný žádné lístku ID je přiřazena. |
| `TimeGenerated` |Datum a čas, který byla výstraha vytvořena. |
| `TimeLastModified` |Datum a čas poslední změny výstrahy. |
| `TimeRaised` |Datum a čas, který byla výstraha vygenerována. |
| `TimeResolved` |Datum a čas vyřešení výstrahy. Prázdný, pokud výstraha dosud nebyl vyřešen. |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy upozornění shromážděné tímto řešením: 

| Dotaz | Popis |
|:---|:---|
| Upozornění &#124; kde SourceSystem == "OpsManager" a AlertSeverity == "Chyba" a TimeRaised > ago(24h) |Kritické výstrahy vyvolané během posledních 24 hodin |
| Upozornění &#124; kde AlertSeverity == "varování" a TimeRaised > ago(24h) |Upozorňující výstrahy vyvolané během posledních 24 hodin |
| Upozornění &#124; kde SourceSystem == "OpsManager" a AlertState! = "Uzavřeno" a TimeRaised > ago(24h) &#124; shrnout počet = count() by SourceDisplayName |Zdroje s aktivními výstrahami vyvolanými během posledních 24 hodin |
| Upozornění &#124; kde SourceSystem == "OpsManager" a AlertSeverity == "Chyba" a TimeRaised > ago(24h) a AlertState! = "Uzavřeno" |Kritické výstrahy vyvolané během posledních 24 hodin, které jsou stále aktivní |
| Upozornění &#124; kde SourceSystem == "OpsManager" a TimeRaised > ago(24h) a AlertState == "Uzavřený" |Výstrahy vyvolané během posledních 24 hodin, které už jsou uzavřené |
| Upozornění &#124; kde SourceSystem == "OpsManager" a TimeRaised > ago(1d) &#124; shrnout počet = count() by AlertSeverity |Výstrahy vyvolané za poslední 1 den seskupené podle závažnosti |
| Upozornění &#124; kde SourceSystem == "OpsManager" a TimeRaised > ago(1d) &#124; seřadit podle RepeatCount desc |Výstrahy vyvolané za poslední 1 den seřazené podle počtu opakování |



## <a name="next-steps"></a>Další kroky
* Podrobnosti o generování upozornění ze služby Log Analytics najdete v tématu [Upozornění v Log Analytics](../../azure-monitor/platform/alerts-overview.md).
