---
title: 'Kurz: vytvoření poznámkového bloku v Azure Cosmos DB pro analýzu a vizualizaci dat'
description: 'Kurz: Naučte se používat vestavěné notebooky Jupyter k importu dat do Azure Cosmos DB, analýze dat a vizualizaci výstupu.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: beb58922172a045242f4f9bcaf647b3cfc8b5551
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380793"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Kurz: vytvoření poznámkového bloku v Azure Cosmos DB pro analýzu a vizualizaci dat
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje, jak pomocí integrovaných poznámkových bloků Jupyter importovat ukázková maloobchodní data do Azure Cosmos DB. Uvidíte, jak použít příkazy SQL a Azure Cosmos DB Magic ke spouštění dotazů, analýze dat a vizualizaci výsledků.

## <a name="prerequisites"></a>Požadavky

* [Povolení poznámkových bloků v účtu Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Vytvoření prostředků a import dat
 
V této části vytvoříte databázi Azure Cosmos, kontejner a naimportujete maloobchodní data do kontejneru.

1. Přejděte k účtu Azure Cosmos a otevřete **Průzkumník dat.**

1. Přejít na kartu **poznámkové bloky** , vyberte `…` vedle do **složky poznámkové bloky** a vytvořte **Nový Poznámkový blok**. Jako výchozí jádro vyberte **Python 3** .

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Vytvoření nového poznámkového bloku":::

1. Po vytvoření nového poznámkového bloku ho můžete přejmenovat na něco, jako je **VisualizeRetailData. ipynb.**

1. V dalším kroku vytvoříte databázi s názvem "RetailDemo" a kontejnerem s názvem "WebsiteData", do kterých budou uložena maloobchodní data. /CartID můžete použít jako klíč oddílu. Zkopírujte následující kód a vložte ho do nové buňky v poznámkovém bloku a spusťte ho:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Chcete-li spustit buňku, vyberte `Shift + Enter` nebo vyberte buňku a zvolte možnost **spustit aktivní buňku** na navigačním panelu Průzkumníka dat.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Spustit aktivní buňku":::

   Databáze a kontejner se vytvoří v aktuálním účtu Azure Cosmos. Kontejner se zřídí s 400 RU/s. Po vytvoření databáze a kontejneru se zobrazí následující výstup. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Kartu **data** můžete také aktualizovat a zobrazit nově vytvořené prostředky:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Aktualizujte kartu data, aby se zobrazil nový kontejner.":::

1. Dále naimportujete ukázková maloobchodní data do kontejneru Azure Cosmos. Tady je formát položky z maloobchodních dat:

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

   Pro účely tohoto kurzu se ukázková maloobchodní data ukládají do úložiště objektů BLOB v Azure. Můžete ho naimportovat do kontejneru Azure Cosmos vložením následujícího kódu do nové buňky. Můžete potvrdit, že se data úspěšně importují spuštěním dotazu pro výběr počtu položek.

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

   Když spustíte předchozí dotaz, vrátí se následující výstup:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Získání dat do datového rámce

Před spuštěním dotazů k analýze dat můžete načíst data z kontejneru do [PANDAS dataframe](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) pro účely analýzy. K načtení dat do datového rámce použijte následující příkaz SQL Magic:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Další informace najdete v tématu [integrované příkazy a funkce poznámkového bloku v článku Azure Cosmos DB](use-python-notebook-features-and-commands.md) . Spustíte dotaz – `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` . Výsledky budou uloženy do PANDAS dataframe s názvem df_cosmos. Do nové buňky poznámkového bloku vložte následující příkaz a spusťte ho:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

V nové buňce poznámkového bloku spusťte následující kód, který přečte prvních 10 položek z výstupu:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Spustit dotaz pro získání prvních 10 položek":::

## <a name="run-queries-and-analyze-your-data"></a>Spouštění dotazů a analýza dat

V této části budete spouštět některé dotazy na načtená data.

* **Dotaz1:** Spusťte v dataframe dotaz na skupinu, abyste získali součet celkových tržeb za jednotlivé země nebo oblasti a zobrazili 5 položek z výsledků. V nové buňce poznámkového bloku spusťte následující kód:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Výstup celkového výnosu prodeje":::

* **Query2:** Chcete-li získat seznam pěti nejvyšších nakupovaných položek, otevřete novou buňku s poznámkovým blokem a spusťte následující kód:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Pět hlavních nakupovaných položek":::

## <a name="visualize-your-data"></a>Vizualizace dat  

1. Teď, když máme data o výnosech z kontejneru Azure Cosmos, můžete vizualizovat data pomocí knihovny vizualizace podle vašeho výběru. V tomto kurzu použijeme knihovnu rozostření. Otevřete novou buňku s poznámkovým blokem a spusťte následující kód pro instalaci knihovny rozostření. Po splnění všech požadavků se knihovna nainstaluje.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Další příprava na vykreslení dat na mapě. Připojte data v Azure Cosmos DB s informacemi o zemi nebo oblasti nacházející se ve službě Azure Blob Storage a převeďte výsledek na formát geografického formátu JSON. Zkopírujte následující kód do nové buňky pro Poznámkový blok a spusťte ji.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Vizualizace tržeb v různých zemích nebo oblastech na světové mapě spuštěním následujícího kódu v nové buňce s poznámkovým blokem:

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

   Výstup zobrazuje mapu světa s různými barvami. Barvy tmavší pro světlejší reprezentují země nebo oblasti s nejvyšším výnosem nejnižším výnosem.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Vizualizace rozvržení výnosů zemí/oblastí":::

1. Pojďme se podívat na další případ vizualizace dat. Kontejner WebsiteData obsahuje záznam o uživatelích, kteří si prohlíželi položku, přidali do svého košíku a koupili položku. Pojďme vykreslit míru konverze položek, které byly koupeny. Spusťte následující kód v nové buňce pro vizualizaci míry převodu pro každou položku:

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

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Míra převodu nákupu vizualizace":::

## <a name="next-steps"></a>Další kroky

* Další informace o příkazech poznámkových bloků Python najdete [v článku Jak používat integrované příkazy a funkce poznámkového bloku v Azure Cosmos DB](use-python-notebook-features-and-commands.md) článku.
