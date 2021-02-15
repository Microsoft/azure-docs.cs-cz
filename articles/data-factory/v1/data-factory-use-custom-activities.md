---
title: Použití vlastních aktivit v kanálu Azure Data Factory
description: Naučte se vytvářet vlastní aktivity a používat je v kanálu Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 3832175910f3a6d3e6a7de8da932b32436cc2452
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393016"
---
# <a name="use-custom-activities-in-an-azure-data-factory-version-1-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory verze 1
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-use-custom-activities.md)
> * [Verze 2 (aktuální verze)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [vlastní aktivity v v2](../transform-data-using-dotnet-custom-activity.md).

Existují dva typy aktivit, které můžete použít v kanálu Azure Data Factory.

- [Aktivity přesunu dat](data-factory-data-movement-activities.md) pro přesun dat mezi [podporovanými úložišti dat a úložišti dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](data-factory-data-transformation-activities.md) pro transformaci dat pomocí výpočetních služeb, jako je Azure HDInsight, Azure Batch a Azure Machine Learning Studio (Classic).

Pokud chcete přesunout data do nebo z úložiště dat, které Data Factory nepodporuje, vytvořte **vlastní aktivitu** s vlastní logikou přesunu dat a použijte aktivitu v kanálu. Podobně pokud chcete transformovat a zpracovávat data způsobem, který není podporován Data Factory, vytvořte vlastní aktivitu s vlastní logikou transformace dat a použijte aktivitu v kanálu.

Vlastní aktivitu můžete nakonfigurovat tak, aby běžela ve fondu **Azure Batch** virtuálních počítačů. Při použití Azure Batch můžete použít jenom existující fond Azure Batch.

Následující návod poskytuje podrobné pokyny k vytvoření vlastní aktivity rozhraní .NET a použití vlastní aktivity v kanálu. Návod používá propojenou službu **Azure Batch** .

> [!IMPORTANT]
> - Pro přístup k místním zdrojům dat není možné použít bránu Správa dat z vlastní aktivity. V současné době [Správa dat brána](data-factory-data-management-gateway.md) podporuje pouze aktivitu kopírovat aktivitu a uloženou proceduru v Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Návod: Vytvoření vlastní aktivity
### <a name="prerequisites"></a>Požadavky
* Visual Studio 2012/2013/2015/2017
* Stažení a instalace [sady Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Azure Batch předpoklady
V tomto návodu spustíte vlastní aktivity rozhraní .NET pomocí Azure Batch jako výpočetní prostředek. **Azure Batch** je služba platformy pro efektivní spouštění rozsáhlých paralelních aplikací a aplikací vysoce výkonného výpočetního prostředí (HPC) v cloudu. Azure Batch plánuje práci náročné na výpočetní výkon na spravované **kolekci virtuálních počítačů** a dokáže automaticky škálovat výpočetní prostředky tak, aby splňovaly potřeby vašich úloh. Podrobný přehled služby Azure Batch najdete v článku [Azure Batch základních][batch-technical-overview] informací.

V tomto kurzu vytvořte účet Azure Batch s fondem virtuálních počítačů. Tady je postup:

1. Vytvořte **účet Azure Batch** pomocí [Azure Portal](https://portal.azure.com). Pokyny najdete v článku [Vytvoření a Správa účtu Azure Batch][batch-create-account] .
2. Poznamenejte si Azure Batch název účtu, klíč účtu, identifikátor URI a název fondu. Budete je potřebovat k vytvoření propojené služby Azure Batch.
    1. Na domovské stránce Azure Batch účtu se zobrazí **Adresa URL** v následujícím formátu: `https://myaccount.westus.batch.azure.com` . V tomto příkladu je **MyAccount** název účtu Azure Batch. Identifikátor URI, který použijete v definici propojené služby, je adresa URL bez názvu účtu. Příklad: `https://<region>.batch.azure.com`.
    2. V nabídce vlevo klikněte na **klíče** a ZKOPÍRUJTE **Primární přístupový klíč**.
    3. Pokud chcete použít existující fond, klikněte v nabídce na **fondy** a poznamenejte si **ID** fondu. Pokud nemáte existující fond, přejděte k dalšímu kroku.
2. Vytvořte **fond Azure Batch**.

   1. V [Azure Portal](https://portal.azure.com)klikněte v levé nabídce na **Procházet** a pak klikněte na **účty Batch**.
   2. Vyberte účet Azure Batch, abyste otevřeli okno **účtu Batch** .
   3. Klikněte na dlaždice **fondy** .
   4. V okně **fondy** kliknutím na tlačítko Přidat na panelu nástrojů přidejte fond.
      1. Zadejte ID fondu (ID fondu). Poznamenejte si **ID fondu**; budete ho potřebovat při vytváření řešení Data Factory.
      2. Pro nastavení řady operačních systémů zadejte **Windows Server 2012 R2** .
      3. Vyberte **cenovou úroveň uzlu**.
      4. Jako hodnotu pro **vyhrazené** nastavení zadejte **2** .
      5. Jako hodnotu pro nastavení **maximální počet úkolů na uzel** zadejte **2** .
   5. Kliknutím na tlačítko **OK** vytvořte fond.
   6. Poznamenejte si **ID** fondu.

### <a name="high-level-steps"></a>Základní kroky
Tady jsou dva kroky vysoké úrovně, které provedete v rámci tohoto návodu:

1. Vytvořte vlastní aktivitu, která obsahuje jednoduchou logiku transformace a zpracování dat.
2. Vytvořte datovou továrnu Azure s kanálem, který používá vlastní aktivitu.

### <a name="create-a-custom-activity"></a>Vytvoření vlastní aktivity
Chcete-li vytvořit vlastní aktivitu rozhraní .NET, vytvořte projekt **knihovny tříd .NET** s třídou, která implementuje rozhraní **IDotNetActivity** . Toto rozhraní obsahuje pouze jednu metodu: [Execute](/dotnet/api/microsoft.azure.management.datafactories.runtime.idotnetactivity) a její signatura je:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Metoda přijímá čtyři parametry:

- **linkedServices**. Tato vlastnost je Výčtový seznam propojených služeb úložiště dat, na které odkazují vstupní a výstupní datové sady pro aktivitu.
- **datové sady**. Tato vlastnost je vyčíslitelné seznam vstupních/výstupních datových sad pro aktivitu. Tento parametr můžete použít k získání umístění a schémat definovaných vstupními a výstupními datovými sadami.
- **aktivita**. Tato vlastnost reprezentuje aktuální aktivitu. Dá se použít pro přístup k rozšířeným vlastnostem přidruženým k vlastní aktivitě. Podrobnosti najdete v tématu věnovaném [rozšířeným vlastnostem přístupu](#access-extended-properties) .
- **protokolovací** nástroj. Tento objekt umožňuje psát poznámky k ladění, které jsou v protokolu uživatele pro kanál.

Metoda vrací slovník, který lze použít k zřetězení vlastních aktivit společně v budoucnu. Tato funkce ještě není implementovaná, proto z metody vraťte prázdný slovník.

### <a name="procedure"></a>Postup

1. Vytvořte projekt **knihovny tříd .NET** .
   
    <ol type="a">
     <li>Spusťte Visual Studio.</li>
     <li>Klikněte na <b>Soubor</b>, přejděte na <b>Nový</b> a klikněte na <b>Projekt</b>.</li>
     <li>Rozbalte <b>Šablony</b> a vyberte <b>Visual C#</b>. V tomto návodu použijete C#, ale můžete použít libovolný jazyk .NET k vývoji vlastní aktivity.</li>
     <li>V seznamu typů projektů napravo vyberte <b>Knihovna tříd</b> . V aplikaci Visual Studio vyberte možnost <b>Knihovna tříd (.NET Framework)</b> </li>
     <li>Jako <b>název</b>zadejte <b>MyDotNetActivity</b> .</li>
     <li>Jako <b>umístění</b>vyberte <b>C:\ADFGetStarted</b> .</li>
     <li>Kliknutím na tlačítko <b>OK</b> vytvořte projekt.</li>
   </ol>

2. Klikněte na **nástroje**, přejděte na **Správce balíčků NuGet** a klikněte na **Konzola správce balíčků**.

3. V konzole správce balíčků spusťte následující příkaz pro import **Microsoft. Azure. Management. Datafactorys**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```

4. Importujte balíček NuGet **Azure Storage** do projektu.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Spouštěč služby Data Factory vyžaduje verzi 4,3 WindowsAzure. Storage. Pokud přidáte odkaz na novější verzi Azure Storage sestavení v projektu vlastní aktivity, zobrazí se chyba, když se aktivita spustí. Chcete-li tuto chybu vyřešit, přečtěte si část věnované [izolaci třídy AppDomain](#appdomain-isolation) .
5. Přidejte následující příkazy **using** do zdrojového souboru v projektu.

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

7. Změňte název třídy na **MyDotNetActivity** a odvodit ji z rozhraní **IDotNetActivity** , jak je znázorněno v následujícím fragmentu kódu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```

8. Implementujte (přidejte) metodu **Execute** rozhraní **IDotNetActivity** do třídy **MyDotNetActivity** a zkopírujte následující vzorový kód do metody.

    Následující ukázka spočítá počet výskytů hledaného termínu ("Microsoft") v jednotlivých objektech blob přidružených k datovému řezu.

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
            // the search term ("Microsoft") in each blob associated
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

    Metoda GetFolderPath vrací cestu ke složce, na kterou datová sada odkazuje, a metoda getfiletable vrátí název objektu BLOB nebo souboru, na který datová sada odkazuje. Pokud jste folderPath definovali pomocí proměnných, jako je {year}, {month}, {Day} atd., metoda vrátí řetězec, protože je bez jejich nahrazení hodnotami za běhu. Podrobnosti o přístupu k vlastnosti slicestart, SliceEnd atd. najdete v části [přístup k rozšířeným vlastnostem](#access-extended-properties) .

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda výpočtu vypočítá počet instancí klíčového slova Microsoft ve vstupních souborech (objekty blob ve složce). Hledaný termín ("Microsoft") je pevně kódovaný v kódu.

10. Zkompilujte projekt. V nabídce klikněte na **sestavit** a pak klikněte na **Sestavit řešení**.

    > [!IMPORTANT]
    > Nastavte verzi 4.5.2 .NET Framework jako cílovou architekturu pro váš projekt: klikněte pravým tlačítkem myši na projekt a kliknutím na **vlastnosti** nastavte cílovou architekturu. Data Factory nepodporuje vlastní aktivity zkompilované proti .NET Framework verzím novějším než 4.5.2.

11. Spusťte **Průzkumníka Windows** a přejděte do složky **bin\Debug** nebo **bin\Release** v závislosti na typu sestavení.

12. Vytvořte soubor zip **MyDotNetActivity.zip** , který obsahuje všechny binární soubory ve \<project folder\> složce \bin\debug. Zahrňte soubor **MyDotNetActivity. pdb** , abyste získali další podrobnosti, jako je číslo řádku ve zdrojovém kódu, který způsobil problém, pokud došlo k chybě.

    > [!IMPORTANT]
    > Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.

    ![Binární výstupní soubory](./media/data-factory-use-custom-activities/Binaries.png)

13. Vytvořte kontejner objektů BLOB s názvem **customactivitycontainer** , pokud ještě neexistuje.

14. Nahrajte MyDotNetActivity.zip jako objekt blob do customactivitycontainer ve službě Azure Blob Storage pro **obecné účely** (ne za horkou/studenou službu BLOB Storage), na kterou odkazuje AzureStorageLinkedService.

> [!IMPORTANT]
> Pokud přidáte tento projekt aktivity rozhraní .NET do řešení v aplikaci Visual Studio, které obsahuje projekt Data Factory a přidáte odkaz na projekt aktivity rozhraní .NET z projektu Data Factory aplikace, není nutné provádět poslední dva kroky ručního vytvoření souboru zip a odeslat ho do úložiště objektů BLOB pro obecné účely. Při publikování Data Factory entit pomocí sady Visual Studio jsou tyto kroky automaticky provedeny procesem publikování. Další informace naleznete v části [Data Factory projektu v aplikaci Visual Studio](#data-factory-project-in-visual-studio) .

## <a name="create-a-pipeline-with-custom-activity"></a>Vytvoření kanálu s vlastní aktivitou

Vytvořili jste vlastní aktivitu a nahráli soubor zip s binárními soubory do kontejneru objektů BLOB v rámci účtu Azure Storage pro **obecné účely** . V této části vytvoříte objekt pro vytváření dat Azure s kanálem, který používá vlastní aktivitu.

Vstupní datová sada pro vlastní aktivitu představuje objekty BLOB (soubory) ve složce customactivityinput kontejneru adftutorial v úložišti objektů BLOB. Výstupní datová sada pro aktivitu představuje výstupní objekty blob ve složce customactivityoutput kontejneru adftutorial v úložišti objektů BLOB.

Vytvořte **file.txt** soubor s následujícím obsahem a nahrajte ho do složky **customactivityinput** kontejneru **adftutorial** . Vytvořte kontejner adftutorial, pokud ještě neexistuje.

```
test custom activity Microsoft test custom activity Microsoft
```

Vstupní složka odpovídá řezu v Azure Data Factory i v případě, že složka obsahuje dva nebo více souborů. Když kanál zpracovává jednotlivé řezy, prochází vlastní aktivita všemi objekty blob ve vstupní složce pro daný řez.

Ve složce adftutorial\customactivityoutput se zobrazí jeden výstupní soubor s jedním nebo více řádky (stejný jako počet objektů BLOB ve vstupní složce):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Tady jsou kroky, které provedete v této části:

1. Vytvořte **datovou továrnu**.
2. Vytvořte **propojené služby** pro fond Azure Batch virtuálních počítačů, na kterých běží vlastní aktivita, a Azure Storage, která obsahuje vstupní a výstupní objekty blob.
3. Vytvořte vstupní a výstupní **datové sady** , které reprezentují vstup a výstup vlastní aktivity.
4. Vytvořte **kanál** , který používá vlastní aktivitu.

> [!NOTE]
> Vytvořte **file.txt** a nahrajte ho do kontejneru objektů blob, pokud jste to ještě neudělali. Přečtěte si pokyny v předchozí části.

### <a name="step-1-create-the-data-factory"></a>Krok 1: vytvoření datové továrny
1. Po přihlášení k Azure Portal proveďte následující kroky:
   1. V nabídce vlevo klikněte na **vytvořit prostředek** .
   2. V okně **Nový** klikněte na **data a analýzy** .
   3. V okně **Analýza dat** klikněte na **Objekt pro vytváření dat**.

      ![Nabídka nový Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. V okně **Nová datová továrna** jako název zadejte **CustomActivityFactory** . Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **název objektu pro vytváření dat "CustomActivityFactory" není k dispozici**, změňte název objektu pro vytváření dat (třeba na **yournameCustomActivityFactory**) a zkuste to znovu.

    ![Okno Nový Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klikněte na **název skupiny prostředků** a vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků.
4. Ověřte, že používáte správné **předplatné** a **oblast** , kde chcete vytvořit datovou továrnu.
5. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
6. Na **řídicím panelu** Azure Portal se zobrazí objekt pro vytváření dat.
7. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí okno Data Factory, ve kterém se zobrazí obsah objektu pro vytváření dat.

    ![Okno Objekt pro vytváření dat](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2: Vytvoření propojených služeb
Propojené služby propojují úložiště dat nebo výpočetní služby s objektem pro vytváření dat Azure. V tomto kroku propojíte účet Azure Storage a Azure Batch účet k datové továrně.

#### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. Klikněte na dlaždici **Autor a nasazení** v okně **objekt pro vytváření dat** pro **CustomActivityFactory**. Zobrazí se editor služby Data Factory.
2. Na panelu příkazů klikněte na **nové úložiště dat** a vyberte **Azure Storage**. V editoru by se měl zobrazit skript JSON pro vytvoření propojené služby Azure Storage.

    ![Nové úložiště dat – Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Nahraďte `<accountname>` názvem účtu služby Azure Storage a `<accountkey>` klíčovým klíčem k účtu úložiště Azure. Informace o tom, jak získat přístupový klíč k úložišti, najdete v tématu [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

    ![Služba Azure Storage se mi líbí](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Propojenou službu nasadíte kliknutím na **Nasadit** na panelu příkazů.

#### <a name="create-azure-batch-linked-service"></a>Vytvoření propojené služby Azure Batch
1. V editoru Data Factory klikněte na **... Další** na panelu příkazů klikněte na **Nový COMPUTE** a v nabídce vyberte **Azure Batch** .

    ![Nové výpočetní prostředí – Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Proveďte následující změny skriptu JSON:

   1. Zadejte Azure Batch název účtu pro vlastnost **účtu** . **Adresa URL** z okna **Azure Batch účtu** je v následujícím formátu: `http://accountname.region.batch.azure.com` . Pro vlastnost **batchUri** ve formátu JSON je nutné odebrat `accountname.` z adresy URL a použít `accountname` `accountName` vlastnost for JSON.
   2. Zadejte Azure Batch klíč účtu pro vlastnost **AccessKey** .
   3. Zadejte název fondu, který jste vytvořili jako součást požadavků pro vlastnost **Pool** . Místo názvu fondu můžete zadat také ID fondu.
   4. Zadejte Azure Batch identifikátor URI pro vlastnost **batchUri** . Příklad: `https://westus.batch.azure.com`.
   5. Zadejte **AzureStorageLinkedService** pro vlastnost **linkedServiceName** .

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

       Pro vlastnost název **fondu** můžete zadat také ID fondu místo názvu fondu.

### <a name="step-3-create-datasets"></a>Krok 3: vytvoření datových sad
V tomto kroku vytvoříte datové sady, které reprezentují vstupní a výstupní data.

#### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
1. V **editoru** Data Factory klikněte na **... Další** na panelu příkazů klikněte na **Nová datová sada** a v rozevírací nabídce vyberte **Azure Blob Storage** .
2. Nahraďte kód JSON v pravém podokně následujícím fragmentem kódu JSON:

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

   Kanál vytvoříte později v tomto návodu s časem spuštění: 2016-11-16T00:00:00Z a koncový čas: 2016-11-16T05:00:00Z. Naplánovalo se zpracování dat každou hodinu, takže je pět vstupních/výstupních řezů (mezi **00**: 00:00-> **05**: 00:00).

   **Frekvence** a **interval** pro vstupní datovou sadu je nastavená na **Hour** a **1**, což znamená, že vstupní řez je dostupný každou hodinu. V této ukázce se jedná o stejný soubor (file.txt) v intputfolder.

   Zde jsou časy spuštění každého řezu, který je reprezentován systémovou proměnnou vlastnosti slicestart ve výše uvedeném fragmentu kódu JSON.
3. Kliknutím na **nasadit** na panelu nástrojů vytvořte a nasaďte rozhraní **InputDataset**. Potvrďte, že se v záhlaví okna editoru zobrazila zpráva **ÚSPĚŠNÉ VYTVOŘENÍ TABULKY**.

#### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
1. V **editoru Data Factory** klikněte na **... Další** na panelu příkazů klikněte na **Nová datová sada** a vyberte **Azure Blob Storage**.
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

     Umístění výstupu je **adftutorial/customactivityoutput/** a název výstupního souboru je yyyy-MM-dd-HH.txt, kde RRRR-MM-DD-hh představuje rok, měsíc, datum a hodinu vyráběného řezu. Podrobnosti najdete v tématu Referenční informace pro [vývojáře][adf-developer-reference] .

    Pro každý vstupní řez se vygeneruje výstupní objekt BLOB nebo soubor. Tady je způsob, jak je výstupní soubor pojmenován pro každý řez. Všechny výstupní soubory jsou vygenerovány v jedné výstupní složce: **adftutorial\customactivityoutput**.

   | Průřez | Čas spuštění | Výstupní soubor |
   |:--- |:--- |:--- |
   | 1 |2016-11 – 16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11 – 16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11 – 16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11 – 16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11 – 16T04:00:00 |2016-11-16-04.txt |

    Mějte na paměti, že všechny soubory ve vstupní složce jsou součástí řezu s časy zahájení uvedenými výše. Při zpracování tohoto řezu vlastní aktivita projde jednotlivé soubory a vytvoří ve výstupním souboru řádek s počtem výskytů hledaného výrazu ("Microsoft"). Pokud vstupní složka obsahuje tři soubory, ve výstupním souboru jsou tři řádky pro každý hodinový řez: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt atd.
3. **OutputDataset** nasadíte kliknutím na **nasadit** na panelu příkazů.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Vytvoření a spuštění kanálu, který používá vlastní aktivitu
1. V editoru Data Factory klikněte na **... A potom na panelu** příkazů vyberte **Nový kanál** .
2. Nahraďte kód JSON v pravém podokně následujícím skriptem JSON:

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

   * **Concurrency** je nastavená na **2** , takže dva řezy se zpracovávají paralelně o 2 virtuální počítače ve fondu Azure Batch.
   * Oddíl Activities obsahuje jednu aktivitu a je typu: **DotNetActivity**.
   * **AssemblyName** je nastaven na název knihovny DLL: **MyDotnetActivity.dll**.
   * **Parametr entryPoint** je nastavený na **MyDotNetActivityNS. MyDotNetActivity**.
   * **PackageLinkedService** je nastavená na **AzureStorageLinkedService** , která odkazuje na úložiště objektů blob, které obsahuje soubor zip vlastní aktivity. Pokud pro vstupní/výstupní soubory a soubor zip vlastní aktivity používáte jiné účty Azure Storage, vytvoříte další Azure Storage propojenou službu. V tomto článku se předpokládá, že používáte stejný účet Azure Storage.
   * **PackageFile** je nastavená na **customactivitycontainer/MyDotNetActivity.zip**. Má formát: containerforthezip/nameofthezip.zip.
   * Vlastní aktivita přijímá **InputDataset** jako vstup a **OutputDataset** jako výstup.
   * Vlastnost linkedServiceName vlastní aktivity odkazuje na **AzureBatchLinkedService**, která oznamuje Azure Data Factory, že vlastní aktivita musí běžet na Azure Batch virtuálních počítačích.
   * vlastnost s **pozastaveným** nastavením je ve výchozím nastavení nastavena na **hodnotu false** . Kanál se spustí hned v tomto příkladu, protože řezy začínají v minulosti. Tuto vlastnost můžete nastavit na hodnotu true, chcete-li kanál pozastavit a nastavit jej zpět na hodnotu false pro restartování.
   * Čas **zahájení** a **ukončení** je **pět** hodin od a řezy se vytvářejí každou hodinu, takže kanál vytvoří pět řezů.
3. Pokud chcete kanál nasadit, klikněte na panelu příkazů na **nasadit** .

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. V okně Data Factory v Azure Portal klikněte na **diagram**.

    ![Dlaždice Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. V zobrazení diagramu nyní klikněte na OutputDataset.

    ![Zobrazení diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Měli byste vidět, že pět výstupních řezů je ve stavu připraveno. Pokud nejsou ve stavu připraveno, zatím se nevytvořily.

   ![Výstupní řezy](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Ověřte, že se ve službě BLOB Storage v kontejneru **adftutorial** generují výstupní soubory.

   ![výstup z vlastní aktivity][image-data-factory-output-from-custom-activity]
5. Pokud otevřete výstupní soubor, měl by se zobrazit výstup podobný následujícímu výstupu:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Pomocí rutin [Azure Portal][azure-preview-portal] nebo Azure PowerShell můžete monitorovat objekt pro vytváření dat, kanály a datové sady. Zprávy z **ActivityLogger** můžete zobrazit v kódu vlastní aktivity v protokolech (konkrétně User-0. log), které si můžete stáhnout z portálu nebo pomocí rutin.

   ![Stáhnout protokoly z vlastní aktivity][image-data-factory-download-logs-from-custom-activity]

Podrobné pokyny k monitorování datových sad a kanálů najdete v tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md) .

## <a name="data-factory-project-in-visual-studio"></a>Data Factory projektu v aplikaci Visual Studio
Pomocí sady Visual Studio můžete vytvářet a publikovat Data Factory entity místo použití Azure Portal. Podrobné informace o vytváření a publikování entit Data Factory pomocí sady Visual Studio najdete v tématu [Vytvoření prvního kanálu pomocí sady Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) a [zkopírování dat z Azure Blob do Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) .

Pokud vytváříte Data Factory projekt v aplikaci Visual Studio, proveďte následující dodatečné kroky:

1. Přidejte Data Factory projektu do řešení sady Visual Studio, které obsahuje projekt vlastní aktivity.
2. Přidejte odkaz na projekt aktivity rozhraní .NET z Data Factory projektu. Klikněte pravým tlačítkem na Data Factory projekt, přejděte na **Přidat** a klikněte na **odkaz**.
3. V dialogovém okně **Přidat odkaz** vyberte projekt **MyDotNetActivity** a klikněte na tlačítko **OK**.
4. Sestavte a publikujte řešení.

    > [!IMPORTANT]
    > Když publikujete Data Factory entit, automaticky se vytvoří soubor zip, který se nahraje do kontejneru objektů BLOB: customactivitycontainer. Pokud kontejner objektů BLOB neexistuje, vytvoří se automaticky.

## <a name="data-factory-and-batch-integration"></a>Integrace Data Factory a Batch
Služba Data Factory vytvoří úlohu v Azure Batch s názvem: **ADF-pool: Job-XXX**. V nabídce vlevo klikněte na **úlohy** .

![Azure Data Factory – dávkové úlohy](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Úkol se vytvoří pro každé spuštění aktivity řezu. V případě, že je připraveno zpracování pěti řezů, v této úloze se vytvoří pět úkolů. Pokud je ve fondu dávek více výpočetních uzlů, lze souběžně spustit dva nebo více řezů. Pokud je maximální počet úkolů na výpočetní uzel nastaven na > 1, může být ve stejném výpočetním prostředí spuštěno více než jeden řez.

![Azure Data Factory – úlohy dávkové úlohy](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Následující diagram znázorňuje vztah mezi Azure Data Factory a úlohami Batch.

![Batch Data Factory &](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Řešení chyb
Řešení potíží se skládá z několika základních technik:

1. Pokud se zobrazí následující chyba, můžete místo používání služby Azure Blob Storage pro obecné účely použít horké nebo studené úložiště objektů BLOB. Nahrajte soubor zip na **účet pro obecné účely Azure Storage**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Pokud se zobrazí následující chyba, zkontrolujte, že název třídy v souboru CS odpovídá názvu, který jste zadali pro vlastnost **EntryPoint** v kanálu JSON. V tomto návodu je název třídy: MyDotNetActivity a parametr EntryPoint ve formátu JSON je: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Pokud se názvy shodují, potvrďte, že všechny binární soubory jsou v **kořenové složce** souboru ZIP. To znamená, že při otevření souboru ZIP by se měly zobrazit všechny soubory v kořenové složce, nikoli v žádné podsložkách.
3. Pokud vstupní řez není nastavený na **připraveno**, zkontrolujte, jestli je struktura vstupní složky správná a **file.txt** existuje ve vstupních složkách.
3. V metodě **Execute** vlastní aktivity použijte objekt **IActivityLogger** k protokolování informací, které vám pomůžou vyřešit problémy. Protokolované zprávy se zobrazí v souborech protokolu uživatele (jeden nebo více souborů s názvem: User-0. log, User-1. log, User-2. log atd.).

   V okně **OutputDataset** klikněte na řez a zobrazte okno **datový řez** pro daný řez. U tohoto řezu vidíte **spuštění aktivit** . Měl by se zobrazit jeden běh aktivity pro řez. Pokud kliknete na tlačítko spustit na panelu příkazů, můžete spustit další spuštění aktivit pro stejný řez.

   Po kliknutí na spuštění aktivity se zobrazí okno Podrobnosti o **spuštění aktivity** se seznamem souborů protokolu. Zaznamenané zprávy se zobrazí v souboru user_0. log. Pokud dojde k chybě, zobrazí se tři spuštění aktivit, protože počet opakování je v souboru JSON kanálu nebo aktivity nastavený na 3. Když kliknete na spuštění aktivity, zobrazí se soubory protokolů, které můžete zkontrolovat a vyřešit chybu.

   V seznamu souborů protokolu klikněte na **User-0. log**. V pravém panelu jsou výsledky použití metody **IActivityLogger. Write** . Pokud nevidíte všechny zprávy, zkontrolujte, jestli máte víc souborů protokolu s názvem: user_1. log, user_2. log atd. V opačném případě se kód pravděpodobně nezdařil po poslední zaznamenané zprávě.

   Kromě toho ověřte **protokol System-0. log** pro všechny systémové chybové zprávy a výjimky.
4. Zahrňte soubor **PDB** do souboru zip, aby podrobnosti o chybě obsahovaly informace, jako je například **zásobník volání** , když dojde k chybě.
5. Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.
6. Zajistěte, aby byl parametr **AssemblyName** (MyDotNetActivity.dll), **EntryPoint**(MyDotNetActivityNS. MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) a **packageLinkedService** (měl by odkazovat na úložiště objektů blob Azure pro **obecné účely**, který obsahuje soubor ZIP), nastaven na správné hodnoty.
7. Pokud jste opravili chybu a chcete řez zpracovat znovu, klikněte na něj v okně **OutputDataset** pravým tlačítkem myši a potom klikněte na **Spustit**.
8. Pokud se zobrazí následující chyba, používáte balíček Azure Storage verze > 4.3.0. Spouštěč služby Data Factory vyžaduje verzi 4,3 WindowsAzure. Storage. Pokud je nutné použít novější verzi Azure Storage sestavení, přečtěte si téma věnované [izolaci domény AppDomain](#appdomain-isolation) pro práci.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Pokud můžete použít verzi 4.3.0 balíčku Azure Storage, odeberte existující odkaz na balíček Azure Storage verze > 4.3.0. Pak spusťte následující příkaz z konzoly Správce balíčků NuGet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Sestavte projekt. Odstraňte sestavení Azure. Storage verze > 4.3.0 ze složky bin\Debug. Vytvořte soubor zip s binárními soubory a souborem PDB. Nahraďte starý soubor zip tímto souborem v kontejneru objektů BLOB (customactivitycontainer). Znovu spusťte řezy, které selhaly (klikněte pravým tlačítkem myši na řez), a pak klikněte na spustit.
8. Vlastní aktivita nepoužívá soubor **app.config** ze svého balíčku. Proto pokud váš kód přečte všechny připojovací řetězce z konfiguračního souboru, nefunguje za běhu. Nejlepším postupem při použití Azure Batch je uložení tajných klíčů v **trezoru klíčů Azure**, použití instančního objektu založeného na certifikátu k ochraně **trezoru** klíčů a distribuce certifikátu do fondu Azure Batch. Vlastní aktivita .NET potom má přístup k tajným klíčům v trezoru za běhu. Toto řešení je obecné řešení a může se škálovat na libovolný typ tajného kódu, nikoli jenom na připojovací řetězec.

   Existuje jednodušší řešení (ale ne doporučený postup): můžete vytvořit **propojenou službu Azure SQL** s nastavením připojovacího řetězce, vytvořit datovou sadu, která používá propojenou službu, a zřetězit datovou sadu jako fiktivní vstupní datovou sadu do vlastní aktivity .NET. Pak můžete přístup k připojovacímu řetězci propojené služby v kódu vlastní aktivity.

## <a name="update-custom-activity"></a>Aktualizace vlastní aktivity
Pokud aktualizujete kód vlastní aktivity, sestavíte ho a nahrajete soubor zip, který obsahuje nové binární soubory, do úložiště objektů BLOB.

## <a name="appdomain-isolation"></a>Izolovaná doména AppDomain
Viz [Ukázka mezi](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) doménami AppDomain, která ukazuje, jak vytvořit vlastní aktivitu, která není omezená na verze sestavení používané spouštěčem Data Factory (příklad: windowsazure. Storage v 4.3.0, Newtonsoft.Json v 6.0. x atd.).

## <a name="access-extended-properties"></a>Přístup k rozšířeným vlastnostem
Můžete deklarovat rozšířené vlastnosti v kódu JSON aktivity, jak je znázorněno v následující ukázce:

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

V tomto příkladu jsou k dispozici dvě rozšířené vlastnosti: **vlastnosti slicestart** a **DataFactory**. Hodnota pro vlastnosti slicestart je založena na systémové proměnné vlastnosti slicestart. Seznam podporovaných systémových proměnných najdete v tématu [systémové proměnné](data-factory-functions-variables.md) . Hodnota vlastnosti DataFactory je pevně zakódována na CustomActivityFactory.

Pro přístup k těmto rozšířeným vlastnostem v metodě **Execute** použijte kód podobný následujícímu kódu:

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
Můžete také vytvořit fond Azure Batch s funkcí **automatického škálování** . Můžete například vytvořit fond služby Azure Batch s 0 vyhrazenými virtuálními počítači a vzorcem automatického škálování na základě počtu nevyřízených úkolů.

Vzorový vzorec v tomto případě dosáhne následujícího chování: při počátečním vytvoření fondu se spustí s 1 virtuálním počítačem. $PendingTasksová metrika definuje počet úloh ve stavu spuštěno a aktivní (ve frontě).  Vzorec nalezne průměrný počet nedokončených úloh za posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy nepřekračuje 25 virtuálních počítačů. Když se tedy odešlou nové úlohy, fond se automaticky rozrůstá a jakmile se dokončí úkoly, virtuální počítače se uvolní jednou a automatické škálování tyto virtuální počítače zmenší. startingNumberOfVMs a maxNumberofVMs je možné upravit podle svých potřeb.

Vzorec automatického škálování:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Podrobnosti najdete v tématu [Automatické škálování výpočetních uzlů ve fondu Azure Batch](../../batch/batch-automatic-scaling.md) .

Pokud fond používá výchozí [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale), může služba Batch trvat 15-30 minut, než se virtuální počítač připraví před spuštěním vlastní aktivity.  Pokud fond používá jiný autoScaleEvaluationInterval, může služba Batch trvat autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Vytvoření vlastní aktivity pomocí sady .NET SDK
V návodu v tomto článku vytvoříte datovou továrnu s kanálem, který používá vlastní aktivitu pomocí Azure Portal. Následující kód ukazuje, jak vytvořit datovou továrnu pomocí .NET SDK. Další podrobnosti o používání sady SDK k programovému vytváření kanálů najdete v článku [vytvoření kanálu s aktivitou kopírování pomocí rozhraní .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) .

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

## <a name="debug-custom-activity-in-visual-studio"></a>Ladění vlastní aktivity v aplikaci Visual Studio
Ukázka [místního prostředí Azure Data Factory](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) na GitHubu obsahuje nástroj, který umožňuje ladit vlastní aktivity .NET v rámci sady Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Ukázkové vlastní aktivity na GitHubu
| Ukázka | Co dělá vlastní aktivita |
| --- | --- |
| [Stažení dat http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Stáhne data z koncového bodu HTTP do Azure Blob Storage pomocí vlastní aktivity C# v Data Factory. |
| [Ukázka Twitteru Analýza mínění](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Vyvolá model Azure Machine Learning Studio (Classic) a provede mínění analýzy, bodování, předpověď atd. |
| [Spusťte skript jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Vyvolá skript R spuštěním RScript.exe v clusteru HDInsight, na kterém už je nainstalovaný jazyk R. |
| [Aktivita aplikace .NET pro více domén](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Používá různé verze sestavení z těch, které používá spouštěč Data Factory. |
| [Znovu zpracovat model v Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Znovu zpracuje model v Azure Analysis Services. |

[batch-net-library]: ../../batch/quick-run-dotnet.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]:../../azure-sql/database/sql-database-paas-overview.md
[batch-get-started]: ../../batch/quick-run-dotnet.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: /previous-versions/azure/mt125880(v=azure.100)
[new-azure-batch-pool]: /previous-versions/azure/mt125936(v=azure.100)
[azure-batch-blog]: /archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png