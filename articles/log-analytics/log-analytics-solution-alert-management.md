---
title: Řešení alert managementu ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Řešení pro správu upozornění v Log Analytics pomáhá analyzovat všechny výstrahy ve vašem prostředí.  Kromě konsolidace výstrahy generované v Log Analytics importuje výstrahy z připojených skupin pro správu System Center Operations Manager do Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: acf9e512e188c34c0124832a6a534135790f1e2d
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049197"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Řešení alert managementu ve službě Azure Log Analytics

![Ikona upozornění správy](media/log-analytics-solution-alert-management/icon.png)

Správa výstrah řešení pomáhá analyzovat všechny výstrahy v úložišti Log Analytics.  Tyto výstrahy mohou pocházet z nejrůznějších zdrojů, včetně těchto zdrojů [vytvořené službou Log Analytics](log-analytics-alerts.md) nebo [naimportované z Nagios a Zabbix](log-analytics-linux-agents.md). Řešení také importuje výstrahy z libovolného [připojené skupiny pro správu System Center Operations Manager](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Požadavky
Toto řešení funguje se všechny záznamy v úložišti Log Analytics s typem **výstrah**, takže je nutné provést jakékoli konfigurace je nutná pro shromažďování těchto záznamů.

- Pro upozornění Log Analytics [vytvořit pravidla výstrah](log-analytics-alerts.md) vytvořit záznamy upozornění přímo v úložišti.
- Výstrahy Nagios a Zabbix [konfiguraci těchto serverů](log-analytics-linux-agents.md) k odesílání upozornění Log Analytics.
- Pro System Center Operations Manageru výstrahy [připojení skupiny pro správu nástroje Operations Manager do vašeho pracovního prostoru Log Analytics](log-analytics-om-agents.md).  Všechny výstrahy vytvořené v nástroji System Center Operations Manager jsou importovány do Log Analytics.  

## <a name="configuration"></a>Konfigurace
Přidejte řešení pro správu výstrah do pracovního prostoru Log Analytics pomocí postupu popsaného v [přidat řešení](log-analytics-add-solutions.md). Není nutná žádná další konfigurace.

## <a name="management-packs"></a>Sady Management Pack
Pokud vaší skupině pro správu System Center Operations Manageru je připojený k pracovnímu prostoru Log Analytics, jsou při přidání tohoto řešení následující sady management Pack nainstalované v nástroji System Center Operations Manager.  Neexistuje žádná konfigurace ani Údržba požadované sady management Pack.

* Microsoft System Center Advisor Správa výstrah (Microsoft.IntelligencePacks.AlertManagement)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](log-analytics-om-agents.md).

## <a name="data-collection"></a>Shromažďování dat
### <a name="agents"></a>Agenti
Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|:--- |:--- |:--- |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne |Přímí agenti Windows nejsou generovány výstrahy.  Upozornění log Analytics je možné vytvořit z události a data o výkonu shromáždí z Windows agentů. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne |Přímí agenti systému Linux negenerují výstrahy.  Upozornění log Analytics můžete vytvořit z události a údaje o výkonu shromážděná z agentů Linuxu.  Výstrahy Nagios a Zabbix se shromažďují z těchto serverů, které vyžadují agenta pro Linux. |
| [Skupina pro správu System Center Operations Manager](log-analytics-om-agents.md) |Ano |Výstrahy, které jsou vytvořeny na agenty nástroje Operations Manager se doručí do skupiny pro správu a pak se předávají do Log Analytics.<br><br>Přímé připojení z agentů nástroje Operations Manager ke službě Log Analytics se nevyžaduje. Upozornění data se přesměrovávají ze skupiny pro správu do úložiště Log Analytics. |


### <a name="collection-frequency"></a>Četnost shromažďování dat
- Záznamy upozornění jsou k dispozici řešení, jako jsou uloženy v úložišti.
- Oznámení se odešlou ze skupiny pro správu Operations Manageru ke službě Log Analytics každé 3 minuty.  

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení pro správu výstrah do pracovního prostoru Log Analytics **Správa výstrah** se přidá dlaždice na řídicí panel.  Na této dlaždici se zobrazuje počet a grafická reprezentace počet aktuálně aktivních výstrah, které byly generovány během posledních 24 hodin.  Nelze změnit tomto časovém rozsahu.

