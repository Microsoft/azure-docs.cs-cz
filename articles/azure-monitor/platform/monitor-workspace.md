---
title: Monitorování stavu Log Analytics pracovního prostoru v Azure Monitor
description: Popisuje, jak monitorovat stav pracovního prostoru Log Analytics pomocí dat v tabulce Operations.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 3cb01a8f1c06bad618ae5c7930920ee0f067038c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184531"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitorování stavu Log Analytics pracovního prostoru v Azure Monitor
Chcete-li zachovat výkon a dostupnost vašeho pracovního prostoru Log Analytics v Azure Monitor, je nutné být schopni aktivně detekovat všechny problémy, které vznikají. Tento článek popisuje, jak monitorovat stav Log Analytics pracovního prostoru pomocí dat v tabulce [Operations](/azure/azure-monitor/reference/tables/operation) . Tato tabulka je zahrnutá v každém pracovním prostoru Log Analytics a obsahuje chyby a varování, ke kterým dochází ve vašem pracovním prostoru. Tato data byste měli pravidelně kontrolovat a vytvářet výstrahy, které se budou aktivně informovat v případě, že v pracovním prostoru existují důležité incidenty.

## <a name="_logoperation-function"></a>_LogOperation funkce

Protokoly Azure Monitor odesílají podrobnosti o všech problémech v tabulce [operace](/azure/azure-monitor/reference/tables/operation) v pracovním prostoru, kde došlo k problému. Funkce systému **_LogOperation** je založena na tabulce **operace** a poskytuje zjednodušenou sadu informací pro analýzu a upozorňování.

## <a name="columns"></a>Sloupce

Funkce **_LogOperation** vrátí sloupce v následující tabulce.

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

Následující tabulka popisuje kategorie z funkce _LogOperation. 

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
| Mezipaměť. | Chyba | Zjistila se chyba konfigurace. | |
| Shromažďování dat | Chyba   | Data byla zahozena, protože požadavek byl vytvořen dříve, než je počet dnů instalace. | [Správa využití a nákladů pomocí protokolů Azure Monitoru](manage-cost-storage.md#alert-when-daily-cap-reached)
| Shromažďování dat | Informace    | Zjistila se konfigurace počítače kolekce.| |
| Shromažďování dat | Informace    | Shromažďování dat bylo zahájeno z důvodu nového dne. | [Správa využití a nákladů pomocí protokolů Azure Monitoru](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Shromažďování dat | Upozornění | Shromažďování dat bylo zastaveno z důvodu dosažení denního limitu.| [Správa využití a nákladů pomocí protokolů Azure Monitoru](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Zpracování dat | Chyba   | Neplatný formát JSON. | [Odeslání dat protokolu do Azure Monitor pomocí rozhraní API kolekce dat HTTP (Public Preview)](data-collector-api.md#request-body) | 
| Zpracování dat | Upozornění | Hodnota byla oříznuta na maximální povolenou velikost. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Zpracování dat | Upozornění | Hodnota pole se ořízne při dosažení limitu velikosti. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Rychlost přijímání zpráv | Informace | Omezení rychlosti přijímání se blíží 70%. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Rychlost přijímání zpráv | Upozornění | Omezení rychlosti přijímání se blíží limitu. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Rychlost přijímání zpráv | Chyba   | Dosáhlo se limitu přenosové rychlosti. | [Omezení služby Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Storage | Chyba   | Nejde získat přístup k účtu úložiště, protože použité přihlašovací údaje jsou neplatné.  |



   

## <a name="alert-rules"></a>Pravidla upozornění
Používejte [výstrahy dotazování protokolu](../platform/alerts-log-query.md) v Azure monitor k proaktivnímu upozorňování při zjištění problému v pracovním prostoru Log Analytics. Měli byste použít strategii, která vám umožní včas reagovat na problémy při minimalizaci nákladů. Vaše předplatné se účtuje za každé pravidlo výstrahy s náklady v závislosti na frekvenci, kterou vyhodnotí.

Doporučenou strategií je začít se dvěma pravidly výstrah na základě úrovně problému. Použijte krátkou frekvenci, například každých 5 minut, pro chyby a delší četnost, jako je 24 hodin pro upozornění. Vzhledem k tomu, že chyby indikují možnou ztrátu dat, chcete je rychle reagovat, abyste minimalizovali případné ztráty. Upozornění obvykle označují problém, který nevyžaduje okamžitou pozornost, takže je můžete denně kontrolovat.

Postup najdete v části [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../platform/alerts-log.md) k vytvoření pravidel upozornění protokolů. V následujících částech jsou popsány podrobnosti pro každé pravidlo.


| Dotaz | Prahová hodnota | Období | Frekvence |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Tato pravidla výstrahy budou odpovídat stejnému pro všechny operace s chybou nebo upozorněním. Jak se seznámíte s operacemi, které generují výstrahy, možná budete chtít pro konkrétní operace reagovat odlišně. Můžete například chtít odesílat oznámení různým lidem pro konkrétní operace. 

Chcete-li vytvořit pravidlo výstrahy pro konkrétní operaci, použijte dotaz, který obsahuje sloupce **kategorie** a **operace** . 

Následující příklad vytvoří výstražné upozornění, pokud rychlost zpracování příjmu dosáhla 80% limitu.

- Cíl: vyberte svůj pracovní prostor Log Analytics
- Měřítk
  - Název signálu: prohledávání vlastního protokolu
  - Vyhledávací dotaz: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Podle: počet výsledků
  - Podmínka: je větší než
  - Prahová hodnota: 0
  - Období: 5 (minuty)
  - Frekvence: 5 (minuty)
- Název pravidla výstrahy: dosáhlo se denního limitu dat.
- Závažnost: upozornění (závažnost 1)


Následující příklad vytvoří výstražné upozornění, pokud shromažďování dat dosáhlo denního limitu. 

- Cíl: vyberte svůj pracovní prostor Log Analytics
- Měřítk
  - Název signálu: prohledávání vlastního protokolu
  - Vyhledávací dotaz: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - Podle: počet výsledků
  - Podmínka: je větší než
  - Prahová hodnota: 0
  - Období: 5 (minuty)
  - Frekvence: 5 (minuty)
- Název pravidla výstrahy: dosáhlo se denního limitu dat.
- Závažnost: upozornění (závažnost 1)



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [výstrahách protokolů](alerts-log.md).
- [Shromažďovat data auditu dotazů](../log-query/query-audit.md) pro váš pracovní prostor.