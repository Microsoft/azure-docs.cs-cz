---
title: "Příprava dat pro kurz klasifikace Iris ve službách Azure Machine Learning | Dokumentace Microsoftu"
description: "V tomto kurzu na pokračování se dozvíte, jak komplexně používat služby Azure Machine Learning (Preview). Toto je první část, ve které se probírá příprava dat."
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 0bef557ee1394e3c786fd2c54e821b5dea28fabf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>Kurz: Klasifikace Iris, část 1 – Příprava dat

Služby Azure Machine Learning (Preview) představují integrované, komplexní řešení datové vědy a pokročilé analýzy pro profesionální datové vědce, které slouží k přípravě dat, vývoji experimentů a nasazování modelů na úrovni cloudu.

Tento kurz je první částí z třídílné série. V tomto kurzu vás provedeme základy služeb Azure Machine Learning (Preview). Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu v aplikaci Azure Machine Learning Workbench
> * Vytvoření balíčku pro přípravu dat
> * Vygenerování kódu Pythonu/PySpark k vyvolání balíčku pro přípravu dat

Tento kurz používá nadčasovou [datovou sadu Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). Snímky obrazovky jsou specifické pro systém Windows, ale prostředí v systému macOS je téměř shodné.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

K dokončení tohoto kurzu potřebujete:
- Účet Experimentování ve službě Azure Machine Learning
- Nainstalovanou aplikaci Azure Machine Learning Workbench

Pokud je ještě nemáte, postupujte podle kroků v článku [Rychlý start: Instalace a spuštění](quickstart-installation.md) a nastavte tento účet a nainstalujte aplikaci Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Vytvoření nového projektu v aplikaci Workbench

Pokud jste postupovali podle kroků v článku [Rychlý start: Instalace a spuštění](quickstart-installation.md), měli byste tento projekt už mít a můžete přeskočit k další části.

1. Otevřete aplikaci Azure Machine Learning Workbench a v případě potřeby se přihlaste. 
   
   + Ve Windows ji spustíte pomocí zástupce **Machine Learning Workbench** na ploše. 
   + V systému macOS vyberte **Azure ML Workbench** na hlavním panelu.

1. V podokně **PROJEKTY** vyberte symbol plus (+) a zvolte **Nový projekt**.  

   ![Nový pracovní prostor](media/tutorial-classifying-iris/new_ws.png)

1. Vyplňte pole formuláře a výběrem tlačítka **Vytvořit** v aplikaci Workbench vytvořte nový projekt.

   Pole|Navrhovaná hodnota pro tento kurz|Popis
   ---|---|---
   Název projektu | myIris |Zadejte jedinečný název, který identifikuje váš účet. Můžete použít své jméno nebo třeba název oddělení nebo projektu, který experiment nejlépe identifikuje. Název musí mít délku od 2 do 32 znaků. Může obsahovat jenom alfanumerické znaky a znak spojovníku (-). 
   Adresář projektu | c:\Temp\ | Zadejte adresář, ve kterém se projekt vytvoří.
   Popis projektu | _ponechte prázdné_ | Volitelné pole, které je užitečné pro popis projektů.
   Visualstudio.com |_ponechte prázdné_ | Volitelné pole. Projekt je volitelně možné přidružit k úložišti Git ve Visual Studio Team Services, které umožňuje správu zdrojového kódu a spolupráci. [Tady najdete potřebné informace.](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo) 
   Pracovní prostor | IrisGarden (pokud existuje) | Zvolte pracovní prostor, který jste vytvořili pro svůj účet Experimentování na webu Azure Portal. <br/>Pokud jste postupovali podle tohoto rychlého startu, měli byste mít pracovní prostor IrisGarden. Pokud ne, vyberte pracovní prostor, který jste vytvořili při vytváření účtu Experimentování, nebo jakýkoli jiný, který chcete použít.
   Šablona projektu | Klasifikace Iris | Šablony obsahují skripty a data, pomocí kterých můžete produkt zkoumat. Tato šablona obsahuje skripty a data, které potřebujete pro tento rychlý start a další kurzy na tomto webu dokumentace. 

   ![Nový projekt](media/tutorial-classifying-iris/new_project.png)
 
 Vytvoří se nový projekt a otevře se řídicí panel projektu s tímto projektem. Teď můžete prozkoumat domovskou stránku projektu, zdroje dat, poznámkové bloky a soubory zdrojového kódu. 

