---
title: Diagnostické protokoly Azure Stream do pracovního prostoru Log Analytics ve službě Azure Monitor
description: Naučíte se Streamovat diagnostické protokoly Azure do pracovního prostoru Log Analytics ve službě Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e8e6276a38f06b5c6ebb24c89f3733b9fd7220f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612828"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Diagnostické protokoly Azure Stream do pracovního prostoru Log Analytics ve službě Azure Monitor

**[Diagnostické protokoly Azure](diagnostic-logs-overview.md)**  můžete streamování v reálném čase k pracovnímu prostoru Log Analytics ve službě Azure Monitor pomocí portálu, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Co můžete dělat s diagnostické protokoly v pracovním prostoru Log Analytics

Azure Monitor nabízí flexibilní protokol dotazování a analýzy nástroj, který umožňuje získat přehled o Nezpracovaný protokol data vygenerovaná prostředky Azure. Některé funkce patří:

* **Dotaz protokolu** – zápis upřesňující dotazy přes data protokolu, korelovat protokoly z různých zdrojů a generovat grafy, které je možné připnout na řídicí panel Azure.
* **Výstrahy** -rozpoznat, kdy se jeden nebo více událostí odpovídají konkrétním dotazu a informováni pomocí volání rozhraní e-mailu nebo webhooku pomocí Azure Monitor výstrah.
* **Pokročilé analýzy** – použití strojového učení a vzor odpovídající algoritmy s cílem identifikovat možné problémy zjištěné při vaše protokoly.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Povolení streamování diagnostických protokolů do pracovního prostoru Log Analytics

Streamování diagnostických protokolů prostřednictvím kódu programu, prostřednictvím portálu, nebo pomocí můžete povolit [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). V obou případech můžete vytvořit nastavení diagnostiky v které zadáte, pracovnímu prostoru Log Analytics a kategorie protokolů a metrik, které chcete odeslat daném pracovním prostoru. Diagnostika **kategorie protokolu** je typ protokolu, který může poskytnout zdroje.

Pracovní prostor Log Analytics, nemusí být ve stejném předplatném jako prostředek, které vysílá protokoly za předpokladu, že uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Například*: Metrika 'Příchozí zprávy' v Centru událostí můžete prozkoumat a převést na graf úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diagnostické protokoly Stream pomocí portálu
1. Na portálu přejděte do Azure monitoru a klikněte na **nastavení diagnostiky** v **nastavení** nabídky.


2. Volitelně můžete filtrovat seznam podle skupiny prostředků nebo prostředek typu a potom klikněte na prostředek, pro kterou chcete nastavit nastavení diagnostiky.

