---
title: Zpracování rozsáhlých datových sad pomocí Data Factory a dávky
description: Popisuje, jak zpracovávat velké objemy dat v Azure Data Factory kanálu pomocí možnosti paralelního zpracování Azure Batch.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 2568fc2ea133a0cdd49e0a2577ce1e580056a74c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783128"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Zpracování rozsáhlých datových sad pomocí Data Factory a dávky
> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [vlastní aktivity v Data Factory](../transform-data-using-dotnet-custom-activity.md).

Tento článek popisuje architekturu ukázkového řešení, které přesouvá a zpracovává velké datové sady automaticky a naplánovaným způsobem. Poskytuje také ucelený návod k implementaci řešení pomocí Data Factory a Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek je delší než typický článek, protože obsahuje návod k celému ukázkovému řešení. Pokud začínáte s dávkou a Data Factory, můžete se dozvědět víc o těchto službách a o tom, jak spolu pracují. Pokud víte něco o službách a navrhujete nebo navrhujete řešení, můžete se zaměřit na část architektury článku. Pokud vyvíjíte prototyp nebo řešení, možná budete chtít vyzkoušet podrobné pokyny v tomto návodu. Vaše připomínky k tomuto obsahu a způsobu, jakým ho používáte, pozvěte.

Nejdřív se podíváme na to, jak Data Factory a služba Batch vám pomůžou zpracovávat velké datové sady v cloudu.     


## <a name="why-azure-batch"></a>Proč Azure Batch?
 Pomocí dávky můžete efektivně spouštět rozsáhlé paralelní a vysoce výkonné výpočetní aplikace (HPC) v cloudu. Jedná se o službu platformy, která plánuje spouštění práce náročné na výpočetní výkon ve spravované kolekci virtuálních počítačů (VM). Může automaticky škálovat výpočetní prostředky tak, aby splňovaly potřeby vašich úloh.

Pomocí služby Batch definujete výpočetní prostředky, které vaše aplikace spustí paralelně a škálovaně. Můžete spouštět úlohy na vyžádání nebo naplánované úlohy. Není nutné ručně vytvářet, konfigurovat a spravovat cluster HPC, jednotlivé virtuální počítače, virtuální sítě ani složitou infrastrukturu úloh a úloh plánování úloh.

 Pokud nejste obeznámeni se službou Batch, následující články vám pomůžou pochopit architekturu a implementaci řešení popsaného v tomto článku:   

* [Základy dávky](../../azure-sql/database/sql-database-paas-overview.md)
* [Přehled funkcí Batch](../../batch/batch-service-workflow-features.md)

Další informace o službě Batch najdete v [dokumentaci](../../batch/index.yml)ke službě Batch.

## <a name="why-azure-data-factory"></a>Proč Azure Data Factory?
Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat. Pomocí Data Factory můžete vytvářet spravované datové kanály, které přesouvají data z místních a cloudových úložišť do centralizovaného úložiště dat. Příkladem je úložiště objektů BLOB v Azure. Data Factory můžete použít ke zpracování nebo transformaci dat pomocí služeb, jako je Azure HDInsight a Azure Machine Learning. Datové kanály můžete také naplánovat tak, aby běžely naplánovaným způsobem (například každou hodinu, denně a týdně). Pokud chcete identifikovat problémy a provést akci, můžete kanály na první pohled monitorovat a spravovat.

  Pokud nejste obeznámeni s Data Factory, následující články vám pomůžou pochopit architekturu a implementaci řešení popsaného v tomto článku:  

* [Úvodní informace k Data Factory](data-factory-introduction.md)
* [Sestavení prvního datového kanálu](data-factory-build-your-first-pipeline.md)   

