---
title: Použití vlastních aktivit v kanálu Azure Data Factory
description: Zjistěte, jak vytvořit vlastní aktivity a používat je v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 54cb06f1c77ab68818d8531b57d6eb936deda8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265724"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-use-custom-activities.md)
> * [Verze 2 (aktuální verze)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Vlastní aktivity ve Verzi 2](../transform-data-using-dotnet-custom-activity.md).

Existují dva typy aktivit, které můžete použít v kanálu Azure Data Factory.

- [Aktivity přesunu dat](data-factory-data-movement-activities.md) k přesunu dat mezi [podporovaným zdrojovým a jímačovými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](data-factory-data-transformation-activities.md) k transformaci dat pomocí výpočetních služeb, jako je Azure HDInsight, Azure Batch a Azure Machine Learning.

Chcete-li přesunout data do nebo z úložiště dat, které data factory nepodporuje, vytvořte **vlastní aktivitu** s vlastní logikou pohybu dat a použijte aktivitu v kanálu. Podobně chcete transformovat nebo zpracovat data způsobem, který není podporován data factory, vytvořit vlastní aktivitu s vlastní logiku transformace dat a použít aktivitu v kanálu.

Můžete nakonfigurovat vlastní aktivitu pro spuštění ve fondu **Azure Batch** virtuálních počítačů. Při použití Azure Batch, můžete použít jenom existující fond Azure Batch.

Následující návod obsahuje podrobné pokyny pro vytvoření vlastní aktivity rozhraní .NET a použití vlastní aktivity v kanálu. Návod používá propojené služby **Azure Batch.**

> [!IMPORTANT]
> - Bránu pro správu dat z vlastní aktivity není možné použít pro přístup k místním zdrojům dat. V současné době [brána pro správu dat](data-factory-data-management-gateway.md) podporuje pouze aktivitu kopírování a aktivitu uložené procedury v datové továrně.

