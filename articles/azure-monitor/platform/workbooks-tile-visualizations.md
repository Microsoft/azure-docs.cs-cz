---
title: Vizualizace dlaždic Azure Monitor sešitu
description: Přečtěte si o všech vizualizacích dlaždic Azure Monitor sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663994"
---
# <a name="tile-visualizations"></a>Vizualizace dlaždic

Dlaždice představují užitečný způsob, jak prezentovat souhrnná data v sešitech. Následující obrázek ukazuje běžný případ použití dlaždic se souhrnem na úrovni aplikace nad detailní mřížkou.

[![Snímek obrazovky se souhrnným zobrazením dlaždice](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Podpora dlaždic sešitu zobrazuje název, Podnadpis, velký text, ikony, přechody na bázi metrik, sparkovou čáru/pruhy, zápatí atd.

## <a name="adding-a-tile"></a>Přidání dlaždice

1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. Vyberte **Přidat** a pak *Přidat dotaz* pro přidání ovládacího prvku dotazování protokolu do sešitu.
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Nastavte velikost na **Úplná**.
6. Nastavte vizualizaci na **dlaždice**.
7. Kliknutím na tlačítko **nastavení dlaždice** otevřete podokno nastavení.
    1. V části *název*nastavte:
        * Použít sloupec: `name` .
    2. V *levé části*nastavte:
        * Použít sloupec: `Requests` .
        * Zobrazovací jednotka sloupce: `Big Number` .
        * Barevná paleta: `Green to Red`
        * Minimální hodnota: `0` .
    3. V *dolní části*nastavte:
        * Použít sloupec: `appName` .
8. V dolní části podokna vyberte tlačítko **Uložit a zavřít** .

[![Snímek obrazovky se souhrnným zobrazením dlaždice s výše uvedeným nastavením dotazu a dlaždice](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Dlaždice v režimu čtení:

[![Snímek obrazovky se souhrnným zobrazením dlaždice v režimu čtení](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Sparkové čáry v dlaždicích

1. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
2. Přidejte parametr časového rozsahu s názvem `TimeRange` .
    1. Vyberte **Přidat** a pak *přidat parametry*.
    2. V ovládacím prvku parametr vyberte možnost **Přidat parametr**.
    3. `TimeRange`Do pole *název parametru* zadejte a vyberte `Time range picker` *typ parametru*.
    4. V horní části podokna vyberte **Uložit** a potom v ovládacím prvku parametru vyberte **hotové úpravy** .
3. Vyberte **Přidat** a pak *Přidat dotaz* a přidejte ovládací prvek dotazu do protokolu pod ovládací prvek parametru.
4. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
5. Pomocí Editoru dotazů zadejte KQL pro analýzu.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Vyberte **Spustit dotaz**. ( `TimeRange` Před spuštěním dotazu nezapomeňte nastavit na hodnotu, kterou zvolíte.)
7. Nastavte *vizualizaci* na dlaždice.
8. Nastavte *Velikost* na Full (plná).
9. Vyberte **nastavení dlaždice**.
    1. V *dlaždici*nastavte:
        * Použít sloupec: `name` .
    2. V *dlaždici*nastavte:
        *  Použít sloupec: `appNAme` .
    3. V *levé části*nastavte:
        *  Použít sloupec: `Requests` .
        * Zobrazovací jednotka sloupce: `Big Number` .
        * Barevná paleta: `Green to Red` .
        * Minimální hodnota: `0` .
    4. V *dolní části*nastavte:
        * Použít sloupec: `Trend` .
        * Zobrazovací jednotka sloupce: `Spark line` .
        * Barevná paleta: `Green to Red` .
        * Minimální hodnota: `0` .
10. V dolní části podokna vyberte **Uložit a zavřít** .

![Snímek obrazovky s vizualizací dlaždic pomocí sparkové čáry](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Velikosti dlaždic

Autor má možnost nastavit šířku dlaždice v nastavení dlaždice.

* `fixed` výchozí

    Výchozím chováním dlaždic má být stejná pevná šířka, přibližně 160 pixelů široké velikosti a mezera kolem dlaždic.

    ![Snímek obrazovky znázorňující dlaždice s pevnou šířkou](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Každý název se zmenší nebo zvětší, aby odpovídal jejich obsahu. dlaždice se ale budou omezovat na šířku zobrazení dlaždic (bez horizontálního posouvání).

    ![Snímek obrazovky zobrazující dlaždice s automatickou šířkou](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Každý nadpis bude vždy plná šířka zobrazení dlaždic, jeden název na řádek.

     ![Snímek obrazovky s dlaždicemi šířky plné velikosti](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Další kroky

* Dlaždice také podporují vykreslování kompozitních pruhů. Další informace najdete v [dokumentaci ke kompozitním panelům](workbooks-composite-bar.md).
* Další informace o časových parametrech jako `TimeRange` najdete v [dokumentaci k parametrům](workbooks-time.md)času v sešitu.