![Dlaždice výstrah správy](media/log-analytics-solution-alert-management/tile.png)

Klikněte na **Správa výstrah** otevřete dlaždici **Správa výstrah** řídicího panelu.  Řídicí panel obsahuje sloupce v následující tabulce.  Každý sloupec uvádí hlavní 10 upozornění podle počtu odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah.  Můžete spustit prohledávání protokolu, který poskytuje úplný seznam kliknutím **zobrazit všechny** v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--- |:--- |
| Důležitá upozornění |Všechny výstrahy se závažností kritický seskupené podle názvu upozornění.  Klikněte na název výstrahy. ke spuštění hledání v protokolu všechny záznamy pro tuto výstrahu. |
| Upozorňující výstrahy |Všechny výstrahy se závažností upozornění seskupené podle názvu upozornění.  Klikněte na název výstrahy. ke spuštění hledání v protokolu všechny záznamy pro tuto výstrahu. |
| Aktivní výstrahy SCOM |Všechny výstrahy shromážděné z nástroje Operations Manager pomocí některému ze stavů, jiné než *uzavřeno* seskupených podle zdroje, které upozornění vygenerovalo. |
| Všechny aktivní výstrahy |Všechny výstrahy s všechny závažnosti seskupené podle názvu upozornění. Zahrnuje výstrahy nástroje Operations Manager s některému ze stavů jenom jiné než *uzavřeno*. |

Pokud se posunete doprava, řídicí panel obsahuje několik běžných dotazů, které můžete kliknout na provádět [prohledávání protokolů](log-analytics-log-searches.md) pro data výstrah.

![Řídicí panel pro správu výstrah](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Správa výstrah řešení analyzuje libovolný záznam s typem **výstrah**.  Výstrahy vytvořené službou Log Analytics nebo odebrané Nagios a Zabbix přímo neshromáždí řešení.

Řešení importovat výstrahy z nástroje System Center Operations Manager a vytvoří odpovídající záznam pro každý typ **výstrah** a jako SourceSystem **OpsManager**.  Tyto záznamy mají vlastnosti v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*Upozornění* |
| SourceSystem |*OpsManager* |
| AlertContext |Podrobnosti o datová položka, která způsobila výstrahu, kterou chcete vygenerovat ve formátu XML. |
| AlertDescription |Podrobný popis výstrahy. |
| AlertId |Identifikátor GUID výstrahy. |
| AlertName |Název výstrahy. |
| AlertPriority |Úroveň priority výstrahy. |
| AlertSeverity |Úroveň závažnosti výstrahy. |
| AlertState |Nejnovější stav řešení výstrahy. |
| LastModifiedBy |Jméno uživatele, kteří poslední změny výstrahy. |
| ManagementGroupName |Název skupiny pro správu ve kterém byla výstraha vygenerována. |
| RepeatCount |Počet pokusů, které je stejná výstraha generovaná pro dané monitorují objekt od přeloženy. |
| ResolvedBy |Jméno uživatele, kteří vyřešené výstrahy. Prázdný, pokud výstraha dosud nebyl vyřešen. |
| SourceDisplayName |Zobrazovaný název objektu monitorování, které upozornění vygenerovalo. |
| SourceFullName |Celý název objektu monitorování, které upozornění vygenerovalo. |
| TicketId |ID lístku pro upozornění, pokud prostředí System Center Operations Manageru je integrovaná s procesem pro přidělování lístků pro výstrahy.  Prázdný žádné lístku ID je přiřazena. |
| TimeGenerated |Datum a čas, který byla výstraha vytvořena. |
| TimeLastModified |Datum a čas poslední změny výstrahy. |
| TimeRaised |Datum a čas, který byla výstraha vygenerována. |
| TimeResolved |Datum a čas vyřešení výstrahy. Prázdný, pokud výstraha dosud nebyl vyřešen. |

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



## <a name="next-steps"></a>Další postup
* Podrobnosti o generování upozornění ze služby Log Analytics najdete v tématu [Upozornění v Log Analytics](log-analytics-alerts.md).
