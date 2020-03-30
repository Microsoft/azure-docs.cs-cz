---
title: Vytváření zobrazení pro analýzu dat protokolu v Azure Monitoru | Dokumenty společnosti Microsoft
description: Pomocí Návrháře zobrazení v Azure Monitoru můžete vytvořit vlastní zobrazení, která se zobrazují na webu Azure Portal a obsahují různé vizualizace dat v pracovním prostoru Log Analytics. Tento článek obsahuje přehled Návrhářzobrazení a představuje postupy pro vytváření a úpravy vlastních zobrazení.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/10/2019
ms.openlocfilehash: 9a7521f61dc59bd954629a05638c159ab0e70556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658485"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Vytváření vlastních zobrazení pomocí Návrháře zobrazení v Azure Monitoru
Pomocí Návrháře zobrazení v Azure Monitoru můžete na webu Azure Portal vytvořit celou řadu vlastních zobrazení, která vám pomůžou vizualizovat data v pracovním prostoru Log Analytics. Tento článek představuje přehled návrháře zobrazení a postupy pro vytváření a úpravy vlastních zobrazení.

> [!IMPORTANT]
> Zobrazení ve službě Azure Monitor jsou postupně vyřazována a nahrazována [sešity,](workbooks-overview.md) které poskytují další funkce. Podrobnosti o převodu existujících zobrazení na sešity najdete v [tématu Návrhář zobrazení Azure Monitor u sešitů.](view-designer-conversion-overview.md)

Další informace o Návrháři zobrazení naleznete v tématu:

* [Odkaz na dlaždice](view-designer-tiles.md): Poskytuje referenční příručku k nastavení pro každou dostupnou dlaždici ve vlastních zobrazeních.
* [Odkaz na vizualizační část](view-designer-parts.md): Poskytuje referenční příručku k nastavení vizualizačních částí, které jsou k dispozici ve vlastních pohledech.


## <a name="concepts"></a>Koncepty
Zobrazení se zobrazují na stránce **Přehled** sledování Azure na webu Azure Portal. Otevřete tuto stránku z nabídky **Azure Monitor** kliknutím na **Další** v části **Přehledy.** Dlaždice v každém vlastním zobrazení jsou zobrazeny abecedně a dlaždice pro řešení monitorování jsou nainstalovány ve stejném pracovním prostoru.

![Stránka s přehledem](media/view-designer/overview-page.png)

Zobrazení, která vytvoříte pomocí návrháře zobrazení, obsahují prvky popsané v následující tabulce:

| Část | Popis |
|:--- |:--- |
| Dlaždice | Zobrazí se na stránce **Přehled** Azure Monitoru. Každá dlaždice zobrazuje vizuální souhrn vlastního zobrazení, které představuje. Každý typ dlaždice poskytuje jinou vizualizaci vašich záznamů. Vyberete dlaždici, chcete-li zobrazit vlastní zobrazení. |
| Vlastní zobrazení | Zobrazí se při výběru dlaždice. Každé zobrazení obsahuje jednu nebo více vizualizačních částí. |
| Vizualizační díly | Prezentujte vizualizaci dat v pracovním prostoru Log Analytics na základě jednoho nebo více [dotazů protokolu](../log-query/log-query-overview.md). Většina částí obsahuje záhlaví, které poskytuje vizualizaci na vysoké úrovni, a seznam, který zobrazuje nejlepší výsledky. Každý typ dílu poskytuje jinou vizualizaci záznamů v pracovním prostoru Log Analytics. Vyberete prvky v dílu k provedení dotazu protokolu, který poskytuje podrobné záznamy. |

