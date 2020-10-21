---
title: Monitorování stavu Log Analytics pracovního prostoru v Azure Monitor
description: Popisuje, jak monitorovat stav pracovního prostoru Log Analytics pomocí dat v tabulce Operations.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: a4f578ca2e9fc448fb85b803cce46974a8c2e4dc
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326008"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitorování stavu Log Analytics pracovního prostoru v Azure Monitor
Chcete-li zachovat výkon a dostupnost vašeho pracovního prostoru Log Analytics v Azure Monitor, je nutné být schopni aktivně detekovat všechny problémy, které vznikají. Tento článek popisuje, jak monitorovat stav Log Analytics pracovního prostoru pomocí dat v tabulce [Operations](/azure-monitor/reference/tables/operation) . Tato tabulka je zahrnutá v každém pracovním prostoru Log Analytics a obsahuje chyby a varování, ke kterým dochází ve vašem pracovním prostoru. Tato data byste měli pravidelně kontrolovat a vytvářet výstrahy, které se budou aktivně informovat v případě, že v pracovním prostoru existují důležité incidenty.

## <a name="_logsoperation-function"></a>_LogsOperation funkce
Protokoly Azure Monitor odesílají podrobnosti o všech problémech v tabulce [operace](/azure-monitor/reference/tables/operation) v pracovním prostoru, kde došlo k problému. Funkce systému **_LogsOperation** je založena na tabulce **operace** a poskytuje zjednodušenou sadu informací pro analýzu a upozorňování.

## <a name="columns"></a>Sloupce

Funkce **_LogsOperation** vrátí sloupce v následující tabulce.

| Sloupec | Popis |
|:---|:---|
| TimeGenerated | Čas, kdy došlo k incidentu v UTC. |
| Kategorie  | Skupina kategorií operací. Dá se použít k filtrování typů operací a k vytvoření přesnější auditování a upozornění systému. Seznam kategorií najdete níže v části. |
| Operace  | Popis typu operace To může znamenat jedno z Log Analytics limitů, typu operace nebo části procesu. |
| Úroveň | Úroveň závažnosti problému:<br>-Info: není nutná žádná konkrétní pozornost.<br>-Upozornění: proces nebyl dokončen podle očekávání a je potřeba věnovat pozornost.<br>-Chyba: proces se nezdařil a je vyžadována naléhavá pozornost. 
| Podrobnosti | Podrobný popis operace zahrnuje konkrétní chybovou zprávu, pokud existuje. |
| _ResourceId | ID prostředku prostředku Azure, který se vztahuje k operaci.  |
| Počítač | Název počítače v případě, že operace souvisí s agentem Azure Monitor. |
| CorrelationId | Slouží k seskupení po sobě jdoucích operací souvisejících s. |


## <a name="categories"></a>Kategorie
Následující tabulka popisuje kategorie z funkce _LogsOperations. 

| Kategorie | Popis |
|:---|:---|
| Příjem dat           | Operace, které jsou součástí procesu přijímání dat. Další podrobnosti najdete níže. |
| Agent               | Označuje problém s instalací agenta. |
| Shromažďování dat     | Operace související s procesy kolekcí dat |
| Cílení na řešení  | Byla zpracována operace typu *ConfigurationScope* . |
| Řešení posouzení | Proces posouzení byl proveden. |