Pokud chcete získat další informace o Data Factory, přečtěte si [dokumentaci Data Factory](/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory a Batch společně
Data Factory obsahuje integrované aktivity. Například aktivita kopírování se používá pro kopírování a přesun dat ze zdrojového úložiště dat do cílového úložiště dat. Aktivita podregistru se používá ke zpracování dat pomocí clusterů Hadoop (HDInsight) v Azure. Seznam podporovaných transformačních aktivit najdete v tématu [aktivity transformace dat](data-factory-data-transformation-activities.md).

Můžete také vytvořit vlastní aktivity rozhraní .NET pro přesun nebo zpracování dat pomocí vlastní logiky. Tyto aktivity můžete spustit na clusteru HDInsight nebo ve fondu služby Batch virtuálních počítačů. Když použijete Batch, můžete nakonfigurovat fond na automatické škálování (přidání nebo odebrání virtuálních počítačů založených na úlohách) na základě vzorce, který zadáte.     

## <a name="architecture-of-a-sample-solution"></a>Architektura ukázkového řešení
  Architektura popsaná v tomto článku je určena pro jednoduché řešení. Také se týká složitých scénářů, jako jsou například modelování rizik podle finančních služeb, zpracování a vykreslování obrázků a analýza genomu.

Diagram znázorňuje, jak Data Factory orchestruje přesun a zpracování dat. Také ukazuje, jak Batch zpracovává data paralelně. Tento diagram si můžete stáhnout a vytisknout pro snadné odkazy (velikost 11 × 17 palců nebo a3). Chcete-li získat přístup k diagramu, abyste ho mohli vytisknout, přečtěte si téma [HPC a orchestrace dat pomocí Batch a Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram zpracování velkých objemů dat](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Následující seznam uvádí základní kroky procesu. Řešení obsahuje kód a vysvětlení pro sestavení komplexního řešení.

* **Nakonfigurujte dávku s fondem výpočetních uzlů (virtuálních počítačů).** Můžete zadat počet uzlů a velikost jednotlivých uzlů.

* **Vytvořte instanci Data Factory** , která je nakonfigurovaná s entitami, které reprezentují úložiště objektů blob, služby Batch COMPUTE, vstupní/výstupní data a pracovní postup nebo kanál s aktivitami, které přesouvají a transformují data.

* **Vytvořte vlastní aktivitu .NET v kanálu Data Factory.** Aktivita je váš uživatelský kód, který běží ve fondu služby Batch.

* **Ukládejte velké objemy vstupních dat jako objekty BLOB v Azure Storage.** Data jsou rozdělena do logických řezů (obvykle podle času).

* **Data Factory kopíruje data zpracovaná paralelně** do sekundárního umístění.

* **Data Factory spustí vlastní aktivitu pomocí fondu přiděleného službou Batch.** Data Factory může spouštět aktivity souběžně. Každá aktivita zpracovává řez dat. Výsledky jsou uloženy v úložišti.

* **Data Factory přesune konečné výsledky do třetího umístění,** a to buď pro distribuci prostřednictvím aplikace, nebo pro další zpracování jinými nástroji.

## <a name="implementation-of-the-sample-solution"></a>Implementace ukázkového řešení
Ukázkové řešení je záměrně jednoduché. Je navržený tak, aby vám ukázal, jak používat Data Factory a Batch společně ke zpracování datových sad. Řešení spočítá počet výskytů hledaného výrazu "Microsoft" ve vstupních souborech, které jsou uspořádány v časové řadě. Pak tento počet vypíše do výstupních souborů.

**Čas:** Pokud jste obeznámeni se základy Azure, Data Factory a Batch a dokončili jste následující požadavky, bude dokončení tohoto řešení trvat jednu až dvě hodiny.

### <a name="prerequisites"></a>Předpoklady
#### <a name="azure-subscription"></a>Předplatné Azure
Pokud nemáte předplatné Azure, můžete rychle vytvořit bezplatný zkušební účet. Další informace najdete v článku [bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Účet služby Azure Storage
K ukládání dat v tomto kurzu použijete účet úložiště. Pokud nemáte účet úložiště, přečtěte si téma [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Ukázkové řešení využívá úložiště objektů BLOB.

#### <a name="azure-batch-account"></a>Účet Azure Batch
Vytvořte účet Batch pomocí [Azure Portal](https://portal.azure.com/). Další informace najdete v tématu [Vytvoření a Správa účtu Batch](../../batch/batch-account-create-portal.md). Poznamenejte si název účtu Batch a klíč účtu. K vytvoření účtu Batch taky můžete použít rutinu [New-AzBatchAccount](/powershell/module/az.batch/new-azbatchaccount) . Pokyny k použití této rutiny najdete v tématu [Začínáme s rutinami PowerShellu pro Batch](../../batch/batch-powershell-cmdlets-get-started.md).

Ukázkové řešení používá dávku (nepřímo prostřednictvím kanálu Data Factory) k paralelnímu zpracování dat ve fondu výpočetních uzlů (spravovaná kolekce virtuálních počítačů).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch fond virtuálních počítačů
Vytvořte fond Batch s aspoň dvěma výpočetními uzly.

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **Procházet** a vyberte **účty Batch**.

1. Vyberte účet Batch a otevřete okno **účtu Batch** .

1. Vyberte dlaždici **fondy** .

1. V okně **fondy** vyberte tlačítko **Přidat** na panelu nástrojů a přidejte fond.

   a. Zadejte ID fondu (**ID fondu**). Poznamenejte si ID fondu. Budete je potřebovat při vytváření řešení Data Factory.

   b. Pro nastavení **řady operačních systémů** zadejte **Windows Server 2012 R2** .

   c. Vyberte **cenovou úroveň uzlu**.

   d. Jako hodnotu pro **vyhrazené nastavení cíle** zadejte **2** .

   e. Jako hodnotu pro nastavení **maximální počet úkolů na uzel** zadejte **2** .

   f. Vyberte **OK** a vytvořte fond.

#### <a name="azure-storage-explorer"></a>Průzkumník služby Azure Storage
Pro kontrolu a změnu dat v projektech úložiště slouží [Průzkumník služby Azure Storage 6](https://azurestorageexplorer.codeplex.com/) nebo [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (ze softwaru ClumsyLeaf). Můžete také zkontrolovat a změnit data v protokolech aplikací hostovaných v cloudu.

1. Vytvořte kontejner s názvem **myContainer** s privátním přístupem (bez anonymního přístupu).

1. Pokud používáte CloudXplorer, vytvořte složky a podsložky s následující strukturou:

   ![Struktura složek a podsložek](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` a `outputfolder` jsou složky nejvyšší úrovně v `mycontainer` . `inputfolder`Složka má podsložky s časovými razítky (rrrr-mm-dd-HH).

   Použijete-li Průzkumník služby Storage, nahrajete v dalším kroku soubory s následujícími názvy: `inputfolder/2015-11-16-00/file.txt` , `inputfolder/2015-11-16-01/file.txt` , a tak dále. Tento krok automaticky vytvoří složky.

1. Vytvořte v počítači textový soubor **file.txt** s obsahem, který má klíčové slovo **Microsoft**. Příkladem je "test vlastní aktivity Microsoft Test Custom Activity Microsoft."

1. Nahrajte soubor do následujících vstupních složek v úložišti objektů BLOB:

   ![Vstupní složky](./media/data-factory-data-processing-using-batch/image4.png)

   Pokud používáte Průzkumník služby Storage, nahrajte soubor **file.txt** do **myContainer**. Na panelu nástrojů vyberte možnost **Kopírovat** a vytvořte kopii objektu BLOB. V dialogovém okně **Kopírovat objekt BLOB** změňte **cílový název objektu BLOB** na `inputfolder/2015-11-16-00/file.txt` . Opakováním tohoto kroku vytvořte `inputfolder/2015-11-16-01/file.txt` , `inputfolder/2015-11-16-02/file.txt` , `inputfolder/2015-11-16-03/file.txt` , `inputfolder/2015-11-16-04/file.txt` a tak dále. Tato akce automaticky vytvoří složky.

1. Vytvořte další kontejner s názvem `customactivitycontainer` . Nahrajte do tohoto kontejneru soubor zip vlastní aktivity.

#### <a name="visual-studio"></a>Visual Studio
Nainstalujte Visual Studio 2012 nebo novější a vytvořte tak vlastní aktivitu Batch, která se má použít v řešení Data Factory.

### <a name="high-level-steps-to-create-the-solution"></a>Kroky vysoké úrovně pro vytvoření řešení
1. Vytvořte vlastní aktivitu, která obsahuje logiku zpracování dat.

1. Vytvořte datovou továrnu, která používá vlastní aktivitu.

### <a name="create-the-custom-activity"></a>Vytvoření vlastní aktivity
Vlastní aktivita Data Factory je srdcem tohoto ukázkového řešení. Ukázkové řešení používá dávku ke spuštění vlastní aktivity. Informace o vývoji vlastních aktivit a jejich použití v kanálech služby Data Factory najdete v tématu [použití vlastních aktivit v kanálu služby Data Factory](data-factory-use-custom-activities.md).

Chcete-li vytvořit vlastní aktivitu rozhraní .NET, kterou můžete použít v kanálu datové továrny, vytvořte projekt knihovny tříd .NET s třídou, která implementuje rozhraní IDotNetActivity. Toto rozhraní má pouze jednu metodu: Execute. Zde je podpis metody:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Tato metoda má několik klíčových součástí, které je třeba pochopit:

* Metoda přijímá čtyři parametry:

  * **linkedServices**. Tento parametr je Výčtový seznam propojených služeb, které propojí vstupní a výstupní zdroje dat (například BLOB Storage) s datovou továrnou. V této ukázce je k dispozici pouze jedna propojená služba typu Azure Storage použitá pro vstup i výstup.
  * **datové sady**. Tento parametr je Výčtový seznam datových sad. Tento parametr můžete použít k získání umístění a schémat definovaných vstupními a výstupními datovými sadami.
  * **aktivita**. Tento parametr představuje aktuální výpočetní entitu. V tomto případě se jedná o službu Batch.
  * **protokolovací** nástroj. Pomocí protokolovacího nástroje můžete zapsat komentáře k ladění, které jsou pro kanál v protokolu "uživatel".
* Metoda vrací slovník, který lze použít k zřetězení vlastních aktivit společně v budoucnu. Tato funkce ještě není implementovaná, takže jednoduše z metody vrátíte prázdný slovník.

#### <a name="procedure-create-the-custom-activity"></a>Postup: Vytvoření vlastní aktivity
1. Vytvořte projekt knihovny tříd .NET v aplikaci Visual Studio.

   a. Spusťte Visual Studio 2012/2013/2015.

   b. Vyberte **soubor**  >  **Nový**  >  **projekt**.

   c. Rozbalte **šablony** a vyberte **Visual C \#**. V tomto návodu použijete C \# , ale můžete použít libovolný jazyk .NET k vývoji vlastní aktivity.

   d. V seznamu typů projektů napravo vyberte **Knihovna tříd** .

   e. Jako **název** zadejte **MyDotNetActivity** .

   f. Jako **umístění** vyberte **C: \\ ADF** . Vytvořte **ADF** složky, pokud neexistuje.

   například Vyberte **OK** a vytvořte projekt.

1. Vyberte **Nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

1. V konzole správce balíčků spusťte následující příkaz pro import Microsoft. Azure. Management. datafactorys:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importujte balíček NuGet **Azure Storage** do projektu. Tento balíček budete potřebovat, protože v této ukázce použijete rozhraní Blob Storage API:

    ```powershell
    Install-Package Az.Storage
    ```
1. Do zdrojového souboru v projektu přidejte následující direktivy using:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Změňte název oboru názvů na **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Změňte název třídy na **MyDotNetActivity** a odvodit ji z rozhraní **IDotNetActivity** , jak je znázorněno níže:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementujte (přidejte) metodu **Execute** rozhraní **IDotNetActivity** do třídy **MyDotNetActivity** . Zkopírujte následující vzorový kód do metody. Vysvětlení logiky použité v této metodě naleznete v části [Metoda Execute](#execute-method) .

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Do třídy přidejte následující pomocné metody. Tyto metody jsou vyvolány metodou **Execute** . Nejdůležitější, metoda **výpočtu** izoluje kód, který projde každým objektem BLOB.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Metoda GetFolderPath vrací cestu ke složce, na kterou datová sada odkazuje, a metoda getfiletable vrátí název objektu BLOB nebo souboru, na který datová sada odkazuje.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoda výpočtu vypočítá počet instancí klíčového slova "Microsoft" ve vstupních souborech (objekty blob ve složce). Hledaný termín "Microsoft" je pevně kódovaný v kódu.

1. Zkompilujte projekt. V nabídce vyberte **sestavení** a pak vyberte **Sestavit řešení**.

1. Spusťte Průzkumníka Windows a otevřete složku pro **\\ ladění** nebo **\\ vydanou** přihrádku. Volba složky závisí na typu sestavení.

1. Vytvořte soubor zip **MyDotNetActivity.zip** , který obsahuje všechny binární soubory ve složce **\\ \\ ladění bin** . Možná budete chtít zahrnout MyDotNetActivity. soubor **PDB** , který vám umožní získat další podrobnosti, jako je číslo řádku ve zdrojovém kódu, který způsobil problém při výskytu chyby.

   ![Seznam složek bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Nahrajte **MyDotNetActivity.zip** jako objekt blob do kontejneru objektů BLOB `customactivitycontainer` v úložišti objektů blob, který používá propojená služba StorageLinkedService v ADFTutorialDataFactory. Vytvořte kontejner objektů blob, `customactivitycontainer` Pokud ještě neexistuje.

#### <a name="execute-method"></a>Execute – metoda
V této části najdete další podrobnosti o kódu v metodě Execute.

1. Členy pro iteraci přes vstupní kolekci najdete v oboru názvů [Microsoft. windowsazure. Storage. blob](/java/api/com.microsoft.azure.storage.blob) . Chcete-li iterovat v kolekci objektů blob, je nutné použít třídu **BlobContinuationToken** . V podstatě je nutné použít smyčku Loop s tokenem jako mechanismus pro ukončení smyčky. Další informace najdete v tématu [použití služby Blob Storage z rozhraní .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md). Základní smyčka je znázorněna zde:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   Další informace najdete v dokumentaci k metodě [ListBlobsSegmented](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) .

1. Kód pro práci prostřednictvím sady objektů BLOB logicky směřuje ve smyčce do-while. V metodě **Execute** projde smyčka do-while seznam objektů blob do metody s názvem **Vypočítat**. Metoda vrátí řetězcovou proměnnou s názvem **Output** , která je výsledkem iterace všemi objekty BLOB v segmentu.

   Vrátí počet výskytů hledaného výrazu "Microsoft" v objektu BLOB předané metodě **výpočtu** .

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Po dokončení metody **výpočtu** je nutné ji zapsat do nového objektu BLOB. U každé zpracované sady objektů BLOB může být do výsledků zapisován nový objekt BLOB. Chcete-li zapisovat do nového objektu blob, nejprve najděte výstupní datovou sadu.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Kód také volá pomocnou metodu **GetFolderPath** , která načte cestu ke složce (název kontejneru úložiště).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoda GetFolderPath přetypování objektu DataSet na AzureBlobDataSet, který má vlastnost s názvem FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. Kód volá metodu **Getfiletable** , aby získal název souboru (název objektu BLOB). Kód je podobný předchozímu kódu, který byl použit k získání cesty ke složce.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. Název souboru je napsán vytvořením objektu identifikátoru URI. Konstruktor identifikátoru URI používá vlastnost **BlobEndpoint** k vrácení názvu kontejneru. K vytvoření výstupního identifikátoru URI objektu BLOB se přidá cesta ke složce a název souboru.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Po zapsání názvu souboru můžete zapsat výstupní řetězec z metody **výpočtu** do nového objektu BLOB:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Vytvoření datové továrny
V části [Vytvoření vlastní aktivity](#create-the-custom-activity) jste vytvořili vlastní aktivitu a nahráli soubor zip s binárními soubory a souborem pdb do kontejneru objektů BLOB. V této části vytvoříte datovou továrnu s kanálem, který používá vlastní aktivitu.

Vstupní datová sada pro vlastní aktivitu představuje objekty BLOB (soubory) ve vstupní složce ( `mycontainer\\inputfolder` ) ve službě BLOB Storage. Výstupní datová sada pro aktivitu představuje výstupní objekty blob ve výstupní složce ( `mycontainer\\outputfolder` ) ve službě BLOB Storage.

Přetáhněte jeden nebo více souborů do vstupních složek:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Například přetáhněte jeden soubor (file.txt) s následujícím obsahem do každé složky:

```
test custom activity Microsoft test custom activity Microsoft
```

Každá vstupní složka odpovídá řezu v datové továrně, a to i v případě, že složka obsahuje dva nebo více souborů. Když kanál zpracovává jednotlivé řezy, prochází vlastní aktivita všemi objekty blob ve vstupní složce pro daný řez.

Vidíte pět výstupních souborů se stejným obsahem. Například výstupní soubor ze zpracování souboru ve složce 2015-11-16-00 má následující obsah:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Pokud přetáhnete více souborů (file.txt, file2.txt, file3.txt) se stejným obsahem do vstupní složky, ve výstupním souboru se zobrazí následující obsah. Každá složka (2015-11-16-00 atd.) odpovídá řezu v této ukázce, i když má složka více vstupních souborů.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Výstupní soubor má nyní tři řádky, jeden pro každý vstupní soubor (objekt BLOB) ve složce přidružené k řezu (2015-11-16-00).

Úkol se vytvoří pro každý běh aktivity. V této ukázce je v kanálu jenom jedna aktivita. Při zpracování řezu kanálem se vlastní aktivita spustí v dávce za účelem zpracování řezu. Vzhledem k tomu, že existuje pět řezů (každý řez může mít více objektů BLOB nebo souborů), v dávce se vytvoří pět úkolů. Když je úloha spuštěna v dávce, jedná se o vlastní aktivitu, která je spuštěná.

Další podrobnosti najdete v následujícím návodu.

#### <a name="step-1-create-the-data-factory"></a>Krok 1: vytvoření datové továrny
1. Po přihlášení k [Azure Portal](https://portal.azure.com/)proveďte následující kroky:

   a. V nabídce vlevo vyberte **Nový** .

   b. V okně **Nový** vyberte **data a analýzy** .

   c. V okně **Analýza dat** vyberte **Data Factory** .

1. V okně **Nová datová továrna** jako název zadejte **CustomActivityFactory** . Název datové továrny musí být globálně jedinečný. Pokud se zobrazí chyba "název objektu pro vytváření dat CustomActivityFactory není k dispozici", změňte název datové továrny. Použijte například yournameCustomActivityFactory a vytvořte datovou továrnu znovu.

1. Vyberte **název skupiny prostředků** a vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků.

1. Ověřte, jestli je předplatné a oblast, kde chcete vytvořit datovou továrnu, správné.

1. V okně **Nová datová továrna** vyberte **vytvořit** .

1. Objekt pro vytváření dat se vytvoří na řídicím panelu portálu.

1. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí stránka **Datová továrna** , která vám ukáže obsah objektu pro vytváření dat.

   ![Stránka pro datovou továrnu](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Krok 2: Vytvoření propojených služeb
Propojené služby propojují úložiště dat nebo výpočetní služby s datovou továrnou. V tomto kroku propojíte svůj účet úložiště a účet Batch s datovou továrnou.

#### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. V okně **objekt pro vytváření dat** pro **CustomActivityFactory** vyberte dlaždici **Autor a nasazení** . Zobrazí se Editor Data Factory.

1. Na panelu příkazů vyberte **nové úložiště dat** a zvolte **úložiště Azure.** Zobrazí se skript JSON, který použijete k vytvoření propojené služby úložiště v editoru.

   ![Nové úložiště dat](./media/data-factory-data-processing-using-batch/image7.png)

1. Nahraďte **název účtu** názvem vašeho účtu úložiště. Nahraďte **klíč účtu** přístupovým klíčem účtu úložiště. Informace o tom, jak získat přístupový klíč k úložišti, najdete v tématu [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

1. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

   ![Nasadit](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Vytvoření propojené služby Azure Batch
V tomto kroku vytvoříte propojenou službu pro účet Batch, který se používá ke spuštění vlastní aktivity objektu pro vytváření dat.

1. Na panelu příkazů vyberte **Nový COMPUTE** a zvolte **Azure Batch.** Zobrazí se skript JSON, který použijete k vytvoření propojené služby Batch v editoru.

1. Ve skriptu JSON:

   a. Nahraďte **název účtu** názvem vašeho účtu Batch.

   b. Nahraďte **přístupová klávesu** přístupovým klíčem účtu Batch.

   c. Zadejte ID fondu pro vlastnost **Pool** . Pro tuto vlastnost můžete zadat buď název fondu, nebo ID fondu.

   d. Zadejte identifikátor URI dávky pro vlastnost **batchUri** JSON.

      > [!IMPORTANT]
      > Adresa URL z okna **účtu Batch** je v následujícím formátu: `<accountname>.<region>.batch.azure.com` . Pro `batchUri` vlastnost ve skriptu JSON je nutné odebrat `<accountname>.` z adresy URL. Příklad: `"batchUri": "https://eastus.batch.azure.com"`.

      ![Okno účtu Batch](./media/data-factory-data-processing-using-batch/image9.png)

      Pro vlastnost **Pool** (název fondu) můžete také zadat ID fondu místo názvu fondu.

      > [!NOTE]
      > Služba Data Factory nepodporuje možnost na vyžádání pro službu Batch, protože pro službu HDInsight. V datové továrně můžete použít jenom vlastní fond služby Batch.
      >
      >

   e. Pro vlastnost **linkedServiceName** zadejte **StorageLinkedService** . Tuto propojenou službu jste vytvořili v předchozím kroku. Toto úložiště se používá jako pracovní oblast pro soubory a protokoly.

1. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

#### <a name="step-3-create-datasets"></a>Krok 3: vytvoření datových sad
V tomto kroku vytvoříte datové sady, které reprezentují vstupní a výstupní data.

#### <a name="create-the-input-dataset"></a>Vytvoření vstupní datové sady
1. V editoru Data Factory klikněte na panelu nástrojů na tlačítko **Nová datová sada** . V rozevíracím seznamu vyberte **úložiště objektů BLOB v Azure** .

1. Nahraďte skript JSON v pravém podokně následujícím fragmentem kódu JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Kanál vytvoříte později v tomto návodu s časem spuštění 2015-11-16T00:00:00Z a koncovým časem 2015-11-16T05:00:00Z. Naplánovalo se zpracování dat každou hodinu, takže je pět vstupních/výstupních řezů (mezi **00**: 00:00- \> **05**: 00:00).

    **Frekvence** a **interval** pro vstupní datovou sadu jsou nastavené na **Hour** a **1**, což znamená, že vstupní řez je dostupný každou hodinu.

    Počáteční čas každého řezu je reprezentován systémovou proměnnou **vlastnosti slicestart** v předchozím fragmentu kódu JSON. Tady jsou časy spuštění jednotlivých řezů.

    | **Průřez** | **Čas spuštění**          |
    |-----------|-------------------------|
    | 1         | 2015-11.16T **00**: 00:00 |
    | 2         | 2015-11 – 16T **01**: 00:00 |
    | 3         | 2015-11 – 16T **02**: 00:00 |
    | 4         | 2015-11 – 16T **03**: 00:00 |
    | 5         | 2015-11 – 16T **04**: 00:00 |

    **FolderPath** se počítá pomocí části rok, měsíc, den a hodina počátečního času řezu (**vlastnosti slicestart**). Tady je způsob, jak je vstupní složka namapována na řez.

    | **Průřez** | **Čas spuštění**          | **Vstupní složka**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11.16T **00**: 00:00 | 2015-11-16-**00** |
    | 2         | 2015-11 – 16T **01**: 00:00 | 2015-11-16 –**01** |
    | 3         | 2015-11 – 16T **02**: 00:00 | 2015-11-16 –**02** |
    | 4         | 2015-11 – 16T **03**: 00:00 | 2015-11-16 –**03** |
    | 5         | 2015-11 – 16T **04**: 00:00 | 2015-11-16 –**04** |

1. Pro vytvoření a nasazení tabulky **InputDataset** vyberte **nasadit** na panelu nástrojů.

#### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
V tomto kroku vytvoříte další datovou sadu typu Azureblobu, která bude představovat výstupní data.

1. V editoru Data Factory klikněte na panelu nástrojů na tlačítko **Nová datová sada** . V rozevíracím seznamu vyberte **úložiště objektů BLOB v Azure** .

1. Nahraďte skript JSON v pravém podokně následujícím fragmentem kódu JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    Pro každý vstupní řez se vygeneruje výstupní objekt BLOB nebo soubor. Tady je způsob, jak je výstupní soubor pojmenován pro každý řez. Všechny výstupní soubory jsou generovány v jedné výstupní složce, `mycontainer\\outputfolder` .

    | **Průřez** | **Čas spuštění**          | **Výstupní soubor**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11.16T **00**: 00:00 | 2015-11-16 –**00.txt** |
    | 2         | 2015-11 – 16T **01**: 00:00 | 2015-11-16 –**01.txt** |
    | 3         | 2015-11 – 16T **02**: 00:00 | 2015-11-16 –**02.txt** |
    | 4         | 2015-11 – 16T **03**: 00:00 | 2015-11-16 –**03.txt** |
    | 5         | 2015-11 – 16T **04**: 00:00 | 2015-11-16 –**04.txt** |

    Mějte na paměti, že všechny soubory ve vstupní složce (například 2015-11-16-00) jsou součástí řezu s časem spuštění 2015-11-16-00. Při zpracování tohoto řezu vlastní aktivita projde každým souborem a vytvoří ve výstupním souboru řádek s počtem výskytů hledaného výrazu "Microsoft". Pokud se ve složce 2015-11-16-00 nacházejí tři soubory, ve výstupním souboru jsou tři řádky 2015-11-16-00.txt.

1. Na panelu nástrojů vyberte **nasadit** a vytvořte a nasaďte rozhraní **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Krok 4: vytvoření a spuštění kanálu s vlastní aktivitou
V tomto kroku vytvoříte kanál s jednou aktivitou, vlastní aktivitou, kterou jste předtím vytvořili.

> [!IMPORTANT]
> Pokud jste neodeslali **file.txt** do vstupních složek v kontejneru objektů blob, udělejte to ještě před vytvořením kanálu. Vlastnost **Inpauseed** je ve formátu JSON kanálu nastavená na false, takže kanál se spustí hned, protože **počáteční** datum je v minulosti.
>
>

1. V editoru Data Factory na panelu příkazů vyberte **Nový kanál** . Pokud příkaz nevidíte, vyberte symbol tří teček, který chcete zobrazit.

1. Nahraďte skript JSON v pravém podokně následujícím fragmentem kódu JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Je třeba počítat s následujícím:

   * V kanálu je pouze jedna aktivita a jedná se o typ **DotNetActivity**.
   * **AssemblyName** je nastaven na název knihovny DLL **MyDotNetActivity.dll**.
   * **Parametr entryPoint** je nastavený na **MyDotNetActivityNS. MyDotNetActivity**. Je v podstatě \<namespace\> .\<classname\> ve vašem kódu.
   * **PackageLinkedService** je nastavená na **StorageLinkedService**, která odkazuje na úložiště objektů blob, které obsahuje soubor zip vlastní aktivity. Pokud používáte jiné účty úložiště pro vstupní a výstupní soubory a vlastní soubor zip aktivity, musíte vytvořit další propojenou službu úložiště. V tomto článku se předpokládá, že používáte stejný účet úložiště.
   * **PackageFile** je nastavená na **customactivitycontainer/MyDotNetActivity.zip**. Je ve formátu \<containerforthezip\> / \<nameofthezip.zip\> .
   * Vlastní aktivita přijímá **InputDataset** jako vstup a **OutputDataset** jako výstup.
   * Vlastnost **linkedServiceName** vlastní aktivity odkazuje na **AzureBatchLinkedService**, která oznamuje Data Factory, že vlastní aktivita potřebuje spustit v dávce.
   * Nastavení **souběžnosti** je důležité. Pokud použijete výchozí hodnotu, která je 1, i když ve fondu dávek máte dva nebo více výpočetních uzlů, tyto řezy se zpracují po druhém. Proto nebudete využívat možnosti paralelního zpracování v dávce. Pokud nastavíte **souběžnost** na vyšší hodnotu, řekněme 2 znamená, že lze zpracovat dva řezy (odpovídající dvěma úkolům ve službě Batch). V takovém případě jsou využívány oba virtuální počítače ve fondu Batch. Nastavte vlastnost Concurrency odpovídajícím způsobem.
   * Ve výchozím nastavení se na virtuálním počítači spustí jenom jeden úkol (řez). Ve výchozím nastavení je **maximální počet úkolů na virtuální počítač** pro fond dávek nastavený na hodnotu 1. Jako součást požadavků jste vytvořili fond s touto vlastností nastavenou na hodnotu 2. Proto mohou být na virtuálním počítači spuštěny dva řezy objektu pro vytváření dat ve stejnou dobu.
     - Vlastnost s vlastností- **pauseed** je ve výchozím nastavení nastavena na hodnotu false. Kanál se spustí hned v tomto příkladu, protože řezy začínají v minulosti. Tuto vlastnost můžete nastavit na **hodnotu true** , chcete-li kanál pozastavit a nastavit jej zpět na **hodnotu false** pro restartování.
     -   Doba **zahájení** a **ukončení** je pět hodin od. Řezy se vytvářejí každou hodinu, takže kanál vytvoří pět řezů.

1. Vyberte **Nasadit** na panelu příkazů a nasaďte kanál.

#### <a name="step-5-test-the-pipeline"></a>Krok 5: testování kanálu
V tomto kroku otestujete kanál odstraněním souborů do vstupních složek. Začněte tím, že otestujete kanál s jedním souborem pro každou vstupní složku.

1. V okně **objekt pro vytváření dat** v Azure Portal vyberte **diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. V zobrazení **diagramu** poklikejte na vstupní datovou sadu **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. Zobrazí se okno **InputDataset** se všemi pěti řezy připravenými pro řezy. Všimněte si **počátečního** a **koncového času** řezu pro každý řez.

   ![Počáteční a koncové časy vstupního řezu](./media/data-factory-data-processing-using-batch/image12.png)

1. V zobrazení **diagramu** vyberte možnost **OutputDataset**.

1. Pět výstupních řezů se zobrazí ve stavu **připraveno** , pokud byly vytvořeny.

   ![Počáteční a koncové časy výstupního řezu](./media/data-factory-data-processing-using-batch/image13.png)

1. Pomocí portálu můžete zobrazit úkoly spojené s řezy a zjistit, v jakém virtuálním počítači běžely jednotlivé řezy. Další informace najdete v části [Data Factory a Batch Integration](#data-factory-and-batch-integration) .

1. Výstupní soubory se zobrazí v části `mycontainer` v `outputfolder` úložišti objektů BLOB.

   ![Výstupní soubory v úložišti](./media/data-factory-data-processing-using-batch/image15.png)

   V seznamu je uvedeno pět výstupních souborů, jeden pro každý vstupní řez. Každý z výstupních souborů má obsah podobný následujícímu výstupu:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Následující diagram znázorňuje, jak jsou řezy Data Factory mapovány na úlohy ve službě Batch. V tomto příkladu má řez jenom jedno spuštění.

   ![Diagram mapování řezů](./media/data-factory-data-processing-using-batch/image16.png)

1. Teď ve složce zkuste použít více souborů. Vytvořte soubory **file2.txt**, **file3.txt**, **file4.txt** a **file5.txt** se stejným obsahem jako v file.txt ve složce **2015-11-06-01**.

1. Ve výstupní složce odstraňte výstupní soubor **2015-11-16-01.txt**.

1. V okně **OutputDataset** klikněte pravým tlačítkem na řez s **časem zahájení řezu** nastaveným na **11/16/2015 01:00:00 dop**. Vyberte možnost **Spustit** pro opětovné spuštění nebo opětovné zpracování řezu. Řez teď má pět souborů místo jednoho souboru.

    ![Spustit](./media/data-factory-data-processing-using-batch/image17.png)

1. Po spuštění řezu a jeho stav je **připraveno** ověřit obsah ve výstupním souboru pro tento řez (**2015-11-16-01.txt**). Výstupní soubor se zobrazí v části `mycontainer` v `outputfolder` úložišti objektů BLOB. Pro každý soubor řezu by měl být řádek.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Pokud jste neodstranili výstupní soubor 2015-11-16-01.txt předtím, než se pokusíte o pět vstupních souborů, uvidíte jeden řádek od předchozího řezu běhu a pět řádků z aktuálního řezu se spustí. Ve výchozím nastavení je obsah připojen do výstupního souboru, pokud již existuje.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integrace Data Factory a Batch
Služba Data Factory v dávce vytvoří úlohu s názvem `adf-poolname:job-xxx` .

![Dávkové úlohy](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Úkol v úloze se vytvoří pro každý běh řezu aktivity. Pokud je možné zpracovat 10 řezů, v úloze se vytvoří 10 úkolů. Pokud máte ve fondu více výpočetních uzlů, můžete mít paralelně spuštěný více než jeden řez. Pokud je maximální počet úkolů na výpočetní uzel nastavený na hodnotu větší než 1, může se ve stejném výpočetním prostředí spustit více než jeden řez.

V tomto příkladu je pět řezů, takže v dávce je pět úkolů. V případě, že je **souběžnost** nastavená na **5** ve formátu JSON kanálu v datové továrně a **maximální počet úloh na virtuální počítač** nastavený na **2** ve fondu Batch se **dvěma** virtuálními počítači, úlohy se spustí rychle. (Ověřte počáteční a koncové časy pro úlohy.)

Pomocí portálu zobrazte úlohu Batch a její úkoly, které jsou k nim přidruženy, a podívejte se, na jakém virtuálním počítači běžely jednotlivé řezy.

![Úlohy dávkové úlohy](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Ladění kanálu
Ladění se skládá z několika základních technik.

1. Pokud vstupní řez není nastavený na **připraveno**, zkontrolujte, jestli je správně nastavená struktura vstupní složky a jestli ve vstupních složkách existuje file.txt.

   ![Struktura vstupní složky](./media/data-factory-data-processing-using-batch/image3.png)

1. V metodě **Execute** vlastní aktivity použijte objekt **IActivityLogger** k protokolování informací, které vám pomůžou vyřešit problémy. Protokolované zprávy se zobrazí v \_ souboru protokolu uživatele 0. log.

   V okně **OutputDataset** Vyberte řez a zobrazte okno **datový řez** pro daný řez. V části **spuštění aktivit** se pro řez zobrazí jedno spuštění aktivity. Vyberete-li možnost **Spustit** na panelu příkazů, můžete spustit další spuštění aktivity pro stejný řez.

   Když vyberete spuštění aktivit, zobrazí se okno Podrobnosti o **spuštění aktivit** se seznamem souborů protokolu. Zaznamenané zprávy se zobrazí v \_ souboru protokolu uživatele 0. log. Pokud dojde k chybě, zobrazí se tři spuštění aktivit, protože počet opakování je v souboru JSON kanálu nebo aktivity nastavený na 3. Když vyberete spuštění aktivit, zobrazí se soubory protokolů, které můžete zkontrolovat a vyřešit chybu.

   ![Okna OutputDataset a datových řezů](./media/data-factory-data-processing-using-batch/image18.png)

   V seznamu souborů protokolu vyberte **User-0. log**. V pravém panelu se zobrazí výsledky použití metody **IActivityLogger. Write** .

   ![Okno podrobností o spuštění aktivity](./media/data-factory-data-processing-using-batch/image19.png)

   V protokolu System-0. log vyhledejte všechny systémové chybové zprávy a výjimky.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Zahrňte soubor **PDB** do souboru zip, aby podrobnosti o chybě obsahovaly informace, jako je například zásobník volání, když dojde k chybě.

1. Všechny soubory v souboru zip pro vlastní aktivitu musí být na nejvyšší úrovni bez podsložek.

   ![Seznam souborů zip vlastní aktivity](./media/data-factory-data-processing-using-batch/image20.png)

1. Zajistěte, aby byl parametr **AssemblyName** (MyDotNetActivity.dll), **EntryPoint** (MyDotNetActivityNS. MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) a **packageLinkedService** (měl by odkazovat na úložiště objektů BLOB obsahující soubor ZIP), nastaven na správné hodnoty.

1. Pokud jste opravili chybu a chcete znovu zpracovat řez, klikněte pravým tlačítkem myši na řez v okně **OutputDataset** a vyberte **Spustit**.

   ![Možnost spuštění okna OutputDataset](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Kontejner je ve vašem úložišti objektů BLOB s názvem `adfjobs` . Tento kontejner se neodstraní automaticky, ale po dokončení testování řešení ho můžete bezpečně odstranit. Podobně řešení Data Factory vytvoří dávkovou úlohu s názvem `adf-\<pool ID/name\>:job-0000000001` . Tuto úlohu můžete odstranit po otestování řešení, pokud chcete.
   >
   >
1. Vlastní aktivita nepoužívá soubor **app.config** ze svého balíčku. Proto pokud váš kód přečte z konfiguračního souboru jakékoli připojovací řetězce, nefunguje za běhu. Osvědčeným postupem při používání Batch je uložení jakýchkoli tajných kódů v Azure Key Vault. Pak použijte instanční objekt založený na certifikátu k ochraně trezoru klíčů a k distribuci certifikátu do fondu služby Batch. Vlastní aktivita rozhraní .NET má k dispozici přístup k tajným klíčům z trezoru klíčů za běhu. Toto obecné řešení se může škálovat na libovolný typ tajného klíče, nikoli jenom na připojovací řetězec.

    Existuje jednodušší řešení, ale nejedná se o doporučený postup. Propojenou službu SQL Database můžete vytvořit s nastavením připojovacího řetězce. Pak můžete vytvořit datovou sadu, která používá propojenou službu, a zřetězit datovou sadu jako fiktivní vstupní datovou sadu do vlastní aktivity .NET. Pak můžete přístup k připojovacímu řetězci propojené služby v kódu vlastní aktivity. Mělo by fungovat při běhu.  

#### <a name="extend-the-sample"></a>Rozšíří ukázku
Pokud chcete získat další informace o funkcích Data Factory a Batch, můžete tuto ukázku zvětšit. Chcete-li například zpracovat řezy v jiném časovém rozsahu, proveďte následující kroky:

1. Přidejte následující podsložky v `inputfolder` : 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 a 2015-11-16-09. Umístěte vstupní soubory do těchto složek. Změňte čas ukončení kanálu z `2015-11-16T05:00:00Z` na `2015-11-16T10:00:00Z` . V zobrazení **diagramu** poklikejte na **InputDataset** a zkontrolujte, že jsou vstupní řezy připravené. Dvojitým kliknutím na **OutputDataset** zobrazíte stav výstupních řezů. Pokud jsou ve stavu **připraveno** , podívejte se do výstupní složky pro výstupní soubory.

1. Zvyšte nebo snižte nastavení **souběžnosti** , abyste pochopili, jak má vliv na výkon vašeho řešení, zejména na zpracování, ke kterému dochází v dávce. Další informace o nastavení **souběžnosti** najdete v části "krok 4: vytvoření a spuštění kanálu s vlastní aktivitou".

1. Vytvoří fond s vyšším nebo nižším **maximálním počtu úloh na virtuální počítač**. Pokud chcete použít nový fond, který jste vytvořili, aktualizujte propojenou službu Batch v řešení Data Factory. Další informace o nastavení **maximálního počtu úkolů na virtuální počítač** najdete v části Krok 4: vytvoření a spuštění kanálu s vlastní aktivitou.

1. Vytvořte fond Batch pomocí funkce **automatického škálování** . Automatické škálování výpočetních uzlů ve fondu služby Batch je dynamické seřízení výkonu zpracování, které vaše aplikace používá.

    Vzorový vzorec tady dosáhne následujícího chování. Při počátečním vytvoření fondu se spustí jeden virtuální počítač. Metrika $PendingTasks definuje počet úloh ve stavu spuštěno a aktivní (ve frontě). Vzorec nalezne průměrný počet nedokončených úloh za posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy nepřekračuje 25 virtuálních počítačů. Při odeslání nových úloh se fond automaticky zvětšuje. Po dokončení úloh se virtuální počítače uvolní jednou a automatické škálování tyto virtuální počítače zmenší. StartingNumberOfVMs a maxNumberofVMs můžete upravit podle svých potřeb.

    Vzorec automatického škálování:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Další informace najdete v tématu [Automatické škálování výpočetních uzlů ve fondu služby Batch](../../batch/batch-automatic-scaling.md).

   Pokud fond používá výchozí [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale), může před spuštěním vlastní aktivity služba Batch trvat 15 až 30 minut, než se virtuální počítač připraví. Pokud fond používá jiný autoScaleEvaluationInterval, může služba Batch trvat autoScaleEvaluationInterval plus 10 minut.

1. V ukázkovém řešení vyvolá metoda **Execute** metodu **výpočtu** , která zpracovává vstupní datový řez a vytváří výstupní datový řez. Můžete napsat vlastní metodu pro zpracování vstupních dat a nahradit volání metody **výpočtu** v metodě **Execute** voláním metody.

### <a name="next-steps-consume-the-data"></a>Další kroky: využití dat
Po zpracování dat je můžete využívat s online nástroji, jako je Power BI. Tady jsou odkazy, které vám pomůžou pochopit Power BI a jak ho používat v Azure:

* [Prozkoumat datovou sadu v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Začínáme s Power BI Desktopem](/power-bi/fundamentals/desktop-getting-started)
* [Aktualizovat data v Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure a Power BI: základní přehled](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Reference
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Úvod do služby Data Factory](data-factory-introduction.md)
  * [Začínáme s Data Factory](data-factory-build-your-first-pipeline.md)
  * [Použití vlastních aktivit v kanálu Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Základy dávky](../../azure-sql/database/sql-database-paas-overview.md)
  * [Přehled funkcí Batch](../../batch/batch-service-workflow-features.md)
  * [Vytvoření a Správa účtu Batch v Azure Portal](../../batch/batch-account-create-portal.md)
  * [Začínáme s klientskou knihovnou Batch pro .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: /archive/blogs/windowshpc/azure-batch-explorer-sample-walkthrough