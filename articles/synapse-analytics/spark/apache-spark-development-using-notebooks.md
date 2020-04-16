---
title: Vytváření, vývoj a údržba poznámkových bloků Azure Synapse Studio (preview)
description: V tomto článku se dozvíte, jak vytvořit a vyvíjet poznámkové bloky Azure Synapse Studio (preview) pro přípravu a vizualizaci dat.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430224"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Vytváření, vývoj a údržba poznámkových bloků Azure Synapse Studio (preview)

Poznámkový blok Azure Synapse Studio (preview) je webové rozhraní pro vytváření souborů, které obsahují živý kód, vizualizace a narativní text. Poznámkové bloky jsou dobrým místem pro ověřování nápadů a rychlé experimenty k získání přehledů z vašich dat. Poznámkové bloky jsou také široce používány při přípravě dat, vizualizaci dat, strojovém učení a dalších scénářích velkých objemů dat.

S poznámkovým blokem Azure Synapse Studio můžete:

* Začínáme s nulovým úsilím o nastavení.
* Udržujte data v bezpečí díky integrovaným funkcím podnikového zabezpečení.
* Analyzujte data napříč nezpracovanými formáty (CSV, txt, JSON atd.), formáty zpracovaných souborů (parkety, Delta Lake, ORC atd.) a tabulkové datové soubory SQL proti Spark a SQL.
* Buďte produktivní díky vylepšeným možnostem vytváření a integrované vizualizaci dat.

Tento článek popisuje, jak používat poznámkové bloky v Azure Synapse Studio.

## <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

Poznámkový blok lze vytvořit dvěma způsoby. Můžete vytvořit nový poznámkový blok nebo importovat existující poznámkový blok do pracovního prostoru Azure Synapse z **Průzkumníka objektů**. Poznámkové bloky Azure Synapse Studio dokáží rozpoznat standardní soubory IPYNB notebooku Jupyter.

![synapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Vývoj notebooků

Poznámkové bloky se skládají z buněk, což jsou jednotlivé bloky kódu nebo textu, které lze schovat samostatně nebo jako skupina.

### <a name="add-a-cell"></a>Přidání buňky

Existuje několik způsobů, jak do poznámkového bloku přidat novou buňku.

1. Rozbalte levé horní **tlačítko + buňka** a vyberte **Přidat buňku kódu** nebo Přidat **textovou buňku**.

    ![tlačítko přidat buňku s buňkou](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Najeďte přes mezeru mezi dvěma buňkami a vyberte **Přidat kód** nebo **Přidat text**.

    ![přidat buňku mezi mezerami](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. V režimu příkazů použijte [klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **klávesy A** vložte buňku nad aktuální buňku. Stisknutím **klávesy B** vložte buňku pod aktuální buňku.

### <a name="set-a-primary-language"></a>Nastavení primárního jazyka

Poznámkové bloky Azure Synapse Studio podporují čtyři jazyky jiskry:

* pyspark (krajta)
* jiskra (Scala)
* sparkSQL
* Spark.NET (C#)

Primární jazyk pro nově přidané buňky můžete nastavit z rozevíracího seznamu v horním panelu příkazů.

   ![výchozí synapse-jazyk](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Použití více jazyků

V jednom poznámkovém bloku můžete použít více jazyků zadáním správného příkazu magie jazyka na začátku buňky. V následující tabulce jsou uvedeny magické příkazy pro přepínání buněk.

|Kouzelný příkaz |Jazyk | Popis |  
|---|------|-----|
|%%pyspark| Python | Spusťte dotaz **Pythonu** proti kontextu Spark.  |
|%%jiskra| Scala | Spusťte dotaz **Scala** proti kontextu Spark.  |  
|%%sql| SparkSQL | Spusťte dotaz **SparkSQL** proti kontextu Spark.  |
|%%csharp | Spark.NET C # | Spusťte dotaz **Spark.NET C#** proti kontextu Spark. |

Následující obrázek je příkladem toho, jak můžete napsat dotaz PySpark pomocí příkazu **%%pyspark** magic nebo sparksql s příkazem **%%sql** magic v poznámkovém bloku **Spark(Scala).** Všimněte si, že primární jazyk poznámkového bloku je nastaven na Scala.

   ![synapse-jiskra-magie](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Použití dočasných tabulek k odkazování na data v různých jazycích

V poznámkovém bloku Synapse Studio nelze odkazovat na data nebo proměnné přímo v různých jazycích. V Spark, dočasná tabulka může odkazovat napříč jazyky. Tady je příklad, jak `Scala` číst dataframe `SparkSQL` a `PySpark` používat proměnlivou tabulku Spark jako řešení.

1. V buňce 1 si přečtěte konektor DataFrame z fondu SQL pomocí scaly a vytvořte dočasnou tabulku.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. V buňce 2 se dotaznají na data pomocí Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. V buňce 3 použijte data v PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>Technologie IntelliSense ve stylu ide

Notebooky Azure Synapse Studio jsou integrované s editorem Monaco, aby do editoru buněk přinesly technologie IntelliSense ve stylu IDE. Zvýraznění syntaxe, tvůrce chyb a automatické dokončování kódu vám pomohou psát kód a rychleji identifikovat problémy.

Funkce Technologie IntelliSense jsou pro různé jazyky na různých úrovních splatnosti. V následující tabulce zjistíte, co je podporováno.

|Jazyky| Zvýraznění syntaxe | Značka chybsyntaxili  | Dokončení kódu syntaxe | Dokončení kódu proměnné| Dokončení kódu systémové funkce| Dokončení kódu uživatelské funkce| Inteligentní odsazení | Skládání kódu|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Jiskra (Scala)|Ano|Ano|Ano|Ano|-|-|-|Ano|
|SparkSQL|Ano|Ano|-|-|-|-|-|-|
|Spark.NET (C#)|Ano|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formátování textové buňky pomocí tlačítek panelu nástrojů

Pomocí tlačítek formátu v pruhu nástrojů textových buněk můžete provést běžné akce markdownu. Obsahuje tučný text, kurzívání textu, vkládání fragmentů kódu, vkládání neuspořádaných seznamů, vkládání uspořádaného seznamu a vkládání obrázku z adresy URL.

  ![synapse-text-cell-panel nástrojů](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Vrátit operace buněk
Klepnutím na tlačítko **zpět** nebo stisknutím **kombinace kláves Ctrl+Z** odvolejte poslední operaci s buňkou. Nyní můžete vrátit až do posledních 20 historických buněčných akcí. 

   ![synapse-undo-buňky](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Přesunutí buňky

Vyberte tři tečky (...) pro přístup k nabídce dalších akcí buněk zcela vpravo. Pak vyberte **Přesunout buňku nahoru** nebo **Přesunout buňku dolů,** abyste přesunuli aktuální buňku. 

V režimu příkazů můžete také použít [klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **kombinace kláves Ctrl+Alt+↑** přesuňte aktuální buňku nahoru. Stisknutím **kombinace kláves Ctrl+Alt+↓** přesuňte aktuální buňku dolů.

   ![přesunutí buňky](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Odstranění buňky

Chcete-li odstranit buňku, vyberte tři tečky (...) pro přístup k nabídce dalších akcí buněk zcela vpravo a pak vyberte **Odstranit buňku**. 

V režimu příkazů můžete také použít [klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **klávesy D,D** odstraňte aktuální buňku.
  
   ![odstranit buňku](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Sbalení vstupu buňky
Kliknutím na tlačítko se šipkou v dolní části aktuální buňky ji sbalte. Chcete-li ji rozbalit, klepněte na tlačítko se šipkou, když je buňka sbalená.

   ![sbalit-buňka-vstup](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Sbalení výstupu buňky

Kliknutím na tlačítko **sbalit výstup** v levém horním rohu aktuálního výstupu buňky jej sbalte. Chcete-li jej rozbalit, klepněte na **tlačítko Zobrazit výstup buňky,** zatímco výstup buňky je sbalený.

   ![sbalit-buňka-výstup](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Spuštění poznámkových bloků

Buňky kódu v poznámkovém bloku můžete spouštět jednotlivě nebo všechny najednou. V poznámkovém bloku je znázorněn stav a průběh jednotlivých buněk.

### <a name="run-a-cell"></a>Spuštění buňky

Existuje několik způsobů, jak spustit kód v buňce.

1. Najeďte přes buňku, kterou chcete spustit, a vyberte tlačítko **Spustit buňku** nebo stiskněte **Ctrl+Enter**.

   ![run-buňka-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Chcete-li získat přístup k nabídce dalších akcí buněk zcela vpravo, vyberte tři tečky (**...**). Potom vyberte **Spustit buňku**.

   ![run-buňka-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. V režimu příkazů použijte [klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **kláves Shift+Enter** spusťte aktuální buňku a vyberte buňku níže. Stisknutím **kláves Alt+Enter** spusťte aktuální buňku a vložte pod ni novou buňku.


### <a name="run-all-cells"></a>Spustit všechny buňky
Kliknutím na tlačítko **Spustit vše** spustíte všechny buňky v aktuálním poznámkovém bloku postupně.

   ![run-all-buňky](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Spustit všechny buňky nad nebo pod

Chcete-li získat přístup k nabídce dalších akcí buněk zcela vpravo, vyberte tři tečky (**...**). Potom vyberte **Spustit buňky výše,** chcete-li spustit všechny buňky nad proudem v pořadí. Vyberte **Spustit buňky níže,** chcete-li spustit všechny buňky pod aktuální v pořadí.

   ![run-buňky nad-nebo-nižší](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indikátor stavu buňky

Pod buňkou se zobrazí stav spuštění podrobné buňky, který vám pomůže zobrazit aktuální průběh. Po dokončení spuštění buňky se zobrazí souhrn spuštění s celkovou dobou trvání a časem ukončení a uchovává se tam pro budoucí použití.

![stav buňky](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indikátor průběhu jiskry

Notebook Azure Synapse Studio je čistě založený na Sparku. Buňky kódu jsou spouštěny ve fondu Spark vzdáleně. Indikátor průběhu úlohy Spark je k dispozici s indikátorem průběhu v reálném čase, který vám pomůže pochopit stav spuštění úlohy.


![ukazatel jiskřice](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfigurace relace Jiskra

Můžete určit dobu trvání časového času, číslo a velikost vykonavatelů, které chcete přidělit aktuální relaci Spark v **relaci Configure**. Restartování relace Spark je pro změny konfigurace se projeví. Všechny proměnné poznámkového bloku uložené v mezipaměti jsou vymazány.

![relace-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Přenesení dat do poznámkového bloku

Můžete načíst data z Azure Blob Storage, Azure Data Lake Store Gen 2 a fondu SQL, jak je znázorněno na ukázkách kódu níže.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Čtení CSV z Azure Data Lake Store Gen2 jako datový rámec Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Čtení csv z Azure Blob Storage jako datový rámec Spark

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Čtení dat z účtu primárního úložiště

K datům v účtu primárního úložiště můžete přistupovat přímo. Není třeba poskytovat tajné klíče. V Průzkumníkovi dat klikněte pravým tlačítkem myši na soubor a výběrem **možnosti Nový poznámkový blok** zobcíte, abyste viděli nový poznámkový blok s automatickým generováním extraktoru dat.

![data do buňky](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Vizualizace dat v poznámkovém bloku

### <a name="display"></a>Display()

Tabulkové zobrazení výsledků je vybaveno možností vytvořit pruhový graf, spojnicový graf, výsečový graf, bodový graf a plošný graf. Můžete vizualizovat data bez nutnosti psát kód. Grafy lze přizpůsobit v **možnostech grafu**. 

Výstup **%%sql** magic příkazy se zobrazí ve výchozím zobrazení vykreslené tabulky. Můžete volat **zobrazení(`<DataFrame name>`)** na Spark DataFrames nebo odolné distribuované datové sady (RDD) funkce k vytvoření vykreslené zobrazení tabulky.

   ![vestavěné grafy](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Html nebo interaktivní knihovny, jako **je bokeh**, můžete vykreslit pomocí **displayHTML()**.

Následující obrázek je příkladem vykreslování glyfů přes mapu pomocí **bokeh**.

   ![bokeh-příklad](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Spusťte následující ukázkový kód a nakreslete výše uvedený obrázek.

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

## <a name="save-notebooks"></a>Uložení poznámkových bloků

Do pracovního prostoru můžete uložit jeden poznámkový blok nebo všechny poznámkové bloky.

1. Chcete-li uložit změny provedené do jednoho poznámkového bloku, vyberte tlačítko **Publikovat** na panelu příkazů poznámkového bloku.

   ![publikovat poznámkový blok](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Chcete-li uložit všechny poznámkové bloky do pracovního prostoru, vyberte na panelu příkazů pracovního prostoru tlačítko **Publikovat vše.** 

   ![publikovat -vše](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Ve vlastnostech poznámkového bloku můžete nakonfigurovat, zda chcete při ukládání zahrnout výstup buňky.

   ![vlastnosti notebooku](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Kouzelné příkazy
V poznámkových blocích Azure Synapse Studio můžete použít známé kouzelnické příkazy Jupyter. Podívejte se na níže uvedený seznam jako aktuální dostupné magické příkazy. Sdělte nám své případy použití na GitHubu, abychom mohli pokračovat v vytváření dalších magických příkazů, které splní vaše potřeby.

Dostupná čárová kouzla: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Dostupná magie buněk: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Klávesové zkratky

Podobně jako poznámkové bloky Jupyter mají poznámkové bloky Azure Synapse Studio modální uživatelské rozhraní. Klávesnice provádí různé věci v závislosti na tom, ve kterém režimu se buňka notebooku nachází. Notebooky Synapse Studio podporují následující dva režimy pro danou buňku kódu: příkazový režim a režim úprav.

1. Buňka je v příkazovém režimu, pokud není k dispozici textový kurzor s výzvou k zadání. Když je buňka v režimu příkazu, můžete poznámkový blok upravit jako celek, ale ne zadávat do jednotlivých buněk. Režim příkazů zadejte stisknutím `ESC` nebo kliknutím myši na tlačítko mimo oblast editoru buňky.

   ![příkazový režim](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Režim úprav je označen textovým kurzorem, který vás vyzve k zadání do oblasti editoru. Když je buňka v režimu úprav, nemůžete do ní zadávat text. Režim úprav zadejte stisknutím `Enter` nebo pomocí myši a klikněte na oblast editoru buňky.
   
   ![rezim-uprav](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Klávesové zkratky v režimu příkazů

Pomocí následujících klávesových zkratek kláves můžete snadněji procházet a spouštět kód v poznámkových blocích Azure Synapse.

| Akce |Synapse Studio notebook zkratky  |
|--|--|
|Spusťte aktuální buňku a vyberte níže | Shift+Enter |
|Spuštění aktuální buňky a vložení pod | Alt+Enter |
|Vybrat buňku nad| Nahoru |
|Vybrat buňku níže| Dolů |
|Vložit buňku nad| A |
|Vložit buňku pod| B |
|Rozšířit vybrané buňky nad| Posun+nahoru |
|Rozšíření vybraných buněk pod| Posun+dolů|
|Přesunutí buňky nahoru| Ctrl+Alt+↑ |
|Přesunutí buňky dolů| Ctrl+Alt+↓ |
|Odstranění vybraných buněk| D, D |
|Přepnutí do režimu úprav| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Klávesové zkratky v režimu úprav

Pomocí následujících klávesových zkratek kláves můžete snadněji procházet a spouštět kód v poznámkových blocích Azure Synapse v režimu úprav.

| Akce |Zástupci notebooků Synapse Studio  |
|--|--|
|Přesunutí kurzoru nahoru | Nahoru |
|Přesunutí kurzoru dolů|Dolů|
|Zpět|Ctrl + Z|
|Opakovat|Ctrl + Y|
|Komentář/Odkomentovat|Ctrl + /|
|Odstranit slovo před|Ctrl + Backspace|
|Odstranit slovo za|Ctrl + Odstranit|
|Přejít na začátek buňky|Ctrl + Domů|
|Přejít na konec buňky |Ctrl + Konec|
|Přejít jedno slovo doleva|Ctrl + doleva|
|Jdi o jedno slovo doprava.|Ctrl + Doprava|
|Vybrat vše|Ctrl + A|
|Odrážka| Ctrl + ]|
|Odsazení|Ctrl + [|
|Přepnutí do příkazového režimu| Esc |

## <a name="next-steps"></a>Další kroky

- [.NET pro dokumentaci Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