## <a name="walkthrough-create-a-custom-activity"></a>Návod: vytvoření vlastní aktivity
### <a name="prerequisites"></a>Požadavky
* Visual Studio 2012/2013/2015/2017
* Stažení a instalace [sady Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Předpoklady azure dávky
V návodu spustíte vlastní aktivity .NET pomocí Azure Batch jako výpočetní prostředek. **Azure Batch** je služba platformy pro efektivní spouštění rozsáhlých paralelních aplikací a aplikací vysoce výkonného výpočetního prostředí (HPC) v cloudu. Azure Batch naplánuje náročnou práci náročnou na výpočetní výkon, která se spustí na spravované **kolekci virtuálních počítačů**, a může automaticky škálovat výpočetní prostředky tak, aby vyhovovaly potřebám vašich úloh. Podrobný přehled služby Azure Batch najdete v článku [Základy azure batch.][batch-technical-overview]

Pro účely kurzu vytvořte účet Azure Batch s fondem virtuálních počítačů. Postup je následující:

1. Vytvořte účet **Azure Batch** pomocí [portálu Azure](https://portal.azure.com). Pokyny najdete v článku Vytvoření a správa účtu [Azure Batch.][batch-create-account]
2. Poznamenejte si název účtu Azure Batch, klíč účtu, identifikátor URI a název fondu. Potřebujete je k vytvoření propojené služby Azure Batch.
    1. Na domovské stránce účtu Azure Batch se zobrazí **adresa** `https://myaccount.westus.batch.azure.com`URL v následujícím formátu: . V tomto příkladu je **můj účet** název účtu Azure Batch. Identifikátor URI, který používáte v definici propojené služby, je adresa URL bez názvu účtu. Například: `https://<region>.batch.azure.com`.
    2. V levé nabídce klepněte na **tlačítko Klávesy** a zkopírujte **klíč PRIMÁRNÍ PŘÍSTUP**.
    3. Pokud chcete použít existující fond, klikněte v nabídce na **Fondy** a poznamenejte si **ID** fondu. Pokud nemáte existující fond, přejděte k dalšímu kroku.
2. Vytvořte **fond dávek Azure**.

   1. Na [webu Azure Portal](https://portal.azure.com)klikněte v levé nabídce na **Procházet** a klikněte na **Dávkové účty**.
   2. Vyberte svůj účet Azure Batch a otevřete okno **Dávkový účet.**
   3. Klikněte na dlaždice **Fondy.**
   4. V okně **Fondy** klikněte na tlačítko Přidat na panelu nástrojů a přidejte fond.
      1. Zadejte ID fondu (ID fondu). Všimněte si **ID fondu**; potřebujete při vytváření řešení Data Factory.
      2. Zadejte **systém Windows Server 2012 R2** pro nastavení Rodina operačního systému.
      3. Vyberte **cenovou úroveň uzlu**.
      4. Zadejte **hodnotu 2** pro nastavení **Vyhrazené cíle.**
      5. Zadejte **hodnotu 2** jako hodnotu pro nastavení **Maximální počet úkolů na uzel.**
   5. Kliknutím na tlačítko **OK** vytvořte fond.
   6. Poznamenejte si **ID** bazénu.

### <a name="high-level-steps"></a>Základní kroky
Zde jsou dva kroky vysoké úrovně, které provedete v rámci tohoto návodu:

1. Vytvořte vlastní aktivitu, která obsahuje jednoduchou logiku transformace/zpracování dat.
2. Vytvořte azure továrně dat s kanálem, který používá vlastní aktivitu.

### <a name="create-a-custom-activity"></a>Vytvoření vlastní aktivity
Chcete-li vytvořit vlastní aktivitu rozhraní .NET, vytvořte projekt **knihovny tříd .NET** s třídou, která implementuje toto rozhraní **IDotNetActivity.** Toto rozhraní má pouze jednu metodu: [Spustit](https://msdn.microsoft.com/library/azure/mt603945.aspx) a jeho podpis je:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Metoda má čtyři parametry:

- **linkedServices**. Tato vlastnost je výčet seznamu propojených služeb úložiště dat odkazuje vstupní a výstupní datové sady pro aktivitu.
- **datových sad**. Tato vlastnost je výčet seznamu vstupních a výstupních datových sad pro aktivitu. Tento parametr můžete použít k získání umístění a schémat definovaných vstupními a výstupními datovými sadami.
- **činnosti**. Tato vlastnost představuje aktuální aktivitu. Lze jej použít pro přístup k rozšířené vlastnosti spojené s vlastní aktivity. Podrobnosti najdete [v tématu Rozšířené vlastnosti aplikace Access.](#access-extended-properties)
- **logger**. Tento objekt umožňuje psát ladicí komentáře, které se objevují v protokolu uživatelů pro kanál.

Metoda vrátí slovník, který lze použít ke řetězení vlastní aktivity společně v budoucnu. Tato funkce ještě není implementována, proto vraťte prázdný slovník z metody.

### <a name="procedure"></a>Postup
1. Vytvořte projekt **knihovny tříd .NET.**
   <ol type="a">
     <li>Spusťte Visual Studio.</li>
     <li>Klikněte na <b>Soubor</b>, přejděte na <b>Nový</b> a klikněte na <b>Projekt</b>.</li>
     <li>Rozbalte <b>Šablony</b> a vyberte <b>Visual C#</b>. V tomto návodu použijete c#, ale k vývoji vlastní aktivity můžete použít libovolný jazyk .NET.</li>
     <li>Vyberte <b>Knihovnu tříd</b> ze seznamu typů projektů vpravo. V sadě Visual Studio zvolte <b>Knihovnu tříd (.NET Framework)</b> </li>
     <li>Zadejte <b>MyDotNetActivity</b> pro <b>název</b>.</li>
     <li>Vyberte <b>možnost C:\ADFGetStarted</b> pro <b>umístění</b>.</li>
     <li>Kliknutím na tlačítko <b>OK</b> vytvořte projekt.</li>
   </ol>

2. Klepněte na **položku Nástroje**, přejděte na **položku NuGet Package Manager**a klepněte na příkaz **Konzola správce balíčků**.

3. V konzole Správce balíčků proveďte následující příkaz pro import **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importujte balíček **Azure Storage** NuGet do projektu.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Spouštěč služeb Data Factory vyžaduje verzi 4.3 WindowsAzure.Storage. Pokud přidáte odkaz na novější verzi sestavení Azure Storage ve vašem projektu vlastní aktivity, zobrazí se chyba při spuštění aktivity. Chcete-li chybu vyřešit, přečtěte si část [Izolace domény aplikace Appdomain.](#appdomain-isolation)
5. Přidejte následující **příkazy pomocí** do zdrojového souboru v projektu.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Změňte název **oboru názvů** na **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Změňte název třídy na **MyDotNetActivity** a odvoděte ji z rozhraní **IDotNetActivity,** jak je znázorněno v následujícím fragmentu kódu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementujte (Add) metodu **Execute** rozhraní **IDotNetActivity** do třídy **MyDotNetActivity** a zkopírujte do metody následující ukázkový kód.

    Následující ukázka počítá počet výskytů hledaného výrazu ("Microsoft") v každém objektu blob přidruženém k řezu dat.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
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

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```
9. Přidejte následující pomocné metody:

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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
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

    Metoda GetFolderPath vrátí cestu ke složce, na kterou datová sada odkazuje, a metoda GetFileName vrátí název objektu blob/souboru, na který datová sada odkazuje. Pokud máte složkuPath definuje pomocí proměnných, jako je například {Year}, {Month}, {Day} atd., metoda vrátí řetězec, protože je bez jejich nahrazení hodnotami runtime. [Podrobnosti](#access-extended-properties) o přístupu k slicestart, sliceend atd.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda Calculate vypočítá počet instancí klíčového slova Microsoft ve vstupních souborech (objekty BLOB ve složce). Hledaný výraz ("Microsoft") je pevně zakódován v kódu.
10. Zkompilujte projekt. V nabídce klikněte na **Vytvořit** a klikněte na **Build Solution**.

    > [!IMPORTANT]
    > Nastavte 4.5.2 verzi rozhraní .NET Framework jako cílovou architekturu projektu: klikněte pravým tlačítkem myši na projekt a klepnutím na příkaz **Vlastnosti** nastavte cílovou architekturu. Factory nepodporuje vlastní aktivity zkompilované proti verze rozhraní .NET Framework později než 4.5.2.

11. Spusťte **Průzkumníka Windows**a v závislosti na typu sestavení přejděte do složky **bin\debug** nebo **bin\release.**
12. Vytvořte soubor ZIP **MyDotNetActivity.zip,** který obsahuje \<všechny\>binární soubory ve složce projektu \bin\Debug. Zahrňte soubor **MyDotNetActivity.pdb,** abyste získali další podrobnosti, například číslo řádku, do zdrojového kódu, který problém způsobil, pokud došlo k chybě.

    > [!IMPORTANT]
    > Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.

    ![Binární výstupní soubory](./media/data-factory-use-custom-activities/Binaries.png)
14. Vytvořte kontejner objektů blob s názvem **customactivitycontainer,** pokud ještě neexistuje.
15. Nahrajte myDotNetActivity.zip jako objekt blob do kontejneru vlastní aktivity v **úložišti** objektů blob pro obecné účely Azure (ne horké nebo cool úložiště objektů blob), které je odkazováno Službou AzureStorageLinkedService.

> [!IMPORTANT]
> Pokud přidáte tento projekt aktivity .NET do řešení v sadě Visual Studio, které obsahuje projekt Factory dat a přidáte odkaz na projekt aktivity .NET z projektu aplikace Data Factory, není nutné provádět poslední dva kroky ručního vytvoření zip a nahraje te do úložiště objektů blob Azure pro obecné účely. Při publikování entity Factory dat pomocí Sady Visual Studio, tyto kroky se automaticky provádí procesem publikování. Další informace naleznete [v tématu Data Factory projektu v](#data-factory-project-in-visual-studio) části Visual Studio.

## <a name="create-a-pipeline-with-custom-activity"></a>Vytvoření kanálu s vlastní aktivitou
Vytvořili jste vlastní aktivitu a nahráli soubor zip s binárními soubory do kontejneru objektů blob v účtu úložiště **Azure pro obecné účely.** V této části vytvoříte azure továrně dat s kanálem, který používá vlastní aktivitu.

Vstupní datová sada pro vlastní aktivitu představuje objekty BLOB (soubory) ve složce customactivityinput kontejneru adftutorial v úložišti objektů blob. Výstupní datová sada pro aktivitu představuje výstupní objekty BLOB ve složce customactivityoutput kontejneru adftutorial v úložišti objektů blob.

Vytvořte soubor **file.txt** s následujícím obsahem a nahrajte jej do **složky customactivityinput** kontejneru **adftutorial.** Vytvořte kontejner adftutorial, pokud již neexistuje.

```
test custom activity Microsoft test custom activity Microsoft
```

Vstupní složka odpovídá řezu v Azure Data Factory i v případě, že složka má dva nebo více souborů. Když je každý řez zpracován kanálem, vlastní aktivita iterates přes všechny objekty BLOB ve vstupní složce pro tento řez.

Zobrazí se jeden výstupní soubor s ve složce adftutorial\customactivityoutput s jedním nebo více řádky (stejné jako počet objektů BLOB ve vstupní složce):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Tady jsou kroky, které provedete v této části:

1. Vytvořte **datovou továrnu**.
2. Vytvořte **propojené služby** pro fond Azure Batch virtuálních počítače, na kterých běží vlastní aktivita a Azure Storage, který obsahuje objekty BLOB vstupu a výstupu.
3. Vytvořte vstupní a výstupní **datové sady,** které představují vstup a výstup vlastní aktivity.
4. Vytvořte **kanál,** který používá vlastní aktivitu.

> [!NOTE]
> Vytvořte **soubor file.txt** a nahrajte ho do kontejneru objektů blob, pokud jste tak ještě neučinili. Viz pokyny v předchozí části.

### <a name="step-1-create-the-data-factory"></a>Krok 1: Vytvoření datové továrny
1. Po přihlášení k portálu Azure postupujte takto:
   1. V levé nabídce klikněte na **Vytvořit zdroj.**
   2. V okně **Nový** klepněte na **položku Data + Analytics.**
   3. V okně **Analýza dat** klikněte na **Objekt pro vytváření dat**.

      ![Nová nabídka Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. V okně **Nová továrna dat** zadejte **CustomActivityFactory** pro název. Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **Název factory dat "CustomActivityFactory" není k dispozici**, změňte název datové továrny (například **yournameCustomActivityFactory**) a zkuste vytvořit znovu.

    ![Nový blade Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klikněte na **NÁZEV SKUPINY PROSTŘEDKŮ**a vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků.
4. Ověřte, zda používáte správné **předplatné** a **oblast,** kde chcete vytvořit datovou továrnu.
5. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
6. Zobrazí se vytváření datové továrny na **řídicím panelu** portálu Azure.
7. Po úspěšném vytvoření datové továrny se zobrazí okno Data Factory, které zobrazuje obsah datové továrny.

    ![Okno Objekt pro vytváření dat](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2: Vytvoření propojených služeb
Propojené služby propojují úložiště dat nebo výpočetní služby s objektem pro vytváření dat Azure. V tomto kroku propojíte svůj účet Azure Storage a účet Azure Batch s vaší továrně dat.

#### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. Klepněte na **dlaždici Author a nasazení** v okně **DATA FACTORY** pro **CustomActivityFactory**. Zobrazí se editor služby Data Factory.
2. Na panelu příkazů klikněte na **Nové úložiště dat** a zvolte Úložiště **Azure**. V editoru by se měl zobrazit skript JSON pro vytvoření propojené služby Azure Storage.

    ![Nové úložiště dat – Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Nahraďte `<accountname>` název svého účtu `<accountkey>` úložiště Azure a přístupový klíč účtu úložiště Azure. Informace o tom, jak získat přístupový klíč úložiště, najdete v [tématu Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

    ![Služba Azure Storage se líbila](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Propojenou službu nasadíte kliknutím na **Nasadit** na panelu příkazů.

#### <a name="create-azure-batch-linked-service"></a>Vytvořit propojenou službu Azure Batch
1. V Editoru datových tove je klikněte na **... Další** informace na panelu příkazů klikněte na **Nový výpočetní výkon**a v nabídce vyberte Azure **Batch.**

    ![Nový výpočetní výkon – Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Ve skriptu JSON proveďte následující změny:

   1. Zadejte název účtu Azure Batch pro vlastnost **accountName.** **Adresa URL** z okna účtu **Azure Batch** `http://accountname.region.batch.azure.com`je v následujícím formátu: . Pro **batchUri** vlastnost v JSON, je `accountname.` třeba odebrat `accountname` z `accountName` adresy URL a použít pro JSON vlastnost.
   2. Zadejte klíč účtu Azure Batch pro vlastnost **accessKey.**
   3. Zadejte název fondu, který jste vytvořili jako součást předpokladů pro vlastnost **poolName.** Můžete také zadat ID fondu namísto názvu fondu.
   4. Zadejte uri dávky Azure pro vlastnost **batchUri.** Příklad: `https://westus.batch.azure.com`.
   5. Zadejte **AzureStorageLinkedService** pro **vlastnost linkedServiceName.**

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       Pro **vlastnost poolName** můžete také zadat ID fondu namísto názvu fondu.

### <a name="step-3-create-datasets"></a>Krok 3: Vytvoření datových sad
V tomto kroku vytvoříte datové sady představující vstupní a výstupní data.

#### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
1. V **Editoru** pro datovou továrnu klikněte na **... Další** informace na panelu příkazů klikněte na **Nová datová sada**a v rozevírací nabídce vyberte **azure blob storage.**
2. Nahraďte json v pravém podokně následujícím fragmentem JSON:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
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

   Můžete vytvořit kanál později v tomto návodu s časem zahájení: 2016-11-16T00:00:00Z a čas ukončení: 2016-11-16T05:00:00Z. Je naplánováno vytváření dat každou hodinu, takže existuje pět **00**vstupních a výstupních řezů (mezi 00:00:00 -> 05:00:00). **05**

   **Frekvence** a **interval** pro vstupní datovou sadu je nastavena na **hodinu** a **1**, což znamená, že vstupní řez je k dispozici každou hodinu. V této ukázce se jedná o stejný soubor (file.txt) ve složce intput.

   Zde jsou počáteční časy pro každý řez, který je reprezentován systémovou proměnnou SliceStart ve výše uvedeném fragmentu JSON.
3. Chcete-li vytvořit a nasadit **sadu InputDataset ,** klepněte na tlačítko **Nasadit** na panelu nástrojů . Potvrďte, že se v záhlaví okna editoru zobrazila zpráva **ÚSPĚŠNÉ VYTVOŘENÍ TABULKY**.

#### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
1. V **editoru Data Factory**klikněte na **... Další** informace na panelu příkazů klikněte na **Nová datová sada**a pak vyberte **Azure Blob storage**.
2. Nahraďte skript JSON v pravém podokně následujícím skriptem JSON:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
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

     Výstupní umístění je **adftutorial/customactivityoutput/** a název výstupního souboru je rrrr-MM-dd-HH.txt, kde yyyy-MM-dd-HH je rok, měsíc, datum a hodina řezu, který se vyrábí. Podrobnosti najdete [v tématu Odkaz na vývojáře.][adf-developer-reference]

    Pro každý vstupní řez se vygeneruje výstupní objekt blob/soubor. Zde je, jak je pro každý řez pojmenován výstupní soubor. Všechny výstupní soubory jsou generovány v jedné výstupní složce: **adftutorial\customactivityoutput**.

   | Plátek | Čas spuštění | Výstupní soubor |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Nezapomeňte, že všechny soubory ve vstupní složce jsou součástí řezu s výše uvedenými počátečními časy. Při zpracování tohoto řezu vlastní aktivita prohledává každý soubor a vytváří řádek ve výstupním souboru s počtem výskytů hledaného výrazu ("Microsoft"). Pokud jsou ve vstupní složce tři soubory, jsou ve výstupním souboru pro každý hodinový řez tři řádky: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt atd.
3. Chcete-li nasadit **sadu OutputDataset**, klepněte na panelu příkazů na **tlačítko Nasadit.**

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Vytvoření a spuštění kanálu, který používá vlastní aktivitu
1. V Editoru datových tove je klikněte na **... Další**a potom na panelu příkazů vyberte **Nový kanál.**
2. Nahraďte json v pravém podokně následujícím skriptem JSON:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Je třeba počítat s následujícím:

   * **Souběžnost** je nastavena na **2** tak, aby dva řezy jsou zpracovány paralelně 2 virtuálnípočítače ve fondu Azure Batch.
   * V části aktivity je jedna aktivita a je typu: **DotNetActivity**.
   * **Název_sestavení** je nastaven na název dll: **MyDotnetActivity.dll**.
   * **EntryPoint** je nastavena na **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** je nastavena na **AzureStorageLinkedService,** která odkazuje na úložiště objektů blob, které obsahuje vlastní aktivity zip souboru. Pokud používáte různé účty Azure Storage pro vstupní a výstupní soubory a vlastní soubor zip aktivity, můžete vytvořit jinou propojenou službu Azure Storage. Tento článek předpokládá, že používáte stejný účet azure storage.
   * **PackageFile** je nastavena na **customactivitycontainer/MyDotNetActivity.zip**. Je ve formátu: containerforthezip/nameofthezip.zip.
   * Vlastní aktivita přebírá **InputDataset** jako vstup a **OutputDataset** jako výstup.
   * Vlastnost linkedServiceName vlastní aktivity odkazuje na **AzureBatchLinkedService**, která říká Azure Data Factory, že vlastní aktivita musí běžet na virtuálních počítačích Azure Batch.
   * **isPaused** vlastnost je nastavena na **false** ve výchozím nastavení. Kanál běží okamžitě v tomto příkladu, protože řezy začínají v minulosti. Tuto vlastnost můžete nastavit na hodnotu true, chcete-li pozastavit kanál a nastavit jej zpět na hodnotu false, aby se restartoval.
   * Počáteční **start** a **koncový** čas jsou od sebe **vzdáleny pět** hodin a řezy jsou vyráběny každou hodinu, takže potrubí množí pět řezů.
3. Pokud chcete nasadit kanál, klikněte na panelu příkazů na **Nasadit.**

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. V okně Data Factory na webu Portál Azure klikněte na **Diagram**.

    ![Dlaždice Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. V zobrazení diagramu nyní klepněte na sadu OutputDataset.

    ![Zobrazení diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Měli byste vidět, že pět výstupních řezů je ve stavu Připraveno. Pokud nejsou ve stavu Ready, nebyly ještě vyrobeny.

   ![Výstupní řezy](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Ověřte, zda jsou výstupní soubory generovány v úložišti objektů blob v kontejneru **adftutorial.**

   ![výstup z vlastní aktivity][image-data-factory-output-from-custom-activity]
5. Pokud otevřete výstupní soubor, měli byste vidět výstup podobný následujícímu výstupu:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Pomocí rutin [Azure Portal][azure-preview-portal] nebo Azure PowerShellu můžete monitorovat vaši datovou továrnu, kanály a sady dat. Zprávy z **ActivityLogger** uvidíte v kódu pro vlastní aktivitu v protokolech (konkrétně user-0.log), které si můžete stáhnout z portálu nebo pomocí rutin.

   ![stažení protokolů z vlastní aktivity][image-data-factory-download-logs-from-custom-activity]

Podrobné kroky pro monitorování datových sad a kanálů v [tématu Sledování a správa kanálů](data-factory-monitor-manage-pipelines.md) najdete v tématu Monitorování a správa kanálů.

## <a name="data-factory-project-in-visual-studio"></a>Projekt Data Factory v sadě Visual Studio
Můžete vytvořit a publikovat entity Data Factory pomocí Visual Studia namísto použití portálu Azure. Podrobné informace o vytváření a publikování entit Data Factory pomocí Visual Studia najdete v tématu [Sestavení prvního kanálu pomocí Visual Studia](data-factory-build-your-first-pipeline-using-vs.md) a kopírování dat z azure blob do článků Azure [SQL.](data-factory-copy-activity-tutorial-using-visual-studio.md)

Pokud vytváříte projekt Data Factory v sadě Visual Studio, proveďte následující další kroky:

1. Přidejte projekt Factory dat do řešení Visual Studio, které obsahuje vlastní projekt aktivity.
2. Přidejte odkaz na projekt aktivity .NET z projektu Data Factory. Klepněte pravým tlačítkem myši na projekt Datové továrny, přejděte na **Přidat**a potom klepněte na **příkaz Odkaz**.
3. V dialogovém okně **Přidat odkaz** vyberte projekt **MyDotNetActivity** a klepněte na **tlačítko OK**.
4. Sestavení a publikování řešení.

    > [!IMPORTANT]
    > Když publikujete entity Data Factory, soubor zip se automaticky vytvoří za vás a nahraje se do kontejneru objektů blob: customactivitycontainer. Pokud kontejner objektů blob neexistuje, je také vytvořen.

## <a name="data-factory-and-batch-integration"></a>Integrace datové továrny a dávky
Služba Data Factory vytvoří úlohu v Azure Batch s názvem: **adf-poolname: job-xxx**. V levé nabídce klikněte na **Úlohy.**

![Azure Data Factory – dávkové úlohy](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Úloha je vytvořena pro každé spuštění aktivity řezu. Pokud je připraveno ke zpracování pět řezů, bude v této úloze vytvořeno pět úkolů. Pokud existuje více výpočetních uzlů ve fondu batch, dva nebo více řezů lze spustit paralelně. Pokud je maximální počet úloh na výpočetní uzel nastaven na > 1, můžete mít také více než jeden řez spuštěný na stejném výpočetním počítači.

![Azure Data Factory – úlohy dávkových úloh](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Následující diagram znázorňuje vztah mezi azure data factory a dávkové úlohy.

![& datové továrny](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Poradce při potížích s chybami
Řešení potíží se skládá z několika základních technik:

1. Pokud se zobrazí následující chyba, můžete používat úložiště objektů blob hot/cool namísto použití úložiště objektů blob Azure pro obecné účely. Nahrajte soubor zip do **univerzálního účtu úložiště Azure**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Pokud se zobrazí následující chyba, zkontrolujte, zda název třídy v souboru CS odpovídá názvu, který jste zadali pro vlastnost **EntryPoint** v kanálu JSON. V návodu je název třídy: MyDotNetActivity a EntryPoint v JSON je: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Pokud se názvy shodují, zkontrolujte, zda jsou všechny binární soubory v **kořenové složce** souboru ZIP. To znamená, že při otevření souboru ZIP byste měli vidět všechny soubory v kořenové složce, nikoli v žádné dílčí složce.
3. Pokud vstupní řez není nastaven na **připraveno**, zkontrolujte, zda je struktura vstupních složek správná a **soubor file.txt** existuje ve vstupních složkách.
3. V **Execute** metoda vlastní aktivity použijte **IActivityLogger** objekt protokolovat informace, které vám pomohou řešit problémy. Zaznamenané zprávy se zobrazují v souborech protokolu uživatele (jeden nebo více souborů s názvem: user-0.log, user-1.log, user-2.log atd.).

   V noži **OutputDataset** klepněte na řez, abyste viděli okno **ŘEZ DATA SLICE** pro tento řez. Zobrazí se **spuštění aktivity** pro tento řez. Měli byste vidět jednu aktivitu spustit pro řez. Pokud klepnete na tlačítko Spustit na panelu příkazů, můžete spustit jinou aktivitu pro stejný řez.

   Po klepnutí na spuštění aktivity se zobrazí okno **PODROBNOSTI spuštění aktivity** se seznamem souborů protokolu. V souboru user_0.log se zobrazí protokolované zprávy. Když dojde k chybě, zobrazí se tři spuštění aktivity, protože počet opakování je nastavena na 3 v kanálu/aktivity JSON. Po klepnutí na spuštění aktivity se zobrazí soubory protokolu, které můžete zkontrolovat k řešení chyby.

   V seznamu souborů protokolu klepněte na **soubor user-0.log**. V pravém panelu jsou výsledky pomocí **metody IActivityLogger.Write.** Pokud nevidíte všechny zprávy, zkontrolujte, zda máte více souborů protokolu s názvem: user_1.log, user_2.log atd. V opačném případě kód pravděpodobně selhal po poslední protokolované zprávy.

   Kromě toho zkontrolujte **system-0.log** pro všechny chybové zprávy systému a výjimky.
4. Zahrnout soubor **PDB** do souboru zip tak, aby podrobnosti o chybě mít informace, jako je **například zásobník volání,** když dojde k chybě.
5. Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.
6. Ujistěte se, že **název assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) a **packageLinkedService** (by měl ystitá na úložiště objektů blob **Azure pro obecné účely,** které obsahuje soubor zip) jsou nastaveny na správné hodnoty.
7. Pokud jste opravili chybu a chcete řez zpracovat znovu, klikněte na něj v okně **OutputDataset** pravým tlačítkem myši a potom klikněte na **Spustit**.
8. Pokud se zobrazí následující chyba, používáte balíček Azure Storage verze > 4.3.0. Spouštěč služeb Data Factory vyžaduje verzi 4.3 WindowsAzure.Storage. Viz Část [izolace appdomény](#appdomain-isolation) pro řešení, pokud je nutné použít novější verzi sestavení Azure Storage.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Pokud můžete použít 4.3.0 verze balíčku Azure Storage, odeberte existující odkaz na balíček Azure Storage verze > 4.3.0. Potom spusťte následující příkaz z konzoly NuGet Package Manager Console.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Sestavte projekt. Odstraňte sestavení Azure.Storage verze > 4.3.0 ze složky bin\Debug. Vytvořte soubor zip s binárními soubory a souborem PDB. Nahraďte starý soubor zip tímto v kontejneru objektů blob (customactivitycontainer). Znovu spusťte řezy, které se nezdařily (klikněte pravým tlačítkem myši na řez a klikněte na Spustit).
8. Vlastní aktivita nepoužívá soubor **app.config** z balíčku. Proto pokud váš kód přečte všechny připojovací řetězce z konfiguračního souboru, nefunguje za běhu. Osvědčeným postupem při použití Azure Batch je uchovat všechny tajné klíče v **Azure KeyVault**, použijte instanční objekt založený na certifikátu k ochraně **trezoru keyvault**a distribuovat certifikát do fondu Azure Batch. Vlastní aktivita .NET potom má přístup k tajným klíčům v trezoru za běhu. Toto řešení je obecné řešení a lze škálovat na libovolný typ tajného klíče, nikoli pouze připojovací řetězec.

   Existuje jednodušší řešení (ale není osvědčeným postupem): můžete vytvořit **azure SQL propojené služby** s nastavením připojovacího řetězce, vytvořit datovou sadu, která používá propojenou službu a řetěz datovou sadu jako fiktivní vstupní datovou sadu na vlastní aktivitu .NET. Potom můžete přistupovat k připojovacímu řetězci propojené služby v kódu vlastní aktivity.

## <a name="update-custom-activity"></a>Aktualizovat vlastní aktivitu
Pokud aktualizujete kód pro vlastní aktivitu, vytvořte jej a nahrajte soubor ZIP, který obsahuje nové binární soubory, do úložiště objektů blob.

## <a name="appdomain-isolation"></a>Izolace domény aplikace
Viz [Cross AppDomain Ukázka,](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) která ukazuje, jak vytvořit vlastní aktivitu, která není omezena na verze sestavení používané spouštěč datové továrny (příklad: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, atd.).

## <a name="access-extended-properties"></a>Přístup k rozšířeným vlastnostem
Můžete deklarovat rozšířené vlastnosti v aktivitě JSON, jak je znázorněno v následující ukázce:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

V příkladu jsou dvě rozšířené vlastnosti: **SliceStart** a **DataFactoryName**. Hodnota pro ŘezStart je založena na systémové proměnné SliceStart. Seznam podporovaných systémových proměnných naleznete v tématu [Systémové proměnné.](data-factory-functions-variables.md) Hodnota pro DataFactoryName je pevně zakódována na CustomActivityFactory.

Chcete-li získat přístup k těmto rozšířeným vlastnostem v metodě **Execute,** použijte kód podobný následujícímu kódu:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Automatické škálování Azure Batch
Můžete také vytvořit fond Azure Batch s funkcí **automatického škálování.** Můžete například vytvořit fond dávek azure s 0 vyhrazenými virtuálními počítači a vzorcem automatického škálování na základě počtu čekajících úloh.

Ukázkový vzorec zde dosahuje následující chování: Při fondu je původně vytvořen, začíná s 1 virtuální mk. $PendingTasks metrika definuje počet úloh ve spuštěném + aktivním (zařazeném) stavu.  Vzorec vyhledá průměrný počet čekajících úkolů v posledních 180 sekundách a podle toho nastaví TargetDedicated. Zajišťuje, že TargetDedicated nikdy přesahuje 25 virtuálních ms. Takže jako nové úkoly jsou odesílány, fond automaticky roste a jako úkoly dokončit, virtuální chod se jeden po druhém zdarma a automatické škálování zmenšuje tyto virtuální chod. startingNumberOfVMs a maxNumberofVMs lze přizpůsobit vašim potřebám.

Vzorec automatického škálování:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Podrobnosti najdete [v tématu Automatické škálování výpočetních uzlů ve fondu Azure Batch.](../../batch/batch-automatic-scaling.md)

Pokud fond používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může před spuštěním vlastní aktivity trvat 15 až 30 minut, než se virtuální počítač připraví.  Pokud fond používá jiný autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Vytvoření vlastní aktivity pomocí sady .NET SDK
V návodu v tomto článku vytvoříte továrnu dat s kanálem, který používá vlastní aktivitu pomocí portálu Azure. Následující kód ukazuje, jak vytvořit továrnu dat pomocí sady .NET SDK místo. Další podrobnosti o použití sady SDK k programovému vytváření kanálů v kanálu s aktivitou kopírování naleznete pomocí článku [rozhraní .NET API.](data-factory-copy-activity-tutorial-using-dotnet-api.md)

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Ladění vlastní aktivity v sadě Visual Studio
Azure Data Factory – ukázka [místního prostředí](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) na GitHubu obsahuje nástroj, který umožňuje ladit vlastní aktivity .NET v rámci sady Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Ukázka vlastních aktivit na GitHubu
| Ukázka | Co dělá vlastní aktivita |
| --- | --- |
| [Http Data Downloader](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Stáhne data z koncového bodu HTTP do úložiště objektů blob Azure pomocí vlastní aktivity Jazyka C# v datové továrně. |
| [Ukázka analýzy mínění na Twitteru](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Vyvolá model studia Azure Machine Learning a provést analýzu mínění, vyhodnocování, předpověď atd. |
| [Spusťte skript R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Vyvolá skript R spuštěním programu RScript.exe v clusteru HDInsight, ve které je již nainstalováno r. |
| [Aktivita mezi doménami .NET](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Používá různé verze sestavení z verzí používaných spouštěč Data Factory |
| [Opětovné zpracování modelu ve službě Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Znovu zpracuje model ve službě Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