3. Pokud neexistuje žádná nastavení pro prostředek jste vybrali, se zobrazí výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiku."

   ![Přidejte nastavení diagnostiky – žádná existující nastavení](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Pokud existuje stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou už nakonfigurovaná na tento prostředek. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko u **odesílat do Log Analytics**, pak vyberte pracovní prostor Log Analytics.

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly se streamují do tohoto pracovního prostoru, jakmile je vygenerována nová data události. Může být až 15 minut mezi při události je vygenerován a pokud je zobrazeno v Log Analytics.

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete povolit streamování prostřednictvím [rutin prostředí Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), můžete použít `Set-AzDiagnosticSetting` rutiny s těmito parametry:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Všimněte si, že vlastnost ID pracovního prostoru trvá úplné Azure resource ID pracovního prostoru, není pracovní prostor ID a klíč uvedené na portálu Log Analytics.

### <a name="via-azure-cli"></a>Via Azure CLI

Pokud chcete povolit streamování prostřednictvím [rozhraní příkazového řádku Azure](../../azure-monitor/platform/cli-samples.md), můžete použít [az monitor diagnostiky – nastavení vytváření](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) příkazu.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Můžete přidat další kategorie pro protokol diagnostiky tak, že přidáte slovníky předané jako pole JSON `--logs` parametru.

`--resource-group` Argument je pouze požadováno pokud `--workspace` není ID objektu.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Jak mám dotazovat data z pracovního prostoru Log Analytics?

V okně protokoly na portálu Azure Monitor můžete dotazovat diagnostické protokoly jako součást řešení Správa protokolů ve složce AzureDiagnostics tabulky. Existují také [několik řešení monitorování pro prostředky Azure](../../azure-monitor/insights/solutions.md) instalací získat okamžitý přehled o data protokolu odesílají do služby Azure Monitor.

### <a name="examples"></a>Příklady

```Kusto
// Resources that collect diagnostic logs into this Log Analytics workspace, using Diagnostic Settings
AzureDiagnostics
| distinct _ResourceId
```
```Kusto
// Resource providers collecting diagnostic logs into this Log Analytics worksapce, with log volume per category
AzureDiagnostics
| summarize count() by ResourceProvider, Category
```
```Kusto
// Resource types collecting diagnostic logs into this Log Analytics workspace, with number of resources onboarded
AzureDiagnostics
| summarize ResourcesOnboarded=dcount(_ResourceId) by ResourceType
```
```Kusto
// Operations logged by specific resource provider, in this example - KeyVault
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| distinct OperationName
```

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure vs. diagnostiky specifické podle prostředků  
Po povolení cílového Log Analytics v konfiguraci diagnostiky Azure existují dva různé způsoby, které se zobrazí data ve vašem pracovním prostoru:  
- **Diagnostika Azure** – Toto je metoda starší verzi ještě dnes používá většina služeb Azure. V tomto režimu se všechna data z libovolné nastavení diagnostiky odkazovala na daný pracovní prostor v skončí _AzureDiagnostics_ tabulky. 
<br><br>Protože mnoho prostředků odesílat data do stejné tabulky (_AzureDiagnostics_), schéma v této tabulce je velmi sadu schémat všech různých datových typů shromažďují. Například pokud jste vytvořili nastavení diagnostiky pro kolekci následující typy dat, všechny odesílají do stejného pracovního prostoru:
    - Auditovat protokoly 1 prostředek (s schématu, který se skládá ze sloupců A, B a C)  
    - Protokoly chyb prostředků 2 (schéma skládající se z sloupců D, E a F s)  
    - Protokoly toku dat 3 prostředků (skládající se z sloupců G, H a jsem schéma s)  

    V tabulce AzureDiagnostics bude vypadat následovně, s ukázkovými daty:  

    | ResourceProvider | Kategorie | A | B | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- **Specifické podle prostředků** – v tomto režimu, vytvářejí jednotlivé tabulky ve vybraném pracovním prostoru na každou kategorii vybraný v konfigurační nastavení diagnostiky. Tato metoda novější mnohem snadněji k nalezení přesně chcete najít přes explicitní oddělení oblastí zájmu: tabulka pro každou kategorii. Kromě toho poskytuje výhody v jeho podpory pro dynamické typy. Tento režim pro vybrané typy prostředků Azure, můžete zobrazit již například [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) nebo [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) protokoly. Nakonec Očekáváme, že každý typ dat k migraci do režimu specifické podle prostředků. 

    V předchozím příkladu by výsledkem vytváří tři tabulky: 
    - Tabulka _mají_ následujícím způsobem:

        | ResourceProvider | Kategorie | A | B | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - Tabulka _nepřenesl_ následujícím způsobem:  

        | ResourceProvider | Kategorie | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | w1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - Tabulka _DataFlowLogs_ následujícím způsobem:  

        | ResourceProvider | Kategorie | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    Další výhody používání režimu specifických pro prostředky zahrnují Vylepšený výkon napříč latence příjmu dat a časů dotazu, lepší vyhledatelnost schémata a jejich strukturu, udělovat oprávnění RBAC na určité tabulky a další.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Výběr Azure diagnostickém režimu vs specifické podle prostředků
Pro většinu služeb Azure prostředky nebudete mít možnost volby, zda se má použít režim diagnostiky Azure nebo specifické podle prostředků; data budou automaticky směrovat prostřednictvím metody vybraný prostředek použít. Podrobnosti najdete v dokumentaci od prostředků jste povolili k odesílání dat do Log Analytics pro podrobnosti, na které se použijí režimu. 

Jak je uvedeno v předchozí části, je nakonec cíl Azure monitoru mají všechny služby v Azure, použijte režim specifické podle prostředků. Pro usnadnění tohoto přechodu a ujistěte se, že se neztratí jako součást jeho některých služeb Azure při připojování ke službě Log Analytics vám poskytne výběru režimu:  
   ![Diagnostické nastavení režimu výběru](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Jsme **důrazně** doporučujeme, že se pokud chcete vyhnout potenciálně složité migrace dolů cestách, všechny nově vytvořené použití nastavení diagnostiky prostředků na režimu.  

Pro existující povolené diagnostické nastavení, jakmile konkrétní prostředek Azure budou moct zpětně přepnutí z diagnostiky Azure do režimu specifické podle prostředků. Dříve ingestuje data se budou dál k dispozici v _AzureDiagnostics_ tabulky, dokud ho ages podle nakonfigurované v nastavení uchovávání dat v pracovním prostoru, ale žádná nová data se odešlou do vyhrazené tabulky. To znamená, které pro všechny dotazy, které mají rozložit stará data a nové (dokud stará data plně ages navýšení kapacity), [sjednocení](https://docs.microsoft.com/azure/kusto/query/unionoperator) operátor v dotazech, budou muset kombinovat dvěma datovými sadami.

Podívejte se prosím o nový Azure služeb podpory protokolů v režimu specifické podle prostředků na [aktualizace Azure](https://azure.microsoft.com/updates/) blogu!

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Známá omezení: omezení počtu sloupců v AzureDiagnostics
Je omezený na explicitní jakékoli dané tabulky protokolu Azure nemá více než 500 sloupce. Po jeho dosažení se v době příjmu se zahodí všechny řádky obsahující data s žádným sloupcem mimo prvních 500. V tabulce AzureDiagnostics je zejména napadnutelné přes bezpečnostní bude dopad na toto omezení. To obvykle proběhne buď protože širokou škálu zdrojů dat se odesílají do stejného pracovního prostoru, nebo několik zdrojů podrobné údaje odesílané do stejného pracovního prostoru. 

#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory, z důvodu velmi podrobné sadu protokolů, je prostředek, který je známé hlavně ovlivní tento limit. Zejména pro diagnostické nastavení před konkrétní prostředky režimu byl povolený nebo explicitně zvolíte pro použití režimu specifické podle prostředků z důvodů zpětné kompatibility:  
- *Uživatel parametry definované pro všechny aktivity v kanálu*: bude nový sloupec, který vytvoří pro každý parametr jedinečně pojmenovaná uživatele pro všechny aktivity. 
- *Aktivita vstupy a výstupy*: tyto aktivity aktivity se liší a kvůli jejich podrobné povahu generování velkého počtu sloupců. 
 
Jako s širší návrhy řešení níže, doporučujeme migrovat vaše protokoly pro použití režimu specifické podle prostředků co nejdříve. Pokud se nemůžete k tomu okamžitě, dočasné alternativou je izolaci ADF protokoly do své vlastní pracovní prostor, který minimalizuje riziko tyto protokoly vliv na jiné typy protokolů shromažďují ve vašich pracovních prostorů. 
 
#### <a name="workarounds"></a>Alternativní řešení
Krátká období, dokud se všechny služby Azure povolené v režimu specifické podle prostředků za služby není zatím podporuje režim specifické podle prostředků, se doporučuje pro jednotlivé typy podrobné údaje publikuje tyto služby do samostatných pracovních prostorů ke snížení možnost dosažení limitu.  
 
Dlouhodobější, Diagnostika Azure bude přechází k všech služeb Azure podporuje režim specifické podle prostředků. Doporučujeme, abyste přesunutí do tohoto režimu co nejdříve pro snížení potenciálu ně neměly vliv toto omezení 500 sloupce.  


## <a name="next-steps"></a>Další kroky

* [Další informace o diagnostických protokolech Azure](diagnostic-logs-overview.md)

