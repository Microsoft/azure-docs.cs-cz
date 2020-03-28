---
title: 'Kurz: Vytvoření poznámkového bloku v Azure Cosmos DB pro analýzu a vizualizaci dat'
description: 'Kurz: Naučte se používat integrované poznámkové bloky Jupyter k importu dat do Azure Cosmos DB, analyzovat data a vizualizovat výstup.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73720922"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Kurz: Vytvoření poznámkového bloku v Azure Cosmos DB pro analýzu a vizualizaci dat

Tento článek popisuje, jak používat integrované poznámkové bloky Jupyter k importu ukázkových maloobchodních dat do Azure Cosmos DB. Uvidíte, jak používat sql a Azure Cosmos DB magické příkazy ke spuštění dotazů, analyzovat data a vizualizovat výsledky.

## <a name="prerequisites"></a>Požadavky

* [Povolení podpory poznámkových bloků při vytváření účtu Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Vytvoření prostředků a import dat
 
V této části vytvoříte databázi, kontejner Azure Cosmos a importujete maloobchodní data do kontejneru.

1. Přejděte na svůj účet Azure Cosmos a otevřete **Průzkumníka dat.**

1. Přejděte na kartu **Poznámkové bloky,** vyberte `…` vedle **položky Poznámkové bloky** a vytvořte nový **poznámkový blok**. Jako výchozí jádro vyberte **Python 3.**

   ![Vytvoření nového poznámkového bloku](./media/create-notebook-visualize-data/create-new-notebook.png)

1. Po vytvoření nového poznámkového bloku jej můžete přejmenovat na něco jako **VisualizeRetailData.ipynb.**

1. Dále vytvoříte databázi s názvem "RetailDemo" a kontejner s názvem "WebsiteData" pro uložení maloobchodních dat. Jako klíč oddílu můžete použít /CardID. Zkopírujte a vložte následující kód do nové buňky v poznámkovém bloku a spusťte ho:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Chcete-li spustit `Shift + Enter` buňku, vyberte Nebo vyberte buňku a na navigačním panelu průzkumníka dat zvolte **spustit možnost Aktivní buňka.**

   ![Spuštění aktivní buňky](./media/create-notebook-visualize-data/run-active-cell.png)

   Databáze a kontejner se vytvoří ve vašem aktuálním účtu Azure Cosmos. Kontejner je zřízen 400 RU/s. Zobrazí se následující výstup po vytvoření databáze a kontejneru. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Můžete také aktualizovat kartu **Data** a zobrazit nově vytvořené prostředky:

   ![Aktualizace karty dat pro zobrazení nového kontejneru](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Dále importujete ukázková maloobchodní data do kontejneru Azure Cosmos. Zde je formát položky z maloobchodních dat:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Pro účely kurzu ukázková maloobchodní data se ukládají v úložišti objektů blob Azure. Můžete importovat do kontejneru Azure Cosmos vložením následující kód do nové buňky. Můžete potvrdit, že data byla úspěšně importována spuštěním dotazu pro výběr počtu položek.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Při spuštění předchozího dotazu vrátí následující výstup:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Získání dat do datového rámce

Před spuštěním dotazů k analýze dat můžete číst data z kontejneru do [datového rámce Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) pro analýzu. Ke čtení dat do datového rámce použijte následující příkaz sql magic:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Další informace najdete [v tématu integrované poznámkové bloky příkazy a funkce v článku Azure Cosmos DB.](use-notebook-features-and-commands.md) Spustíte dotaz- `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`. Výsledky budou uloženy do datového rámce Pand s názvem df_cosmos. Vložte do nové buňky poznámkového bloku následující příkaz a spusťte ho:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

V nové buňce poznámkového bloku spusťte následující kód a přečtěte si prvních 10 položek z výstupu:

```python
# See a sample of the result
df_cosmos.head(10)
```

![Spuštění dotazu pro získání 10 nejlepších položek](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Spouštění dotazů a analýza dat

V této části spustíte některé dotazy na načtená data.

* **Dotaz1:** Spuštěním skupiny podle dotazu na datovém prvku získáte součet celkových tržeb za každou zemi a z výsledků se zobrazí 5 položek. V nové buňce poznámkového bloku spusťte následující kód:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Výstup celkových výnosů z prodeje](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Dotaz2:** Chcete-li získat seznam pěti nejlepších zakoupených položek, otevřete novou buňku poznámkového bloku a spusťte následující kód:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![Pět nakoupených položek](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Vizualizace dat  

1. Teď, když máme naše data o tržbách z kontejneru Azure Cosmos, můžete si je vizualizovat pomocí vizualizační knihovny podle vašeho výběru. V tomto kurzu budeme používat knihovnu Bokeh. Otevřete novou buňku poznámkového bloku a spusťte následující kód pro instalaci knihovny Bokeh. Po splnění všech požadavků bude knihovna nainstalována.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Dále se připravte na vykreslení dat na mapě. Připojte data v Azure Cosmos DB s informacemi o zemi umístěnými v úložišti objektů Blob Azure a převeďte výsledek do formátu GeoJSON. Zkopírujte následující kód do nové buňky poznámkového bloku a spusťte ji.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Vizualizujte tržby z prodeje různých zemí na mapě světa spuštěním následujícího kódu v nové buňce poznámkového bloku:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   Výstup zobrazuje mapu světa s různými barvami. Barvy tmavší až světlejší představují země s nejvyššími příjmy až nejnižšími příjmy.

   ![Vizualizace mapy tržeb zemí](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Podívejme se na další případ vizualizace dat. Kontejner WebsiteData má záznam uživatelů, kteří si prohlíželi položku, přidali ji do košíku a zakoupili ji. Vykreslete přepočítací koeficient zakoupených položek. Spusťte následující kód v nové buňce a vizualizujte míru konverze pro každou položku:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   ![Vizualizujte konverzní poměr nákupu](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>Další kroky

* Další informace o příkazech poznámkového bloku najdete v článku o [používání integrovaných příkazů a funkcí poznámkového bloku.](use-notebook-features-and-commands.md)
