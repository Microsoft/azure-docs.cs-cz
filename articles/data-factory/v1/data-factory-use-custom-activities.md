---
title: Použití vlastních aktivit v kanálu Azure Data Factory
description: Zjistěte, jak vytvořit vlastní aktivity a použít je v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 580dd5bf6a7e905927189f4b1ae42ab49a1cbc80
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730708"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-use-custom-activities.md)
> * [Verze 2 (aktuální verze)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [vlastní aktivity ve V2](../transform-data-using-dotnet-custom-activity.md).

Existují dva typy aktivit, které můžete použít v kanálu Azure Data Factory.

- [Aktivity přesunu dat](data-factory-data-movement-activities.md) pro přesun dat mezi [podporovanými úložišti dat zdroje a jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](data-factory-data-transformation-activities.md) k transformaci dat pomocí výpočetních služeb, jako je například Azure HDInsight, Azure Batch a Azure Machine Learning. 

Pro přesun dat do a z úložiště dat, která nepodporuje objekt pro vytváření dat, vytváření **vlastní aktivity** pomocí vlastní logiky pohybu dat a použití aktivity v kanálu. Podobně transformovat a zpracovávat data způsobem, který není podporován službou Data Factory, vytvořit vlastní aktivitu s vlastní logiku transformace dat a použití aktivity v kanálu. 

Můžete vytvořit vlastní aktivitu pro spouštění **Azure Batch** fondu virtuálních počítačů. Při použití služby Azure Batch, můžete použít pouze existujícího fondu Azure Batch.

Následující návod obsahuje podrobné pokyny pro vytváření vlastní aktivitu .NET a používání vlastní aktivity v kanálu. Návod používá **Azure Batch** propojenou službu. 

> [!IMPORTANT]
> - Není možné používat pro přístup k místním zdrojům dat brány pro správu dat z vlastní aktivity. V současné době [brána správy dat](data-factory-data-management-gateway.md) podporuje pouze aktivitu kopírování a aktivitu uložené procedury ve službě Data Factory.   

## <a name="walkthrough-create-a-custom-activity"></a>Návod: vytvoření vlastní aktivity
### <a name="prerequisites"></a>Požadavky
* Visual Studio 2012/2013/2015
* Stáhněte sadu [Azure .NET SDK](https://azure.microsoft.com/downloads/) a nainstalujte ji.

### <a name="azure-batch-prerequisites"></a>Požadavky služby Azure Batch
V tomto návodu spustit vaše vlastní aktivity .NET pomocí služby Azure Batch jako výpočetní prostředek. **Služba Azure Batch** je platformu, služby pro spouštění rozsáhlých paralelních a vysoce výkonné výpočetní (prostředí HPC) aplikace účinně v cloudu. Služba Azure Batch plánuje výpočetně náročné práce ke spuštění na spravované **kolekci virtuálních počítačů**, a dokáže automaticky škálovat výpočetní prostředky pro splnění požadavků vašich úloh. Zobrazit [Základy služby Azure Batch] [ batch-technical-overview] článku podrobnější přehled služby Azure Batch.

Pro tento kurz vytvoření účtu Azure Batch s fondem virtuálních počítačů. Postup je následující:

1. Vytvoření **účtu Azure Batch** pomocí [webu Azure portal](http://portal.azure.com). Zobrazit [vytvořit a spravovat účet Azure Batch] [ batch-create-account] článku instrukce.
2. Poznamenejte si název účtu služby Azure Batch, klíč účtu, identifikátor URI a název fondu. Je k vytvoření služby propojené služby Azure Batch budete potřebovat.
    1. Na domovské stránce účtu Azure Batch, se zobrazí **URL** v následujícím formátu: `https://myaccount.westus.batch.azure.com`. V tomto příkladu **myaccount** je název účtu služby Azure Batch. Identifikátor URI, které můžete použít v definici propojené služby je adresa URL bez názvu účtu. Například: `https://<region>.batch.azure.com`.
    2. Klikněte na tlačítko **klíče** v levé nabídce a zkopírujte **primární přístupový klíč**.
    3. Chcete-li použít existující fond, klikněte na tlačítko **fondy** v nabídce a poznamenejte si **ID** fondu. Pokud nemáte existující fond, přesune k dalšímu kroku.     
2. Vytvoření **fondu Azure Batch**.

   1. V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **Procházet** v levé nabídce a klepněte na **účty Batch**.
   2. Vyberte svůj účet Azure Batch **účtu Batch** okno.
   3. Klikněte na tlačítko **fondy** dlaždici.
   4. V **fondy** okna, klikněte na tlačítko Přidat na panelu nástrojů a přidání fondu.
      1. Zadejte ID fondu (ID fondu). Poznámka: **ID fondu**; ji budete potřebovat při vytváření řešení Data Factory.
      2. Zadejte **systému Windows Server 2012 R2** nastavení řada operačních systémů.
      3. Vyberte **cenové úrovně uzlů**.
      4. Zadejte **2** jako hodnota **cílové vyhrazené** nastavení.
      5. Zadejte **2** jako hodnota **maximální počet úkolů na uzel** nastavení.
   5. Kliknutím na tlačítko **OK** vytvořte fond.
   6. Poznamenejte si **ID** fondu. 



### <a name="high-level-steps"></a>Postup vysoké úrovně
Tady jsou dva hlavní kroky, které můžete provádět v rámci tohoto návodu: 

1. Vytvořte vlastní aktivitu, která obsahuje jednoduché datové transformaci nebo zpracování logiky.
2. Vytvoření služby Azure data factory s kanálem, který používá vlastní aktivity.

### <a name="create-a-custom-activity"></a>Vytvořit vlastní aktivitu
Pokud chcete vytvořit vlastní aktivitu .NET, vytvořte **knihovny tříd .NET** projektu s třídou, která implementuje **IDotNetActivity** rozhraní. Toto rozhraní obsahuje pouze jednu metodu: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) a je jeho podpis:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


Tato metoda přebírá čtyři parametry:

- **linkedServices**. Tato vlastnost je výčtový seznam Data Store propojené služby odkazuje vstupních a výstupních datových sad pro aktivity.   
- **datové sady**. Tato vlastnost je výčtový seznam vstupních a výstupních datových sad pro aktivity. Tento parametr slouží k získání umístění a schémata definované vstupní a výstupní datové sady.
- **aktivita**. Tato vlastnost představuje aktuální aktivitu. Slouží pro přístup k rozšířené vlastnosti přidružené k vlastní aktivity. Zobrazit [přístup k rozšířené vlastnosti](#access-extended-properties) podrobnosti.
- **Protokolovací nástroj**. Tento objekt umožňuje psát komentáře ladění tuto plochu v protokolu uživatele pro kanál.

Metoda vrací slovník, který je možné zřetězit vlastních aktivit v budoucnu. Tato funkce není dosud implementována, proto vrátí prázdný slovník z metody.  

### <a name="procedure"></a>Postup
1. Vytvoření **knihovny tříd .NET** projektu.
   <ol type="a">
     <li>Spuštění <b>Visual Studio 2017</b> nebo <b>Visual Studio 2015</b> nebo <b>Visual Studio 2013</b> nebo <b>Visual Studio 2012</b>.</li>
     <li>Klikněte na <b>Soubor</b>, přejděte na <b>Nový</b> a klikněte na <b>Projekt</b>.</li>
     <li>Rozbalte <b>Šablony</b> a vyberte <b>Visual C#</b>. V tomto názorném postupu použijete C#, ale můžete použít libovolný jazyk .NET k vývoji vlastní aktivity.</li>
     <li>Vyberte <b>knihovny tříd</b> ze seznamu typů projektů napravo. V sadě VS 2017, zvolte <b>knihovna tříd (.NET Framework)</b> </li>
     <li>Zadejte <b>MyDotNetActivity</b> pro <b>název</b>.</li>
     <li>Vyberte <b>C:\ADFGetStarted</b> pro <b>umístění</b>.</li>
     <li>Projekt vytvoříte kliknutím na <b>OK</b>.</li>
   </ol>
   
2. Klikněte na **Nástroje**, přejděte na **Správce balíčků NuGet** a klikněte na **Konzola Správce balíčků**.

3. V konzole Správce balíčků spustíte následující příkaz k importu **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Import **služby Azure Storage** balíčku NuGet do projektu.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Spouštěč služby Data Factory vyžaduje 4.3 verzi WindowsAzure.Storage. Pokud chcete přidat odkaz na novější verzi sestavení služby Azure Storage ve vašem projektu vlastní aktivitu, se zobrazí chyba, když tato aktivity spustí. Pokud chcete chybu vyřešit, přečtěte si téma [izolace domény Appdomain](#appdomain-isolation) oddílu. 
5. Přidejte následující **pomocí** příkazy ke zdrojovému souboru v projektu.

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
6. Změňte název **obor názvů** k **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Změnit název třídy, která se **MyDotNetActivity** a odvodí z **IDotNetActivity** rozhraní, jak je znázorněno v následujícím fragmentu kódu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementace (Přidat) **Execute** metodu **IDotNetActivity** rozhraní při **MyDotNetActivity** třídy a zkopírujte následující vzorový kód k metodě.

    Následující příklad počítá počet výskytů prvku hledaným termínem (dále jen "společnost Microsoft") v jednotlivých objektů blob přidružený datový řez.

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
    
        // get the first Azure Storate linked service from linkedServices object
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
9. Přidejte následující metody pomocné rutiny: 

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

    Metoda GetFolderPath vrací cestu ke složce, která datová sada odkazuje na a GetFileName metoda vrátí název souboru objektu blob nebo odkazující na datovou sadu. Pokud jste havefolderPath definuje pomocí proměnných, jako je například {Year}, {Month} {Day} atd., metoda vrátí řetězec, protože je bez nutnosti vyměnit s hodnotami modulu runtime. Zobrazit [přístup k rozšířené vlastnosti](#access-extended-properties) podrobné informace o přístupu k vlastnosti SliceStart, SliceEnd atd.    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda Calculate vypočítá počet instancí – klíčové slovo Microsoft ve vstupních souborů (objekty BLOB ve složce). Hledaný termín (dále jen "společnost Microsoft") je pevně zakódovaný v kódu.
10. Zkompilujte projekt. Klikněte na tlačítko **sestavení** z nabídky a na **sestavit řešení**.

    > [!IMPORTANT]
    > Nastavit 4.5.2 verzi rozhraní .NET Framework jako cílový rámec pro projekt: klikněte pravým tlačítkem na projekt a klikněte na tlačítko **vlastnosti** nastavit cílové rozhraní. Data Factory nepodporuje vlastní aktivity, které jsou novější než 4.5.2 zkompilovali verze rozhraní .NET Framework.

11. Spuštění **Windows Explorer**a přejděte do **bin\debug** nebo **bin\release** složce v závislosti na typu sestavení.
12. Vytvořte soubor zip **MyDotNetActivity.zip** , která obsahuje všechny binární soubory v <project folder>složky \bin\Debug. Zahrnout **MyDotNetActivity.pdb** souboru tak, aby získat další podrobnosti, jako je číslo řádku ve zdrojovém kódu, která způsobila problém, pokud došlo k chybě. 

    > [!IMPORTANT]
    > Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.

    ![Binární výstupní soubory](./media/data-factory-use-custom-activities/Binaries.png)
14. Vytvořte kontejner objektů blob s názvem **customactivitycontainer** Pokud ještě neexistuje. 
15. Nahrát jako objekt blob do customactivitycontainer v MyDotNetActivity.zip **pro obecné účely** úložiště objektů blob v Azure (ne vrstvami hot a cool Blob storage), který se odkazuje AzureStorageLinkedService.  

> [!IMPORTANT]
> Pokud přidáte tento projekt aktivity .NET do řešení v sadě Visual Studio, který obsahuje projekt Data Factory a přidejte odkaz na projekt .NET aktivitu z projektu aplikace služby Data Factory, není nutné provádět poslední dva kroky ručně vytvořit souboru zip soubor a pak ho nahrát do úložiště objektů blob v Azure pro obecné účely. Když publikujete entity služby Data Factory pomocí sady Visual Studio, tyto kroky automaticky provádí proces publikování. Další informace najdete v tématu [projekt Data Factory v sadě Visual Studio](#data-factory-project-in-visual-studio) oddílu.

## <a name="create-a-pipeline-with-custom-activity"></a>Vytvoření kanálu s vlastní aktivity
Vytvoření vlastní aktivity a nahrání souboru zip s binárními soubory do kontejneru objektů blob v **pro obecné účely** účet služby Azure Storage. V této části vytvoříte datovou továrnu Azure s kanálem, který používá vlastní aktivity.

Vstupní datová sada pro vlastní aktivity představuje objektů BLOB (soubory) ve složce customactivityinput kontejneru adftutorial ve službě blob storage. Výstupní datovou sadu aktivity představuje výstupních objektů BLOB ve složce customactivityoutput kontejneru adftutorial ve službě blob storage.

Vytvoření **soubor.txt** soubor s následujícím obsahem a nahrajte ho do **customactivityinput** složky **adftutorial** kontejneru. Pokud ho ještě neexistuje, vytvořte kontejner adftutorial. 

```
test custom activity Microsoft test custom activity Microsoft
```

Vstupní složky odpovídá řez ve službě Azure Data Factory i v případě, že složka obsahuje dva nebo více souborů. Když každý řez je zpracován kanálem, vlastní aktivita Iteruje přes všechny objekty BLOB ve vstupní složce pro tento řez.

Uvidíte, že jeden výstupní soubor se ve složce adftutorial\customactivityoutput jeden nebo více řádků (stejný jako počet objektů BLOB ve vstupní složce):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Tady jsou kroky, které provedete v této části:

1. Vytvoření **služby data factory**.
2. Vytvoření **propojené služby** fondu Azure Batch virtuálních počítačů na který se spouští vlastní aktivity a Azure Storage, který obsahuje vstupní a výstupní objekty BLOB.
3. Vytvoření vstupní a výstupní **datových sad** , které představují vstupní a výstupní vlastní aktivity.
4. Vytvoření **kanálu** , která používá vlastní aktivity.

> [!NOTE]
> Vytvořte **soubor.txt** a nahrajte ho do kontejneru objektů blob, pokud jste tak již neučinili. Pokyny naleznete v předchozí části.   

### <a name="step-1-create-the-data-factory"></a>Krok 1: Vytvoření datové továrny
1. Po přihlášení na webu Azure portal, proveďte následující kroky:
   1. Klikněte na tlačítko **vytvořit prostředek** v nabídce vlevo.
   2. Klikněte na tlačítko **Data a analýzy** v **nový** okno.
   3. V okně **Analýza dat** klikněte na **Objekt pro vytváření dat**.
   
    ![Nová nabídka služby Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. V **nová datová továrna** okně zadejte **CustomActivityFactory** pro název. Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chybová zpráva: **název objektu pro vytváření dat "CustomActivityFactory" není k dispozici**, změňte název datové továrny (například **yournameCustomActivityFactory**) a zkuste to znovu.

    ![Okno Nový objekt pro vytváření dat Azure](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klikněte na tlačítko **název skupiny prostředků**a vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků.
4. Ověřte, že používáte správné **předplatné** a **oblasti** místo, kam chcete datovou továrnu vytvořit.
5. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
6. Zobrazit objektu pro vytváření dat vytváří **řídicí panel** na webu Azure Portal.
7. Po úspěšném vytvoření objektu pro vytváření dat, se zobrazí okno objekt pro vytváření dat, které zobrazuje obsah služby data factory.
    
    ![Okno Objekt pro vytváření dat](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2: Vytvoření propojené služby
Propojené služby propojují úložiště dat nebo výpočetní služby s objektem pro vytváření dat Azure. V tomto kroku propojíte svůj účet úložiště Azure a účet Azure Batch pro vytváření dat.

#### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. Klikněte na tlačítko **Autor a nasadit** na dlaždici **služby DATA FACTORY** okno **CustomActivityFactory**. Zobrazí se editor služby Data Factory.
2. Klikněte na tlačítko **nové datové úložiště** na příkaz pruhové a zvolte **služby Azure storage**. V editoru by se měl zobrazit skript JSON pro vytvoření propojené služby Azure Storage.
    
    ![Úložiště dat – Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Nahraďte `<accountname>` s názvem účtu služby Azure storage a `<accountkey>` přístupovým klíčem účtu úložiště Azure. Informace o tom, jak získat přístupový klíč k úložišti, najdete v článku o [zobrazení, kopírování a opětovném vytváření přístupových klíčů úložiště](../../storage/common/storage-account-manage.md#access-keys).

    ![Líbilo služby Azure Storage](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Propojenou službu nasadíte kliknutím na **Nasadit** na panelu příkazů.

#### <a name="create-azure-batch-linked-service"></a>Vytvoření propojené služby Azure Batch
1. V editoru služby Data Factory, klikněte na tlačítko **... Další** na panelu příkazů klikněte na tlačítko **nový výpočet**a pak vyberte **Azure Batch** z nabídky.

    ![Nový výpočet – Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Skript JSON proveďte následující změny:

   1. Zadejte název účtu služby Azure Batch **accountName** vlastnost. **URL** z **okno účtu Azure Batch** je v následujícím formátu: `http://accountname.region.batch.azure.com`. Pro **batchUri** vlastností v kódu JSON, budete muset odebrat `accountname.` z adresy URL a použití `accountname` pro `accountName` vlastnost JSON.
   2. Zadejte klíč účtu služby Azure Batch pro **accessKey** vlastnost.
   3. Zadejte název fondu, který jste vytvořili jako součást požadavky **poolName** vlastnost. Můžete také zadat ID fondu namísto název fondu.
   4. Zadejte identifikátor URI služby Azure Batch pro **batchUri** vlastnost. Příklad: `https://westus.batch.azure.com`.  
   5. Zadejte **AzureStorageLinkedService** pro **linkedServiceName** vlastnost.

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

       Pro **poolName** vlastností, můžete také zadat ID fondu namísto název fondu.

    

### <a name="step-3-create-datasets"></a>Krok 3: Vytvoření datové sady
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data.

#### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
1. V **editoru** služby Data Factory klikněte na **... Další** na panelu příkazů klikněte na tlačítko **nová datová sada**a pak vyberte **úložiště objektů Blob v Azure** z rozevírací nabídky.
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

   Vytvoření kanálu dále v tomto návodu s časem zahájení: 2016-11-16T00:00:00Z a koncový čas: 2016-11-16T05:00:00Z. Je naplánován ke vytvářejí data po hodinách, takže máte pět řezy vstupní a výstupní (mezi **00**: -> 00:00 **05**: 00:00).

   **Frekvence** a **interval** vstupní datová sada je nastavený na **hodinu** a **1**, což znamená, že vstupní řez je dostupný po hodinách. V této ukázce je stejný soubor (soubor.txt) intputfolder.

   Tady jsou časy zahájení pro každý řez, který je reprezentován SliceStart systémové proměnné ve výše uvedeném fragmentu JSON.
3. Klikněte na tlačítko **nasadit** na panelu nástrojů můžete vytvářet a nasazovat **InputDataset**. Potvrďte, že se v záhlaví okna editoru zobrazila zpráva **ÚSPĚŠNÉ VYTVOŘENÍ TABULKY**.

#### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
1. V **editoru služby Data Factory**, klikněte na tlačítko **... Další** na panelu příkazů klikněte na tlačítko **nová datová sada**a pak vyberte **úložiště objektů Blob v Azure**.
2. Skript JSON v pravém podokně nahraďte následující skript JSON:

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

     Umístění výstupu je **adftutorial/customactivityoutput/** a název výstupního souboru je rrrr MM-dd-HH.txt tam, kde je rrrr MM-dd HH rok, měsíc, datum a hodina řez se vytváří. Zobrazit [referenční informace pro vývojáře] [ adf-developer-reference] podrobnosti.

    Výstupní objekt blob nebo soubor se generuje pro každý vstupní řez. Zde je, jak je výstupní soubor s názvem pro každý řez. Všechny výstupní soubory jsou generovány v jedné složce výstupu: **adftutorial\customactivityoutput**.

   | Řez | Čas spuštění | Výstupní soubor |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Mějte na paměti, že všechny soubory ve vstupní složce jsou součástí řez s časem spuštění uvedeno výše. Při zpracování této řez, vlastní aktivita prohledává každý soubor a vytváří čáry ve výstupním souboru s počtem výskytů hledaný termín (dále jen "společnost Microsoft"). Pokud se inputfolder tři soubory, jsou ve výstupní soubor pro jednotlivé hodinový řezy tři řádky: 2016-11-16-00.txt 2016-11-16:01:00:00.txt atd.
3. K nasazení **OutputDataset**, klikněte na tlačítko **nasadit** na panelu příkazů.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Vytvoření a spuštění kanálu, který používá vlastní aktivity
1. V editoru služby Data Factory, klikněte na tlačítko **... Další**a pak vyberte **nový kanál** na panelu příkazů. 
2. Nahraďte kód JSON v pravém podokně následující skript JSON:

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

   * **Souběžnost** je nastavena na **2** tak, aby dva řezy jsou zpracovávány paralelně 2 virtuální počítače ve fondu Azure Batch.
   * V části aktivit je jedna aktivita, a je typu: **DotNetActivity**.
   * **AssemblyName** je nastavena na název knihovny DLL: **MyDotnetActivity.dll**.
   * **Vstupní bod** je nastavena na **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** je nastavena na **AzureStorageLinkedService** , která odkazuje na úložiště objektů blob, který obsahuje příslušný soubor .zip pro vlastní aktivity. Pokud používáte různé účty Azure Storage pro vstup/výstup souborů a soubor zip vlastní aktivitu, vytvořte další propojenou službu Azure Storage. Tento článek předpokládá, že používáte stejný účet Azure Storage.
   * **PackageFile** je nastavena na **customactivitycontainer/MyDotNetActivity.zip**. Je ve formátu: containerforthezip/nameofthezip.zip.
   * Vlastní aktivita přijímá **InputDataset** jako vstup a **OutputDataset** jako výstup.
   * Vlastnost linkedServiceName vlastní aktivity odkazuje **AzureBatchLinkedService**, které instruuje Azure Data Factory, který vlastní aktivita je potřeba spustit na virtuálních počítačích Azure Batch.
   * **isPaused** je nastavena na **false** ve výchozím nastavení. Kanálu se spustí okamžitě v tomto příkladě vzhledem k tomu, že řezy spustit v minulosti. Tuto vlastnost lze nastavit na hodnotu true pro pozastavení kanálu a nastavte ji zpět na hodnotu false. restartujte.
   * **Start** čas a **end** časy jsou **pět** hodin od sebe a kolekce obsahuje nějaké řezy se produkují po hodinách, takže pět řezy se tvoří kanál.
3. Pro nasazení kanálu, klikněte na tlačítko **nasadit** na panelu příkazů.

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. V okně Data Factory na webu Azure Portal klikněte na tlačítko **Diagram**.

    ![Dlaždice Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. V zobrazení diagramu teď klikněte na tlačítko OutputDataset.

    ![Zobrazení diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Měli byste vidět, že pět výstupní řezy jsou ve stavu Připraveno. Pokud nejsou ve stavu Připraveno, dosud byl vytvořen ještě. 

   ![Výstupní řezy](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Ověřte, že výstupní soubory jsou generovány v úložišti objektů blob ve **adftutorial** kontejneru.

   ![výstup z vlastních aktivit][image-data-factory-ouput-from-custom-activity]
5. Pokud otevřete výstupní soubor, měli vidět výstup podobný následující výstup:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Použití [webu Azure portal] [ azure-preview-portal] nebo rutin Azure Powershellu pro monitorování služby data factory, kanálů a datových sad. Zobrazí zprávy z **ActivityLogger** v kódu pro vlastní aktivity v protokolech (konkrétně user-0.log), které si můžete stáhnout z portálu nebo pomocí rutin.

   ![Stáhnout protokoly z vlastní aktivity][image-data-factory-download-logs-from-custom-activity]

Zobrazit [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md) podrobné pokyny k monitorování datových sad a kanálů.      

## <a name="data-factory-project-in-visual-studio"></a>Projektu data Factory v sadě Visual Studio  
Můžete vytvořit a publikování entit služby Data Factory pomocí sady Visual Studio namísto pomocí webu Azure portal. Podrobné informace o vytváření a publikování entit služby Data Factory pomocí sady Visual Studio, naleznete v tématu [sestavit svůj první kanál pomocí sady Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) a [kopírování dat z objektů Blob v Azure do Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) články.

Pokud vytváříte projekt Data Factory v sadě Visual Studio, proveďte následující kroky:
 
1. Přidáte projekt Data Factory do řešení sady Visual Studio, která obsahuje projekt vlastní aktivity. 
2. Přidáte odkaz na projekt .NET aktivitu z projektu Data Factory. Klikněte pravým tlačítkem na projekt Data Factory, přejděte na **přidat**a potom klikněte na tlačítko **odkaz**. 
3. V **přidat odkaz** dialogové okno, vyberte **MyDotNetActivity** projektu a klikněte na tlačítko **OK**.
4. Vytvoření a publikování řešení.

    > [!IMPORTANT]
    > Při publikování entit služby Data Factory souboru zip je pro vás automaticky vytvoří a nahraje do kontejneru objektů blob: customactivitycontainer. Pokud neexistuje kontejner objektů blob, je automaticky vytvořen příliš.  


## <a name="data-factory-and-batch-integration"></a>Integrace služby Data Factory a Batch
Služba Data Factory vytvoří úlohu ve službě Azure Batch s názvem: **adf poolname: Úloha xxx**. Klikněte na tlačítko **úlohy** v levé nabídce. 

![Azure Data Factory - dávkových úloh Hive](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Pro každé spuštění aktivity řezu se vytvoří úkol. Pokud je pět řezů zpracovat, vytvoří se v této úloze pět úloh. Pokud existují několika výpočetních uzlech ve fondu Batch, nejmíň dva řezy můžou běžet paralelně. Pokud se maximální úkolů na výpočetním uzlu je nastavená na > 1, může mít také více než jeden řez spuštěná ve stejné výpočetní.

![Azure Data Factory – úlohy služby Batch](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Následující diagram znázorňuje vztah mezi úlohami Azure Data Factory a Batch.

![Data Factory a Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Řešení potíží s chybami
Řešení potíží se skládá z několik základních technik:

1. Pokud se zobrazí následující chyba, můžete používat úložiště objektů blob v horké/studené namísto použití úložiště objektů blob v Azure pro obecné účely. Nahrání souboru zip **účet úložiště pro obecné účely Azure**. 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. Pokud se zobrazí následující chyba, zkontrolujte, že název třídy v souboru CS odpovídá názvu zadanému pro **EntryPoint** vlastností v kódu JSON kanálu. V tomto návodu je název třídy: MyDotNetActivity a vstupní bod v kódu JSON: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Pokud se názvy neshodují, ověřte, zda všechny binární soubory jsou v **kořenovou složku** komprimovaného souboru. To znamená když otevřete soubor zip, měli byste vidět všechny soubory v kořenové složce, ne na všechny podsložky.   
3. Pokud vstupní řez není nastavený **připravené**, potvrďte, že je správná struktura vstupní složky a **soubor.txt** existuje ve vstupních složkách.
3. V **Execute** metoda vaše vlastní aktivity použijte **IActivityLogger** objektu do protokolu informace, které vám pomohou s řešením problémů. Protokolované zprávy zobrazí v souborech protokolu uživatele (jeden nebo více souborů s názvem: user-0.log, user-1.log, user-2.log atd.).

   V **OutputDataset** okno, kliknutím na řez otevřete najdete v článku **datový ŘEZ** okno pro tento řez. Zobrazí **spuštění aktivit** pro tento řez. Zobrazí se jedné aktivity spustit řez. Pokud klepnete na tlačítko spustit na příkazovém řádku, můžete spustit jiné aktivity spuštění pro stejný řez.

   Po kliknutí na aktivity při spuštění se zobrazí **podrobnosti o spuštění aktivit** okno se seznamem souborů protokolů. Zobrazí zprávy zaznamenané v souboru user_0.log. Při výskytu chyby se zobrazí tři spuštění aktivit, protože počet opakování je nastavená na 3 v kódu JSON kanálu a aktivity. Po kliknutí na spuštění aktivity se zobrazí soubory protokolů obsahující informace pro řešení potíží s chybou.

   V seznamu souborů protokolů, klikněte **user-0.log**. V pravém panelu jsou výsledkem použití **IActivityLogger.Write** metody. Pokud nevidíte všechny zprávy, zkontrolujte, zda máte další soubory protokolu s názvem: user_1.log, user_2.log atd. V opačném případě kód mohlo dojít k selhání po poslední zprávu.

   Kromě toho zkontrolujte **system-0.log** pro všechny chybové zprávy systému a výjimky.
4. Zahrnout **PDB** souboru v souboru zip tak, aby podrobnosti o chybě, jako jsou informace **zásobníku volání** , když dojde k chybě.
5. Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.
6. Ujistěte se, **assemblyName** (MyDotNetActivity.dll), **vstupního bodu**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), a **packageLinkedService** (by měly odkazovat na **pro obecné účely**úložiště objektů blob v Azure, které obsahuje příslušný soubor .zip) jsou nastavené na správné hodnoty.
7. Pokud jste opravili chybu a chcete řez zpracovat znovu, klikněte na něj v okně **OutputDataset** pravým tlačítkem myši a potom klikněte na **Spustit**.
8. Pokud se zobrazí následující chyba, používáte balíček služby Azure Storage verze > 4.3.0. Spouštěč služby Data Factory vyžaduje 4.3 verzi WindowsAzure.Storage. Zobrazit [izolace domény Appdomain](#appdomain-isolation) části pro řešení, pokud je nutné použít na novější verzi sestavení služby Azure Storage. 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Pokud používáte 4.3.0 verze balíčku služby Azure Storage, odeberte existující odkaz na balíček verze > 4.3.0 služby Azure Storage. Potom spusťte následující příkaz z konzoly Správce balíčků NuGet. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Sestavte projekt. Odstraňte Azure.Storage sestavení verze > 4.3.0 ve složce bin\Debug. Vytvořte soubor zip se soubory PDB a binárních souborů. Nahraďte původní soubor zip s touto položkou v kontejneru objektů blob (customactivitycontainer). Znovu spustit řezy, které selhaly (klikněte pravým tlačítkem na řez a klikněte na tlačítko Spustit).   
8. Vlastní aktivita nepoužívá **app.config** souborů z vašeho balíčku. Proto pokud váš kód načítá řetězce připojení z konfiguračního souboru, to nebude fungovat v době běhu. Nejosvědčenější při použití služby Azure Batch je uložit všechny tajné klíče v **Azure Key Vaultu**, používat k ochraně hlavního názvu služby na základě certifikátů **keyvault**a distribuovat tento certifikát do služby Azure Batch fond. Vlastní aktivita .NET potom má přístup k tajným klíčům v trezoru za běhu. Toto řešení je obecné řešení a můžete škálovat pro libovolný typ tajného klíče, nejen připojovací řetězec.

   Je snazší alternativní řešení (ale ne osvědčený postup): můžete vytvořit **propojená služba Azure SQL** s nastavení připojovacího řetězce, vytvořte datovou sadu, která používá propojenou službu a zřetězení datovou sadu jako zástupný vstupní datovou sadu, která vlastní aktivita .NET. Potom můžete přistupovat připojovací řetězec propojené služby v kódu vlastní aktivity.  

## <a name="update-custom-activity"></a>Aktualizovat vlastní aktivity
Pokud aktualizujete kód pro vlastní aktivitu, sestavit a nahrajte soubor zip, který obsahuje nové binární soubory do úložiště objektů blob.

## <a name="appdomain-isolation"></a>Izolace domény aplikace
Naleznete v tématu [různé ukázky AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) , který ukazuje, jak vytvořit vlastní aktivitu, která není omezen na verze sestavení používají Spouštěč služby Data Factory (Příklad: ve verzi 4.3.0 WindowsAzure.Storage Newtonsoft.Json v6.0.x, atd.).

## <a name="access-extended-properties"></a>Přístup k rozšířené vlastnosti
Je možné deklarovat rozšířené vlastnosti v aktivitě JSON, jak je znázorněno v následujícím příkladu:

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


V tomto příkladu jsou dva rozšířené vlastnosti: **SliceStart** a **DataFactoryName**. Hodnota vlastnosti SliceStart vychází z vlastnosti SliceStart systémové proměnné. Zobrazit [systémové proměnné](data-factory-functions-variables.md) seznam podporovaných systémových proměnných. Hodnota DataFactoryName je pevně zakódované CustomActivityFactory.

Pro přístup k těmto rozšířené vlastnosti v **Execute** metodou, použít kód podobně jako následující kód:

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

## <a name="auto-scaling-of-azure-batch"></a>Automatické škálování služby Azure Batch
Můžete také vytvořit fond služby Azure Batch s **automatického škálování** funkce. Můžete například vytvořit fond služby azure batch s 0 vyhrazených virtuálních počítačích a se vzorec automatického škálování na základě počtu úkolů čekajících na vyřízení. 

Ukázkové vzorce zde dosáhne následující chování: při počátečním vytvoření fondu začíná 1 virtuální počítač. Metrika $PendingTasks definuje počet úloh ve spuštění + aktivní (ve frontě) stavu.  Vzorec najde průměrný počet čekající úlohy za posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy nedostane mimo 25 virtuálních počítačů. Tak jako jsou odeslány nové úkoly, fondu automaticky rozšíří a jako dokončení úkolů, budou virtuální počítače zdarma jednu po druhé a automatickým Škálováním zmenšuje těchto virtuálních počítačů. startingNumberOfVMs a maxNumberofVMs lze upravit podle vašich potřeb.

Vzorec automatického škálování:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zobrazit [automatické škálování výpočetních uzlů ve fondu služby Azure Batch](../../batch/batch-automatic-scaling.md) podrobnosti.

Pokud fond používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může trvat 15 – 30 minut přípravy virtuálního počítače před spuštěním vlastní aktivity.  Pokud se fond používá různé autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Vytvořit vlastní aktivitu s použitím sady .NET SDK
V tomto návodu v tomto článku vytvoříte datovou továrnu s kanálem, který používá vlastní aktivity pomocí webu Azure portal. Následující kód ukazuje, jak vytvářet datové továrny pomocí sady .NET SDK. Další podrobnosti o použití sady SDK můžete programově vytvořit kanály ve službě můžete najít [vytvoření kanálu s aktivitou kopírování pomocí rozhraní .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) článku. 

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
[Azure Data Factory – místní prostředí](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) ukázka na Githubu zahrnuje nástroj, který umožňuje ladit vlastní aktivity .NET v sadě Visual Studio.  


## <a name="sample-custom-activities-on-github"></a>Vlastní aktivity ukázka na Githubu
| Ukázka | Jaké vlastní aktivita naloží |
| --- | --- |
| [Pro stahování dat protokolu HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Stáhne data z koncového bodu HTTP do Azure Blob Storage pomocí vlastního jazyka C# aktivity ve službě Data Factory. |
| [Ukázka analýzy subjektivního hodnocení na twitteru](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Vyvolá model Azure ML a proveďte analýzu subjektivního hodnocení, vyhodnocování, předpovědi atd. |
| [Spustit skript jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Vyvolá skript jazyka R spuštěním RScript.exe v clusteru HDInsight, který už má nainstalovaný jazyk R na něj. |
| [Různé domény AppDomain aktivita technologie .NET](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Používá jiné sestavení verze z těch, které jsou používané Spouštěč služby Data Factory |
| [Znovuzpracování modelů ve službě Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Znovu zpracuje modelu ve službě Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