## <a name="required-permissions"></a>Požadovaná oprávnění
K vytvoření nebo úpravě zobrazení vyžadujete v pracovním prostoru Log Analytics alespoň [oprávnění na úrovni přispěvatele.](manage-access.md#manage-access-using-azure-permissions) Pokud toto oprávnění nemáte, nebude v nabídce zobrazena možnost Návrhář zobrazení.


## <a name="work-with-an-existing-view"></a>Práce s existujícím zobrazením
Zobrazení, která byla vytvořena pomocí návrháře zobrazení, zobrazují následující možnosti:

![Nabídka Přehled](media/view-designer/overview-menu.png)

Možnosti jsou popsány v následující tabulce:

| Možnost | Popis |
|:--|:--|
| Obnovení   | Aktualizuje zobrazení s nejnovějšími daty. | 
| Protokoly      | Otevře [analýzu protokolů](../log-query/portals.md) pro analýzu dat pomocí dotazů protokolu. |
| Upravit       | Otevře zobrazení v Návrháři zobrazení a upraví jeho obsah a konfiguraci.  |
| Klonování      | Vytvoří nové zobrazení a otevře jej v Návrháři zobrazení. Název nového zobrazení je stejný jako původní název, ale s *připojenou kopií.* |
| Rozsah dat | Nastavte filtr data a časového rozsahu pro data, která jsou zahrnuta v zobrazení. Toto časové období se použije před libovolnými rozsahy dat nastavených v dotazech v zobrazení.  |
| +          | Definujte vlastní filtr, který je definován pro zobrazení. |


## <a name="create-a-new-view"></a>Vytvoření nového zobrazení
Nové zobrazení v Návrháři zobrazení můžete vytvořit tak, že v nabídce pracovního prostoru Analýzy protokolů vyberete **Návrhář zobrazení.**

![Dlaždice Zobrazit návrháře](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Práce s návrhářem zobrazení
Návrhář zobrazení slouží k vytváření nových zobrazení nebo úpravám existujících zobrazení. 

Návrhář zobrazení má tři podokna: 
* **Návrh**: Obsahuje vlastní zobrazení, které vytváříte nebo upravujete. 
* **Ovládací prvky**: Obsahuje dlaždice a části, které přidáte do **podokna Návrh.** 
* **Vlastnosti**: Zobrazí vlastnosti dlaždic nebo vybraných dílů.

![Návrhář zobrazení](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurace dlaždice zobrazení
Vlastní zobrazení může mít pouze jednu dlaždici. Chcete-li zobrazit aktuální dlaždici nebo vybrat alternativní dlaždici, vyberte kartu **Dlaždice** v podokně **Ovládací prvek.** Podokno **Vlastnosti** zobrazuje vlastnosti aktuální dlaždice. 

Vlastnosti dlaždice můžete nakonfigurovat podle informací v [odkazu dlaždice](view-designer-tiles.md) a klepnutím na **tlačítko Použít** změny uložit.

### <a name="configure-the-visualization-parts"></a>Konfigurace vizualizačních dílů
Zobrazení může obsahovat libovolný počet vizualizačních částí. Chcete-li přidat součásti do zobrazení, vyberte kartu **Zobrazení** a pak vyberte vizualizační díl. Podokno **Vlastnosti** zobrazuje vlastnosti vybraného dílu. 

Vlastnosti zobrazení můžete nakonfigurovat podle informací v [odkazu vizualizační ho dílu](view-designer-parts.md) a klepnutím na tlačítko **Použít** změny uložit.

Pohledy mají pouze jeden řádek vizualizačních částí. Uspořádání existujících součástí můžete změnit jejich přetažením do nového umístění.

Vizualizační díl můžete ze pohledu odstranit výběrem **x** v pravém horním rohu dílu.


### <a name="menu-options"></a>Možnosti nabídky
Možnosti práce se zobrazeními v režimu úprav jsou popsány v následující tabulce.

![Nabídka Upravit](media/view-designer/edit-menu.png)

| Možnost | Popis |
|:--|:--|
| Uložit        | Uloží provedené změny a zavře zobrazení. |
| Zrušit      | Zahodí změny a zavře zobrazení. |
| Odstranit zobrazení | Odstraní zobrazení. |
| Export      | Exportuje zobrazení do [šablony Azure Resource Manager,](../../azure-resource-manager/templates/template-syntax.md) kterou můžete importovat do jiného pracovního prostoru. Název souboru je název zobrazení a má *rozšíření omsview.* |
| Import      | Importuje soubor *omsview,* který jste exportovali z jiného pracovního prostoru. Tato akce přepíše konfiguraci existujícího zobrazení. |
| Klonování       | Vytvoří nové zobrazení a otevře jej v Návrháři zobrazení. Název nového zobrazení je stejný jako původní název, ale s *připojenou kopií.* |

## <a name="next-steps"></a>Další kroky
* Přidejte [dlaždice](view-designer-tiles.md) do vlastního zobrazení.
* Přidejte části [vizualizace](view-designer-parts.md) do vlastního zobrazení.
