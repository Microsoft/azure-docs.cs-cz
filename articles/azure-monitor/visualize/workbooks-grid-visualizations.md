---
title: Azure Monitor vizualizace mřížky sešitu
description: Přečtěte si o všech vizualizacích mřížky sešitu Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100610018"
---
# <a name="grid-visualizations"></a>Vizualizace pomocí mřížky

Mřížky nebo tabulky představují běžný způsob, jak prezentovat data uživatelům. Pracovní sešity umožňují uživatelům individuálně naformátovat sloupce mřížky, aby poskytovaly bohatou uživatelské rozhraní pro své sestavy.

Níže uvedený příklad ukazuje mřížku, která kombinuje ikony, Heat mapy a Spark-bar a prezentuje komplexní informace. Sešit také nabízí řazení, vyhledávací pole a tlačítko Přejít na analýzu.

[![Snímek obrazovky s tabulkou založenou na protokolu](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Přidání mřížky založené na protokolu

1. Kliknutím na položku panelu nástrojů pro **Úpravy** přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** .
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte KQL pro analýzu (například virtuální počítače s pamětí pod prahovou hodnotou).
5. Nastavení vizualizace na **mřížku**
6. V případě potřeby nastavte další parametry, jako je třeba časový rozsah, velikost, paleta barev a legenda.

[![Snímek obrazovky s dotazem na mřížku založenou na protokolu](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Parametry grafu protokolu

| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ dotazu, který se má použít. | Log, Azure Resource Graph atd. |
| `Resource Type` | Typ prostředku, na který se má cílit | Nejdřív Application Insights, Log Analytics nebo Azure – |
| `Resources` | Sada prostředků, ze kterých se má získat hodnota metrik. | MyApp1 |
| `Time Range` | Časový interval pro zobrazení grafu protokolu. | Poslední hodina, posledních 24 hodin atd. |
| `Visualization` | Vizualizace, která se má použít. | Mřížka |
| `Size` | Svislá velikost ovládacího prvku | Malá, střední, Velká nebo plná |
| `Query` | Libovolný dotaz KQL, který vrací data ve formátu očekávaném vizualizací grafu. | _požadavky \| shrnují požadavky = Count () podle názvu_ |

## <a name="simple-grid"></a>Jednoduchá mřížka

Sešity mohou vykreslovat KQL výsledky jako jednoduchou tabulku. Následující mřížka znázorňuje počet požadavků a jedinečných uživatelů na typ požadavků v aplikaci.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Snímek obrazovky mřížky na bázi protokolu v režimu úprav](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Styly mřížky

I když Jednoduchá tabulka zobrazuje data, je těžké číst a přehledy nejsou vždy zjevné. Vytváření stylů v mřížce vám může usnadnit čtení a interpretaci dat.

Níže je stejná mřížka z předchozí části se stylem Heat mapy.

[![Snímek obrazovky s mřížkou založenou na protokolu se sloupci ve stylu Heat mapy](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Tady je stejná mřížka jako na pruzích: [ ![ snímek obrazovky mřížky na bázi protokolu se sloupci, ve kterých jsou sloupce ve stylu](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox) .

### <a name="styling-a-grid-column"></a>Stylování sloupce mřížky

1. Na panelu nástrojů ovládacího prvku dotazu vyberte tlačítko **Nastavení sloupce** .
2. V *nastavení upravit sloupec* vyberte sloupec, který chcete stylovat.
3. Vyberte objekt pro vykreslení sloupce (například heatmapu, pruhový, pruhový pod ním atd.) a související nastavení pro styl daného sloupce.

Níže je příklad, který vymění styl sloupce *Request* jako pruh:

[![Snímek obrazovky s mřížkou založenou na protokolu se sloupcem požadavku, ve kterém je styl pruhu](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Zobrazovací jednotky sloupců

| Vykreslovací modul sloupců | Vysvětlení | Další možnosti |
|:------------- |:-------------|:-------------|
| `Automatic` | Výchozí – používá nejvhodnější zobrazovací jednotku na základě typu sloupce.  |  |
| `Text` | Vykreslí hodnoty sloupce jako text. | |
| `Right Aligned` | Podobný textu, s tím rozdílem, že je zarovnán vpravo. | |
| `Date/Time` | Vykreslí čitelný řetězec data a času. | |
| `Heatmap` | Vybarví buňky mřížky na základě hodnoty buňky. | Barevná paleta a minimální/maximální hodnota použitá pro škálování |
| `Bar` | Vykreslí pruh vedle buňky na základě hodnoty buňky. | Barevná paleta a minimální/maximální hodnota použitá pro škálování |
| `Bar underneath` | Vykreslí pruh poblíž dolní části buňky na základě hodnoty buňky. | Barevná paleta a minimální/maximální hodnota použitá pro škálování |
| `Composite bar` | Vykreslí složený pruh pomocí zadaných sloupců v daném řádku. Podrobnosti najdete u [složeného panelu](workbooks-composite-bar.md) . | Sloupce s odpovídajícími barvami pro vykreslení pruhu a popisku, který se zobrazí v horní části pruhu. |
| `Spark bars` | Vykreslí pruh Sparku v buňce na základě hodnot dynamického pole v buňce. Například sloupec trend z obrazovky nahoře. | Barevná paleta a minimální/maximální hodnota použitá pro škálování |
| `Spark lines` | Vykreslí sparkovou čáru v buňce na základě hodnot dynamického pole v buňce. | Barevná paleta a minimální/maximální hodnota použitá pro škálování |
| `Icon` | Vykreslí ikony na základě textových hodnot v buňce. Podporovány jsou následující hodnoty: `cancelled` , `critical` , `disabled` , `error` , `failed` , `info` , `none` , `pending` , `stopped` , `question` , `success` ,,,,, `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,,,,,,,,, a.|  |
| `Link` | Vykreslí odkaz, který po kliknutí nebo provede konfigurovatelnou akci. Tuto položku *použijte, pokud chcete,* aby položka byla odkazem.  Libovolný jiný typ může být *také* odkazem pomocí tohoto `Make this item a link` nastavení. Další informace najdete v tématu [Akce odkazů](#link-actions) níže. |  |
| `Location` | Vykreslí popisný název oblasti Azure na základě ID oblasti. |  |
| `Resource type` | Vykreslí popisný řetězec typu prostředku na základě ID typu prostředku.  |  |
| `Resource` | Vykreslí popisný název prostředku a odkaz na základě ID prostředku.  | Možnost zobrazení ikony typu prostředku  |
| `Resource group` | Vykreslí popisný název skupiny prostředků a odkaz na základě ID skupiny prostředků. Pokud hodnota buňky není skupinou prostředků, bude převedena na jednu.  | Možnost zobrazení ikony skupiny prostředků  |
| `Subscription` | Vykreslí popisný název předplatného a odkaz na základě ID předplatného.  Pokud hodnota buňky není předplatné, bude převedena na jednu.  | Možnost, která zobrazí ikonu předplatného.  |
| `Hidden` | Skryje sloupec v mřížce. Užitečné, pokud výchozí dotaz vrátí více sloupců, než kolik jich je potřeba, ale nepožaduje se projekt. |  |

### <a name="link-actions"></a>Akce s odkazy

Pokud je `Link` vybrána možnost zobrazovací jednotka nebo je zaškrtnuto políčko *vytvořit tuto položku a odkaz* , může autor nakonfigurovat akci odkazu, ke které dojde při výběru buňky. To obvykle přebírá uživatele do jiného zobrazení s kontextem přicházejícím z buňky nebo může otevřít adresu URL.

### <a name="custom-formatting"></a>Vlastní formátování

Sešity také umožňují uživatelům nastavit formátování čísel jejich hodnot buňky. To lze provést kliknutím na zaškrtávací políčko *vlastní formátování* , pokud je k dispozici.

| Možnost formátování | Vysvětlení |
|:------------- |:-------------|
| `Units` | Jednotky pro sloupce – různé možnosti pro procenta, počty, čas, bajt, počet/čas, bajty/čas atd. Například jednotka pro hodnotu 1234 se dá nastavit na milisekundy, která se vykreslí jako 1,234 s. |
| `Style` | Formát, který se vykreslí jako Desítková, měna, procenta. |
| `Show group separator` | Zaškrtnutím políčka zobrazíte oddělovače skupin. Vykreslí 1234 jako 1 234 v USA. |
| `Minimum integer digits` | Minimální počet číslic typu Integer, které se mají použít (výchozí 1) |
| `Minimum fractional digits` | Minimální počet zlomkových číslic, které se mají použít (výchozí 0).  |
| `Maximum fractional digits` | Maximální počet zlomkových číslic, které se mají použít. |
| `Minimum significant digits` | Minimální počet platných číslic, které se mají použít (výchozí hodnota 1). |
| `Maximum significant digits` | Maximální počet platných číslic, které se mají použít. |
| `Custom text for missing values` | Pokud datový bod nemá hodnotu, zobrazí tento vlastní text místo prázdné. |

### <a name="custom-date-formatting"></a>Vlastní formátování data

Když autor zadal, že se sloupec nastaví na zobrazovací datum/čas, autor může zadat vlastní možnosti formátování data pomocí zaškrtávacího políčka *vlastní formátování data* .

| Možnost formátování | Vysvětlení |
|:------------- |:-------------|
| `Style` | Formát pro vykreslení data jako krátkých, dlouhých, úplných formátů nebo jako formát krátkého nebo dlouhého formátu času. |
| `Show time as` | Umožňuje autorovi rozhodnout se mezi zobrazením času v místním čase (výchozím) nebo jako UTC. V závislosti na vybraném stylu formátu data se nemusí zobrazovat informace o časovém pásmu UTC. |

## <a name="custom-column-width-setting"></a>Nastavení šířky vlastního sloupce

Autor může přizpůsobit šířku libovolného sloupce v mřížce pomocí pole *Šířka vlastního sloupce* v *Nastavení sloupce*.

![Snímek obrazovky s nastavením sloupce s polem šířka vlastního sloupce, který je uvedený v červeném poli](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Pokud je pole černé, pak se šířka automaticky určí na základě počtu znaků ve sloupci a počtu viditelných sloupců. Výchozí jednotka je "ch" (znaky).

Když v popisku vyberete tlačítko modrá **(aktuální šířka)** , vyplní se textové pole aktuální šířkou vybraného sloupce. Pokud se v poli Vlastní šířka nachází hodnota bez měrné jednotky, použije se výchozí hodnota.

Dostupné jednotky měření jsou:

| Jednotka měření | Definice           |
|:--------------------|:---------------------|
| ch                  | znaky (výchozí) |
| px                  | pixelech               |
| FR                  | Zlomkové jednotky     |
| %                   | procentuální_hodnota           |

Ověřování vstupu – Pokud se ověření nezdaří, zobrazí se pod polem místní zpráva s pokynem, ale uživatel může šířku použít i nadále. Pokud je hodnota ve vstupu přítomna, bude analyzována. Pokud není nalezena žádná platná jednotka míry, bude použita výchozí hodnota.

Neexistuje žádná minimální nebo maximální šířka, protože to je ponecháno na uvážení autora. Pole Šířka vlastního sloupce je pro skryté sloupce zakázané.

## <a name="examples"></a>Příklady

### <a name="spark-lines-and-bar-underneath"></a>Sparkové čáry a pruhové pod

Následující příklad znázorňuje počty požadavků a jejich trend podle názvu žádosti.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Snímek obrazovky s mřížkou založenou na protokolu s pruhem pod a sparkovou čárou](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Heatmapu se sdílenými škálami a vlastním formátováním

Tento příklad ukazuje různé metriky doby trvání žádosti a jejich počty. Modul pro vykreslování heatmapu používá minimální hodnoty nastavené v nastavení nebo vypočítá minimální a maximální hodnotu sloupce a přiřadí barvu pozadí z vybrané palety pro buňku na základě hodnoty buňky relativně k minimální a maximální hodnotě sloupce.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Snímek obrazovky s mřížkou založenou na protokolu s heatmapu se sdíleným škálováním napříč sloupci](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

Ve výše uvedeném příkladu se pro barevné sloupce (střední a střední, P80, p95 a p99) používá sdílená paleta (zelená nebo červená) a měřítko. Samostatná paleta (modrá), která se používá pro sloupec Request.

#### <a name="shared-scale"></a>Sdílený rozsah

Postup získání sdíleného škálování:

1. Pomocí regulárních výrazů můžete vybrat více než jeden sloupec, na který chcete použít nastavení. Například nastavte název sloupce tak, aby byl `Mean|Median|p80|p95|p99` vybrán jako všechny.
2. Odstraňte výchozí nastavení pro jednotlivé sloupce.

To způsobí, že nové nastavení pro více sloupců použije jeho nastavení, aby zahrnovalo sdílené škálování.

[![Snímek obrazovky s nastavením mřížky založené na protokolu pro získání sdíleného škálování napříč sloupci](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Ikony, které reprezentují stav

Následující příklad ukazuje vlastní stav požadavků na základě doby trvání p95.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Snímek obrazovky s tabulkou založenou na protokolu s heatmapu se sdíleným škálováním napříč sloupci pomocí dotazu výše.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Podporovány jsou názvy ikon: `cancelled` , `critical` , `disabled` , `error` , `failed` , `info` , `none` , `pending` , `stopped` , `question` , `success` , `unknown` ,, `warning` `uninitialized` `resource` , `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,, a.

### <a name="using-thresholds-with-links"></a>Použití prahových hodnot s odkazy

Následující pokyny vám ukážou, jak používat prahové hodnoty s odkazy k přiřazení ikon a otevírání různých sešitů. Každé propojení v mřížce otevře pro daný Application Insights prostředek jinou šablonu sešitu.

1. Přepněte sešit do režimu úprav výběrem možnosti *Upravit* položku panelu nástrojů.
2. Vyberte **Přidat** a pak *Přidat dotaz*.
3. Změňte *zdroj dat* na "JSON" a *vizualizaci* na "Grid".
4. Zadejte následující dotaz.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Spustit dotaz.
6. Vyberte **Nastavení sloupce** a otevřete nastavení.
7. Ze *sloupců* vyberte název.
8. V části *vykreslovací modul sloupců* vyberte "prahové hodnoty".
9.  Zadejte a vyberte následující *nastavení prahové hodnoty*.
   
    | Operátor | Hodnota   | Ikony   |
    |----------|---------|---------|
    | ==       | upozornění | Upozornění |
    | ==       | error   | Neúspěšný  |

    ![Snímek obrazovky s nastavením sloupce pro úpravy s výše uvedeným nastavením](./media/workbooks-grid-visualizations/column-settings.png)

    Ponechte výchozí řádek tak, jak je. Můžete zadat jakýkoli text, který chcete. Textový sloupec má jako vstup formát řetězce a naplní ho hodnotou sloupce a jednotky, pokud je zadaný. Pokud je například upozornění hodnotou sloupce, může být text " {0} {1} Link!", zobrazí se jako "upozornění".
10. Zaškrtněte políčko *vytvořit tuto položku a odkaz* .
    1. V části *zobrazení k otevření* vyberte sešit (šablona).
    2. V části *hodnota propojení pochází z* vyberte odkaz.
    3. Vyberte *odkaz otevřít v okně kontext* .
    4. V *Nastavení odkazu na sešit* vyberte následující nastavení.
        1. V části *ID šablony pochází z* vyberte sloupec.
        2. V části *sloupec* vyberte odkaz.

    ![Snímek nastavení odkazů s výše uvedenými nastaveními](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. V části *sloupce* vyberte "propojit". V části nastavení vedle pole pro *vykreslení sloupce* vyberte **(skrýt sloupec)**.
1. Chcete-li změnit zobrazovaný název sloupce "název", vyberte kartu **popisky** . Na řádku s názvem "název" jako *ID sloupce* zadejte do pole * popisek sloupce název, který chcete zobrazit.
2. Vybrat **použít**

![Snímek obrazovky s mezními hodnotami v mřížce s výše uvedeným nastavením](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Procenta zlomkových jednotek

Desetinná jednotka (FR) je běžně používaná dynamická jednotka měření v různých typech mřížek. Při změně velikosti okna nebo rozlišení se změní i šířka fr.

Níže uvedený snímek obrazovky ukazuje tabulku s osmi sloupci, které mají 1fr šířku a všechny stejné šířky. Při změně velikosti okna se Šířka každého sloupce mění proporcionálně.

[![Snímek obrazovky se sloupci v mřížce s hodnotou šířky sloupce 1fr](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

Následující obrázek ukazuje stejnou tabulku s výjimkou prvního sloupce, který je nastaven na 50% Width. Tím se dynamicky nastaví sloupec na polovinu celkové šířky mřížky. Změna velikosti okna bude i nadále zachovávat šířku 50%, pokud velikost okna nebude příliš malá. Tyto dynamické sloupce mají na základě jejich obsahu minimální šířku. Zbývající 50% mřížky je rozdělené o osm celkových zlomkových jednotek. Sloupec "druh" níže je nastaven na 2FR, takže zabírá jeden čtvrtý z zbývajícího prostoru. Vzhledem k tomu, že se ostatní sloupce 1fr, každá z nich zabere jednu osmina pravé poloviny mřížky.

[![Snímek obrazovky se sloupci v mřížce s hodnotou 1 sloupce a hodnotou šířky sloupce 50% a zbytek jako 1fr](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

Kombinování operátorů FR,%, px a ch je možné a funguje podobně jako v předchozích příkladech. Šířky nastavené pomocí statických jednotek (ch a px) jsou pevné konstanty, které se nemění ani v případě, že dojde ke změně okna nebo rozlišení. Sloupce nastavené podle% zabírají procento na základě celkové šířky mřížky (možná nepůjde přesně kvůli předchozím minimálním šířkám). Sloupce nastavené pomocí fr budou dělit jenom zbývající místo v mřížce na základě počtu zlomkových jednotek, které jsou přidělené.

[![Snímek obrazovky se sloupci v mřížce s použitým sortimentem různých jednotek šířky](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Naučte se vytvořit [strom v sešitech](workbooks-tree-visualizations.md).
* Naučte se vytvářet [textové parametry sešitu](workbooks-text.md).