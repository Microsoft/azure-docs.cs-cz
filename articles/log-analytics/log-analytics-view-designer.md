---
title: Vytvoření zobrazení k analýze dat v Azure Log Analytics | Microsoft Docs
description: Pomocí zobrazení návrhu v analýzy protokolů můžete vytvořit vlastní zobrazení, která se zobrazí na portálu Azure a obsahují řadu vizualizaci dat v pracovním prostoru analýzy protokolů. Tento článek obsahuje přehled Návrhář zobrazení a uvede postupy pro vytváření a úpravy vlastních zobrazení.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: d63d47c39054230307416e24ed1c8295fbf68d93
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29939865"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Vytváření vlastních pohledů pomocí návrháře zobrazení v analýzy protokolů
Pomocí zobrazení návrhu v [Azure Log Analytics](log-analytics-overview.md), různých vlastních zobrazení můžete vytvořit na portálu Azure, který můžete vizualizovat data v pracovním prostoru analýzy protokolů. Tento článek obsahuje přehled Návrhář zobrazení a postupy pro vytváření a úpravy vlastních zobrazení.

Další informace o návrháři zobrazení najdete v tématu:

* [Odkaz na dlaždici](log-analytics-view-designer-tiles.md): poskytuje referenční příručka k nastavení pro jednotlivé dostupné dlaždice do vlastních zobrazení.
* [Odkaz na část vizualizace](log-analytics-view-designer-parts.md): poskytuje referenční příručka k nastavení pro vizualizaci částí, které jsou k dispozici do vlastních zobrazení.


## <a name="concepts"></a>Koncepty
Zobrazení se zobrazují na **přehled** stránka pracovní prostor analýzy protokolů na portálu Azure. Dlaždice v každém vlastní zobrazení se zobrazí abecedně, a dlaždice pro řešení jsou nainstalované ve stejném pracovním prostoru.

![Stránka s přehledem](media/log-analytics-view-designer/overview-page.png)

Zobrazení, které vytvoříte pomocí návrháře zobrazení obsahují prvky, které jsou popsané v následující tabulce:

| Část | Popis |
|:--- |:--- |
| Dlaždice | Zobrazí se na pracovní prostor analýzy protokolů **přehled** stránky. Každou dlaždici zobrazí přehled vlastních zobrazení, který představuje. Každý typ dlaždice poskytuje různé vizualizace vašeho záznamů. Vyberete dlaždici vlastní zobrazení. |
| Vlastní zobrazení | Zobrazí, když vyberete dlaždici. Každé zobrazení obsahuje jednu nebo více částí vizualizace. |
| Vizualizace částí | K dispozici vizualizace dat v pracovním prostoru analýzy protokolů založené na jeden nebo více [protokolu hledání](log-analytics-log-searches.md). Většina částí zahrnují záhlaví, která poskytuje podrobný vizualizace, a seznam, který zobrazí nejvyšší výsledky. Každý typ část poskytuje různé vizualizace záznamy v pracovním prostoru analýzy protokolů. Elementy, vyberte v části pro vyhledávání protokolu, který poskytuje podrobné záznamy. |


## <a name="work-with-an-existing-view"></a>Práce s existující zobrazení
Zobrazení, které byly vytvořeny pomocí návrháře zobrazení zobrazí následující možnosti:

![Přehled nabídky](media/log-analytics-view-designer/overview-menu.png)

Tyto možnosti jsou popsány v následující tabulce:

