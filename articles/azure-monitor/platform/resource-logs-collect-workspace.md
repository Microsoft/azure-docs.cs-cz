---
title: Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics
description: Naučte se streamovat protokoly prostředků Azure do pracovního prostoru Log Analytics v Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391039"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Shromažďování protokolů platformy Azure v pracovním prostoru Log Analytics v Azure Monitor
[Protokoly platforem](platform-logs-overview.md) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. Tento článek popisuje, jak shromažďovat protokoly prostředků v pracovním prostoru Log Analytics, který umožňuje jejich analýzu s dalšími daty monitorování shromážděnými v protokolech Azure Monitor pomocí výkonných dotazů protokolu a také k využití dalších Azure Monitor funkcí, jako jsou výstrahy a. vizualizace. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Co se dá dělat s protokoly platforem v pracovním prostoru
Shromažďování protokolů platforem do pracovního prostoru Log Analytics umožňuje analyzovat protokoly všech vašich prostředků Azure společně a využívat všechny funkce, které jsou k dispozici pro [Azure monitor protokolů](data-platform-logs.md) , které zahrnují následující:

* **Dotazy k protokolu** – Vytvářejte [dotazy protokolu](../log-query/log-query-overview.md) pomocí výkonného dotazovacího jazyka, abyste mohli rychle analyzovat a získávat přehledy o diagnostických datech a analyzovat je pomocí dat shromážděných z jiných zdrojů v Azure monitor.
* **Upozornění** – Získejte proaktivní oznamování kritických podmínek a vzorů identifikovaných v protokolech prostředků pomocí [výstrah protokolu v Azure monitor](alerts-log.md).
* **Vizualizace** – připnout výsledky dotazu protokolu na řídicí panel Azure nebo zahrnout ho do sešitu jako součást interaktivní sestavy.

## <a name="prerequisites"></a>Požadavky
Je potřeba [vytvořit nový pracovní prostor](../learn/quick-create-workspace.md) , pokud ho ještě nemáte. Pracovní prostor nemusí být ve stejném předplatném jako prostředek odesílající protokoly, pokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC k oběma předplatným.

## <a name="create-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky
Pomocí nastavení diagnostiky pro prostředek Azure můžete odesílat protokoly platforem do pracovního prostoru Log Analytics a dalších cílů. Podrobnosti najdete [v tématu Vytvoření nastavení diagnostiky pro shromáždění protokolů a metrik v Azure](diagnostic-settings.md) .


## <a name="activity-log-collection"></a>Shromažďování protokolů aktivit
Protokol aktivit můžete odeslat z libovolného jednoho předplatného do pěti Log Analyticsch pracovních prostorů. Data protokolu prostředků shromážděná v Log Analytics pracovním prostoru se ukládají do tabulky **AzureActivity** . 

## <a name="resource-log-collection-mode"></a>Režim shromažďování protokolu prostředků
Data protokolu prostředků shromážděná v Log Analytics pracovním prostoru jsou uložena v tabulkách, jak je popsáno v [části struktura protokolů Azure monitor](../log-query/logs-structure.md). Tabulky používané v protokolech prostředků závisí na typu kolekce, kterou prostředek používá:

- Diagnostika Azure – všechna zapsaná data jsou do tabulky _AzureDiagnostics_ .
- Data specifická pro prostředky jsou zapsána do jednotlivých tabulek pro každou kategorii prostředku.

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics režim 
V tomto režimu budou všechna data z jakéhokoli [nastavení diagnostiky](diagnostic-settings.md) shromažďována v tabulce _AzureDiagnostics_ . Toto je starší metoda, kterou dnes používá většina služeb Azure.

Vzhledem k tomu, že více typů prostředků odesílá data do stejné tabulky, je její schéma nadmnožinou schémat všech shromažďovaných datových typů.

Vezměte v úvahu následující příklad, kdy se diagnostické nastavení shromažďují do stejného pracovního prostoru pro následující typy dat:

- Protokoly auditu služby 1 (se schématem, který se skládá ze sloupců A, B a C)  
- Protokoly chyb služby 1 (se schématem, které obsahuje sloupce D, E a F)  
- Protokol auditu služby 2 (se schématem, který se skládá ze sloupců G, H a I)  

Tabulka AzureDiagnostics bude vypadat takto:  

| ResourceProvider    | Kategorie     | A  | B  | C  | D  | E  | Ž  | Věcn  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | Chybná protokolu    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft. Jazyka2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft. Service1 | Chybná protokolu    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft. Jazyka2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft. Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specifické pro prostředky
V tomto režimu se pro každou kategorii vybranou v nastavení diagnostiky vytvoří jednotlivé tabulky ve vybraném pracovním prostoru. Tato metoda se doporučuje, protože je mnohem jednodušší pracovat s daty v protokolových dotazech, poskytuje lepší zjistitelnost schémat a jejich struktury, zlepšuje výkon v rámci latence příjmu a doby dotazování a možnost udělovat práva RBAC pro konkrétní tabulka Všechny služby Azure nakonec budou migrovány do režimu specifického pro prostředky. 

