---
title: Použití vlastních aktivit v kanálu Azure Data Factory
description: Zjistěte, jak vytvořit vlastní aktivity a použít je v kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 88095c7b3c31c5111f1e1d1e5b157403359053bd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-use-custom-activities.md)
> * [Verze 2 – Preview](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [vlastní aktivity v V2](../transform-data-using-dotnet-custom-activity.md).

Existují dva typy aktivit, které můžete použít v kanál služby Azure Data Factory.

- [Aktivity přesunu dat](data-factory-data-movement-activities.md) pro přesun dat mezi [podporovanými úložišti dat zdroj a jímka](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](data-factory-data-transformation-activities.md) k transformaci dat pomocí výpočetních služeb, například Azure HDInsight, Azure Batch a Azure Machine Learning. 

Pro přesun dat do nebo z úložiště dat, který nepodporuje objekt pro vytváření dat, vytvořit **vlastní aktivity** s vlastní logiky přesun dat a použití aktivity v kanálu. Podobně transformace nebo zpracovat data způsobem, který není podporován službou Data Factory, vytvořte vlastní aktivity s vlastní logikou transformaci dat a použijte aktivitu v kanálu. 

Můžete nakonfigurovat vlastní aktivity ke spuštění na **Azure Batch** fondu virtuálních počítačů. Pokud používáte Azure Batch, můžete použít pouze existujícího fondu Azure Batch.

Následující postup obsahuje podrobné pokyny pro vytváření vlastních aktivit rozhraní .NET a používání vlastní aktivity v kanálu. Průvodce používá **Azure Batch** propojené služby. 

> [!IMPORTANT]
> - Není možné používat pro přístup k místním zdrojům dat Brána pro správu dat z vlastních aktivit. V současné době [Brána pro správu dat](data-factory-data-management-gateway.md) podporuje pouze aktivity kopírování a aktivity uložené procedury v datové továrně.   

## <a name="walkthrough-create-a-custom-activity"></a>Návod: vytvoření vlastní aktivity
### <a name="prerequisites"></a>Požadavky
* Visual Studio 2012/2013/2015
* Stáhněte sadu [Azure .NET SDK](https://azure.microsoft.com/downloads/) a nainstalujte ji.

### <a name="azure-batch-prerequisites"></a>Požadavky Azure Batch
V tomto návodu spusťte vlastní aktivitu .NET pomocí Azure Batch jako výpočetní prostředek. **Azure Batch** je platforma služby pro spuštění rozsáhlé paralelní a vysoce výkonné výpočty aplikace (HPC) efektivně v cloudu. Azure Batch plány výpočetně náročných na spravované spouštět **kolekci virtuálních počítačů**, a dokáže automaticky škálovat výpočetní prostředky ke splnění potřeb vašich úloh. V tématu [základy Azure Batch] [ batch-technical-overview] článku podrobnější přehled služby Azure Batch.

Pro tento kurz vytvoření účtu Azure Batch s fondem virtuálních počítačů. Postup je následující:

1. Vytvoření **účtu Azure Batch** pomocí [portál Azure](http://portal.azure.com). V tématu [vytvoření a Správa účtu Azure Batch] [ batch-create-account] pokyny najdete v článku.
2. Poznamenejte si název účtu Azure Batch, klíč účtu, identifikátor URI a název fondu. Je k vytvoření služby Azure Batch propojené potřebujete.
    1. Na domovské stránce pro účet Azure Batch, uvidíte **URL** v následujícím formátu: `https://myaccount.westus.batch.azure.com`. V tomto příkladu **stránku Můj účet** je název účtu Azure Batch. Identifikátor URI, které můžete použít v definici propojené služby je adresa URL bez názvu účtu. Například: `https://<region>.batch.azure.com`.
    2. Klikněte na tlačítko **klíče** v levé nabídce a zkopírujte **primární přístupový klíč**.
    3. Chcete-li použít existující fond, klikněte na tlačítko **fondy** v nabídce a zapište **ID** fondu. Pokud nemáte existující fond, přejděte k dalšímu kroku.     
2. Vytvoření **fondu Azure Batch**.

   1. V [portál Azure](https://portal.azure.com), klikněte na tlačítko **Procházet** v levé nabídce a klikněte na **účty Batch**.
   2. Vyberte svůj účet Azure Batch **účtu Batch** okno.
   3. Klikněte na tlačítko **fondy** dlaždici.
   4. V **fondy** okně klikněte na tlačítko Přidat na panelu nástrojů na Přidat fond.
      1. Zadejte ID fondu (ID fondu). Poznámka: **ID fondu**; je nutné při vytváření řešení Data Factory.
      2. Zadejte **Windows Server 2012 R2** pro nastavení řada operačního systému.
      3. Vyberte **cenovou úroveň uzlu**.
      4. Zadejte **2** jako hodnota **cíl vyhrazené** nastavení.
      5. Zadejte **2** jako hodnota **maximální počet úloh na uzel** nastavení.
   5. Kliknutím na tlačítko **OK** vytvořte fond.
   6. Poznamenejte si **ID** fondu. 



### <a name="high-level-steps"></a>Postup vysoké úrovně
Zde jsou dva hlavní kroky, které můžete provádět v rámci tohoto návodu: 

1. Vytvořte vlastní aktivitu, která obsahuje jednoduché datové transformaci/zpracování logiky.
2. Vytvořte objekt pro vytváření dat Azure s kanál, který používá vlastní aktivity.

### <a name="create-a-custom-activity"></a>Vytvoření vlastní aktivity
Chcete-li vytvořit vlastní aktivity rozhraní .NET, vytvořte **knihovna tříd rozhraní .NET** projektu s třídou, která implementuje **IDotNetActivity** rozhraní. Toto rozhraní obsahuje pouze jednu metodu: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) a jeho podpis je:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


Tato metoda přebírá čtyř parametrů:

- **linkedServices**. Tato vlastnost je vyčíslitelná seznam úložiště dat propojené služby odkazuje vstupní a výstupní datové sady pro aktivity.   
- **datové sady**. Tato vlastnost je vyčíslitelná seznam vstupní a výstupní datové sady pro aktivity. Tento parametr můžete získat umístění a schémata definované vstupní a výstupní datové sady.
- **aktivita**. Tato vlastnost představuje aktuální aktivity. Slouží pro přístup k rozšířené vlastnosti související s vlastní aktivity. V tématu [přístup rozšířené vlastnosti](#access-extended-properties) podrobnosti.
- **protokolovač**. Tento objekt umožňuje psát komentáře ladění tento prostor v protokolu uživatele pro kanál.

Metoda vrátí slovník, který slouží k řetězu vlastní aktivity společně v budoucnu. Tato funkce není dosud implementována, takže vrátí prázdný slovník z metody.  

### <a name="procedure"></a>Postup
1. Vytvoření **knihovna tříd rozhraní .NET** projektu.
   <ol type="a">
     <li>Spusťte <b>Visual Studio 2017</b> nebo <b>sady Visual Studio 2015</b> nebo <b>Visual Studio 2013</b> nebo <b>sady Visual Studio 2012</b>.</li>
     <li>Klikněte na <b>Soubor</b>, přejděte na <b>Nový</b> a klikněte na <b>Projekt</b>.</li>
     <li>Rozbalte <b>Šablony</b> a vyberte <b>Visual C#</b>. V tomto návodu použijete C#, ale můžete v kterémkoli jazyce platformy .NET vyvíjet vlastní aktivity.</li>
     <li>Vyberte <b>knihovny tříd</b> ze seznamu typů projektu na pravé straně. V VS 2017, zvolte <b>knihovny tříd (rozhraní .NET Framework)</b> </li>
     <li>Zadejte <b>MyDotNetActivity</b> pro <b>název</b>.</li>
     <li>Vyberte <b>C:\ADFGetStarted</b> pro <b>umístění</b>.</li>
     <li>Projekt vytvoříte kliknutím na <b>OK</b>.</li>
   </ol>
   
2. Klikněte na **Nástroje**, přejděte na **Správce balíčků NuGet** a klikněte na **Konzola Správce balíčků**.

3. V konzole Správce balíčků spustíte následující příkaz pro import **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Import **Azure Storage** balíčku NuGet do projektu.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Spouštěč služby Data Factory vyžaduje 4.3 verzi WindowsAzure.Storage. Pokud přidáte odkaz na novější verzi sestavení Azure Storage ve vašem projektu vlastní aktivitu, zobrazí chybu, když aktivita provede. Chcete-li vyřešit chyby, najdete v části [izolace domény aplikace](#appdomain-isolation) části. 
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
7. Změnit název třídy pro **MyDotNetActivity** a odvodí z **IDotNetActivity** rozhraní, jak je znázorněno v následující fragment kódu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementace (Přidat) **Execute** metodu **IDotNetActivity** rozhraní k **MyDotNetActivity** třídy a zkopírujte následující vzorový kód do metody.

    Následující příklad vrátí počet výskytů hledaný termín ("Microsoft") v jednotlivých objektů blob přidružený datový řez.

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

    Metoda GetFolderPath vrací cestu ke složce, která odkazuje datovou sadu na a vrátí název objektu blob nebo soubor, který datová sada odkazuje na metodu GetFileName. Pokud jste havefolderPath definuje pomocí proměnných, například {Year}, {Month}, {Day} atd., metoda vrátí řetězec, protože je bez jejich nahrazení s hodnotami runtime. V tématu [přístup rozšířené vlastnosti](#access-extended-properties) část Podrobnosti o přístup k SliceStart, SliceEnd atd.    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda Calculate vypočítá počet instancí – klíčové slovo Microsoft ve vstupní soubory (objekty BLOB ve složce). Hledaný termín ("Microsoft") je pevně zakódovaná v kódu.
10. Kompilace projektu. Klikněte na tlačítko **sestavení** z nabídky a klikněte na tlačítko **sestavit řešení**.

    > [!IMPORTANT]
    > Nastavit 4.5.2 verzi rozhraní .NET Framework jako cílové rozhraní pro svůj projekt: klikněte pravým tlačítkem na projekt a klikněte na tlačítko **vlastnosti** nastavit cílové rozhraní. Objekt pro vytváření dat nepodporuje vlastní aktivity, které jsou novější než 4.5.2 zkompilovat proti verze rozhraní .NET Framework.

11. Spusťte **Průzkumníka Windows**a přejděte do **bin\debug** nebo **bin\release** složku v závislosti na typu sestavení.
12. Vytvořte soubor zip **MyDotNetActivity.zip** obsahující všechny binární soubory v <project folder>\bin\Debug složka. Zahrnout **MyDotNetActivity.pdb** tak, aby získat další podrobnosti, jako je například číslo řádku ve zdrojovém kódu, která způsobila problém, pokud došlo k chybě. 

    > [!IMPORTANT]
    > Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.

    ![Binární výstupní soubory](./media/data-factory-use-custom-activities/Binaries.png)
14. Vytvořte kontejner objektů blob s názvem **customactivitycontainer** Pokud ještě neexistuje. 
15. Nahrát MyDotNetActivity.zip jako objekt blob customactivitycontainer v **pro obecné účely** úložiště objektů blob v Azure (úložiště objektů Blob není za provozu nebo nástrojů), který odkazuje AzureStorageLinkedService.  

> [!IMPORTANT]
> Pokud přidáte tento projekt aktivity .NET na řešení v sadě Visual Studio, který obsahuje projekt Data Factory a přidejte odkaz na rozhraní .NET projektu aktivity z projektu pro vytváření dat aplikace, není potřeba provádět poslední dva kroky ručně vytvoření souboru zip soubor a pak ho nahrát do úložiště objektů blob v Azure pro obecné účely. Když publikujete entity služby Data Factory pomocí sady Visual Studio, tyto kroky automaticky provádí proces publikování. Další informace najdete v tématu [projekt Data Factory v sadě Visual Studio](#data-factory-project-in-visual-studio) části.

## <a name="create-a-pipeline-with-custom-activity"></a>Vytvoření kanálu s vlastní aktivity
Vytvoření vlastní aktivity a nahrané do kontejneru objektů blob v souboru zip binární soubory služby **pro obecné účely** účet úložiště Azure. V této části vytvoříte objekt pro vytváření dat Azure s kanál, který používá vlastní aktivity.

Vstupní datové sady pro vlastní aktivita představuje objekty BLOB (soubory) ve složce customactivityinput adftutorial kontejneru ve službě blob storage. Výstupní datovou sadu aktivity představuje výstupní objekty BLOB ve složce customactivityoutput adftutorial kontejneru ve službě blob storage.

Vytvoření **soubor.txt** soubor s následujícím obsahem a nahrajte ho do **customactivityinput** složky **adftutorial** kontejneru. Vytvoření kontejneru adftutorial, pokud ho ještě neexistuje. 

```
test custom activity Microsoft test custom activity Microsoft
```

Vstupní složky odpovídá řez v Azure Data Factory i v případě, že složka obsahuje dvě nebo více souborů. Při zpracování každý řez v kanálu vlastní aktivita iteruje všechny objekty BLOB ve složce vstupní pro tento řez.

Uvidíte, že jednu výstupní soubor s ve složce adftutorial\customactivityoutput s jedním nebo více řádky (stejný jako počet objektů BLOB ve složce vstupní):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Tady jsou kroky, které můžete provádět v této části:

1. Vytvoření **objekt pro vytváření dat**.
2. Vytvořit **propojené služby** fondu Azure Batch virtuálních počítačů, na který vlastní aktivita se spustí a úložišti Azure, který obsahuje objekty BLOB vstupu a výstupu.
3. Vytvoření vstupní a výstupní **datové sady** které představují vstupní a výstupní vlastní aktivity.
4. Vytvoření **kanálu** používající vlastní aktivity.

> [!NOTE]
> Vytvořte **soubor.txt** a nahrajte ho do kontejneru objektů blob, pokud jste tak již neučinili. Pokyny naleznete v předchozí části.   

### <a name="step-1-create-the-data-factory"></a>Krok 1: Vytvoření objektu pro vytváření dat
1. Po přihlášení k portálu Azure, proveďte následující kroky:
   1. Klikněte na tlačítko **vytvořit prostředek** v levé nabídce.
   2. Klikněte na tlačítko **Data + analýzy** v **nový** okno.
   3. V okně **Analýza dat** klikněte na **Objekt pro vytváření dat**.
   
    ![Nové nabídky Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. V **nový objekt pro vytváření dat** okno, zadejte **CustomActivityFactory** pro název. Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **název objektu pro vytváření dat "CustomActivityFactory" není k dispozici**, změňte název objektu pro vytváření dat (například **yournameCustomActivityFactory**) a zkuste to znovu.

    ![Nové okno Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klikněte na tlačítko **název skupiny prostředků**a vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků.
4. Ověřte, že používáte správný **předplatné** a **oblast** místo, kam chcete objekt pro vytváření dat vytvořit.
5. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
6. Zobrazí objektu pro vytváření dat vytváří ve **řídicí panel** na portálu Azure.
7. Po úspěšném vytvoření objektu pro vytváření dat, zobrazí se okno objekt pro vytváření dat, který zobrazuje obsah objektu pro vytváření dat.
    
    ![Okno Objekt pro vytváření dat](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2: Vytvoření propojených služeb
Propojené služby propojují úložiště dat nebo výpočetní služby s objektem pro vytváření dat Azure. V tomto kroku propojíte svůj účet úložiště Azure a účet Azure Batch pro vytváření dat.

#### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. Klikněte na tlačítko **vytvořit a nasadit** na dlaždici **objekt pro vytváření dat** okno pro **CustomActivityFactory**. Zobrazí se editor služby Data Factory.
2. Klikněte na tlačítko **nové datové úložiště** na příkaz panelu a vyberte **úložiště Azure**. V editoru by se měl zobrazit skript JSON pro vytvoření propojené služby Azure Storage.
    
    ![Nové datové úložiště - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Nahraďte `<accountname>` s názvem účtu úložiště Azure a `<accountkey>` s přístupovým klíčem k účtu úložiště Azure. Informace o tom, jak získat přístupový klíč k úložišti, najdete v článku o [zobrazení, kopírování a opětovném vytváření přístupových klíčů úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    ![Úložiště Azure líbilo služby](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Propojenou službu nasadíte kliknutím na **Nasadit** na panelu příkazů.

#### <a name="create-azure-batch-linked-service"></a>Vytvoření služby Azure Batch propojené
1. V editoru služby Data Factory, klikněte na tlačítko **... Další** na panelu příkazů klikněte na tlačítko **nový výpočet**a potom vyberte **Azure Batch** z nabídky.

    ![Nový výpočet - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Skript JSON proveďte následující změny:

   1. Zadejte název účtu Azure Batch **accountName** vlastnost. **URL** z **okně účtu Azure Batch** je v následujícím formátu: `http://accountname.region.batch.azure.com`. Pro **batchUri** vlastností v kódu JSON, je třeba odebrat `accountname.` z adresy URL a použití `accountname` pro `accountName` vlastnost JSON.
   2. Zadejte klíč účtu Azure Batch pro **accessKey** vlastnost.
   3. Zadejte název fondu, který jste vytvořili v rámci požadavků pro **poolName** vlastnost. Můžete také zadat ID fondu namísto názvu fondu.
   4. Zadejte Azure Batch URI **batchUri** vlastnost. Příklad: `https://westus.batch.azure.com`.  
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

       Pro **poolName** vlastnost, můžete také zadat ID fondu namísto názvu fondu.

    

### <a name="step-3-create-datasets"></a>Krok 3: Vytvoření datové sady
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data.

#### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
1. V **editoru** služby Data Factory klikněte na **... Další** na panelu příkazů klikněte na tlačítko **nová datová sada**a potom vyberte **úložiště objektů Azure Blob** z rozevírací nabídky.
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

   Vytvoření kanálu dále v tomto návodu se časem spuštění: 2016-11-16T00:00:00Z a koncový čas: 2016-11-16T05:00:00Z. Je naplánována nevytvořila data každou hodinu, takže se pět řezy vstupní a výstupní (mezi **00**: -> 00:00 **05**: 00:00).

   **Frekvence** a **interval** vstupní datové sady je nastavený na **hodinu** a **1**, což znamená, že vstupní řez je k dispozici každou hodinu. V této ukázce je stejný soubor (soubor.txt) v intputfolder.

   Tady jsou časy zahájení pro každý řez, která je reprezentována SliceStart systémové proměnné ve výše uvedeném fragmentu JSON.
3. Klikněte na tlačítko **nasadit** na panelu nástrojů vytvořit a nasadit **InputDataset**. Potvrďte, že se v záhlaví okna editoru zobrazila zpráva **ÚSPĚŠNÉ VYTVOŘENÍ TABULKY**.

#### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
1. V **editoru služby Data Factory**, klikněte na tlačítko **... Další** na panelu příkazů klikněte na tlačítko **nová datová sada**a potom vyberte **úložiště objektů Azure Blob**.
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

     Výstupní umístění je **adftutorial/customactivityoutput/** a název výstupního souboru je rrrr MM-dd-HH.txt, kde je rrrr MM-dd HH rok, měsíc, datum a hodinu řez se vytváří. V tématu [referenční informace pro vývojáře] [ adf-developer-reference] podrobnosti.

    Objekt blob nebo soubor výstupu se generuje pro každý vstupní řez. Zde je, jak je výstupní soubor s názvem pro každý řez. Výstupní soubory jsou generovány v jednu výstupní složky: **adftutorial\customactivityoutput**.

   | Řez | Čas zahájení | Výstupní soubor |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Mějte na paměti, že všechny soubory ve vstupní složky jsou součástí řez s časy zahájení uvedených výše. Při zpracování této řezu se vlastní aktivita prohledává každý soubor a vytvoří řádek ve výstupním souboru s počtem výskytů hledaný termín ("Microsoft"). Pokud jsou inputfolder tři soubory, se ve výstupním souboru pro každý hodinové řez tři řádky: 2016-11-16-00.txt 2016-11-16:01:00:00.txt atd.
3. K nasazení **OutputDataset**, klikněte na tlačítko **nasadit** na panelu příkazů.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Vytvoření a spuštění kanálu, který používá vlastní aktivity
1. V editoru služby Data Factory, klikněte na tlačítko **... Další**a potom vyberte **nový kanál** na panelu příkazů. 
2. Nahraďte kód JSON v pravém podokně se následující skript JSON:

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

   * **Concurrency** je nastaven na **2** tak, aby dva řezy jsou zpracovávány paralelně 2 virtuální počítače ve fondu Azure Batch.
   * V části aktivit je jedna aktivita a je typu: **DotNetActivity**.
   * **AssemblyName** nastavena na název knihovny DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** je nastaven na **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** je nastaven na **AzureStorageLinkedService** který odkazuje na úložiště objektů blob, který obsahuje soubor zip vlastní aktivity. Pokud používáte jiné účty Azure Storage pro vstupní a výstupní soubory a soubor zip vlastní aktivitu, vytvoříte jiné propojené služby Azure Storage. Tento článek předpokládá, že používáte stejný účet úložiště Azure.
   * **PackageFile** je nastaven na **customactivitycontainer/MyDotNetActivity.zip**. Je ve formátu: containerforthezip/nameofthezip.zip.
   * Vlastní aktivita přijímá **InputDataset** jako vstup a **OutputDataset** jako výstup.
   * Vlastnost linkedServiceName vlastní aktivity odkazuje na **AzureBatchLinkedService**, která říká službě Azure Data Factory, který vlastní aktivita je potřeba spustit na virtuálních počítačích Azure Batch.
   * **isPaused** je nastavena na **false** ve výchozím nastavení. Kanál se spustí okamžitě v tomto příkladu protože řezy spustit v minulosti. Tuto vlastnost lze nastavit na hodnotu true pozastavení kanálu a nastavte ji zpět na hodnotu false restartovat.
   * **Spustit** čas a **end** časy jsou **pět** hodiny od sebe a řezy vytváří každou hodinu, takže pět řezy vytváří v kanálu.
3. Chcete-li nasadit kanálu, klikněte na tlačítko **nasadit** na panelu příkazů.

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. V okně Data Factory na webu Azure portal, klikněte na tlačítko **Diagram**.

    ![Dlaždice Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. V zobrazení diagramu klikněte teď OutputDataset.

    ![Zobrazení diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Měli byste vidět, že pět Výstup řezy jsou ve stavu Připraveno. Pokud nejsou ve stavu Připraveno, nebyly byla vypracována ještě. 

   ![Výstup řezy](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Ověřte, že jsou generovány výstupní soubory v úložišti objektů blob v **adftutorial** kontejneru.

   ![výstup z vlastní aktivity][image-data-factory-ouput-from-custom-activity]
5. Pokud můžete otevřít výstupní soubor, byste měli vidět výstup podobný následující výstup:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Použití [portál Azure] [ azure-preview-portal] nebo rutin prostředí Azure PowerShell sledovat vaše služby data factory, kanálů a datové sady. Zobrazí se zprávy z **ActivityLogger** v kódu vlastní aktivity v protokoly (konkrétně uživatel 0.log), které si můžete stáhnout z portálu nebo pomocí rutin.

   ![stažení protokolů z vlastní aktivity][image-data-factory-download-logs-from-custom-activity]

V tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md) podrobné pokyny pro monitorování datových sad a kanálů.      

## <a name="data-factory-project-in-visual-studio"></a>Projekt data Factory v sadě Visual Studio  
Můžete vytvořit a publikovat entity služby Data Factory pomocí sady Visual Studio místo použití portálu Azure. Podrobné informace o vytváření a publikování entit služby Data Factory pomocí sady Visual Studio, najdete v části [sestavit svůj první kanál pomocí sady Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) a [kopírování dat z objektu Blob Azure do Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) články.

Pokud vytváříte projekt Data Factory v sadě Visual Studio, proveďte následující kroky:
 
1. Přidejte do řešení sady Visual Studio, který obsahuje vlastní aktivity projektu projekt Data Factory. 
2. Přidáte odkaz na projekt aktivity .NET z projekt Data Factory. Klikněte pravým tlačítkem na projekt Data Factory, přejděte na **přidat**a potom klikněte na **odkaz**. 
3. V **přidat odkaz na** dialogové okno, vyberte **MyDotNetActivity** projektu a klikněte na tlačítko **OK**.
4. Sestavení a publikování řešení.

    > [!IMPORTANT]
    > Když publikujete entity služby Data Factory, soubor zip je pro vás automaticky vytvoří a je odeslán do kontejneru objektů blob: customactivitycontainer. Pokud kontejner objektů blob neexistuje, je vytvořeno automaticky příliš.  


## <a name="data-factory-and-batch-integration"></a>Integrace služby Data Factory a Batch
Služba Data Factory vytvoří úlohu ve službě Azure Batch s názvem: **adf poolname: xxx úlohy**. Klikněte na tlačítko **úlohy** v levé nabídce. 

![Azure Data Factory - úlohy Batch](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Úloha se vytvoří při každém spuštění aktivity řezu. Pokud existují pět řezy, které jsou připravené ke zpracování, jsou v této úloze vytvořit pět úloh. Pokud existují několika výpočetních uzlech ve fondu Batch, dva nebo více řezů může běžet paralelně. Pokud > 1 je nastavena maximální úkolů na výpočetním uzlu, může mít více než jeden řez na stejném výpočetním spuštěna.

![Azure Data Factory - úlohy Batch](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Následující diagram znázorňuje vztah mezi Azure Data Factory a dávkové úlohy.

![Objekt pro vytváření dat & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Řešení chyb
Řešení potíží se skládá z několika základní techniky:

1. Pokud se zobrazí následující chyba, můžete pomocí úložiště objektů blob aktivní/nástrojů místo použití úložiště objektů blob v Azure pro obecné účely. Nahrajte soubor zip **účet úložiště pro obecné účely Azure**. 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. Pokud se zobrazí následující chyba, zkontrolujte, zda název třídy v souboru CS odpovídá název zadaný pro **EntryPoint** vlastnost v kódu JSON kanálu. V tomto návodu je název třídy: MyDotNetActivity a vstupní bod v kódu JSON: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Pokud se názvy shodují, potvrďte, že jsou všechny binární soubory **kořenové složky** souboru zip. To znamená když otevřete soubor zip, měli byste vidět všechny soubory v kořenové složce, ne všechny podsložek.   
3. Pokud vstupní řez není nastaven na **připraven**, potvrďte správnost struktuře vstupní složky a **soubor.txt** existuje ve vstupní složkách.
3. V **Execute** metoda vlastní aktivity, použijte **IActivityLogger** objektu k protokolování informací, které vám pomůže vyřešit problémy. Zprávy zaznamenané objeví v souborech protokolu uživatele (jeden nebo více souborů s názvem: uživatel 0.log, 1.log uživatele, uživatel 2.log atd.).

   V **OutputDataset** okně klikněte na tlačítko řez zobrazíte **datový ŘEZ** okno pro tento řez. Zobrazí **běh aktivit** pro tento řez. Měli byste vidět jednu aktivitu spustit řez. Pokud jste na panelu příkazů klikněte na tlačítko spustit, můžete spustit jiné aktivity při spuštění stejné řez.

   Když kliknete na aktivity při spuštění, uvidíte **podrobnosti o spuštění aktivit** okno se seznamem souborů protokolu. Zobrazí zprávy zaznamenané v souboru user_0.log. Když dojde k chybě, zobrazí tři běh aktivit, protože počet opakování je nastavena na hodnotu 3 v kódu JSON kanálu nebo aktivity. Po kliknutí na tlačítko spustit aktivitu, zobrazí se soubory protokolů, které můžete zkontrolovat, chcete-li vyřešit chyby.

   V seznamu souborů protokolu, klikněte na **uživatele 0.log**. V pravém panelu jsou výsledky pomocí **IActivityLogger.Write** metoda. Pokud nevidíte všechny zprávy, zkontrolujte, zda máte další soubory protokolu s názvem: user_1.log, user_2.log atd. Kód nebyla úspěšná, jinak hodnota po poslední přihlášení zprávy.

   Kromě toho zkontrolujte **systému 0.log** pro všechny chybové zprávy systému a výjimky.
4. Zahrnout **PDB** souborů v souboru zip tak, aby podrobnosti o chybě informace, jako **zásobník volání** když dojde k chybě.
5. Všechny soubory v souboru .zip pro vlastní aktivitu musí být na **nejvyšší úrovni**, bez podsložek.
6. Ujistěte se, že **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), a **packageLinkedService** (by měla odkazovat na **pro obecné účely**úložiště objektů blob v Azure, který obsahuje soubor zip) jsou nastaveny na správný hodnoty.
7. Pokud jste opravili chybu a chcete řez zpracovat znovu, klikněte na něj v okně **OutputDataset** pravým tlačítkem myši a potom klikněte na **Spustit**.
8. Pokud se zobrazí následující chyba, používáte Azure Storage balíček verze > 4.3.0. Spouštěč služby Data Factory vyžaduje 4.3 verzi WindowsAzure.Storage. V tématu [izolace domény aplikace](#appdomain-isolation) části pro řešení, pokud musíte použít novější verzi sestavení Azure Storage. 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Pokud použijete 4.3.0 verzi balíčku Azure Storage, odeberte existující odkaz na balíček verze > 4.3.0 Azure Storage. Potom spusťte následující příkaz z konzoly Správce balíčků NuGet. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Sestavte projekt. Odstraňte ze složky bin\Debug Azure.Storage sestavením verze > 4.3.0. Vytvořte soubor zip s binární soubory a souboru PDB. Nahraďte původní soubor zip touto v kontejneru objektů blob (customactivitycontainer). Znovu spustit datové řezy, které se nezdařilo (klikněte pravým tlačítkem na řez a klikněte na tlačítko Spustit).   
8. Vlastní aktivity nepoužívá **app.config** soubor ze svého balíčku. Proto pokud váš kód čte libovolné púřipojovací řetězce z konfiguračního souboru, ale nefunguje za běhu. Osvědčeným postupem při použití Azure Batch je pro uložení všech tajných klíčů v **Azure KeyVault**, použijte objekt služby založené na certifikátech k ochraně **keyvault**a distribuovat certifikát, který chcete Azure Batch fond. Vlastní aktivita .NET potom má přístup k tajným klíčům v trezoru za běhu. Toto řešení je obecný řešení a můžete škálovat k libovolnému typu tajný klíč, ne jenom připojovací řetězec.

   Je snazší alternativní řešení (ale není z hlediska): můžete vytvořit **propojená služba Azure SQL** pomocí nastavení připojovacího řetězce, vytvořit datovou sadu, která používá propojené služby a řetězu datovou sadu jako fiktivní vstupní datové sady do vlastní aktivity .NET. Máte přístup připojovací řetězec propojené služby v kódu vlastní aktivity.  

## <a name="update-custom-activity"></a>Aktualizovat vlastní aktivitu
Pokud aktualizujete kód vlastní aktivity, sestavte jej a nahrajte soubor zip, který obsahuje nové binární soubory do úložiště objektů blob.

## <a name="appdomain-isolation"></a>Izolace domény aplikace
V tématu [mezi ukázka AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) který ukazuje, jak vytvořit vlastní aktivity, které nejsou rozděleny do verze sestavení používá Spouštěče objekt pro vytváření dat (Příklad: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x atd.).

## <a name="access-extended-properties"></a>Přístup rozšířené vlastnosti
Je možné deklarovat rozšířených vlastností v kódu JSON, jak znázorňuje následující ukázka aktivity:

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


V příkladu jsou dvě rozšířené vlastnosti: **SliceStart** a **DataFactoryName**. Hodnota pro SliceStart je založená na proměnnou SliceStart systému. V tématu [systémové proměnné](data-factory-functions-variables.md) seznam podporovaných systémové proměnné. Hodnota DataFactoryName je pevně zakódovaná na CustomActivityFactory.

Pro přístup k těmto rozšířené vlastnosti v **Execute** metoda, použít kód podobná následující kód:

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
Můžete také vytvořit fond Azure Batch s **škálování** funkce. Můžete například vytvořit fondu azure batch s 0 vyhrazených virtuálních počítačích a vzorec škálování na základě počtu úkolů čekajících na zpracování. 

Vzorec ukázka tady dosahuje následující chování: při vytvoření fondu, začne s virtuálním Počítačem 1. Metrika $PendingTasks definuje počet úloh v spuštěná + aktivní (zařazených do fronty) stavu.  Vzorec najde průměrný počet úkolů čekajících na zpracování v posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy překročí 25 virtuálních počítačů. Tak, jako jsou odeslány nové úkoly, fondu automaticky zvětšování a jako dokončení úkolů, virtuální počítače stane volné jeden po druhém a automatické škálování zmenšuje těchto virtuálních počítačů. startingNumberOfVMs a maxNumberofVMs lze upravit vašim potřebám.

Vzorec škálování:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

V tématu [automatické škálování výpočetních uzlů ve fondu Azure Batch](../../batch/batch-automatic-scaling.md) podrobnosti.

Pokud fondu používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může trvat 15 až 30 minut Příprava virtuálního počítače před spuštěním vlastní aktivity.  Pokud fondu používá jiný autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Vytvoření vlastní aktivity pomocí .NET SDK
V tomto návodu v tomto článku vytvoříte objekt pro vytváření dat kanál, který používá vlastní aktivity pomocí portálu Azure. Následující kód ukazuje, jak vytvořit objekt pro vytváření dat pomocí .NET SDK. Můžete najít další podrobnosti o použití sady SDK k vytváření prostřednictvím kódu programu kanály v [vytvoření kanálu s aktivitou kopírování pomocí rozhraní API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) článku. 

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
[Azure Data Factory - místní prostředí](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) ukázce na Githubu zahrnuje nástroj, který vám umožní ladit vlastní .NET aktivity v sadě Visual Studio.  


## <a name="sample-custom-activities-on-github"></a>Ukázka vlastních aktivit na Githubu
| Ukázka | Jaké vlastní aktivita provede |
| --- | --- |
| [Stažení programu HTTP Data](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Stahování dat z koncový bod HTTP do Azure Blob Storage pomocí vlastní C# aktivitu v datové továrně. |
| [Ukázka postojích Analysis služby Twitter.](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Vyvolá modelu Azure ML a proveďte analýzu postojích, vyhodnocování, předpovědi atd. |
| [Spustit skript jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Vyvolá skript jazyka R spuštěním RScript.exe, který již má nainstalované R na clusteru HDInsight. |
| [Mezi domény aplikace .NET aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Používá jiný sestavení verze z těch, které jsou používané Spouštěče objekt pro vytváření dat |
| [Zpracování modelu v Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Znovu zpracuje model v Azure Analysis Services. |

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
