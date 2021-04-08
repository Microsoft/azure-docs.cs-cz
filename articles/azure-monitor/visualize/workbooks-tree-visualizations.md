---
title: Vizualizace stromu Azure Monitorového sešitu
description: Přečtěte si o všech vizualizacích stromu Azure Monitorového sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100612286"
---
# <a name="tree-visualizations"></a>Vizualizace stromu

Pracovní sešity podporují hierarchická zobrazení prostřednictvím stromové mřížky. Stromy umožňují rozšířit některé řádky na další úroveň pro prostředí pro přechod k podrobnostem.

Následující příklad ukazuje metriky stavu kontejneru (velikost pracovní sady), která je znázorněna jako mřížka stromu. Uzly nejvyšší úrovně jsou uzly Azure Kubernetes Service (AKS), další úroveň je lusky a konečná úroveň jsou kontejnery. Všimněte si, že stále můžete formátovat sloupce jako v mřížce (heatmapu, ikony, odkaz). Podkladový zdroj dat v tomto případě je Log Analytics pracovní prostor s protokoly AKS.

[![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Přidání mřížky stromu
1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. Vyberte **Přidat** a pak *Přidat dotaz* pro přidání ovládacího prvku dotazování protokolu do sešitu.
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Nastavení vizualizace na **mřížku**
6. Kliknutím na tlačítko **Nastavení sloupce** otevřete podokno nastavení.
7. V části **strom/seskupení podle nastavení** v dolní části nastavte:
    * Typ stromu: `Parent/Child`
    * Pole ID: `Id`
    * Nadřazené pole ID: `ParentId`
    * Zobrazit rozšíření na: `Name`
    * Zaškrtněte políčko *Rozbalit nejvyšší úroveň stromu* .
8. V části _sloupce_ nahoře nastavte:
    * _ID_ – vykreslovací modul sloupců: `Hidden`
    * _Nadřazené ID_ – vykreslovací modul sloupců: `Hidden`
    * _Požadavky_ – vykreslovací modul sloupců: `Bar` , barva: `Blue` , minimální hodnota: `0`
9. V dolní části podokna vyberte tlačítko **Uložit a zavřít** .

[![Snímek obrazovky se souhrnným zobrazením dlaždice s výše uvedeným dotazem a nastavením](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Nastavení stromu

| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `Id Field` | Jedinečné ID každého řádku v mřížce |
| `Parent Id Field` | ID nadřazeného prvku aktuálního řádku |
| `Show the expander on` | Sloupec, ve kterém se má zobrazit rozšíření stromu Je běžné, že stromové mřížky skryjí své ID a nadřazené pole ID, protože nejsou velmi čitelné. Místo toho se rozšíření zobrazí v poli s čitelnější hodnotou, jako je název entity. |
| `Expand the top level of the tree` | Pokud je zaškrtnuto, bude se stromová mřížka rozbalí na nejvyšší úrovni. Užitečné, pokud chcete ve výchozím nastavení zobrazit další informace. |

## <a name="grouping-in-a-grid"></a>Seskupení v mřížce

Seskupení vám umožní sestavovat hierarchická zobrazení podobná těm, která jsou výše v rámci významně jednodušších dotazů. Agregaci můžete ztratit na vnitřních uzlech stromu, ale budou přijatelné pro některé scénáře. Použijte *Group by* pro sestavování zobrazení stromu, když se podkladová sada výsledků nedá transformovat do správné bezplatné formy, například výstrahy, stavu a dat metriky.

## <a name="adding-a-tree-using-grouping"></a>Přidání stromu pomocí seskupení

1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. Vyberte **Přidat** a pak *Přidat dotaz* pro přidání ovládacího prvku dotazování protokolu do sešitu.
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Nastavte vizualizaci na *Grid*.
2. Kliknutím na **tlačítko Nastavení sloupce** otevřete podokno nastavení.
3. V části *strom/seskupení podle nastavení* v dolní části nastavte:
    * Typ stromu: `Group By`
    * Seskupit podle: `App`
    * Potom podle: `None`
    * Zaškrtněte políčko *Rozbalit nejvyšší úroveň stromu* .
4. V části *sloupce* nahoře nastavte:
    * Vykreslování sloupců *aplikace* :`Hidden`
    * *Požadavky* – vykreslovací modul sloupců: `Bar` , barva: `Blue` , minimální hodnota: `0`
5. V dolní části podokna vyberte tlačítko **Uložit a zavřít** .

[![Snímek obrazovky znázorňující vytvoření stromové vizualizace v sešitech](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [v sešitech vytvořit graf](workbooks-graph-visualizations.md).
* Naučte se, jak [v sešitech vytvořit dlaždici](workbooks-tile-visualizations.md).