V předchozím příkladu by se vytvořily tři tabulky:
 
- Tabulka *Service1AuditLogs* následujícím způsobem:

    | Poskytovatel prostředků | Kategorie | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Tabulka *Service1ErrorLogs* následujícím způsobem:  

    | Poskytovatel prostředků | Kategorie | D | E | Ž |
    | -- | -- | -- | -- | -- | 
    | Service1 | Chybná protokolu |  q1 | w1 | e1 |
    | Service1 | Chybná protokolu |  q2 | w2 | e2 |
    | ... |

- Tabulka *Service2AuditLogs* následujícím způsobem:  

    | Poskytovatel prostředků | Kategorie | Věcn | H | I |
    | -- | -- | -- | -- | -- |
    | Jazyka2 | AuditLogs | j1 | k1 | l1|
    | Jazyka2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Výběr režimu kolekce
Většina prostředků Azure zapíše data do pracovního prostoru v režimu **diagnostiky Azure** nebo **specifického prostředku** , a to bez toho, abyste si zvolili. Podrobnosti o tom, který režim používá, najdete v [dokumentaci ke každé službě](diagnostic-logs-schema.md) . Všechny služby Azure nakonec budou používat režim specifický pro prostředky. V rámci tohoto přechodu vám některé prostředky umožní vybrat režim v nastavení diagnostiky. Pro všechna nová nastavení diagnostiky byste měli určit režim specifický pro určitý prostředek, protože to usnadňuje správu dat a může vám pomohou se vyhnout složitým migracím později.
  
   ![Selektor režimu nastavení diagnostiky](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> V současné době lze **diagnostiku Azure** a režim **specifický pro prostředky** vybrat pouze při konfiguraci nastavení diagnostiky v Azure Portal. Pokud nakonfigurujete nastavení pomocí rozhraní příkazového řádku, PowerShellu nebo rozhraní REST API, bude se používat jako výchozí pro **Azure Diagnostics**.

Existující nastavení diagnostiky můžete upravit do režimu specifického pro prostředky. V tomto případě zůstanou shromážděná data v tabulce _AzureDiagnostics_ , dokud je neodeberete podle nastavení uchování pro daný pracovní prostor. Do vyhrazené tabulky budou shromažďována nová data. Použijte operátor [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) k dotazování dat napříč oběma tabulkami.

Dál Sledujte Blog o [aktualizacích Azure](https://azure.microsoft.com/updates/) , kde najdete oznámení o službách Azure, které podporují režim specifický pro prostředky.

### <a name="column-limit-in-azurediagnostics"></a>Omezení počtu sloupců v AzureDiagnostics
Pro všechny tabulky v Azure Monitorch protokolech je omezení vlastností 500. Po dosažení tohoto limitu budou všechny řádky obsahující data s jakoukoli vlastností mimo první 500 vyhozeny při zpracování času přijímání. Tabulka *AzureDiagnostics* je zvláště náchylná k tomuto limitu, protože obsahuje vlastnosti pro všechny služby Azure, které do ní zapisují.

Pokud shromažďujete protokoly prostředků z více služeb, _AzureDiagnostics_ může tento limit překročit a data budou chybět. Až budou všechny služby Azure podporovat režim konkrétního prostředku, měli byste nakonfigurovat prostředky pro zápis do několika pracovních prostorů, abyste snížili možnosti dosažení limitu sloupce 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory vzhledem k velmi podrobné sadě protokolů je služba, která je známá k zápisu velkého počtu sloupců a potenciálně způsobila, že _AzureDiagnostics_ překročila limit. Pro všechna nastavení diagnostiky, která byla nakonfigurovaná před tím, než byl povolen režim specifický pro prostředky, se vytvoří nový sloupec pro každý jedinečný parametr uživatele u jakékoli aktivity. Vytvoří se další sloupce z důvodu podrobného charakteru vstupů a výstupů aktivit.
 
Své protokoly byste měli migrovat tak, aby používaly režim specifický pro prostředky, co nejrychleji. Pokud to nemůžete udělat okamžitě, budete dočasnou alternativou izolovat Azure Data Factory protokoly do svého vlastního pracovního prostoru, abyste minimalizovali riziko, že tyto protokoly mají vliv na jiné typy protokolů shromažďované ve vašich pracovních prostorech.


## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o protokolech prostředků](platform-logs-overview.md).
* [Vytvořte nastavení diagnostiky pro shromažďování protokolů a metrik v Azure](diagnostic-settings.md).
