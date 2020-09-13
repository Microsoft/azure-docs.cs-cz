---
title: 'Kurz: vytvoření definice úlohy Apache Spark v synapse studiu'
description: Kurz – použití analýzy Azure Synapse k vytvoření definic úloh Spark a jejich odeslání do Apache Spark pro fond Azure synapse Analytics.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: a9e5660e6fbf1e1329af8622e982d44a4da3d86b
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033604"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Kurz: vytvoření definice úlohy Apache Spark v synapse studiu

Tento kurz ukazuje, jak pomocí Azure synapse studia vytvořit definice úloh Apache Spark a pak je odeslat do fondu Apache Spark.

Tento kurz se zabývá následujícími úkony:

* Vytvoření definice úlohy Apache Spark pro PySpark (Python)
* Vytvoření definice úlohy Apache Spark pro Spark (Scala)
* Vytvoření definice úlohy Apache Spark pro .NET Spark (C#/F #)
* Odeslání definice úlohy Apache Spark jako úlohy služby Batch
* Přidání definice úlohy Apache Spark do kanálu

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:

* Pracovní prostor analýzy Azure synapse. Pokyny najdete v tématu [Vytvoření pracovního prostoru Azure synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Fond Apache Spark
* ADLS Gen2 účet úložiště. Musíte být vlastníkem dat objektu BLOB úložiště ADLS Gen2 systému souborů, se kterým chcete pracovat. Pokud ne, budete muset oprávnění přidat ručně.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Vytvoření definice úlohy Apache Spark pro PySpark (Python)

V této části vytvoříte definici úlohy Apache Spark pro PySpark (Python).

1. Otevřete [Azure synapse Studio](https://web.azuresynapse.net/).

2. Můžete přejít na [ukázkové soubory pro vytvoření Apache Spark definice úloh](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) a stáhnout **WORDCOUNT.py** a **shakespear.txt**. Pak tyto soubory nahrajte do Azure Storage: klikněte na **data**, vyberte **účty úložiště**a nahrajte související soubory do adls Gen2 systému souborů. Tento krok přeskočte, pokud už máte soubory ve službě Azure Storage. 

     ![nahrát soubor Pythonu](./media/apache-spark-job-definitions/upload-python-file.png)

3. Klikněte na **vytvořit** centrum, v levém podokně vyberte **Definice úloh Spark** a klikněte na. Uzel akce vedle **definice úlohy Spark**a pak v místní nabídce vyberte **Nová definice úlohy Spark** .

     ![vytvořit novou definici pro Python](./media/apache-spark-job-definitions/create-new-definition.png)

4. V rozevíracím seznamu jazyk v hlavním okně Definice úlohy Apache Spark vyberte **PySpark (Python)** .

5. Vyplňte informace pro Apache Spark definice úlohy. Můžete zkopírovat ukázkové informace.

     |  Vlastnost   | Popis   |  
     | ----- | ----- |  
     |Název definice úlohy| Zadejte název definice úlohy Apache Spark. Tento název se dá kdykoli aktualizovat, dokud nebude publikovaný. Vzorku `job definition sample`|
     |Soubor hlavní definice| Hlavní soubor, který se používá pro úlohu. Vyberte ze svého úložiště soubor PY. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště. Vzorku `abfss://…/path/to/wordcount.py`|
     |Argumenty příkazového řádku| Nepovinné argumenty úlohy. Vzorku `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Referenční soubory| Další soubory používané pro reference v hlavním definičním souboru. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště. |
     |Fond Spark| Úloha se odešle do vybraného fondu Apache Spark.|
     |Verze Sparku| Verze Apache Spark, kterou fond Apache Spark používá|
     |Prováděcí moduly| Počet prováděcích modulů, které se mají udělit v zadaném fondu Apache Spark pro úlohu.|
     |Velikost prováděcího modulu| Počet jader a paměti, které se mají použít pro vykonavatele zadané v zadaném fondu Apache Spark pro úlohu.|  
     |Velikost ovladače| Počet jader a paměti, které se mají použít pro ovladač zadaný v zadaném fondu Apache Spark pro úlohu.|

     ![Nastavení hodnoty definice úlohy Spark pro Python](./media/apache-spark-job-definitions/create-py-definition.png)

6. Vyberte **publikovat** a uložte definici úlohy Apache Spark.

     ![publikovat definici py](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Vytvoření definice úlohy Apache Spark pro Apache Spark (Scala)

V této části vytvoříte definici úlohy Apache Spark pro Apache Spark (Scala).

 1. Otevřete [Azure synapse Studio](https://web.azuresynapse.net/).

 2. Můžete přejít na [ukázkové soubory pro vytvoření Apache Spark definice úloh](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) a stáhnout **WORDCOUNT. jar** a **shakespear.txt**. Pak tyto soubory nahrajte do Azure Storage: klikněte na **data**, vyberte **účty úložiště**a nahrajte související soubory do adls Gen2 systému souborů. Tento krok přeskočte, pokud už máte soubory ve službě Azure Storage. 
 
     ![Příprava struktury Scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Klikněte na **vytvořit** centrum, v levém podokně vyberte **Definice úloh Spark** a klikněte na. Uzel akce vedle **definice úlohy Spark**a pak v místní nabídce vyberte **Nová definice úlohy Spark** .
     ![vytvořit novou definici pro Scala](./media/apache-spark-job-definitions/create-new-definition.png)

 4. V rozevíracím seznamu jazyk v hlavním okně Definice úlohy Apache Spark vyberte **Spark (Scala)** .

 5. Vyplňte informace pro Apache Spark definice úlohy. Můžete zkopírovat ukázkové informace.

     |  Vlastnost   | Popis   |  
     | ----- | ----- |  
     |Název definice úlohy| Zadejte název definice úlohy Apache Spark. Tento název se dá kdykoli aktualizovat, dokud nebude publikovaný. Vzorku `job definition sample`|
     |Soubor hlavní definice| Hlavní soubor, který se používá pro úlohu. Vyberte ze svého úložiště soubor JAR. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště. Vzorku `abfss://…/path/to/wordcount.jar`|
     |Název hlavní třídy| Plně kvalifikovaný identifikátor nebo hlavní třída, která je v hlavním definičním souboru. Vzorku `WordCount`|
     |Argumenty příkazového řádku| Nepovinné argumenty úlohy. Vzorku `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Referenční soubory| Další soubory používané pro reference v hlavním definičním souboru. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště.|
     |Fond Spark| Úloha se odešle do vybraného fondu Apache Spark.|
     |Verze Sparku| Verze Apache Spark, kterou fond Apache Spark používá|
     |Prováděcí moduly| Počet prováděcích modulů, které se mají udělit v zadaném fondu Apache Spark pro úlohu.|  
     |Velikost prováděcího modulu| Počet jader a paměti, které se mají použít pro vykonavatele zadané v zadaném fondu Apache Spark pro úlohu.|
     |Velikost ovladače| Počet jader a paměti, které se mají použít pro ovladač zadaný v zadaném fondu Apache Spark pro úlohu.|

     ![Nastavte hodnotu definice úlohy Spark pro Scala.](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. Vyberte **publikovat** a uložte definici úlohy Apache Spark.

     ![publikování definice Scala](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Vytvoření definice úlohy Apache Spark pro .NET Spark (C#/F #)

V této části vytvoříte definici úlohy Apache Spark pro .NET Spark (C#/F #).
 1. Otevřete [Azure synapse Studio](https://web.azuresynapse.net/).

 2. Můžete přejít na [ukázkové soubory pro vytvoření Apache Spark definice úloh](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) a stáhnout **wordcount.zip** a **shakespear.txt**. Pak tyto soubory nahrajte do Azure Storage: klikněte na **data**, vyberte **účty úložiště**a nahrajte související soubory do adls Gen2 systému souborů. Tento krok přeskočte, pokud už máte soubory ve službě Azure Storage. 

     ![Příprava struktury dotnet](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Klikněte na **vytvořit** centrum, v levém podokně vyberte **Definice úloh Spark** a klikněte na. Uzel akce vedle **definice úlohy Spark**a pak v místní nabídce vyberte **Nová definice úlohy Spark** .

     ![vytvořit novou definici pro dotnet](./media/apache-spark-job-definitions/create-new-definition.png)

 4. V rozevíracím seznamu jazyk v hlavním okně Definice úlohy Apache Spark vyberte **.NET Spark (C#/f #)** .

 5. Vyplňte informace pro Apache Spark definice úlohy. Můžete zkopírovat ukázkové informace.
    
     |  Vlastnost   | Popis   |  
     | ----- | ----- |  
     |Název definice úlohy| Zadejte název definice úlohy Apache Spark. Tento název se dá kdykoli aktualizovat, dokud nebude publikovaný. Vzorku `job definition sample`|
     |Soubor hlavní definice| Hlavní soubor, který se používá pro úlohu. Vyberte soubor ZIP, který obsahuje vaši aplikaci .NET for Apache Spark (tj. hlavní spustitelný soubor, knihovny DLL obsahující uživatelsky definované funkce a další požadované soubory) ze svého úložiště. Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště. Vzorku `abfss://…/path/to/wordcount.zip`|
     |Hlavní spustitelný soubor| Hlavní spustitelný soubor v hlavní definici souboru ZIP. Vzorku `WordCount`|
     |Argumenty příkazového řádku| Nepovinné argumenty úlohy. Vzorku `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Referenční soubory| Další soubory, které jsou vyžadovány pracovními uzly pro spuštění aplikace .NET pro Apache Spark aplikaci, která není obsažena v hlavní definici souboru ZIP (to znamená, že závisí na jar, dalších uživatelsky definovaných funkcí DLL a dalších konfiguračních souborech). Můžete vybrat **Odeslat soubor** a nahrát ho do účtu úložiště.|
     |Fond Spark| Úloha se odešle do vybraného fondu Apache Spark.|
     |Verze Sparku| Verze Apache Spark, kterou fond Apache Spark používá|
     |Prováděcí moduly| Počet prováděcích modulů, které se mají udělit v zadaném fondu Apache Spark pro úlohu.|  
     |Velikost prováděcího modulu| Počet jader a paměti, které se mají použít pro vykonavatele zadané v zadaném fondu Apache Spark pro úlohu.|
     |Velikost ovladače| Počet jader a paměti, které se mají použít pro ovladač zadaný v zadaném fondu Apache Spark pro úlohu.|

     ![Nastavení hodnoty definice úlohy Sparku pro dotnet](./media/apache-spark-job-definitions/create-net-definition.png)

 6. Vyberte **publikovat** a uložte definici úlohy Apache Spark.

      ![publikovat definici dotnet](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Odeslání definice úlohy Apache Spark jako úlohy služby Batch

Po vytvoření Apache Spark definice úlohy ji můžete odeslat do fondu Apache Spark. Ujistěte se, že jste vlastníkem dat objektu BLOB úložiště ADLS Gen2 systém souborů, se kterým chcete pracovat. Pokud ne, budete muset oprávnění přidat ručně.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Scénář 1: odeslání definice úlohy Apache Spark
 1. Kliknutím otevřete okno Definice úlohy Apache Spark.

      ![Otevření definice úlohy Spark k odeslání ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Kliknutím na ikonu **Odeslat** odešlete projekt do vybraného fondu Apache Spark. Kliknutím na kartu **Adresa URL monitorování Spark** můžete zobrazit LogQuery aplikace Apache Spark.

    ![Kliknutím na tlačítko Odeslat odešlete definici úlohy Spark.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialogové okno pro odeslání Sparku](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Scénář 2: zobrazení průběhu úlohy Apache Spark běhu

 1. Klikněte na **monitorování**a pak vyberte možnost **aplikace Spark** . Můžete najít odeslanou aplikaci Apache Spark.

     ![Zobrazit aplikaci Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Pak klikněte na Apache Spark aplikace, zobrazí se okno **LogQuery** . Průběh provádění úlohy můžete zobrazit z **LogQuery**.
     
     ![Zobrazit LogQuery aplikace Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scénář 3: zkontrolování výstupního souboru

 1. Klikněte na **data**a pak vyberte **účty úložiště**. Po úspěšném spuštění můžete přejít do úložiště ADLS Gen2 a vygeneruje se výstupy kontroly.

     ![Zobrazit výstupní soubor](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Přidání definice úlohy Apache Spark do kanálu

V této části přidáte do kanálu definici úlohy Apache Spark.

 1. Otevřete existující definici úlohy Apache Spark.

 2. Klikněte na ikonu v pravém horním rohu definice úlohy Apache Spark, vyberte **existující kanál**nebo **Nový kanál**. Další informace najdete na stránce kanálu.

     ![Přidat do kanálu](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Přidat do kanálu](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Další kroky

Tento kurz ukazuje, jak pomocí Azure synapse studia vytvořit definice úloh Apache Spark a pak je odeslat do fondu Apache Spark. Dále můžete pomocí Azure synapse studia vytvářet Power BI datové sady a spravovat Power BI data.