## <a name="create-a-data-preparation-package"></a>Vytvoření balíčku pro přípravu dat

V této části kurzu prozkoumáte data a zahájíte proces přípravy dat. Při přípravě dat v aplikaci Azure Machine Learning Workbench se JSON s reprezentací provedených transformací v aplikaci Workbench uloží v místním balíčku pro přípravu dat (soubor *.dprep). Tento balíček pro přípravu dat je primárním kontejnerem pro práci na přípravě dat v aplikaci Workbench.

Tento balíček pro přípravu dat je možné předat ke zpracování do modulu runtime, jako je místní C#/CoreCLR, Scala/Spark, nebo Scala/HDI. kde se vygeneruje kód ke spuštění pro odpovídající modul runtime. 

1. Výběrem ikony složky otevřete zobrazení souborů a pak výběrem otevřete soubor **iris.csv**.  

   Tento soubor představuje tabulku s 5 sloupci a 150 řádky. Obsahuje čtyři sloupce s čísly a cílový sloupec řetězcového typu. Neobsahuje záhlaví sloupců.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Nezahrnujte datové soubory do složky projektu, zejména pokud jsou soubory velké. Do této šablony jsme pro demonstrační účely soubor **iris.csv** zahrnuli, protože je malý. Další informace najdete v tématu [Čtení a zápis velkých datových souborů](how-to-read-write-files.md).

2. V **Zobrazení dat** vyberte symbol plus (**+**) a přidejte nový zdroj dat. Otevře se stránka **Přidat zdroj dat**. 

   ![Zobrazení dat](media/tutorial-classifying-iris/data_view.png)

3. Vyberte **Textové soubory (*.csv, .json, .txt.,... )** a klikněte na **Další**.
   ![Zdroj dat](media/tutorial-classifying-iris/data-source.png)
   

