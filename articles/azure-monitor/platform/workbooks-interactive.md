---
title: Sešity Azure Monitoru s vlastními parametry
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658264"
---
# <a name="interactive-workbooks"></a>Interaktivní sešity

Sešity umožňují autorům vytvářet interaktivní sestavy a prostředí pro své spotřebitele. Interaktivita je podporována mnoha způsoby.

## <a name="parameter-changes"></a>Změny parametrů
Když uživatel sešitu aktualizuje parametr, každý ovládací prvek, který používá parametr, se automaticky aktualizuje a překreslí tak, aby odrážel nový stav. Takto podporuje interaktivitu většina sestav portálu Azure. Sešity poskytují to velmi přímočarým způsobem s minimálním uživatelským úsilím.

Další informace o [parametrech v sešitech](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Kliknutí na řádek mřížky
Sešity umožňují autorům vytvářet scénáře, kdy klepnutím na řádek v mřížce aktualizujete následné grafy na základě obsahu řádku. 

Uživatel může mít například mřížku, která zobrazuje seznam požadavků a některé statistiky, jako je počet selhání. Mohli by ji nastavit tak, že kliknutím na řádek odpovídající požadavku bude mít za následek podrobné grafy níže, které se aktualizují, aby se filtrovali až na tento požadavek.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Nastavení interaktivity na řádku mřížky klikněte na
1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů _úpravy_
2. Pomocí odkazu _Přidat dotaz_ přidejte do sešitu ovládací prvek dotazu protokolu. 
3. Vyberte typ dotazu jako _Protokol_, typ prostředku (například Application Insights) a prostředky, na které chcete cílit.
4. Zadání kql pro analýzu pomocí editoru dotazů
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`zobrazíte výsledky
6. Klikněte na ikonu _Upřesnit nastavení_ v zápatí dotazu (ikona vypadá jako ozubené kolo). Tím se otevře podokno upřesňujících nastavení. 
7. Zkontrolujte nastavení:`When an item is selected, export a parameter`
    1. Pole pro export:`Request`
    2. Název parametru:`SelectedRequest`
    3. Výchozí hodnota:`All requests`
    
    ![Obrázek znázorňující rozšířený editor s nastavením pro export polí jako parametrů](./media/workbooks-interactive/advanced-settings.png)

8. Klikněte na `Done Editing` (Další).
9. Přidejte další ovládací prvek dotazu pomocí kroků 2 a 3.
10. Zadání kql pro analýzu pomocí editoru dotazů
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`zobrazíte výsledky.
12. Změna _vizualizace_ na`Area chart`
12. Klikněte na řádek v první mřížce. Všimněte si, jak oblastní graf pod filtry na vybraný požadavek.

Výsledná sestava vypadá takto v režimu úprav:

![Obrázek znázorňující vytvoření interaktivního prostředí pomocí kliknutí na řádek mřížky](./media/workbooks-interactive//grid-click-create.png)

Na obrázku níže je zobrazena propracovanější interaktivní sestava v režimu čtení založená na stejných principech. Sestava používá kliknutí mřížky k exportu parametrů - které se zase používají ve dvou grafech a textovém bloku.

![Obrázek znázorňující vytvoření interaktivního prostředí pomocí kliknutí na řádek mřížky](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Export obsahu celého řádku
Někdy je žádoucí exportovat celý obsah vybraného řádku namísto pouze určitého sloupce. V takových případech `Field to export` ponechte vlastnost unset v kroku 7.1 výše. Sešity exportují celý obsah řádku jako json do parametru. 

Na odkazující KQL ovládací `todynamic` prvek, použijte funkci analyzovat json a přístup k jednotlivým sloupcům.

 ## <a name="grid-cell-clicks"></a>Kliknutí na buňky mřížky
Sešity umožňují autorům přidávat interaktivitu pomocí speciálního typu vykreslovače sloupců mřížky nazývaného `link renderer`. Vykreslovač propojení převede buňku mřížky na hypertextový odkaz na základě obsahu buňky. Sešity podporují mnoho druhů vykreslovačů odkazů – včetně těch, které umožňují otevírání listů přehledu prostředků, prohlížečů vak vlastností, vyhledávání Přehledů aplikací, využití, trasování transakcí atd.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Nastavení interaktivity pomocí kliknutí na buňky mřížky
1. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů _úpravy_
2. Pomocí odkazu _Přidat dotaz_ přidejte do sešitu ovládací prvek dotazu protokolu. 
3. Vyberte typ dotazu jako _Protokol_, typ prostředku (například Application Insights) a prostředky, na které chcete cílit.
4. Zadání kql pro analýzu pomocí editoru dotazů
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`zobrazíte výsledky
6. Kliknutím na _Nastavení sloupců_ otevřete podokno nastavení.
7. V části _Sloupce_ nastavte:
    1. _Ukázka_ - Vykreslovač sloupců: `Link`, Zobrazení otevření: `Cell Details`, Popisek odkazu:`Sample`
    2. _Počet_ - Vykreslovač sloupců: `Bar`, Paleta barev: `Blue`, Minimální hodnota:`0`
    3. _Požadavek_ - vykreslovač sloupců:`Automatic`
    4. Chcete-li použít změny, klepněte na tlačítko _Uložit a zavřít._
8. Klikněte na `Sample` jeden z odkazů v mřížce. Tím se otevře podokno vlastností s podrobnostmi o ukázkové žádosti.

    ![Obrázek znázorňující vytvoření interaktivního prostředí pomocí kliknutí na buňku mřížky](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Akce vykreslovače propojení
| Akce propojení | Akce při kliknutí |
|:------------- |:-------------|
| `Generic Details` | Zobrazuje hodnoty řádků v okně kontextu mřížky vlastností. |
| `Cell Details` | Zobrazuje hodnotu buňky v okně kontextu mřížky vlastností. Užitečné, pokud buňka obsahuje dynamický typ s informacemi (například json s vlastnostmi požadavku, jako je umístění, instance role atd.). |
| `Cell Details` | Zobrazuje hodnotu buňky v okně kontextu mřížky vlastností. Užitečné, pokud buňka obsahuje dynamický typ s informacemi (například json s vlastnostmi požadavku, jako je umístění, instance role atd.). |
| `Custom Event Details` | Otevře podrobnosti hledání Application Insights s vlastním ID události (itemId) v buňce. |
| `* Details` | Podobně jako podrobnosti o vlastní události, s výjimkou závislostí, výjimek, zobrazení stránek, požadavků a trasování. |
| `Custom Event User Flows` | Otevře prostředí Uživatelských toků aplikace, které se zaměřilo na název vlastní události v buňce. |
| `* User Flows` | Podobné tok uživatelů vlastní události s výjimkou výjimek, zobrazení stránek a požadavků |
| `User Timeline` | Otevře časovou osu uživatele s ID uživatele (user_Id) v buňce. |
| `Session Timeline` | Otevře prostředí pro vyhledávání Application Insights pro hodnotu v buňce (například vyhledejte text abc, kde abc je hodnota v buňce) |
| `Resource overview` | Otevření přehledu zdroje na portálu na základě hodnoty ID prostředku v buňce |

## <a name="conditional-visibility"></a>Podmíněná viditelnost
Sešit umožňuje uživatelům zobrazit nebo zmizet určité ovládací prvky na základě hodnot parametrů. To umožňuje autorům, aby sestavy vypadaly jinak na základě stavu vstupu uživatele nebo telemetrie. Příkladem je zobrazení spotřebitelů jen shrnutí, když jsou věci dobré, ale ukazují úplné podrobnosti, když je něco špatně.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Nastavení interaktivity pomocí podmíněné viditelnosti
1. Podle pokynů v `Setting up interactivity on grid row click` části nastavte dva interaktivní ovládací prvky.
2. Nahoře přidejte nový parametr:
    1. Jméno:`ShowDetails`
    2. Typ parametru:`Drop down`
    3. Požadované:`checked`
    4. Získat data z:`JSON`
    5. Vstup JSON:`["Yes", "No"]`
    6. Uložit pro potvrzení změn.
3. Nastavit hodnotu parametru na`Yes`
4. V ovládacím prvku dotazu s plošným grafem klikněte na ikonu _Upřesnit nastavení_ (ikona ozubeného kola).
5. Zkontrolujte nastavení`Make this item conditionally visible`
    1. Tato položka je `ShowDetails` viditelná, pokud hodnota parametru `equals``Yes`
6. Chcete-li potvrdit změny, klepněte na _tlačítko Hotovo úpravy._
7. Chcete-li přejít do režimu čtení, klepněte na _tlačítko Dokončeno s úpravami_ na panelu nástrojů sešitu.
8. Přepněte hodnotu `ShowDetails` `No`parametru na . Všimněte si, že níže uvedená tabulka zmizí.

Na obrázku níže je `ShowDetails` zobrazen viditelný případ, kde je`Yes`

![Obrázek znázorňující podmíněnou viditelnost, kde je graf viditelný](./media/workbooks-interactive/conditional-visibility.png)

Na obrázku níže je `ShowDetails` zobrazen skrytý případ, kde je`No`

![Obrázek znázorňující podmíněnou viditelnost, kde je graf skrytý](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Další kroky


* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
