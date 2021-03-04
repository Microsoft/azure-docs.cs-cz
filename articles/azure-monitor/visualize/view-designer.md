---
title: Vytváření zobrazení k analýze dat protokolu v Azure Monitor | Microsoft Docs
description: Pomocí návrháře zobrazení v Azure Monitor můžete vytvořit vlastní zobrazení, která jsou zobrazena v Azure Portal a obsahují celou řadu vizualizací dat v pracovním prostoru Log Analytics. Tento článek obsahuje přehled návrháře zobrazení a prezentuje postupy pro vytváření a úpravy vlastních zobrazení.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: 11a6d21a7a066d331a92453e9aaa876800da0bf4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700537"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Vytváření vlastních zobrazení pomocí návrháře zobrazení v Azure Monitor
Pomocí návrháře zobrazení v Azure Monitor můžete v Azure Portal vytvořit nejrůznější vlastní zobrazení, která vám pomůžou vizualizovat data v pracovním prostoru Log Analytics. Tento článek obsahuje přehled návrháře zobrazení a postupy pro vytváření a úpravy vlastních zobrazení.

> [!IMPORTANT]
> Zobrazení v Azure Monitor byla převedena na [sešity](workbooks-overview.md) , které poskytují další funkce. Podrobnosti o převodu stávajících zobrazení na sešity najdete v tématu [Průvodce přechodem Azure monitor návrháře zobrazení na sešity](view-designer-conversion-overview.md) .
 


Další informace o návrháři zobrazení najdete v těchto tématech:

* [Odkaz na dlaždici](view-designer-tiles.md): poskytuje referenční příručku k nastavením pro každý z dostupných dlaždic ve vlastních zobrazeních.
* [Odkaz na součást vizualizace](view-designer-parts.md): poskytuje referenční příručku k nastavením pro části vizualizace, které jsou k dispozici ve vlastních zobrazeních.


## <a name="concepts"></a>Koncepty
Zobrazení se zobrazí na stránce **přehled** Azure Monitor v Azure Portal. Tuto stránku otevřete z nabídky **Azure monitor** kliknutím na **Další** v části **přehledy** . Dlaždice v každém vlastním zobrazení se zobrazují abecedně a dlaždice pro řešení monitorování jsou nainstalovány do stejného pracovního prostoru.

![Stránka přehledu](media/view-designer/overview-page.png)

Zobrazení, která vytvoříte pomocí návrháře zobrazení, obsahují prvky, které jsou popsány v následující tabulce:

| Část | Popis |
|:--- |:--- |
| Dlaždice | Se zobrazí na stránce s **přehledem** Azure monitor. Každá dlaždice zobrazuje vizuální souhrn vlastního zobrazení, které představuje. Každý typ dlaždice poskytuje jinou vizualizaci záznamů. Výběrem dlaždice zobrazíte vlastní zobrazení. |
| Vlastní zobrazení | Zobrazí se při výběru dlaždice. Každé zobrazení obsahuje jednu nebo více částí vizualizace. |
| Části vizualizace | Prezentovat vizualizaci dat v pracovním prostoru Log Analytics v závislosti na jednom nebo několika [dotazech protokolu](../logs/log-query-overview.md). Většina částí zahrnuje hlavičku, která poskytuje vizualizaci na nejvyšší úrovni a seznam, který zobrazuje nejvyšší výsledky. Každý typ součásti poskytuje jinou vizualizaci záznamů v pracovním prostoru Log Analytics. V části vyberete prvky pro provedení dotazu protokolu, který poskytuje podrobné záznamy. |

