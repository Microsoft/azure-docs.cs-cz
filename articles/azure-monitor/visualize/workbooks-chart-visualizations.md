---
title: Vizualizace grafu Azure Monitorho sešitu
description: Přečtěte si o všech vizualizacích grafu Azure Monitorového sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100611650"
---
# <a name="chart-visualizations"></a>Vizualizace grafů

Pracovní sešity umožňují, aby se data monitorování mohla prezentovat jako grafy. Mezi podporované typy grafů patří spojnicový, pruhový, pruhový kategorií, plošný, bodový graf, výsečový a čas. Autoři si můžou zvolit, že se má přizpůsobit výška, Šířka, paleta barev, legenda, názvy, zpráva bez dat atd. v grafu a přizpůsobit typy OS a barvy řad pomocí nastavení grafu.

Pracovní sešity podporují grafy pro protokoly i zdroje dat metriky.

## <a name="log-charts"></a>Grafy protokolů

Protokoly Azure Monitor poskytují vlastníkům prostředků podrobné informace o práci s jejich aplikacemi a infrastrukturou. Na rozdíl od metrik se informace protokolu ve výchozím nastavení neshromažďují a vyžadují nějaký druh zaregistrování kolekce. Pokud však přítomné protokoly poskytují velké množství informací o stavu prostředku a data užitečná pro diagnostiku. Pracovní sešity umožňují prezentovat data protokolů jako vizuální grafy pro analýzu uživatelů.

### <a name="adding-a-log-chart"></a>Přidání grafu protokolu

Níže uvedený příklad znázorňuje trend požadavků na aplikaci v předchozích dnech.

1. Přepněte sešit do režimu úprav výběrem položky panel nástrojů pro **Úpravy** .
2. K přidání ovládacího prvku dotaz na protokol do sešitu použijte odkaz **Přidat dotaz** .
3. Vyberte typ dotazu jako **protokol**, typ prostředku (například Application Insights) a prostředky k cíli.
4. Pomocí Editoru dotazů zadejte [KQL](/azure/kusto/query/) pro analýzu (například trend požadavků).
5. Nastavte vizualizaci na jednu z těchto: **oblast**, **pruhový**, **pruhový (kategorií)**, **spojnicový**, **výsečový**, **bodový** nebo **čas**.
6. V případě potřeby nastavte další parametry jako časový rozsah, vizualizaci, velikost, paletu barev a legendu.

