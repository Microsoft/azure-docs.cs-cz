---
title: Azure Data Factory – ukázky
description: Poskytuje podrobnosti o ukázkách, které dodávají službu Azure Data Factory.
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
ms.openlocfilehash: f1e861bf9c6cc2a485ac9f153a0c932ec2e6fbb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85318905"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory – ukázky
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [ukázky PowerShellu v tématu Data Factory](../samples-powershell.md) a [ukázky kódu v galerii ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Ukázky na GitHubu
[Úložiště GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) obsahuje několik ukázek, které vám pomůžou rychle vymezit pomocí služby Azure Data Factory (nebo) změnit skripty a použít je v vlastní aplikaci. Složka Samples\JSON obsahuje fragmenty JSON pro běžné scénáře.

| Ukázka | Popis |
|:--- |:--- |
| [Návod k ADF](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Tato ukázka poskytuje ucelený návod ke zpracování souborů protokolu pomocí Azure Data Factory k přepínání dat ze souborů protokolů v nástroji na přehledy. <br/><br/>V tomto návodu kanál Data Factory shromažďuje ukázkové protokoly, zpracovává a vylepšuje data z protokolů pomocí referenčních dat a transformuje je k vyhodnocení efektivity marketingové kampaně, která se nedávno spustila. |
| [Ukázky JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Tato ukázka poskytuje příklady JSON pro běžné scénáře. |
| [Ukázka stahovacího data http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Tato ukázka předvádí stažení dat z koncového bodu HTTP do Azure Blob Storage pomocí vlastní aktivity rozhraní .NET. |
| [Ukázka aktivity mezi dvěma doménami AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Tato ukázka umožňuje vytvořit vlastní aktivitu rozhraní .NET, která není omezená na verze sestavení používané spouštěčem ADF (například WindowsAzure. Storage v 4.3.0, Newtonsoft.Jsna v 6.0. x atd.). |
| [Spuštění skriptu jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Tato ukázka obsahuje Data Factory vlastní aktivity, které lze použít k vyvolání RScript.exe. Tato ukázka funguje jenom s vlastním clusterem HDInsight (ne na vyžádání), na kterém už je nainstalovaný R. |
| [Vyvolání úloh Spark v clusteru HDInsight Hadoop](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |V této ukázce se dozvíte, jak používat aktivitu MapReduce k vyvolání programu Spark. Program Spark pouze kopíruje data z jednoho kontejneru objektů BLOB v Azure do jiného. |
| [Analýza Twitteru pomocí aktivity dávkového vyhodnocování Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |V této ukázce se dozvíte, jak použít AzureMLBatchScoringActivity k vyvolání Azure Machine Learningho modelu, který provádí Twitter mínění Analysis, bodování, předpovědi atd. |
| [Analýza Twitteru pomocí vlastní aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |V této ukázce se dozvíte, jak použít vlastní aktivitu .NET k vyvolání modelu Azure Machine Learning, který provádí Twitter mínění Analysis, bodování, předpovědi atd. |
| [Parametrizované kanály pro Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Ukázka poskytuje ucelený kód v jazyce C# pro nasazení N kanálů pro vyhodnocování a přeškolení každého s jiným parametrem oblasti, kde seznam oblastí pochází z parameters.txt souboru, který je součástí této ukázky. |
| [Aktualizace referenčních dat pro úlohy Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Tento příklad ukazuje, jak použít Azure Data Factory a Azure Stream Analytics společně ke spouštění dotazů s referenčními daty a nastavení aktualizace referenčních dat podle plánu. |
| [Hybridní kanál s místními Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Ukázka používá místní cluster Hadoop jako výpočetní cíl pro spouštění úloh v Data Factory stejným způsobem jako při přidávání dalších výpočetních cílů, jako je cluster Hadoop založený na HDInsight v cloudu. |
| [Nástroj pro převod JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Tento nástroj umožňuje převést JSON z verze před verzí 2015-07-01-Preview na nejnovější nebo 2015-07-01-Preview (výchozí). |
| [Vzorový vstupní soubor U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Tento soubor je ukázkový soubor používaný aktivitou U-SQL. |
| [Odstranit soubor objektu BLOB](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Tento příklad prezentuje soubor jazyka C#, který se dá použít jako součást vlastní aktivity rozhraní .NET ADF k odstranění souborů ze zdrojového umístění Azure Blob po zkopírování souborů.|

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Následující šablony Azure Resource Manager můžete najít Data Factory na GitHubu.

| Šablona | Popis |
| --- | --- |
| [Kopírování z Azure Blob Storage do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Při nasazení této šablony se vytvoří objekt pro vytváření dat Azure s kanálem, který kopíruje data ze zadaného úložiště objektů BLOB v Azure do Azure SQL Database. |
| [Kopírování z Salesforce do Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Při nasazení této šablony se vytvoří objekt pro vytváření dat Azure s kanálem, který kopíruje data ze zadaného účtu Salesforce do úložiště objektů BLOB v Azure. |
| [Transformace dat spuštěním skriptu podregistru v clusteru Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Při nasazení této šablony se vytvoří objekt pro vytváření dat Azure s kanálem, který transformuje data spuštěním ukázkového skriptu podregistru na Azure HDInsight Hadoop clusteru. |

## <a name="samples-in-azure-portal"></a>Ukázky v Azure Portal
Dlaždici **ukázkové kanály** na domovské stránce vaší datové továrny můžete použít k nasazení ukázkových kanálů a jejich přidružených entit (datových sad a propojených služeb) do vaší datové továrny.

1. Vytvořte datovou továrnu nebo otevřete existující datovou továrnu. Postup vytvoření datové továrny najdete v tématu [kopírování dat z BLOB Storage. SQL Database pomocí Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. V okně **objekt** pro vytváření dat pro objekt pro vytváření dat klikněte na dlaždici **ukázkové kanály** .

    ![Dlaždice ukázek kanálů](./media/data-factory-samples/SamplePipelinesTile.png)
3. V okně **ukázkové kanály** klikněte na **ukázku** , kterou chcete nasadit.

    ![Okno ukázkové kanály](./media/data-factory-samples/SampleTile.png)
4. Zadejte konfigurační nastavení pro ukázku. Například název účtu úložiště Azure a klíč účtu, název logického SQL serveru, databáze, ID uživatele a heslo atd.

    ![Okno vzorku](./media/data-factory-samples/SampleBlade.png)
5. Po dokončení zadání nastavení konfigurace kliknutím na **vytvořit** vytvořte nebo nasadíte ukázkové kanály a propojené služby nebo tabulky používané kanály.
6. Stav nasazení se zobrazí na dlaždici ukázka, na kterou jste klikli v okně **ukázkové kanály** .

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Po zobrazení zprávy o **úspěšném nasazení** na dlaždici pro ukázku zavřete okno **ukázkové kanály** .  
8. V okně **Data Factory** vidíte, že propojené služby, datové sady a kanály se přidávají do vaší datové továrny.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Ukázky v aplikaci Visual Studio
### <a name="prerequisites"></a>Požadavky
Na počítači musíte mít nainstalované tyto položky:

* Visual Studio 2013 nebo Visual Studio 2015.
* Stáhněte si sadu Azure SDK pro Visual Studio 2013 nebo Visual Studio 2015. Přejděte na [stránku položek ke stažení pro Azure](https://azure.microsoft.com/downloads/) a klikněte na **VS 2013** nebo **VS 2015** v části **.NET**.
* Stáhněte si nejnovější modul plug-in Azure Data Factory pro Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) nebo [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Pokud používáte Visual Studio 2013, můžete modul plug-in také aktualizovat pomocí následujících kroků: v nabídce klikněte na **nástroje**  ->  **rozšíření a aktualizace**  ->  **online**  ->  **Galerie Visual Studio**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update**.

### <a name="use-data-factory-templates"></a>Použití šablon Data Factory
1. V nabídce klikněte na **soubor** , přejděte na **Nový**a klikněte na **projekt**.
2. V dialogovém okně **Nový projekt** proveďte následující kroky:

   1. V části **šablony**vyberte možnost **DataFactory** .
   2. V pravém podokně vyberte **šablony Data Factory** .
   3. Zadejte **název** projektu.
   4. Vyberte **umístění** projektu.
   5. Klikněte na **OK**.

      ![Dialogové okno Nový projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. V dialogovém okně **Data Factory šablony** vyberte vzorovou šablonu v části **šablony použití** a klikněte na **Další**. Následující kroky vás provedou použitím šablony **profilace zákazníka** . Postup je podobný jako u ostatních ukázek.

    ![Dialogové okno Data Factory šablony](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. V dialogovém okně **konfigurace Data Factory** klikněte na stránce **základy Data Factory** na **Další** .
5. Na stránce **konfigurace objektu pro vytváření dat** proveďte následující kroky:
   1. Vyberte **vytvořit novou Data Factory**. Můžete také vybrat **použít existující datovou továrnu**.
   2. Zadejte **název** objektu pro vytváření dat.
   3. Vyberte **předplatné Azure** , ve kterém chcete datovou továrnu vytvořit.
   4. Vyberte **skupinu prostředků** pro datovou továrnu.
   5. Pro **oblast**vyberte **západní USA**, **východní USA**nebo **Severní Evropa** .
   6. Klikněte na **Next** (Další).
6. Na stránce **Konfigurovat úložiště dat** Zadejte existující **databázi v Azure SQL Database** a **účet úložiště Azure** (nebo) vytvořte databázi nebo úložiště a klikněte na další.
7. Na stránce **Konfigurace COMPUTE** vyberte výchozí a klikněte na **Další**.
8. Na stránce **Souhrn** Zkontrolujte všechna nastavení a klikněte na **Další**.
9. Na stránce **stav nasazení** počkejte na dokončení nasazení a klikněte na tlačítko **Dokončit**.
10. V Průzkumníku řešení klikněte pravým tlačítkem na požadovaný projekt a poté klikněte na **Publikovat**.
11. Pokud se zobrazí dialogové okno **Přihlásit se pomocí účtu Microsoft**, zadejte přihlašovací údaje k účtu s předplatným Azure a klikněte na **Přihlásit**.
12. Mělo by se zobrazit následující dialogové okno:

    ![Dialogové okno publikování](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stránce **Konfigurace datové továrny** proveďte následující kroky:

    1. Potvrďte, že **použijte existující možnost Datové továrny** .
    2. Vyberte **datovou továrnu** , kterou jste při použití šablony vybrali.
    3. Kliknutím na **Další** přejděte na stránku **Publish Items** (Publikovat položky). (Pokud je tlačítko **Další** neaktivní, opusťte pole Název stisknutím klávesy **TAB**.)
14. Na stránce **Publish Items** (Publikovat položky) zkontrolujte, jestli jsou vybrané všechny entity služby Data Factory, a kliknutím na **Další** přejděte na stránku **Souhrn**.     
15. Zkontrolujte souhrn a klikněte na **Další**. Spustí se proces nasazení a zobrazí se **Stav nasazení**.
16. Na stránce **Stav nasazení** byste měli vidět stav procesu nasazení. Až se nasazení dokončí, klikněte na Dokončit.

Podrobné informace o použití sady Visual Studio k vytváření Data Factory entit a jejich publikování do Azure najdete v tématu [Vytvoření první datové továrny (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) .          