| Možnost | Popis |
|:--|:--|
| Obnovení   | Aktualizuje zobrazení s nejnovější data. | 
| Analýzy | Otevře se [Advanced Analytics portál](log-analytics-log-search-portals.md#advanced-analytics-portal) k analýze dat pomocí protokolu hledání. |
| Filtr    | Nastaví filtr času pro data, která je zahrnuta v zobrazení. |
| Upravit      | Otevře zobrazení v Návrháři zobrazení, chcete-li upravit jeho obsah a konfiguraci.  |
| Klon     | Vytvoří nové zobrazení a otevře ji v Návrháři zobrazení. Název nového zobrazení je stejný jako původní název, ale s *kopie* přidá se k němu. |


## <a name="create-a-new-view"></a>Vytvoření nového zobrazení
Můžete vytvořit nové zobrazení v Návrháři zobrazení tak, že vyberete **Návrhář zobrazení** na dlaždici **přehled** stránka pracovní prostor analýzy protokolů.

![Dlaždice Návrhář zobrazení](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Práce s Návrhář zobrazení
Návrhář zobrazení použijete k vytvoření nového zobrazení nebo upravit stávající. 

Návrhář zobrazení má tři podokna: 
* **Návrh**: obsahuje vlastní zobrazení, který vytváříte nebo upravujete. 
* **Ovládací prvky**: obsahuje dlaždice a částí, které přidáte do **návrhu** podokně. 
* **Vlastnosti**: Zobrazí vlastnosti dlaždice nebo vybraných částí.

![Návrhář zobrazení](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurace dlaždici zobrazení
Vlastní zobrazení může mít pouze jedna dlaždice. Chcete-li zobrazit aktuální dlaždice nebo vyberte alternativní šablonu, vyberte **dlaždici** ve **řízení** podokně. **Vlastnosti** podokně se zobrazí vlastnosti aktuální dlaždice. 

Můžete konfigurovat vlastnosti dlaždice podle informací v [dlaždici odkaz](log-analytics-view-designer-tiles.md) a pak klikněte na **použít** a uložte změny.

### <a name="configure-the-visualization-parts"></a>Konfigurace části vizualizace
Zobrazení může obsahovat libovolný počet částí vizualizace. K přidání částí do zobrazení, vyberte **zobrazení** a pak vyberte součástí vizualizace. **Vlastnosti** podokně se zobrazí vlastnosti vybrané části. 

Můžete konfigurovat vlastnosti zobrazení podle informací v [vizualizace část odkaz](log-analytics-view-designer-parts.md) a pak klikněte na **použít** a uložte změny.

Zobrazení je k dispozici pouze jeden řádek částí vizualizace. Stávající části můžete přeuspořádat jejich přetažením do nového umístění.

Vizualizace součástí ze zobrazení můžete odebrat výběrem **X** v horní části práva.


### <a name="menu-options"></a>Možnosti nabídky
Možnosti práce se zobrazeními v režimu úprav jsou popsány v následující tabulce.

![Upravit nabídky](media/log-analytics-view-designer/edit-menu.png)

| Možnost | Popis |
|:--|:--|
| Uložení        | Uloží změny a zavře zobrazení. |
| Zrušit      | Zruší změny a zavře zobrazení. |
| Odstranit zobrazení | Odstraní zobrazení. |
| Export      | Exportuje zobrazení tak, aby [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) který můžete importovat do jiného pracovního prostoru. Název souboru je název zobrazení, a má *omsview* rozšíření. |
| Import      | Importy *omsview* soubor, který jste exportovali z jiného pracovního prostoru. Tato akce přepíše konfiguraci existující zobrazení. |
| Klon       | Vytvoří nové zobrazení a otevře ji v Návrháři zobrazení. Název nového zobrazení je stejný jako původní název, ale s *kopie* přidá se k němu. |
| Publikování     | Exportuje zobrazení do souboru JSON, který můžete vložit do [řešení pro správu](../operations-management-suite/operations-management-suite-solutions-resources-views.md). Název souboru je stejný jako název zobrazení, ale *json* rozšíření. Druhý soubor, který je vytvořen s *resjson* rozšíření, obsahuje hodnoty pro prostředky, které jsou definovány v souboru JSON.

## <a name="next-steps"></a>Další postup
* Přidat [dlaždice](log-analytics-view-designer-tiles.md) do vlastních zobrazení.
* Přidat [vizualizace částí](log-analytics-view-designer-parts.md) do vlastních zobrazení.
