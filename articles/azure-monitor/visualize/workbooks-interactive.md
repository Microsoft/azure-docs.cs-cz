---
title: Azure Monitor sešitů s vlastními parametry
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 2cb284e1978ad6c890835318c51c6095891397cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723006"
---
# <a name="interactive-workbooks"></a>Interaktivní sešity

Sešity umožňují autorům vytvářet interaktivní sestavy a prostředí pro své příjemce. Interaktivita se podporuje mnoha různými způsoby.

## <a name="parameter-changes"></a>Změny parametru

Když uživatel sešitu aktualizuje parametr, jakýkoli ovládací prvek, který používá parametr, se automaticky aktualizuje a znovu vykreslí, aby odrážel nový stav. To je způsob, jakým většina sestav Azure Portal podporuje interaktivitu. Sešity poskytují přímý posun s minimálním úsilím uživatele.

Další informace o [parametrech v sešitech](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Mřížka, dlaždice, výběry grafu

Pracovní sešity umožňují autorům vytvářet scénáře, ve kterých se po kliknutí na řádek v mřížce aktualizují další grafy na základě obsahu řádku.

Uživatel může například mít mřížku, která zobrazuje seznam požadavků a některé statistiky, jako je počet selhání. Můžou si je nastavit tak, aby klikli na řádek, který odpovídá požadavku, a výsledkem je, že se v grafu níže aktualizují, aby se vyfiltroval jenom na tento požadavek.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Nastavení interaktivity při kliknutí na řádek mřížky

1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz _Přidat dotaz_ .
3. Vyberte typ dotazu jako _protokol_, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` zobrazení výsledků
6. Vyberte ikonu _Upřesnit nastavení_ v zápatí dotazu (ikona vypadá jako ozubené kolo). Otevře se podokno Upřesnit nastavení.
7. Ověřte nastavení: `When an item is selected, export a parameter` .
8. V kontrolovaném nastavení vyberte *Přidat parametr* a vyplňte ho informacemi níže.
    1. Pole, které se má exportovat: `Request`
    2. Název parametru: `SelectedRequest`
    3. Výchozí hodnota: `All requests`
9. Vyberte možnost Uložit.

    ![Snímek obrazovky znázorňující rozšířený editor s nastavením pro export polí jako parametrů](./media/workbooks-interactive/export-parameters-add.png)

10. Vyberte `Done Editing`.
11. Přidejte další ovládací prvek dotazu pomocí kroků 2 a 3.
12. Pomocí Editoru dotazů zadejte KQL pro analýzu.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` pro zobrazení výsledků.
14. Změňte _vizualizaci_ na `Area chart` .
15. Zvolte řádek, který chcete vybrat v první mřížce. Všimněte si, jak plošný graf níže filtruje vybraný požadavek.

Výsledná sestava bude vypadat v režimu úprav:

![Snímek obrazovky prvního dvou dotazů v režimu úprav.](./media/workbooks-interactive//interactivity-grid-create.png)

Následující obrázek ukazuje komplexnější interaktivní sestavu v režimu čtení na základě stejných principů. Sestava používá kliknutí na mřížku k exportu parametrů, které se pak používají ve dvou grafech a textovém bloku.

![Snímek obrazovky se sestavou, která používá kliknutí v mřížce v režimu čtení.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Export obsahu celého řádku

Někdy je žádoucí exportovat celý obsah vybraného řádku místo pouze konkrétního sloupce. V takových případech ponechte `Field to export` vlastnost nenastavenou v kroku 7,1 výše. V sešitech budou exportovány celé obsahy řádků jako JSON pro parametr.

Na referenčním ovládacím prvku KQL použijte `todynamic` funkci pro analýzu JSON a přístup k jednotlivým sloupcům.

## <a name="grid-cell-clicks"></a>Kliknutí na buňky mřížky

Pracovní sešity umožňují autorům přidat interaktivitu prostřednictvím speciálního typu vykreslovacího modulu sloupců tabulky s názvem `link renderer` . Modul pro vykreslování odkazů převede buňku mřížky na hypertextový odkaz na základě obsahu buňky. Pracovní sešity podporují mnoho druhů vykreslovacích objektů, včetně těch, které umožňují otevřít okna s přehledem prostředků, diváků kontejnerů vlastností, hledání v App Insights, využití, trasování transakcí atd.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Nastavení interaktivity pomocí kliknutí na buňky mřížky

1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz _Přidat dotaz_ .
3. Vyberte typ dotazu jako _protokol_, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` zobrazení výsledků
6. Vyberte _Nastavení sloupce_ a otevřete tak podokno nastavení.
7. V části _sloupce_ nastavte:
    1. _Ukázka_ -sloupcový Renderer: `Link` , zobrazení pro otevření: `Cell Details` , popisek odkazu: `Sample`
    2. _Count_ -Column Renderer: `Bar` , barevná paleta: `Blue` , minimální hodnota: `0`
    3. Vykreslovací modul sloupců _požadavků_ :`Automatic`
    4. Pokud chcete použít změny, klikněte na _Uložit a zavřít_ .

    ![Snímek obrazovky s kartou nastavení sloupce](./media/workbooks-interactive/column-settings.png)

8. Klikněte na jeden z `Sample` odkazů v mřížce. Otevře se okno s podrobnostmi o vzorku žádosti.

    ![Snímek obrazovky s podoknem podrobností žádosti s ukázkovým požadavkem.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Akce vykreslování odkazů

| Akce propojení | Akce při kliknutí |
|:------------- |:-------------|
| `Generic Details` | Zobrazuje hodnoty řádků na kartě kontextu v tabulce vlastností. |
| `Cell Details` | Zobrazuje hodnotu buňky na kartě kontextu v tabulce vlastností. Užitečné, pokud buňka obsahuje dynamický typ s informacemi (například JSON s vlastnostmi žádosti, jako je umístění, instance role atd.). |
| `Cell Details` | Zobrazuje hodnotu buňky na kartě kontextu v tabulce vlastností. Užitečné, pokud buňka obsahuje dynamický typ s informacemi (například JSON s vlastnostmi žádosti, jako je umístění, instance role atd.). |
| `Custom Event Details` | Otevře Application Insights podrobnosti hledání s ID vlastní události ( `itemId` ) v buňce. |
| `* Details` | Podobně jako v podrobnostech o vlastních událostech, s výjimkou závislostí, výjimek, zobrazení stránky, požadavků a trasování. |
| `Custom Event User Flows` | Otevře Application Insights Toky uživatelů prostředí, které se v buňce Překlopí na vlastní název události. |
| `* User Flows` | Podobně jako u vlastních událostí Toky uživatelů s výjimkou výjimek, zobrazení stránky a požadavků |
| `User Timeline` | Otevře časovou osu uživatele s ID uživatele (user_Id) v buňce. |
| `Session Timeline` | Otevře Application Insights vyhledávání hodnoty v buňce (například vyhledat text ' ABC ', kde ABC je hodnota v buňce). |
| `Resource overview` | Otevřete přehled prostředku na portálu na základě hodnoty ID prostředku v buňce. |

## <a name="conditional-visibility"></a>Podmíněná viditelnost

Sešit umožňuje uživatelům, aby se na základě hodnot parametrů zobrazovaly nebo zmizely určité ovládací prvky. To umožňuje autorům sestav, které se liší v závislosti na vstupu uživatele nebo stavu telemetrie. Příklad ukazuje, že spotřebitelé mají jenom souhrn, pokud jsou nějaké věci dobré, ale v případě špatného zobrazení jsou úplné podrobnosti.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Nastavení interaktivity pomocí podmíněné viditelnosti

1. Postupujte podle kroků uvedených v části [Nastavení interaktivity na řádku mřížky](#setting-up-interactivity-on-grid-row-click) a nastavte dvě interaktivní ovládací prvky.
2. Přidejte nový parametr v horní části:
    1. Název: `ShowDetails`
    2. Typ parametru: `Drop down`
    3. Požadovanou `checked`
    4. Získat data z: `JSON`
    5. Vstup JSON: `["Yes", "No"]`
    6. Uložením potvrďte změny.

    ![Po výběru tlačítka Přidat parametr se zobrazí podokno upravit parametr.](./media/workbooks-interactive/edit-parameter.png)

3. Nastavte hodnotu parametru na `Yes`

    ![Nad tlačítkem hotové úpravy je rozevírací seznam, který vám umožní nastavit hodnotu parametru.](./media/workbooks-interactive/set-parameter.png)

4. V ovládacím prvku dotaz s plošným grafem vyberte ikonu _Upřesnit nastavení_ (ikona ozubeného kolečka).
5. Ověřte nastavení `Make this item conditionally visible`
    1. Tato položka se zobrazí, pokud `ShowDetails` hodnota `equals` parametru `Yes`
6. Vyberte _hotové úpravy_ a potvrďte změny.
7. Na panelu nástrojů sešitu vyberte _hotové úpravy_ a přejděte do režimu čtení.
8. Přepněte hodnotu parametru `ShowDetails` na `No` . Všimněte si, že graf uvedený níže zmizí.

Následující obrázek ukazuje viditelný případ, kde `ShowDetails` je `Yes`

![Snímek obrazovky znázorňující podmíněný přehled, kde je graf viditelný](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

Následující obrázek ukazuje skrytý případ, kde `ShowDetails` je `No`

![Snímek obrazovky znázorňující podmíněný přehled, kde je graf skrytý](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interakce s více výběry v Gridech a grafech

Kroky dotazování a metrik mohou také exportovat jeden nebo více parametrů, pokud je vybrán řádek (nebo více řádků).

![Snímek obrazovky zobrazující nastavení parametrů exportu s více parametry ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. V kroku dotazu zobrazujícím mřížku přejdete na Upřesnit nastavení.
2. Zaškrtněte políčko `When items are selected, export parameters` . Zobrazí se další ovládací prvky.
3. Zaškrtněte políčko `allow selection of multiple values` .
    1. Zobrazená vizualizace umožní, aby hodnoty více výběrů a exportovaných parametrů byly pole hodnot, jako při použití parametrů rozevíracího seznamu s vícenásobným výběrem.
    2. Pokud není zaškrtnuto, vizualizace zobrazení bude brát ohled jenom na poslední vybranou položku. Pouze Export jedné hodnoty najednou.
4. Pro každý parametr, který chcete exportovat, použijte tlačítko *Přidat parametr* . Zobrazí se automaticky otevírané okno obsahující nastavení pro parametr, který má být exportován.

Když je povolený jeden výběr, autor může určit, které pole původních dat se má exportovat. Pole obsahují název parametru, typ parametru a výchozí hodnotu, která se má použít, pokud není nic vybráno (volitelné).

Pokud je povolen vícenásobný výběr, autor určuje, které pole původních dat se má exportovat. Pole zahrnují název parametru, typ parametru, uvozovky s a oddělovačem. Uvozovky s hodnotami a oddělovači se používají při změně hodnoty šipky na text při jejich nahrazení v dotazu. Pokud nejsou vybrány žádné hodnoty, je výchozí hodnotou prázdné pole v části vícenásobný výběr.

> [!NOTE]
> U vícenásobného výběru budou exportovány jenom jedinečné hodnoty, neuvidíte výstupní pole, jako je 1, 1, 2, 1, jako výstupní hodnoty dostanete "1, 2".

Nastavení "pole pro export" v nastavení exportu můžete nechat prázdné. V takovém případě budou všechna dostupná pole v datech exportována jako objekt dokument JSON klíčs: value páry. U mřížek a názvů se jedná o všechna pole v mřížce. V grafech jsou dostupná pole x, y, Series a Label (v závislosti na typu grafu).

Výchozí chování je exportovat parametr jako text, pokud víte, že pole je předplatné nebo ID prostředku, použijte jako typ parametru export. Tím umožníte, aby se parametr použil pro podřízené položky v místech, která vyžadují tyto typy parametrů.

## <a name="next-steps"></a>Další kroky

* [Začínáme](./workbooks-overview.md#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](./workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.