[![Snímek obrazovky s grafem protokolu v režimu úprav](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Parametry grafu protokolu

| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ dotazu, který se má použít. | Log, Azure Resource Graph atd. |
| `Resource Type` | Typ prostředku, na který se má cílit | Nejdřív Application Insights, Log Analytics nebo Azure – |
| `Resources` | Sada prostředků, ze kterých se má získat hodnota metrik. | MyApp1 |
| `Time Range` | Časový interval pro zobrazení grafu protokolu. | Poslední hodina, posledních 24 hodin atd. |
| `Visualization` | Vizualizace, která se má použít. | Oblast, pruhový, spojnicový, výsečový, bodový, čas, pruhový kategorií |
| `Size` | Svislá velikost ovládacího prvku | Malá, střední, Velká nebo plná |
| `Color palette` | Paleta barev, která se má použít v grafu Ignorováno v režimu s více metrikami nebo v režimu segmentace. | Modrá, zelená, červená atd. |
| `Legend` | Agregační funkce, která se má použít pro legendu | Součet nebo průměr hodnot nebo maximum, minimum, první, poslední hodnota |
| `Query` | Libovolný dotaz KQL, který vrací data ve formátu očekávaném vizualizací grafu. | _vyžádá požadavky \| -Series = Count () výchozí nastavení = 0 v časovém razítku před (1d) do Now () krok 1H_ |

### <a name="time-series-charts"></a>Grafy časových řad

Grafy časových řad, jako je oblast, pruh, čára, bodový a čas, lze snadno vytvořit pomocí ovládacího prvku dotaz v sešitech. Klíč má informace o čase a metrikě v sadě výsledků dotazu.

#### <a name="simple-time-series"></a>Jednoduchá časová řada

Následující dotaz vrátí tabulku se dvěma sloupci: *časové razítko* a *požadavky*. Ovládací prvek dotazu používá pro osu X a *požadavky* na osu Y *časové razítko* .

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Snímek obrazovky s jednoduchým řádkem protokolu časových řad](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Časová řada s více metrikami

Následující dotaz vrátí tabulku se třemi sloupci: *časové razítko*, *požadavky* a *Uživatelé*. Ovládací prvek dotazu používá *časové razítko* pro osu X a *požadavky*  &  *uživatelů* na osu Y jako samostatné řady.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Snímek obrazovky s časovou řadou s více typy spojnicový graf s protokolem metriky](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Segmentovaná časová řada

Následující dotaz vrátí tabulku se třemi sloupci: *časové razítko*, *požadavky* a název *žádosti* , kde název *žádosti* je kategorií sloupec s názvy požadavků. Zde uvedený ovládací prvek dotazu používá pro osu X *časové razítko* a přidává řadu na hodnotu *požadoval*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Snímek obrazovky s rozděleným grafem s protokolem časové řady](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Shrnutí vs. vytvořit-Series

Příklady v předchozí části používají `summarize` operátor, protože je snazší ho pochopit. Sumarizace ale má zásadní omezení, protože vynechává řádek výsledků, pokud v tomto kontejneru nejsou žádné položky. Může mít vliv na posunutí okna času grafu v závislosti na tom, zda jsou prázdné intervaly v přední nebo Backside časovém rozsahu.

Je obvykle lepší použít `make-series` operátor k vytváření dat časových řad, což má možnost zadat výchozí hodnoty pro prázdné sady.

Následující dotaz používá `make-series` operátor.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

Níže uvedený dotaz znázorňuje podobný graf s `summarize` operátorem.

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

I když se podkladová sada výsledků liší. Všichni uživatelé musí provést nastavení vizualizace na oblast, řádek, pruh nebo čas a sešity se postará o zbytek.

[![Snímek obrazovky s grafem řádkového záznamu vytvořeným z dotazu make-Series](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Pruhový graf nebo histogram kategorií

Kategorií grafy umožňují uživatelům znázornit dimenzi nebo sloupec na ose X grafu, což je zvláště užitečné v histogramech. Následující příklad ukazuje distribuci požadavků podle jejich kódu výsledku.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Dotaz vrátí dva sloupce: metrika a kategorie *výsledku* *žádosti* . Každá hodnota sloupce *výsledek* získá svůj vlastní pruh v grafu se výškou odpovídající *metrikě požadavků*.

[![Snímek obrazovky kategorií pruhového grafu pro žádosti podle kódu výsledku](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Výsečové grafy

Výsečové grafy umožňují vizualizaci číselného poměru. Následující příklad ukazuje poměr požadavků podle jejich kódu výsledku.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Dotaz vrátí dva sloupce: metrika a kategorie *výsledku* *žádosti* . Každá hodnota sloupce *Result (výsledek* ) získá svůj vlastní řez ve výsečovém grafu o velikosti úměrné metrikě *požadavků* .

[![Snímek obrazovky výsečového grafu s řezy reprezentujícími kód výsledku](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Grafy metrik

Většina prostředků Azure emituje data metrik o stavu a stavu (například využití procesoru, dostupnost úložiště, počet transakcí databáze, neúspěšné žádosti o aplikace atd.). Pracovní sešity umožňují vizualizaci těchto dat jako grafu časových řad.)

### <a name="adding-a-metric-chart"></a>Přidání grafu metriky

V následujícím příkladu se zobrazí počet transakcí v účtu úložiště během předchozí hodiny. Vlastník úložiště tak může zobrazit trend transakce a vyhledat anomálie v chování.

1. Přepněte sešit do režimu úprav výběrem položky panel nástrojů pro **Úpravy** .
2. K přidání ovládacího prvku metriky do sešitu použijte odkaz **Přidat metriku** .
3. Vyberte typ prostředku (například účet úložiště), prostředky k cíli, obor názvů metriky a název a agregaci, která se má použít.
4. Nastavte další parametry, pokud je to potřeba, jako časový rozsah, rozdělené, vizualizace, velikost a paleta barev.

[![Snímek grafu metriky v režimu úprav](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Parametry grafu metriky

| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `Resource Type` | Typ prostředku, na který se má cílit | Úložiště nebo virtuální počítač. |
| `Resources` | Sada prostředků, ze kterých se má získat hodnota metrik. | MyStorage1 |
| `Namespace` | Obor názvů s metrikou | Objekt BLOB > úložiště |
| `Metric` | Metrika, která se má vizualizovat | Transakce > BLOB > úložiště |
| `Aggregation` | Agregační funkce, která se má použít pro metriku | Sum, Count, Average atd. |
| `Time Range` | Časový interval pro zobrazení metriky. | Poslední hodina, posledních 24 hodin atd. |
| `Visualization` | Vizualizace, která se má použít. | Plošný, pruhový, spojnicový, bodový, mřížka |
| `Split By` | Volitelně můžete rozdělit metriku na dimenzi. | Transakce podle geografického typu |
| `Size` | Svislá velikost ovládacího prvku | Malá, střední nebo Velká |
| `Color palette` | Paleta barev, která se má použít v grafu Ignoruje se, pokud `Split by` je použit parametr. | Modrá, zelená, červená atd. |

### <a name="examples"></a>Příklady

Transakce rozdělené podle názvu rozhraní API jako spojnicový graf:

[![Snímek obrazovky se spojnicovým grafem metriky pro transakce úložiště rozdělené podle názvu rozhraní API](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Transakce rozdělené podle typu odpovědi jako velkého pruhového grafu:

[![Snímek obrazovky velkého pruhového grafu pro transakce úložiště rozdělené podle typu odpovědi](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Průměrná latence jako bodový graf:

[![Snímek obrazovky bodového grafu metriky pro latenci úložiště](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Nastavení grafu

Autoři mohou pomocí nastavení grafu přizpůsobit, která pole se používají na osách grafu, jednotkách osy, vlastním formátování, rozsahech, chování seskupení, legendách a barvách řad.

### <a name="the-settings-tab"></a>Karta nastavení

Ovládací prvky karty nastavení:

- Nastavení osy, včetně polí, vlastního formátování, které umožňuje uživatelům nastavit formátování čísel na hodnoty osy a vlastní rozsahy.
- Nastavení seskupení, včetně pole, omezení před vytvořením skupiny "ostatní".
- Nastavení legendy, včetně zobrazení metrik (název řady, barvy a čísla), v dolní části a z legendy (názvy řad a barvy).

![Snímek obrazovky s nastavením grafu](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Vlastní formátování

Mezi možnosti formátování čísel patří:

| Možnost formátování             | Vysvětlení                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | Jednotky pro sloupce – různé možnosti pro procenta, počty, čas, bajt, počet/čas, bajty/čas atd. Například jednotka pro hodnotu 1234 se dá nastavit na milisekundy, která se vykreslí jako číslo 1,234 s.                                  |
| `Style`                      | Formát, který se vykreslí jako Desítková, měna, procenta.                                               |
| `Show group separator`       | Zaškrtnutím políčka zobrazíte oddělovače skupin. Vykreslí 1234 jako 1 234 v USA.                                    |
| `Minimum integer digits`     | Minimální počet číslic typu Integer, které se mají použít (výchozí 1)                                                   |
| `Minimum fractional digits`  | Minimální počet zlomkových číslic, které se mají použít (výchozí 0).                                                |
| `Maximum fractional digits`  | Maximální počet zlomkových číslic, které se mají použít.                                                            |
| `Minimum significant digits` | Minimální počet platných číslic, které se mají použít (výchozí hodnota 1).                                               |
| `Maximum significant digits` | Maximální počet platných číslic, které se mají použít.                                                           |

![Snímek obrazovky s nastavením osy X](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Karta řada

Karta nastavení řady vám umožní upravit popisky a barvy, které se zobrazují pro řady v grafu.

- `Series name`Pole se používá ke spárování řad v datech a pokud se shoduje, zobrazí se popisek zobrazení a barva.
- `Comment`Toto pole je užitečné pro autory šablon, protože tento komentář můžou použít překladatelé k lokalizaci zobrazovaných popisků.

![Snímek obrazovky s nastavením řady](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Další kroky

- Naučte se, jak [v sešitech vytvořit dlaždici](workbooks-tile-visualizations.md).
- Naučte se vytvářet [interaktivní sešity](workbooks-interactive.md).