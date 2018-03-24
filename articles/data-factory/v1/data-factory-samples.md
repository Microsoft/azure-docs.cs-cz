---
title: Pro vytváření dat Azure – ukázky
description: Poskytuje podrobnosti o vzorků, které jsou dodávány se službou Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 1e85d5f48ce998ebaf4ccaa231bb75449e2bab16
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory---samples"></a>Pro vytváření dat Azure – ukázky
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [ukázky prostředí PowerShell v datové továrně verze 2](../samples-powershell.md) a [ukázky v galerii ukázky kódu Azure kódu](https://azure.microsoft.com/en-us/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Ukázky z webu GitHub
[Úložiště GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) obsahuje několik vzorků, které vám pomohou rychle doba přípravy službou Azure Data Factory (nebo) upravit skripty a použít ho v vlastní aplikace. Složka Samples\JSON obsahuje fragmenty kódu JSON pro běžné scénáře.

| Ukázka | Popis |
|:--- |:--- |
| [Návod ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Tato ukázka poskytuje návod začátku do konce pro zpracování souborů protokolů pomocí Azure Data Factory, chcete-li data ze souborů protokolů v ke statistice. <br/><br/>V tomto návodu kanál Data Factory shromažďuje protokoly ukázkové, procesy a vylepšuje data z protokolů u referenčních dat a transformuje údaje pro hodnocení účinnosti marketingovou kampaň, která byla nedávno spuštěná. |
| [JSON – ukázky](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Tato ukázka obsahuje příklady JSON pro běžné scénáře. |
| [Stažení programu vzorek dat protokolu HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Tento ukázkový server Showcase stahování dat z koncový bod HTTP do Azure Blob Storage pomocí vlastní aktivity .NET. |
| [Mezi ukázka Net aktivity AppDomain tečku](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Tato ukázka vám umožňuje vytvářet vlastní aktivity rozhraní .NET, které nejsou rozděleny do verze sestavení používá Spouštěče ADF (například WindowsAzure.Storage v4.3.0 Newtonsoft.Json v6.0.x, atd.). |
| [Spustit skript jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Tato ukázka obsahuje aktivitu vlastní objekt pro vytváření dat, která slouží k vyvolání RScript.exe. Tato ukázka funguje pouze s vlastní cluster HDInsight (ne na vyžádání), který již má nainstalované R na. |
| [Vyvolání úlohy Spark v HDInsight Hadoop clusteru](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Tento příklad ukazuje použití činnost MapReduce k vyvolání Spark program. Spark program právě zkopíruje data z jednoho kontejneru objektu Blob Azure do jiného. |
| [Analýza Twitter pomocí Azure Machine Learning dávkové vyhodnocování aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Tento příklad ukazuje, jak používat u AzureMLBatchScoringActivity k vyvolání model Azure Machine Learning, která provede analýzu postojích twitter, vyhodnocování předpovědi atd. |
| [Analýza Twitter pomocí vlastní aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Tento příklad ukazuje, jak používat vlastní aktivity .NET k vyvolání model Azure Machine Learning, která provede analýzu postojích twitter, vyhodnocování předpovědi atd. |
| [Parametrizované kanály pro Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |Ukázka poskytuje začátku do konce kód C# k nasazení N kanály pro vyhodnocování a každý parametr jiné oblasti, kde seznam oblastí pochází z parameters.txt souboru, který je součástí této ukázce přeučování. |
| [Referenční dokumentace aktualizace dat pro úlohy Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Tento příklad ukazuje, jak používat Azure Data Factory a Azure Stream Analytics společně spouštět dotazy u referenčních dat a nastavení aktualizace pro referenční data podle plánu. |
| [Hybridní kanál s místními Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Příklad používá místního clusteru Hadoop jako cíl výpočetní ke spuštění úloh v objektu pro vytváření dat, stejně jako HDInsight na základě clusteru Hadoop v cloudu, měli byste přidat další výpočetní cíle. |
| [Převodní nástroj JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Tento nástroj umožňuje převést JSONs z verze před 2015-07-01-preview nejnovější nebo 2015-07-01-preview (výchozí). |
| [Vstupní soubor ukázka U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Tento soubor je ukázkový soubor používaná aktivitou U-SQL. |
| [Odstranění objektů blob souboru](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Tato ukázka umožňující prezentovat soubor C#, který můžete použít jako součást vlastní aktivity .net ADF k odstranění souborů ze zdrojového umístění objektu Blob Azure až po zkopírování souborů.|

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Následující šablony Azure Resource Manager můžete získat Data Factory na webu GitHub.

| Šablona | Popis |
| --- | --- |
| [Kopírování z Azure Blob Storage do Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Nasazení Tato šablona vytvoří objekt pro vytváření dat Azure s kanál, který kopíruje data ze zadaného Azure blob storage do Azure SQL database |
| [Kopírování ze služby Salesforce do Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Nasazení Tato šablona vytvoří objekt pro vytváření dat Azure s kanál, který kopíruje data ze zadaného účtu Salesforce do Azure blob storage. |
| [Transformace dat pomocí skriptu Hive v clusteru Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Nasazení Tato šablona vytvoří objekt pro vytváření dat Azure s kanál, který transformuje data pomocí skriptu Hive ukázka v clusteru Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Ukázky na portálu Azure
Můžete použít **ukázkové kanály** dlaždice na domovské stránce k nasazení ukázkové kanály a jejich přidružených entit (datové sady a propojených služeb) pro vytváření dat v datové továrně.

1. Objekt pro vytváření dat vytvořit nebo otevřít existující datovou továrnu. V tématu [kopírování dat z úložiště objektů Blob do SQL Database pomocí služby Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kroky pro vytvoření služby data factory.
2. V **DATA FACTORY** služby data Factory klikněte na **ukázkové kanály** dlaždici.

    ![Ukázka kanály dlaždice](./media/data-factory-samples/SamplePipelinesTile.png)
3. V **ukázkové kanály** okně klikněte na tlačítko **ukázka** , kterou chcete nasadit.

    ![Okno ukázku kanálů](./media/data-factory-samples/SampleTile.png)
4. Zadejte nastavení konfigurace pro vzorovou. Například váš klíč pro účet a název účtu úložiště Azure, název serveru Azure SQL, databáze, ID uživatele a heslo atd.

    ![Ukázka okna](./media/data-factory-samples/SampleBlade.png)
5. Jakmile jste hotovi s určujícím nastavení konfigurace, klikněte na tlačítko **vytvořit** k vytvoření nebo nasazení ukázkové kanálů a propojených služeb/tabulek používané kanály.
6. Stav nasazení se zobrazí na dlaždici ukázka jste klikli na dříve **ukázkové kanály** okno.

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Až se zobrazí **nasazení bylo úspěšné** zpráva na dlaždici pro ukázku, zavřete **ukázkové kanály** okno.  
8. Na **DATA FACTORY** okně se zobrazí, propojených služeb, datových sad a kanálů jsou přidány do vaší služby data factory.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>V sadě Visual Studio – ukázky
### <a name="prerequisites"></a>Požadavky
Na počítači musíte mít nainstalované tyto položky:

* Visual Studio 2013 nebo Visual Studio 2015.
* Stáhněte si sadu Azure SDK pro Visual Studio 2013 nebo Visual Studio 2015. Přejděte na [stránku položek ke stažení pro Azure](https://azure.microsoft.com/downloads/) a klikněte na **VS 2013** nebo **VS 2015** v části **.NET**.
* Stáhněte si nejnovější modul plug-in Azure Data Factory pro Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) nebo [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Pokud používáte sadu Visual Studio 2013, můžete modul plug-in také aktualizovat provedením následujících kroků: V nabídce klikněte na **Nástroje**  ->  **Rozšíření a aktualizace**  ->  **Online**  ->  **Galerie sady Visual Studio**  ->  **Nástroje služby Microsoft Azure Data Factory pro Visual Studio**  ->  **Aktualizovat**.

### <a name="use-data-factory-templates"></a>Použití šablon objekt pro vytváření dat
1. Klikněte na tlačítko **soubor** přejděte v nabídce, **nový**a klikněte na tlačítko **projektu**.
2. V **nový projekt** dialogové okno pole, proveďte následující kroky:

   1. Vyberte **DataFactory** pod **šablony**.
   2. Vyberte **šablony objekt pro vytváření dat** v pravém podokně.
   3. Zadejte **název** pro projekt.
   4. Vyberte **umístění** pro projekt.
   5. Klikněte na **OK**.

      ![Dialogové okno Nový projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. V **šablony objekt pro vytváření dat** dialogové okno, vyberte šablonu ukázka z **případ použití šablony** a klikněte na **Další**. Následující kroky vás provedou pomocí **odběratele profilace** šablony. Postup je podobný jako u ostatních vzorků.

    ![Dialogové okno šablony objekt pro vytváření dat](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. V **konfigurace objektu pro vytváření dat** dialogové okno, klikněte na tlačítko **Další** na **základy objektu pro vytváření dat** stránky.
5. Na **objekt pro vytváření dat konfigurace** proveďte následující kroky:
   1. Vyberte **vytvořit nový objekt pro vytváření dat**. Můžete také vybrat **použít existující datovou továrnu**.
   2. Zadejte **název** služby data Factory.
   3. Vyberte **předplatného Azure** do kterých chcete objekt pro vytváření dat vytvořit.
   4. Vyberte **skupiny prostředků** služby data Factory.
   5. Vyberte **západní USA**, **východní USA**, nebo **Severní Evropa** pro **oblast**.
   6. Klikněte na **Další**.
6. V **konfigurace úložiště dat** zadejte existující **Azure SQL database** a **účtu úložiště Azure** (nebo) vytvořte databáze nebo úložiště a klikněte na tlačítko Další.
7. V **konfigurace výpočetních** vyberte výchozí hodnoty a klikněte na tlačítko **Další**.
8. V **Souhrn** zkontrolujte všechna nastavení a klikněte na tlačítko **Další**.
9. V **stav nasazení** stránky, počkejte na dokončení nasazení a klikněte na tlačítko **Dokončit**.
10. V Průzkumníku řešení klikněte pravým tlačítkem na požadovaný projekt a poté klikněte na **Publikovat**.
11. Pokud se zobrazí dialogové okno **Přihlásit se pomocí účtu Microsoft**, zadejte přihlašovací údaje k účtu s předplatným Azure a klikněte na **Přihlásit**.
12. Mělo by se zobrazit následující dialogové okno:

    ![Dialogové okno publikování](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stránce **Konfigurace datové továrny** proveďte následující kroky:

    1. Potvrďte, že **použít existující datovou továrnu** možnost.
    2. Vyberte **objekt pro vytváření dat** měl vyberte při použití šablony.
    3. Kliknutím na **Další** přejděte na stránku **Publish Items** (Publikovat položky). (Pokud je tlačítko **Další** neaktivní, opusťte pole Název stisknutím klávesy **TAB**.)
14. Na stránce **Publish Items** (Publikovat položky) zkontrolujte, jestli jsou vybrané všechny entity služby Data Factory, a kliknutím na **Další** přejděte na stránku **Souhrn**.     
15. Zkontrolujte souhrn a klikněte na **Další**. Spustí se proces nasazení a zobrazí se **Stav nasazení**.
16. Na stránce **Stav nasazení** byste měli vidět stav procesu nasazení. Až se nasazení dokončí, klikněte na Dokončit.

V tématu [vytvoření vaší první objekt pro vytváření dat (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) podrobné informace o vytváření entit služby Data Factory pomocí sady Visual Studio a publikováním do Azure.          
