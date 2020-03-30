---
title: Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics
description: Zjistěte, jak streamovat protokoly prostředků Azure do pracovního prostoru Analýzy protokolů v Azure Monitoru.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248590"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Shromažďování protokolů platformy Azure v pracovním prostoru Log Analytics v Azure Monitoru
[Protokoly platformy](platform-logs-overview.md) v Azure, včetně protokolu aktivit Azure a protokolů prostředků, poskytují podrobné diagnostické a auditující informace pro prostředky Azure a platformu Azure, na které závisí. Tento článek popisuje shromažďování protokolů prostředků v pracovním prostoru Log Analytics, který umožňuje analyzovat s dalšími daty monitorování shromážděnými v protokolech monitorování Azure pomocí výkonných dotazů protokolu a také využít další funkce Azure Monitoru, jako jsou výstrahy a Vizualizace. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Co můžete dělat s protokoly platformy v pracovním prostoru
Shromažďování protokolů platformy do pracovního prostoru Log Analytics umožňuje analyzovat protokoly všech vašich prostředků Azure dohromady a využít všech funkcí dostupných pro [protokoly azure monitoru,](data-platform-logs.md) které zahrnují následující:

* **Protokolové dotazy** – vytvářejte [dotazy protokolu](../log-query/log-query-overview.md) pomocí výkonného dotazovacího jazyka, abyste rychle analyzovali a získali přehled o diagnostických datech a analyzovali je pomocí dat shromážděných z jiných zdrojů v Azure Monitoru.
* **Výstrahy** – získejte proaktivní oznámení o kritických podmínkách a vzorech identifikovaných v protokolech prostředků pomocí [výstrah protokolu v Azure Monitoru](alerts-log.md).
* **Vizualizace** – připněte výsledky dotazu protokolu na řídicí panel Azure nebo jej zahrňte do sešitu jako součást interaktivní sestavy.

## <a name="prerequisites"></a>Požadavky
Pokud ještě nemáte, musíte [vytvořit nový pracovní prostor.](../learn/quick-create-workspace.md) Pracovní prostor nemusí být ve stejném předplatném jako prostředek odesílání protokolů tak dlouho, dokud uživatel, který konfiguruje nastavení má odpovídající RBAC přístup k oběma odběry.

## <a name="create-a-diagnostic-setting"></a>Vytvoření diagnostického nastavení
Odešlete protokoly platformy do pracovního prostoru Analýzy protokolů a do dalších cílů vytvořením diagnostického nastavení pro prostředek Azure. Podrobnosti najdete [v tématu Vytvoření diagnostického nastavení pro shromažďování protokolů a metrik v Azure.](diagnostic-settings.md)


## <a name="activity-log-collection"></a>Kolekce protokolu aktivit
Protokol aktivit můžete odeslat z libovolného předplatného až do pěti pracovních prostorů Analýzy protokolů. Data protokolu prostředků shromážděná v pracovním prostoru Log Analytics se ukládají v tabulce **AzureActivity.** 

## <a name="resource-log-collection-mode"></a>Režim kolekce protokolu prostředků
Data protokolu prostředků shromážděná v pracovním prostoru Log Analytics jsou uložena v tabulkách, jak je popsáno ve [struktuře protokolů monitorování Azure](../log-query/logs-structure.md). Tabulky používané protokoly prostředků závisí na typu kolekce, kterou prostředek používá:

- Diagnostika Azure – všechna zapsaná data jsou do tabulky _AzureDiagnostics._
- Specifické pro prostředek – data se zapisují do jednotlivé tabulky pro každou kategorii zdroje.

### <a name="azure-diagnostics-mode"></a>Režim Diagnostika Azure 
V tomto režimu budou všechna data z [libovolného nastavení diagnostiky](diagnostic-settings.md) shromážděna v tabulce _AzureDiagnostics._ Toto je starší metoda, kterou dnes používá většina služeb Azure.

Vzhledem k tomu, že více typů prostředků odesílá data do stejné tabulky, jeho schéma je nadmnožinou schémat všech různých shromažďovaných datových typů.

Zvažte následující příklad, kde jsou ve stejném pracovním prostoru shromažďována diagnostická nastavení pro následující datové typy:

- Protokoly auditu služby 1 (schéma skládající se ze sloupců A, B a C)  
- Protokoly chyb služby 1 (schéma skládající se ze sloupců D, E a F)  
- Protokoly auditu služby 2 (schéma skládající se ze sloupců G, H a I)  

Tabulka AzureDiagnostics bude vypadat takto:  

| ResourceProvider    | Kategorie     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | Auditní protokoly    | x1 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft.Service1 | Protokoly chyb    |    |    |    | Q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | Auditní protokoly    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | Protokoly chyb    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | Auditní protokoly    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | Auditní protokoly    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specifické pro prostředky
V tomto režimu jsou pro každou kategorii vybranou v diagnostickém nastavení vytvořeny jednotlivé tabulky ve vybraném pracovním prostoru. Tato metoda se doporučuje, protože usnadňuje práci s daty v dotazech protokolu, poskytuje lepší zjistitelnost schémat a jejich struktury, zlepšuje výkon v rámci latence ingestování a doby dotazu a možnost udělit práva RBAC na konkrétní tabulku. Všechny služby Azure se nakonec migrují do režimu specificképro prostředky. 

