---
title: Vytváření interaktivních sestav pomocí vlastních parametrů Azure Monitorch sešitů | Dokumentace Microsoftu
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: eeb6eb5d8b3ad6498ff90a9afe1fa4f2c18d30e5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165950"
---
# <a name="interactive-workbooks"></a>Interaktivní sešity

Sešity umožňují autorům vytvářet interaktivní sestavy a prostředí pro své příjemce. Interaktivita se podporuje mnoha různými způsoby.

## <a name="parameter-changes"></a>Změny parametru
Když uživatel sešitu aktualizuje parametr, jakýkoli ovládací prvek, který používá parametr, se automaticky aktualizuje a znovu vykreslí, aby odrážel nový stav. To je způsob, jakým většina sestav Azure Portal podporuje interaktivitu. Sešity poskytují velmi přímý způsob, s minimálním úsilím pro uživatele.

Další informace o [parametrech v sešitech](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Kliknutí na řádek mřížky
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
5. zobrazení výsledků `Run query`
6. Klikněte na ikonu _Upřesnit nastavení_ v zápatí dotazu (ikona vypadá jako ozubené kolo). Otevře se podokno Upřesnit nastavení. 
7. Ověřte nastavení: `When an item is selected, export a parameter`
    1. Pole, které se má exportovat: `Request`
    2. Název parametru: `SelectedRequest`
    3. Výchozí hodnota: `All requests`
    
    ![Obrázek znázorňující rozšířený editor s nastavením pro export polí jako parametrů](./media/workbooks-interactive/advanced-settings.png)

8. Klikněte na `Done Editing` (Další).
9. Přidejte další ovládací prvek dotazu pomocí kroků 2 a 3.
10. Pomocí Editoru dotazů zadejte KQL pro analýzu.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. výsledky zobrazíte `Run query`.
12. Změna _vizualizace_ na `Area chart`
12. V první mřížce klikněte na řádek. Všimněte si, jak plošný graf níže filtruje vybraný požadavek.

Výsledná sestava bude vypadat v režimu úprav:

![Obrázek znázorňující vytvoření interaktivního prostředí pomocí kliknutí na řádek mřížky](./media/workbooks-interactive//grid-click-create.png)

Následující obrázek ukazuje komplexnější interaktivní sestavu v režimu čtení na základě stejných principů. Sestava používá kliknutí na mřížku k exportu parametrů, které se pak používají ve dvou grafech a textovém bloku.

![Obrázek znázorňující vytvoření interaktivního prostředí pomocí kliknutí na řádek mřížky](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Export obsahu celého řádku
Někdy je žádoucí exportovat celý obsah vybraného řádku místo pouze konkrétního sloupce. V takových případech ponechte vlastnost `Field to export` v kroku 7,1 výše. V sešitech budou exportovány celé obsahy řádků jako JSON pro parametr. 

Na referenčním ovládacím prvku KQL použijte funkci `todynamic` pro analýzu JSON a přístup k jednotlivým sloupcům.

 ## <a name="grid-cell-clicks"></a>Kliknutí na buňky mřížky
Sešity umožňují autorům přidat interaktivitu prostřednictvím speciálního typu vykreslovacího modulu sloupců tabulky s názvem `link renderer`. Modul pro vykreslování odkazů převede buňku mřížky na hypertextový odkaz na základě obsahu buňky. Pracovní sešity podporují mnoho druhů vykreslovacích objektů, včetně těch, které umožňují otevřít okna s přehledem prostředků, diváků kontejnerů vlastností, hledání v App Insights, využití, trasování transakcí atd.

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
5. zobrazení výsledků `Run query`
6. Kliknutím na _Nastavení sloupce_ otevřete podokno nastavení.
7. V části _sloupce_ nastavte:
    1. _Ukázka_ -sloupcový renderer: `Link`, zobrazení pro otevření: `Cell Details`, popisek odkazu: `Sample`
    2. _Count_ -Column renderer: `Bar`, barevná paleta: `Blue`, minimální hodnota: `0`
    3. Vykreslovací modul sloupců _požadavků_ : `Automatic`
    4. Změny aplikujte kliknutím na _Uložit a zavřít_ .
8. Klikněte na jeden z odkazů `Sample` v mřížce. Otevře se podokno vlastností s podrobnostmi o vzorku požadavku.

    ![Obrázek znázorňující vytvoření interaktivního prostředí pomocí kliknutí na buňky mřížky](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Akce vykreslování odkazů
| Akce propojení | Akce při kliknutí |
|:------------- |:-------------|
| `Generic Details` | Zobrazuje hodnoty řádků v okně kontextu pro mřížku vlastností. |
| `Cell Details` | Zobrazuje hodnotu buňky v okně kontextu mřížky vlastností. Užitečné, pokud buňka obsahuje dynamický typ s informacemi (například JSON s vlastnostmi žádosti, jako je umístění, instance role atd.). |
| `Cell Details` | Zobrazuje hodnotu buňky v okně kontextu mřížky vlastností. Užitečné, pokud buňka obsahuje dynamický typ s informacemi (například JSON s vlastnostmi žádosti, jako je umístění, instance role atd.). |
| `Custom Event Details` | Otevře Application Insights podrobnosti hledání s ID vlastní události (itemId) v buňce. |
| `* Details` | Podobně jako v podrobnostech o vlastních událostech, s výjimkou závislostí, výjimek, zobrazení stránky, požadavků a trasování. |
| `Custom Event User Flows` | Otevře Application Insights Toky uživatelů prostředí, které se v buňce Překlopí na vlastní název události. |
| `* User Flows` | Podobně jako u vlastních událostí Toky uživatelů s výjimkou výjimek, zobrazení stránky a požadavků |
| `User Timeline` | Otevře časovou osu uživatele s ID uživatele (user_Id) v buňce. |
| `Session Timeline` | Otevře Application Insights vyhledávání hodnoty v buňce (například vyhledat text ' ABC ', kde ABC je hodnota v buňce). |
| `Resource overview` | Otevřete přehled prostředku na portálu na základě hodnoty ID prostředku v buňce. |

## <a name="conditional-visibility"></a>Podmíněná viditelnost
Sešit umožňuje uživatelům, aby se na základě hodnot parametrů zobrazovaly nebo zmizely určité ovládací prvky. To umožňuje autorům sestav, které se liší v závislosti na vstupu uživatele nebo stavu telemetrie. Příklad ukazuje, že spotřebitelé mají jenom souhrn, pokud jsou nějaké věci dobré, ale v případě špatného zobrazení jsou úplné podrobnosti.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Nastavení interaktivity pomocí podmíněné viditelnosti
1. Postupujte podle kroků v části `Setting up interactivity on grid row click` a nastavte dvě interaktivní ovládací prvky.
2. Přidejte nový parametr v horní části:
    1. Název: `ShowDetails`
    2. Typ parametru: `Drop down`
    3. Požadováno: `checked`
    4. Získat data z: `JSON`
    5. Vstup JSON: `["Yes", "No"]`
    6. Uložením potvrďte změny.
3. Nastavte hodnotu parametru na `Yes`
4. V ovládacím prvku dotaz s plošným grafem klikněte na ikonu _Upřesnit nastavení_ (ikona ozubeného kolečka).
5. Ověřte nastavení `Make this item conditionally visible`
    1. Tato položka je viditelná, pokud `ShowDetails` hodnota parametru `equals` `Yes`
6. Potvrďte změny kliknutím na _hotové úpravy_ .
7. Na panelu nástrojů sešitu klikněte na _hotové úpravy_ a zadejte režim čtení.
8. Přepněte hodnotu parametru `ShowDetails` na `No`. Všimněte si, že graf uvedený níže zmizí.

Následující obrázek ukazuje viditelný případ, ve kterém je `ShowDetails` `Yes`

![Obrázek znázorňující podmíněnou viditelnost, kde je graf viditelný](./media/workbooks-interactive/conditional-visibility.png)

Následující obrázek ukazuje skrytý případ, kde je `ShowDetails` `No`

![Obrázek znázorňující podmíněnou viditelnost, kde je graf skrytý](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Další kroky


* [Začínáme](workbooks-visualizations.md) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.
