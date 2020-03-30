---
title: Azure Data Factory – ukázky
description: Obsahuje podrobnosti o ukázkách, které jsou dodávány se službou Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63db1810cbdd2133bc0577530325351997b31f30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139266"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory – ukázky
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, podívejte se na [ukázky prostředí PowerShell v datové továrně](../samples-powershell.md) a [ukázky kódu v galerii Ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Ukázky na GitHubu
Úložiště [GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) obsahuje několik ukázek, které vám pomůžou rychle se zintenzivnit pomocí služby Azure Data Factory (nebo) upravit skripty a použít je ve vlastní aplikaci. Složka Samples\JSON obsahuje úryvky JSON pro běžné scénáře.

| Ukázka | Popis |
|:--- |:--- |
| [Návod k systému ADF](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Tato ukázka poskytuje komplexní návod pro zpracování souborů protokolu pomocí Azure Data Factory k přepnutí dat ze souborů protokolu do přehledů. <br/><br/>V tomto návodu kanál Data Factory shromažďuje ukázkové protokoly, zpracovává a obohacuje data z protokolů s referenčními daty a transformuje data k vyhodnocení účinnosti marketingové kampaně, která byla nedávno spuštěna. |
| [Vzorky JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Tato ukázka obsahuje příklady JSON pro běžné scénáře. |
| [Ukázka stahovače dat http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Tato ukázka představuje stahování dat z koncového bodu HTTP do úložiště objektů blob Azure pomocí vlastní aktivity .NET. |
| [Ukázka čisté aktivity mezi aplikacemi](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Tato ukázka umožňuje vytvořit vlastní aktivitu .NET, která není omezena na verze sestavení používané spouštěč adf (například WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, atd.). |
| [Spustit skript R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Tato ukázka zahrnuje vlastní aktivitu Factory dat, kterou lze použít k vyvolání souboru RScript.exe. Tato ukázka funguje pouze s vlastním (nikoli na vyžádání) hdinsight clusteru, který již má R nainstalován na něm. |
| [Vyvolat úlohy Spark v clusteru HDInsight Hadoop](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |Tato ukázka ukazuje, jak pomocí MapReduce aktivity vyvolat program Spark. Spark program jen zkopíruje data z jednoho kontejneru objektů Blob Azure do jiného. |
| [Twitter analýza pomocí Azure Machine Learning batch scoring aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Tato ukázka ukazuje, jak pomocí AzureMLBatchScoringActivity vyvolat model Azure Machine Learning, který provádí analýzu mínění twitter, vyhodnocování, předpověď atd. |
| [Twitter Analýza pomocí vlastní aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Tato ukázka ukazuje, jak použít vlastní aktivitu .NET k vyvolání modelu Azure Machine Learning, který provádí analýzu mínění twitter, vyhodnocování, předpověď atd. |
| [Parametrizované kanály pro Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Ukázka poskytuje kód c#end pro nasazení n kanálů pro vyhodnocování a rekvalifikaci s jiným parametrem oblasti, kde seznam oblastí pochází ze souboru parameters.txt, který je součástí této ukázky. |
| [Referenční aktualizace dat pro úlohy Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Tato ukázka ukazuje, jak používat Azure Data Factory a Azure Stream Analytics společně spustit dotazy s referenčními daty a nastavit aktualizaci pro referenční data podle plánu. |
| [Hybridní potrubí s místním Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Ukázka používá místní cluster Hadoop jako výpočetní cíl pro spouštění úloh v Datové továrně stejně jako byste přidali další výpočetní cíle, jako je cluster Hadoop založený na HDInsight v cloudu. |
| [Nástroj pro převod JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Tento nástroj umožňuje převést JSONs z verze před 2015-07-01-preview na nejnovější nebo 2015-07-01-preview (výchozí). |
| [Ukázkový vstupní soubor U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Tento soubor je ukázkový soubor používaný aktivitou U-SQL. |
| [Odstranění souboru objektů blob](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Tato ukázka představuje soubor C#, který lze použít jako součást vlastní aktivity ADF .net k odstranění souborů ze zdrojového umístění objektu Blob Azure po zkopírování souborů.|

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Na GitHubu najdete následující šablony Azure Resource Manageru pro Data Factory.

| Šablona | Popis |
| --- | --- |
| [Kopírování z úložiště objektů blob Azure do databáze Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Nasazení této šablony vytvoří azure továrnu dat s kanálem, který kopíruje data ze zadaného úložiště objektů blob Azure do databáze Azure SQL |
| [Kopírování z Salesforce do úložiště objektů blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Nasazení této šablony vytvoří továrnu dat Azure s kanálem, který kopíruje data ze zadaného účtu Salesforce do úložiště objektů blob Azure. |
| [Transformace dat spuštěním skriptu Hive v clusteru Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Nasazení této šablony vytvoří továrnu dat Azure s kanálem, který transformuje data spuštěním ukázkového skriptu Hive v clusteru Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Ukázky na webu Azure Portal
Pomocí dlaždice **Ukázkové kanály** na domovské stránce vaší datové továrny můžete nasadit ukázkové kanály a jejich přidružené entity (datové sady a propojené služby) do vaší datové továrny.

1. Vytvořte datovou továrnu nebo otevřete existující datovou továrnu. Postup vytvoření datové továrny najdete v tématu [Kopírování dat z úložiště objektů blob do databáze SQL pomocí Data Factory.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
2. V okně **DATA FACTORY** pro datovou továrnu klikněte na dlaždici **Ukázkové kanály.**

    ![Ukázková dlaždice potrubí](./media/data-factory-samples/SamplePipelinesTile.png)
3. V okně **Ukázkové kanály** klikněte na **ukázku,** kterou chcete nasadit.

    ![Vzorové potrubí čepel](./media/data-factory-samples/SampleTile.png)
4. Určete nastavení konfigurace pro ukázku. Například název účtu úložiště Azure a klíč účtu, název serveru Azure SQL, databáze, ID uživatele a heslo atd.

    ![Čepel vzorku](./media/data-factory-samples/SampleBlade.png)
5. Po dokončení s určením nastavení konfigurace, klepněte na tlačítko **Vytvořit** vytvořit nebo nasadit ukázkové kanály a propojené služby/tabulky používané kanály.
6. Stav nasazení se zobrazí na ukázkové dlaždici, na kterou jste klikli dříve v okně **Ukázkové kanály.**

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Když se zobrazí **zpráva deployment succeeded** na dlaždici pro ukázku, zavřete okno **ukázkové kanály.**  
8. V okně **DATA FACTORY** uvidíte, že propojené služby, datové sady a kanály jsou přidány do vaší datové továrny.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Ukázky v sadě Visual Studio
### <a name="prerequisites"></a>Požadavky
Na počítači musíte mít nainstalované tyto položky:

* Visual Studio 2013 nebo Visual Studio 2015.
* Stáhněte si sadu Azure SDK pro Visual Studio 2013 nebo Visual Studio 2015. Přejděte na [stránku položek ke stažení pro Azure](https://azure.microsoft.com/downloads/) a klikněte na **VS 2013** nebo **VS 2015** v části **.NET**.
* Stáhněte si nejnovější modul plug-in Azure Data Factory pro Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) nebo [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Pokud používáte Visual Studio 2013, můžete plugin aktualizovat také následujícím postupem: V nabídce klikněte na **Nástroje** -> **rozšíření a aktualizace** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Nástroje pro vytváření dat pro aktualizaci Visual** -> **Studio**.

### <a name="use-data-factory-templates"></a>Použití šablon factory dat
1. V nabídce klepněte na **Soubor,** přejděte na **Nový**a klepněte na **položku Projekt**.
2. V dialogovém okně **Nový projekt** proveďte následující kroky:

   1. V části Šablony vyberte **Možnost DataFactory** **.**
   2. V pravém podokně **vyberte Šablony datové továrny.**
   3. Zadejte **název** projektu.
   4. Vyberte **umístění** projektu.
   5. Klikněte na tlačítko **OK**.

      ![Dialogové okno Nový projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. V dialogovém okně **Šablony továrny dat** vyberte ukázkovou šablonu z části **Šablony použití případu** a klepněte na tlačítko **Další**. Následující kroky vás provedou pomocí šablony **Profilování zákazníků.** Kroky jsou podobné pro ostatní vzorky.

    ![Dialogové okno Šablony továrny na data](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. V dialogovém okně **Konfigurace datové továrny** klikněte na stránce **Základy datové továrny** na **další.**
5. Na stránce **Konfigurovat vytváření dat** postupujte takto:
   1. Vyberte **Vytvořit novou továrnu na data**. Můžete také vybrat **Použít existující datovou továrnu**.
   2. Zadejte **název** datové továrny.
   3. Vyberte **předplatné Azure,** ve kterém chcete vytvořit datovou továrnu.
   4. Vyberte **skupinu prostředků** pro datovou továrnu.
   5. Vyberte možnost **Západní USA**, **Východní USA**nebo Severní **Evropa** pro **tento region**.
   6. Klikněte na **Další**.
6. Na stránce **Konfigurace úložišť dat** zadejte existující **databázi Azure SQL** a účet úložiště **Azure** (nebo) vytvořte databázi/úložiště a klikněte na Další.
7. Na stránce **Konfigurovat výpočetní výkon** vyberte výchozí hodnoty a klepněte na tlačítko **Další**.
8. Na stránce **Souhrn** zkontrolujte všechna nastavení a klepněte na tlačítko **Další**.
9. Na stránce **Stav nasazení** počkejte na dokončení nasazení a klepněte na tlačítko **Dokončit**.
10. V Průzkumníku řešení klikněte pravým tlačítkem na požadovaný projekt a poté klikněte na **Publikovat**.
11. Pokud se zobrazí dialogové okno **Přihlásit se pomocí účtu Microsoft**, zadejte přihlašovací údaje k účtu s předplatným Azure a klikněte na **Přihlásit**.
12. Mělo by se zobrazit následující dialogové okno:

    ![Dialogové okno publikování](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stránce **Konfigurace datové továrny** proveďte následující kroky:

    1. Potvrďte, že použít existující možnost **factory dat.**
    2. Vyberte **továrnu dat,** kterou jste vybrali při používání šablony.
    3. Kliknutím na **Další** přejděte na stránku **Publish Items** (Publikovat položky). (Pokud je tlačítko **Další** neaktivní, opusťte pole Název stisknutím klávesy **TAB**.)
14. Na stránce **Publish Items** (Publikovat položky) zkontrolujte, jestli jsou vybrané všechny entity služby Data Factory, a kliknutím na **Další** přejděte na stránku **Souhrn**.     
15. Zkontrolujte souhrn a klikněte na **Další**. Spustí se proces nasazení a zobrazí se **Stav nasazení**.
16. Na stránce **Stav nasazení** byste měli vidět stav procesu nasazení. Až se nasazení dokončí, klikněte na Dokončit.

Podrobnosti o použití Visual Studia k vytváření entit Data Factory a publikování do Azure najdete v článku [Vytvoření první datové továrny (Visual Studio).](data-factory-build-your-first-pipeline-using-vs.md)          