Výše uvedený příklad by měl za následek vytvoření tří tabulek:
 
- Tabulka *Service1AuditLogs* takto:

    | Zprostředkovatel prostředků | Kategorie | A | B | C |
    | -- | -- | -- | -- | -- |
    | Služba1 | Auditní protokoly | x1 | y1 | Z1 |
    | Služba1 | Auditní protokoly | x5 | y5 | z5 |
    | ... |

- Tabulka *Service1ErrorLogs* takto:  

    | Zprostředkovatel prostředků | Kategorie | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Služba1 | Protokoly chyb |  Q1 | w1 | e1 |
    | Služba1 | Protokoly chyb |  q2 | w2 | e2 |
    | ... |

- Table *Service2AuditLogs* takto:  

    | Zprostředkovatel prostředků | Kategorie | G | H | I |
    | -- | -- | -- | -- | -- |
    | Služba2 | Auditní protokoly | j1 | k1 | l1|
    | Služba2 | Auditní protokoly | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Výběr režimu sběru
Většina prostředků Azure bude zapisovat data do pracovního prostoru v režimu **Azure Diagnostic** nebo **Resource-Specific,** aniž byste měli na výběr. Podrobnosti o tom, který režim používá, naleznete [v dokumentaci ke každé službě.](diagnostic-logs-schema.md) Všechny služby Azure nakonec budou používat režim specifický pro prostředky. V rámci tohoto přechodu vám některé prostředky umožní vybrat režim v diagnostickém nastavení. Pro všechna nová nastavení diagnostiky byste měli určit režim specifický pro prostředky, protože to usnadňuje správu dat a může vám pomoci vyhnout se složitým migracím později.
  
   ![Volič režimu nastavení diagnostiky](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> V současné době **lze diagnostiku Azure** a režim **specifický pro prostředky** vybrat jenom při konfiguraci nastavení diagnostiky na webu Azure Portal. Pokud nakonfigurujete nastavení pomocí rozhraní PŘÍKAZOVÉHO PŘÍKAZU, PowerShellu nebo rozhraní Rest API, bude ve výchozím nastavení **diagnostika Azure**.

Existující diagnostické nastavení můžete upravit do režimu specifického pro prostředky. V takovém případě data, která již byla shromážděna zůstane v tabulce _AzureDiagnostics,_ dokud se odebere podle nastavení uchovávání informací pro pracovní prostor. Do vyhrazené tabulky budou shromážděny nové údaje. Pomocí operátoru [sjednocení](https://docs.microsoft.com/azure/kusto/query/unionoperator) můžete dotazovat data v obou tabulkách.

Pokračujte ve sledování blogu [Azure Updates](https://azure.microsoft.com/updates/) pro oznámení o službách Azure podporujících režim specifický pro prostředky.

### <a name="column-limit-in-azurediagnostics"></a>Limit sloupce v AzureDiagnostics
Existuje limit vlastností 500 pro všechny tabulky v protokolech monitorování Azure. Po dosažení tohoto limitu budou všechny řádky obsahující data s libovolnou vlastností mimo prvních 500 vynechány v době požití. Tabulka *AzureDiagnostics* je obzvláště náchylná k tomuto limitu, protože zahrnuje vlastnosti pro všechny služby Azure, které do ní zapisují.

Pokud shromažďujete protokoly prostředků z více služeb, _AzureDiagnostics_ může překročit tento limit a data budou chybět. Dokud všechny služby Azure nepodporují režim specifický pro prostředky, měli byste nakonfigurovat prostředky pro zápis do více pracovních prostorů, abyste snížili možnost dosažení limitu sloupců 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, protože velmi podrobnou sadu protokolů, je služba, která je známo, že psát velký počet sloupců a potenciálně způsobit _AzureDiagnostics_ překročit svůj limit. Pro všechna diagnostická nastavení nakonfigurovaná před povolením režimu specifického pro daný prostředek bude vytvořen nový sloupec pro každý jedinečně pojmenovaný uživatelský parametr proti jakékoli aktivitě. Další sloupce budou vytvořeny z důvodu podrobné povahy vstupů a výstupů aktivity.
 
Měli byste migrovat protokoly použít režim specifický pro prostředky co nejdříve. Pokud to nemůžete udělat okamžitě, prozatímní alternativou je izolovat protokoly Azure Data Factory do vlastního pracovního prostoru, aby se minimalizovalo riziko, že tyto protokoly ovlivní jiné typy protokolů, které se shromažďují ve vašich pracovních prostorech.


## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech prostředků](platform-logs-overview.md).
* [Vytvořte diagnostické nastavení pro shromažďování protokolů a metrik v Azure](diagnostic-settings.md).