### <a name="ingestion"></a>Příjem dat
Operace příjmu jsou problémy, ke kterým došlo během příjmu dat, včetně oznámení o dosažení limitů pracovních prostorů Azure Log Analytics. Chybové stavy v této kategorii mohou navrhovat ztrátu dat, takže jsou obzvláště důležité pro monitorování. Následující tabulka uvádí podrobnosti o těchto operacích. Omezení služby pro Log Analytics pracovní prostory najdete v tématu [omezení služby Azure monitor](../service-limits.md#log-analytics-workspaces) .


| Operace | Úroveň | Podrobnosti | Související článek |
|:---|:---|:---|:---|
| Vlastní protokol | Chyba   | Bylo dosaženo limitu sloupců vlastních polí. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Vlastní protokol | Chyba   | Přijímání vlastních protokolů se nezdařilo. | |
| Vlastní protokol | Chyba   | Mezipaměť. | |
| Data | Chyba   | Data byla zahozena, protože požadavek byl vytvořen dříve, než je počet dnů instalace. | [Správa využití a nákladů pomocí protokolů Azure Monitoru](manage-cost-storage.md#alert-when-daily-cap-reached)
| Shromažďování dat | Informace    | Zjistila se konfigurace počítače kolekce.| |
| Shromažďování dat | Informace    | Shromažďování dat bylo zahájeno z důvodu nového dne. | [Správa využití a nákladů pomocí protokolů Azure Monitoru](/manage-cost-storage.md#alert-when-daily-cap-reached) |
| Shromažďování dat | Upozornění | Shromažďování dat bylo zastaveno z důvodu dosažení denního limitu.| [Správa využití a nákladů pomocí protokolů Azure Monitoru](/manage-cost-storage.md#alert-when-daily-cap-reached) |
| Rychlost přijímání zpráv | Informace | Omezení rychlosti přijímání se blíží 70%. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Rychlost přijímání zpráv | Upozornění | Omezení rychlosti přijímání se blíží limitu. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Rychlost přijímání zpráv | Chyba   | Dosáhlo se limitu přenosové rychlosti. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Analýza JSON | Chyba   | Neplatný formát JSON. | [Odeslání dat protokolu do Azure Monitor pomocí rozhraní API kolekce dat HTTP (Public Preview)](data-collector-api.md#request-body) | 
| Analýza JSON | Upozornění | Hodnota byla oříznuta na maximální povolenou velikost. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Maximální omezení velikosti sloupce | Upozornění | Hodnota pole se ořízne při dosažení limitu velikosti. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Storage | Chyba   | Nejde získat přístup k účtu úložiště, protože použité přihlašovací údaje jsou neplatné.  |
| Tabulka   | Chyba   | Bylo dosaženo maximálního počtu vlastních polí. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces)|


   

## <a name="alert-rules"></a>Pravidla výstrah
Používejte [výstrahy dotazování protokolu](../platform/alerts-log-query.md) v Azure monitor k proaktivnímu upozorňování při zjištění problému v pracovním prostoru Log Analytics. Měli byste použít strategii, která vám umožní včas reagovat na problémy při minimalizaci nákladů. Vaše předplatné se účtuje za každé pravidlo výstrahy s náklady v závislosti na frekvenci, kterou vyhodnotí.

Doporučenou strategií je začít se dvěma pravidly výstrah na základě úrovně problému. Použijte krátkou frekvenci, například každých 5 minut, pro chyby a delší četnost, jako je 24 hodin pro upozornění. Vzhledem k tomu, že chyby indikují možnou ztrátu dat, chcete je rychle reagovat, abyste minimalizovali případné ztráty. Upozornění obvykle označují problém, který nevyžaduje okamžitou pozornost, takže je můžete denně kontrolovat.

Postup najdete v části [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../platform/alerts-log.md) k vytvoření pravidel upozornění protokolů. V následujících částech jsou popsány podrobnosti pro každé pravidlo.


| Dotaz | Prahová hodnota | Období | Frequency |
|:---|:---|:---|:---|
| `_LogsOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogsOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Tato pravidla výstrahy budou odpovídat stejnému pro všechny operace s chybou nebo upozorněním. Jak se seznámíte s operacemi, které generují výstrahy, možná budete chtít pro konkrétní operace reagovat odlišně. Můžete například chtít odesílat oznámení různým lidem pro konkrétní operace. 

Chcete-li vytvořit pravidlo výstrahy pro konkrétní operaci, použijte dotaz, který obsahuje sloupce **kategorie** a **operace** . 

Následující příklad vytvoří výstražné upozornění, pokud rychlost zpracování příjmu dosáhla 80% limitu.

```kusto
_LogsOperation
| where Category == "Ingestion"
| where Operation == "Ingestion rate"
| where Level == "Warning"
```

Následující příklad vytvoří výstražné upozornění, pokud shromažďování dat dosáhlo denního limitu. 
```kusto
Operation 
| where OperationCategory == "Ingestion" 
|where OperationKey == "Data Collection" 
| where OperationStatus == "Warning"
```




## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [výstrahách protokolů](alerts-log.md).
- [Shromažďovat data auditu dotazů](../log-query/query-audit.md) pro váš pracovní prostor.