## <a name="required-permissions"></a>Požadovaná oprávnění
Pro vytváření nebo úpravy zobrazení potřebujete alespoň [oprávnění na úrovni přispěvatele](../logs/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics. Pokud toto oprávnění nemáte, v nabídce se nezobrazí možnost Návrhář zobrazení.


## <a name="work-with-an-existing-view"></a>Práce s existujícím zobrazením
Zobrazení, která byla vytvořena pomocí návrháře zobrazení, zobrazují následující možnosti:

![Nabídka přehled](media/view-designer/overview-menu.png)

Možnosti jsou popsány v následující tabulce:

| Možnost | Popis |
|:--|:--|
| Aktualizovat   | Aktualizuje zobrazení o nejnovější data. | 
| Protokoly      | Otevře [Log Analytics](../logs/log-query-overview.md) pro analýzu dat pomocí dotazů protokolu. |
| Upravit       | Otevře zobrazení v Návrháři zobrazení, kde můžete upravit jeho obsah a konfiguraci.  |
| Klonování      | Vytvoří nové zobrazení a otevře ho v Návrháři zobrazení. Název nového zobrazení je stejný jako původní název, ale s připojeným *kopírováním* . |
| Rozsah dat | U dat obsažených v zobrazení nastavte filtr rozsahu data a času. Tento rozsah kalendářních dat se použije před všemi rozsahy dat nastavenými v dotazech v zobrazení.  |
| +          | Definujte vlastní filtr, který je definován pro zobrazení. |


## <a name="create-a-new-view"></a>Vytvořit nové zobrazení
Nové zobrazení můžete vytvořit v Návrháři zobrazení tak, že v nabídce pracovního prostoru Log Analytics vyberete **zobrazení Návrhář** .

![Dlaždice návrháře zobrazení](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Práce s návrhářem zobrazení
Pomocí návrháře zobrazení můžete vytvářet nová zobrazení nebo upravovat existující. 

Návrhář zobrazení má tři podokna: 
* **Design**: obsahuje vlastní zobrazení, které vytváříte nebo upravujete. 
* **Ovládací prvky**: obsahuje dlaždice a části, které přidáte do podokna **Návrh** . 
* **Properties**: zobrazí vlastnosti dlaždic nebo vybraných částí.

![Návrhář zobrazení](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurovat dlaždici zobrazení
Vlastní zobrazení může mít pouze jednu dlaždici. Chcete-li zobrazit aktuální dlaždici nebo vybrat jinou, vyberte v podokně **ovládací** okno kartu **dlaždice** . V podokně **vlastnosti** se zobrazí vlastnosti aktuální dlaždice. 

Můžete nakonfigurovat vlastnosti dlaždice podle informací v [odkazu dlaždice](view-designer-tiles.md) a potom kliknutím na **použít** Uložit změny.

### <a name="configure-the-visualization-parts"></a>Konfigurovat části vizualizace
Zobrazení může obsahovat libovolný počet částí vizualizace. Chcete-li přidat části do zobrazení, vyberte kartu **zobrazení** a potom vyberte součást vizualizace. V podokně **vlastnosti** se zobrazí vlastnosti vybrané části. 

Můžete nakonfigurovat vlastnosti zobrazení podle informací v [odkazu na součást vizualizace](view-designer-parts.md) a potom kliknutím na tlačítko **použít** Uložit změny.

Zobrazení mají pouze jeden řádek částí vizualizace. Existující části můžete změnit jejich přetažením na nové místo.

Část vizualizace můžete ze zobrazení odebrat výběrem **X** v pravém horním rohu části.


### <a name="menu-options"></a>Možnosti nabídky
Možnosti pro práci se zobrazeními v režimu úprav jsou popsány v následující tabulce.

![Nabídka upravit](media/view-designer/edit-menu.png)

| Možnost | Popis |
|:--|:--|
| Uložit        | Uloží změny a zavře zobrazení. |
| Zrušit      | Zahodí vaše změny a zavře zobrazení. |
| Odstranit zobrazení | Odstraní zobrazení. |
| Export      | Exportuje zobrazení do [šablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) , kterou můžete importovat do jiného pracovního prostoru. Název souboru je název zobrazení a má příponu *omsview* . |
| Import      | Importuje soubor *omsview* , který jste exportovali z jiného pracovního prostoru. Tato akce přepíše konfiguraci stávajícího zobrazení. |
| Klonování       | Vytvoří nové zobrazení a otevře ho v Návrháři zobrazení. Název nového zobrazení je stejný jako původní název, ale s připojeným *kopírováním* . |

## <a name="next-steps"></a>Další kroky
* Přidejte [dlaždice](view-designer-tiles.md) do vlastního zobrazení.
* Přidejte do vlastního zobrazení [části vizualizace](view-designer-parts.md) .