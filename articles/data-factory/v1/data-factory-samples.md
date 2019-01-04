---
title: Azure Data Factory – ukázky
description: Poskytuje podrobnosti o ukázky, které se dodávají se službou Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 03127dc777588f669ef07af52c8f73d986bfe0ea
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020808"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory – ukázky
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [ukázky Powershellu ve službě Data Factory](../samples-powershell.md) a [kódu ukázek v galerii vzorových kódů Azure](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Ukázky na GitHubu
[Úložiště GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) obsahuje několik ukázek, které vám pomůžou rychle Seznamte se se službou Azure Data Factory (nebo) změňte skripty a použít ve vlastní aplikaci. Složka Samples\JSON obsahuje fragmenty kódu JSON pro běžné scénáře.

| Ukázka | Popis |
|:--- |:--- |
| [Návod ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Tato ukázka poskytuje návod začátku do konce pro zpracování souborů protokolů pomocí Azure Data Factory, chcete-li k přehledům dat ze souborů protokolů v. <br/><br/>V tomto názorném postupu kanálu služby Data Factory shromažďuje ukázky protokolů, procesy a vylepšuje data z protokolů s referenčními daty a transformuje data pro vyhodnocení účinnosti marketingovou kampaň, která byla nedávno spuštěna. |
| [Ukázky JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Tato ukázka obsahuje příklady JSON pro běžné scénáře. |
| [Ukázka pro stahování dat protokolu HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Tato ukázka představuje stahování dat z koncového bodu HTTP do Azure Blob Storage pomocí vlastní aktivitu .NET. |
| [Mezi ukázkové Net aktivity AppDomain tečka](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Tato ukázka umožňuje vytvářet vlastní aktivitu .NET, který není omezen na verze sestavení používají Spouštěč ADF (třeba ve verzi 4.3.0 WindowsAzure.Storage Newtonsoft.Json v6.0.x, atd.). |
| [Spustit skript jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Tato ukázka obsahuje vlastní aktivity služby Data Factory, který můžete použít k vyvolání RScript.exe. Tento příklad funguje pouze s vlastní cluster HDInsight (ne na vyžádání), který už má nainstalovaný jazyk R na něj. |
| [Vyvolání Sparkových úloh na clusteru HDInsight Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Tento příklad ukazuje, jak pomocí aktivity MapReduce k vyvolání programů Spark. Program sparku právě kopíruje data z jednoho kontejneru objektů Blob v Azure do jiného. |
| [Analýza twitteru pomocí vyhodnocování aktivita služby Azure Machine Learning služby Batch](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Tento příklad ukazuje způsob použití u AzureMLBatchScoringActivity k vyvolání Azure Machine Learning model, který provádí analýza sentimentu na twitteru, vyhodnocování, předpovědi atd. |
| [Analýza twitteru s využitím vlastní aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Tento příklad ukazuje, jak použít vlastní aktivitu .NET k vyvolání Azure Machine Learning model, který provádí analýza sentimentu na twitteru, vyhodnocování, předpovědi atd. |
| [Parametrizované kanály pro Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |Ukázka obsahuje začátku do konce C# kód pro nasazení N kanály pro vyhodnocování a přetrénování každý s parametrem jiné oblasti, kde seznam oblastí pochází z parameters.txt soubor, který je součástí této ukázky. |
| [Aktualizace dat – referenční dokumentace pro úlohy Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Tento příklad ukazuje, jak pomocí Azure Data Factory a Azure Stream Analytics dohromady spouštět dotazy s referenčními daty a nastavit aktualizaci pro referenční data podle plánu. |
| [Hybridní kanál s Hortonworks místního clusteru Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Ukázka používá v místním clusteru Hadoop jako cílové výpočetní prostředí pro spouštění úloh ve službě Data Factory, stejně jako byste přidali další cílových výpočetních prostředí jako HDInsight na základě cluster Hadoop v cloudu. |
| [Nástroj pro převod formátu JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Tento nástroj umožňuje převést JSONs z verze před 2015-07-01-preview nejnovější nebo 2015-07-01-preview (výchozí). |
| [U-SQL ukázkového vstupního souboru](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Tento soubor je ukázkový soubor používá aktivita U-SQL. |
| [Odstranit soubor objektu blob](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Tato ukázka prezentuje C# souboru, který může sloužit jako součást vlastní aktivitu .net ADF odstranit soubory ze zdrojového umístění objektu Blob Azure, jakmile se soubory zkopírovaly.|

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Následující šablony Azure Resource Manageru můžete najít pro službu Data Factory na Githubu.

| Šablona | Popis |
| --- | --- |
| [Zkopírujte z Azure Blob Storage do služby Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Nasazení Tato šablona vytvoří objekt pro vytváření dat Azure s kanálem, který kopíruje data ze zadaného Azure blob storage do služby Azure SQL database |
| [Kopírování ze Salesforce do úložiště objektů Blob v Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Nasazení Tato šablona vytvoří objekt pro vytváření dat Azure s kanálem, který kopíruje data ze zadaného účtu Salesforce do úložiště objektů blob v Azure. |
| [Transformovat data spuštěním skriptu Hivu v clusteru Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Nasazení Tato šablona vytvoří objekt pro vytváření dat Azure s kanálem, který transformuje data pomocí skriptu Hive ukázkové v clusteru Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Ukázky na webu Azure portal
Můžete použít **ukázkové kanály** dlaždici na domovské stránce vaší datové továrně nasazení ukázkové kanály a jejich přidružené entity (datové sady a propojené služby) v data Factory.

1. Vytvořte objekt pro vytváření dat nebo otevřete existující datové továrny. Zobrazit [kopírování dat z Blob Storage do SQL Database s použitím služby Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pokyny k vytvoření datové továrny.
2. V **služby DATA FACTORY** okno služby data factory, klikněte **ukázkové kanály** dlaždici.

    ![Ukázkové kanály dlaždice](./media/data-factory-samples/SamplePipelinesTile.png)
3. V **ukázkové kanály** okna, klikněte na tlačítko **ukázka** , kterou chcete nasadit.

    ![Okno ukázkové kanály](./media/data-factory-samples/SampleTile.png)
4. Určení nastavení konfigurace pro vzorku. Například váš název a účet klíč účtu úložiště Azure, název serveru Azure SQL, databáze, ID uživatele a heslo atd.

    ![Okno ukázkové](./media/data-factory-samples/SampleBlade.png)
5. Jakmile budete hotovi s určení nastavení konfigurace, klikněte na tlačítko **vytvořit** vytvořit a nasadit ukázkové kanály a propojených služeb/tabulek používají kanály.
6. Zobrazí stav nasazení na dlaždici ukázka předtím jste klikli **ukázkové kanály** okno.

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Když se zobrazí **nasazení bylo úspěšné** zprávy na dlaždici pro ukázku, zavřete **ukázkové kanály** okno.  
8. Na **služby DATA FACTORY** okně uvidíte, že propojené služby, datové sady a kanály jsou přidány do služby data factory.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Ukázky v sadě Visual Studio
### <a name="prerequisites"></a>Požadavky
Na počítači musíte mít nainstalované tyto položky:

* Visual Studio 2013 nebo Visual Studio 2015.
* Stáhněte si sadu Azure SDK pro Visual Studio 2013 nebo Visual Studio 2015. Přejděte na [stránku položek ke stažení pro Azure](https://azure.microsoft.com/downloads/) a klikněte na **VS 2013** nebo **VS 2015** v části **.NET**.
* Stáhněte si nejnovější modul plug-in Azure Data Factory pro Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) nebo [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Pokud používáte Visual Studio 2013, můžete modul plug-in také aktualizovat provedením následujících kroků: V nabídce klikněte na tlačítko **nástroje** -> **rozšíření a aktualizace** -> **Online** -> **GaleriesadyVisualStudio**  ->  **Microsoft Azure Data Factory Tools for Visual Studio** -> **aktualizace**.

### <a name="use-data-factory-templates"></a>Použití šablon objekt pro vytváření dat
1. Klikněte na tlačítko **souboru** přejděte v nabídce **nový**a klikněte na tlačítko **projektu**.
2. V dialogovém okně **Nový projekt** proveďte následující kroky:

   1. Vyberte **DataFactory** pod **šablony**.
   2. Vyberte **datové továrny šablony** v pravém podokně.
   3. Zadejte **název** projektu.
   4. Vyberte **umístění** pro projekt.
   5. Klikněte na **OK**.

      ![Dialogové okno Nový projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. V **datové továrny šablony** dialogového okna, vyberte šablonu ukázky z **případ použití šablony** části a klikněte na tlačítko **Další**. Následující postup vás provede použitím **profilace zákazníka** šablony. Kroky jsou podobné pro další ukázky.

    ![Dialogové okno objekt pro vytváření šablon dat](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. V **konfigurace objekt pro vytváření dat** dialogového okna, klikněte na tlačítko **Další** na **Data Factory Základy** stránky.
5. Na **konfigurace datové továrny** stránce, proveďte následující kroky:
   1. Vyberte **vytvořit nová datová továrna**. Můžete také vybrat **používat existující datové továrny**.
   2. Zadejte **název** služby data factory.
   3. Vyberte **předplatného Azure** do kterých chcete datovou továrnu vytvořit.
   4. Vyberte **skupiny prostředků** služby data factory.
   5. Vyberte **USA – západ**, **USA – východ**, nebo **Severní Evropa** pro **oblasti**.
   6. Klikněte na **Další**.
6. V **konfigurace úložiště dat** určete existující, **Azure SQL database** a **účtu služby Azure storage** (nebo) vytvořte úložiště/databáze a klikněte na tlačítko Další.
7. V **konfigurace výpočtů** stránky, vyberte výchozí nastavení a klikněte na tlačítko **Další**.
8. V **Souhrn** stránce zkontrolujte všechna nastavení a klikněte na tlačítko **Další**.
9. V **stav nasazení** stránce, počkejte na dokončení nasazení a klikněte na tlačítko **Dokončit**.
10. V Průzkumníku řešení klikněte pravým tlačítkem na požadovaný projekt a poté klikněte na **Publikovat**.
11. Pokud se zobrazí dialogové okno **Přihlásit se pomocí účtu Microsoft**, zadejte přihlašovací údaje k účtu s předplatným Azure a klikněte na **Přihlásit**.
12. Mělo by se zobrazit následující dialogové okno:

    ![Dialogové okno publikování](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na stránce **Konfigurace datové továrny** proveďte následující kroky:

    1. Ujistěte se, že **používat existující datové továrny** možnost.
    2. Vyberte **služby data factory** jste měli vybrat při použití šablony.
    3. Kliknutím na **Další** přejděte na stránku **Publish Items** (Publikovat položky). (Pokud je tlačítko **Další** neaktivní, opusťte pole Název stisknutím klávesy **TAB**.)
14. Na stránce **Publish Items** (Publikovat položky) zkontrolujte, jestli jsou vybrané všechny entity služby Data Factory, a kliknutím na **Další** přejděte na stránku **Souhrn**.     
15. Zkontrolujte souhrn a klikněte na **Další**. Spustí se proces nasazení a zobrazí se **Stav nasazení**.
16. Na stránce **Stav nasazení** byste měli vidět stav procesu nasazení. Až se nasazení dokončí, klikněte na Dokončit.

Zobrazit [sestavit svou první datovou továrnu (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) podrobné informace o vytváření entit služby Data Factory pomocí sady Visual Studio a jejich publikování do Azure.          
