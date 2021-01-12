---
title: Vizualizace
description: Použití poznámkových bloků Azure Synapse k vizualizaci dat
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: f11693b34048b11c02668e086561b9a6521a5213
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121521"
---
# <a name="visualize-data"></a>Vizualizace dat
Azure synapse je integrovaná analytická služba, která zrychluje čas na přehledy napříč datovými sklady a systémy pro analýzy velkých objemů dat. Vizualizace dat je klíčovou komponentou, která dokáže získat přehled o vašich datech. Pomáhá lépe pochopit velká a malá data pro lidi. Usnadňuje to rozpoznávání vzorů, trendů a odlehlých hodnot ve skupinách dat. 

Při použití Apache Spark ve službě Azure synapse Analytics jsou k dispozici různé předdefinované možnosti, které vám pomohou vizualizovat data, včetně možností grafu poznámkového bloku synapse, přístupu k oblíbeným Open Source knihovnám a integraci s synapse SQL a Power BI.

## <a name="notebook-chart-options"></a>Možnosti grafu poznámkového bloku
Pokud používáte Poznámkový blok Azure synapse, můžete zobrazení tabulkových výsledků zapnout v přizpůsobeném grafu pomocí možností grafu. Tady můžete vizualizovat data, aniž byste museli psát žádný kód. 

### <a name="displaydf-function"></a>Display (DF) – funkce
```display```Funkce umožňuje zapnout dotazy SQL a Apache Spark datové rámce a RDD do bohatých vizualizací dat. ```display```Funkci lze použít pro datový rámec nebo RDD vytvořené v PySpark, Scala, Java a .NET.

Přístup k možnostem grafu:
1. Výstup ```%%sql``` příkazů Magic se ve výchozím nastavení zobrazí v zobrazení vykreslené tabulky. ```display(df)```K tvorbě zobrazení vykreslené tabulky můžete také volat funkce Spark Dataframes nebo odolné distribuované datové sady (RDD). 
   
2. Jakmile budete mít zobrazení vykreslené tabulky, přepněte do zobrazení grafu.
   ![předdefinované grafy](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Vizualizaci teď můžete přizpůsobit zadáním následujících hodnot:
   | Konfigurace | Popis |
   |--|--| 
   | Typ grafu | ```display```Funkce podporuje široké spektrum typů grafů, včetně pruhových grafů, bodových pruhů, spojnicových grafů a dalších. |
   | Klíč | Zadejte rozsah hodnot pro osu x.|
   | Hodnota | Zadejte rozsah hodnot pro hodnoty osy y. |
   | Skupina řad | Slouží k určení skupin pro agregaci. | 
   | Agregace | Metoda agregace dat ve vizualizaci| 
   
   
    > [!NOTE]
    > Ve výchozím nastavení ```display(df)``` bude funkce při vykreslování grafů provádět pouze první 1000 řádky dat. Podívejte se na **agregaci všech výsledků** a klikněte na tlačítko **použít** , použije se generování grafu z celé datové sady. Při změně nastavení grafu se aktivuje úloha Spark. Všimněte si, že dokončení výpočtu a vykreslení grafu může trvat několik minut.
   
4. Po dokončení můžete zobrazit a pracovat s poslední vizualizací.

### <a name="displaydf-statistic-details"></a>Zobrazit podrobnosti o statistice (DF)
Můžete použít <code>display(df, summary = true)</code> ke kontrole souhrnu statistik daného Apache Spark dataframe, které zahrnují název sloupce, typ sloupce, jedinečné hodnoty a chybějící hodnoty pro každý sloupec. Můžete také vybrat konkrétní sloupec, abyste zobrazili jeho minimální hodnotu, maximální hodnotu, střední hodnotu a směrodatnou odchylku.
    ![předdefinované grafy – souhrn](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (DF) – možnost
Poznámkové bloky Azure synapse Analytics podporují grafiku HTML pomocí ```displayHTML``` funkce.

Následující obrázek je příkladem vytváření vizualizací pomocí [D3.js](https://d3js.org/).

   ![D3-js – příklad](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Spusťte následující kód k vytvoření vizualizace výše.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Oblíbené knihovny
V případě, že je součástí vizualizace dat, Python nabízí více knihoven grafů, které jsou zabaleny s mnoha různými funkcemi. Ve výchozím nastavení obsahuje každý Apache Spark fond ve službě Azure synapse Analytics sadu známých a oblíbených open source knihoven. Můžete také přidat nebo spravovat další knihovny & verze pomocí možností správy knihovny Azure synapse Analytics. 

### <a name="bokeh"></a>Bokeh
Můžete vykreslit HTML nebo interaktivní knihovny, jako je **rozostření**, pomocí ```displayHTML(df)``` . 

Následující obrázek je příkladem vykreslení glyfů přes mapu pomocí **rozostření**.

   ![rozostření – příklad](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Spusťte následující vzorový kód pro vykreslení obrázku výše.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
Pomocí vestavěných funkcí vykreslování pro každou knihovnu můžete vykreslit standardní knihovny pro vykreslení, jako je matplotlib.

Následující obrázek je příkladem vytvoření pruhového grafu pomocí **matplotlib**.
   ![Příklad spojnicového grafu](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Spusťte následující vzorový kód pro vykreslení obrázku výše.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>Další knihovny 
Kromě těchto knihoven obsahuje modul runtime analýzy Azure synapse také následující sadu knihoven, které se často používají pro vizualizaci dat:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

Nejaktuálnější informace o dostupných knihovnách a verzích najdete v [dokumentaci](./spark/../apache-spark-version-support.md) běhového prostředí Azure synapse Analytics.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Připojení k Power BI pomocí Apache Spark & SQL na vyžádání
Azure synapse Analytics se úzce integruje s Power BI, které umožňují datovým technikům vytvářet Analytická řešení.

Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky mezi svými fondy Sparku a SQL fondem bez serveru. Pomocí [sdíleného modelu metadat](../metadata/overview.md)můžete zadávat dotazy na Apache Spark tabulky pomocí SQL na vyžádání. Po dokončení můžete připojit koncový bod SQL na vyžádání a Power BI, abyste mohli snadno dotazovat synchronizované tabulky Spark.


## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak nastavit konektor Spark SQL DW: [konektor synapse SQL](./spark/../synapse-spark-sql-pool-import-export.md)
- Zobrazit výchozí knihovny: [Azure synapse Analytics runtime](../spark/apache-spark-version-support.md)