4. Přejděte k souboru **iris.csv** a klikněte na **Další**.  
 
   ![Výběr iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Zkontrolujte, že jste pro účely tohoto cvičení vybrali soubor **iris.csv** z aktuálního adresáře projektu. Jinak se pozdější kroky nemusí podařit.
   
5. Ponechte výchozí hodnoty a klikněte na **Dokončit**.

6. Vytvoří se nový soubor s názvem **iris-1.dsource**. Soubor má jedinečný název s „-1“, protože ukázkový projekt už obsahuje neočíslovaný soubor **iris.dsource**.  

   Soubor se otevře a zobrazí se data. Do této datové sady se automaticky přidá řada záhlaví sloupců od **Column1** po **Column5**. Přejděte do dolní části a všimněte si, že poslední řádek datové sady je prázdný. Řádek je prázdný, protože v souboru CSV je zalomení řádku navíc.

   ![Zobrazení dat iris](media/tutorial-classifying-iris/iris_data_view.png)

7. Vyberte tlačítko **Metriky**. Podívejte se na histogramy. Pro každý sloupec se vypočítala úplná sada statistik. Můžete také vybrat tlačítko **Data** a znovu zobrazit data. 

   ![Zobrazení dat iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Vyberte tlačítko **Připravit**. Otevře se dialogové okno **Připravit**. 

   Ukázkový projekt již obsahuje soubor **iris.dprep**. Ve výchozím nastavení se zobrazí výzva k vytvoření nového toku dat v již existujícím balíčku pro přípravu dat **iris.dprep**. 

   Z rozevírací nabídky vyberte **+ Nový balíček pro přípravu dat**, jako název balíčku zadejte novou hodnotu **iris-1** a pak vyberte **OK**.

   ![Zobrazení dat iris](media/tutorial-classifying-iris/new_dprep.png)

   Vytvoří se nový balíček pro přípravu dat s názvem **iris-1.dprep** a otevře se v editoru přípravy dat.

9. Teď provedeme základní přípravu dat. Výběrem záhlaví jednotlivých sloupců umožníte úpravu jejich textu. Přejmenujte jednotlivé sloupce následujícím způsobem: 

   V uvedeném pořadí zadejte pro jednotlivé sloupce názvy **Sepal Length**, **Sepal Width**, **Petal Length**, **Petal Width** a **Species**.

   ![Přejmenování sloupců](media/tutorial-classifying-iris/rename_column.png)

10. Pokud chcete zjistit počet jedinečných hodnot, vyberte sloupec **Species** a kliknutím pravým tlačítkem jej vyberte. Z rozevírací nabídky vyberte **Četnost hodnot**. 

   Tato akce otevře pod daty podokno **Kontroly**. Zobrazí se histogram se čtyřmi pruhy. Cílový sloupec obsahuje tři různé hodnoty **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** a hodnotu **(null)**.

   ![Výběr možnosti Četnost hodnot](media/tutorial-classifying-iris/value_count.png)

11. Pokud chcete vyfiltrovat hodnoty null, vyberte štítek Null a pak symbol minus (**-**). Záznam s hodnotou Null pak zešedne, což značí, že je vyfiltrovaný. 

   ![Histogram počtu hodnot](media/tutorial-classifying-iris/filter_out.png)

12. Všimněte si jednotlivých kroků uvedených v podokně **KROKY**. Při přejmenování sloupců a vyfiltrování řádků s hodnotou null se každá akce zaznamenala jako krok přípravy dat. Jednotlivé kroky můžete upravit a tím upravit nastavení, změnit pořadí kroků a odebrat jednotlivé kroky.

   ![Kroky](media/tutorial-classifying-iris/steps.png)

13. Zavřete editor přípravy dat. Vyberte **Zavřít** (x) na kartě **iris-1** s ikonou grafu. Vaše práce se automaticky uloží do souboru **iris-1.dprep** zobrazeného pod nadpisem **Příprava dat**.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Vygenerování kódu Pythonu/PySpark k vyvolání balíčku pro přípravu dat

<!-- The output/results of a Package can be explored in Python or via a Jupyter Notebook. A Package can be executed across multiple runtimes including local Python, Spark (including in Docker), and HDInsight. A Package contains one or more Dataflows that are the steps and transforms applied to the data. A Package may use another Package as a Data Source (referred to as a Reference Data Flow). -->

1. Na kartě Příprava dat vyhledejte soubor **iris-1.dprep**.

1. Klikněte pravým tlačítkem na soubor **iris-1.dprep** a v místní nabídce vyberte **Vygenerovat soubor s kódem pro přístup k datům**. 

   ![Generování kódu](media/tutorial-classifying-iris/generate_code.png)

   Otevře se nový soubor **iris-1.py** s následujícími řádky kódu vyvolávajícími logiku, kterou jste vytvořili jako balíček pro přípravu dat:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   V závislosti na kontextu, ve kterém je tento kód spuštěný, může `df` představovat různé druhy datových rámců. [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) se používá při spuštění v modulu runtime Pythonu a [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) se používá při spuštění v kontextu Sparku. 
   
   Další informace o přípravě dat v aplikaci Azure Machine Learning Workbench najdete v příručce [Začínáme s přípravou dat](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí aplikace Azure Machine Learning Workbench provedli následující úlohy:
> [!div class="checklist"]
> * Vytvoření nového projektu
> * Vytvoření balíčku pro přípravu dat
> * Vygenerování kódu Pythonu/PySpark k vyvolání balíčku pro přípravu dat

Jste připraveni přejít k další části této série kurzů, ve které se naučíte sestavit model ve službě Azure Machine Learning:
> [!div class="nextstepaction"]
> [Kurz 2 – Sestavení modelů](tutorial-classifying-iris-part-2.md)
