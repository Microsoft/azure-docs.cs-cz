---
title: Pomocí objektu pro vytváření dat a dávkové zpracování rozsáhlých datových sad | Microsoft Docs
description: Popisuje, jak zpracovávat obrovské objemy dat v kanál služby Azure Data Factory pomocí paralelní zpracování funkce Azure Batch.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ad883248097fd84e2fa064515b6c7a5232aaa3d3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621934"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Proces rozsáhlých datových sad pomocí objektu pro vytváření dat a Batch
> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [vlastní aktivity v datové továrně verze 2](../transform-data-using-dotnet-custom-activity.md).

Tento článek popisuje architekturu ukázkové řešení, které přesune a zpracuje rozsáhlých datových sad automatické a naplánované způsobem. Je také začátku do konce návod k implementaci řešení pomocí služby Data Factory a Azure Batch.

Tento článek je delší než typické článek, protože obsahuje návod celé ukázkové řešení. Pokud jste pro Batch a služby Data Factory nové, můžete další informace o těchto službách a jak pracují společně. Pokud znáte něco o službách a jsou návrhu nebo architektury řešení, můžete se zaměřit na [části architektura](#architecture-of-sample-solution) článku. Pokud vyvíjíte prototypu nebo řešení, můžete chtít vyzkoušet podrobných pokynů v [návod](#implementation-of-sample-solution). Doporučujeme komentář k tomuto obsahu a jeho použití.

Nejprve podíváme, jak služby Data Factory a Batch můžete proces rozsáhlých datových sad v cloudu.     

## <a name="why-azure-batch"></a>Proč Azure Batch?
 Dávky můžete efektivně spouštět rozsáhlé paralelní a vysoce výkonné výpočty (HPC) aplikace v cloudu. Je platforma služba, která plánuje výpočetně náročných ke spuštění ve spravované kolekci virtuálních počítačů (VM). Ji může automaticky škálovat výpočetní prostředky, které splňovaly potřeby vašich úloh.

Pomocí služby Batch definujete výpočetní prostředky, které vaše aplikace spustí paralelně a škálovaně. Můžete spustit na vyžádání nebo naplánované úlohy. Nemusíte ručně vytvářet, konfigurovat a spravovat clusteru HPC, jednotlivé virtuální počítače, virtuální sítě, nebo komplexních úloh a úloh plánování infrastruktury.

 Pokud se nevyznáte v Batch, v těchto článcích vám pomůže porozumět architektura nebo implementace řešení popsaných v tomto článku:   

* [Základy služby Batch](../../batch/batch-technical-overview.md)
* [Přehled funkcí Batch](../../batch/batch-api-basics.md)

Volitelně můžete další informace o Batch, najdete v části [studijní Batch](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Proč Azure Data Factory?
Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat. Data Factory slouží k vytvoření spravované datové kanály, které přesun dat z místní a cloudové úložiště dat do úložiště dat centralizované. Příkladem je úložiště objektů Blob v Azure. Data Factory slouží k procesu nebo transformace dat pomocí služby, například Azure HDInsight a Azure Machine Learning. Můžete také naplánovat datových kanálů spustit naplánované způsobem (například hodinový, denní a jednou týdně). Můžete monitorovat a Správa kanálů na první pohled identifikovat problémy a provést akci.

  Pokud se nevyznáte v objektu pro vytváření dat, v těchto článcích vám pomůže porozumět architektura nebo implementace řešení popsaných v tomto článku:  

* [Úvod do služby Data Factory](data-factory-introduction.md)
* [Sestavit svůj první kanál dat](data-factory-build-your-first-pipeline.md)   

Volitelně můžete další informace o objektu pro vytváření dat, najdete v části [studijní postup pro vytváření dat](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Objekt pro vytváření dat a společně Batch
Objekt pro vytváření dat obsahuje zabudované aktivity. Například aktivita kopírování se používá k zkopírovat nebo přesunout data ze zdrojového úložiště dat do cílového úložiště dat. Aktivita Hive se používá ke zpracování dat pomocí clusterů systému Hadoop (HDInsight) v Azure. Seznam aktivit transformace podporované, najdete v části [aktivit transformace dat](data-factory-data-transformation-activities.md).

Také můžete vytvořit vlastní aktivity .NET chcete přesunout nebo zpracování dat s vlastní logikou. Tyto aktivity můžete spustit v clusteru HDInsight nebo ve fondu Batch virtuálních počítačů. Při použití služby Batch, můžete nakonfigurovat fond, který chcete používat automatické škálování (Přidat nebo odebrat na základě příslušného zatížení virtuálních počítačů) podle vzorce zadáte.     

## <a name="architecture-of-a-sample-solution"></a>Architektura ukázkové řešení
  Jednoduchým řešením je architektury popsané v tomto článku. Se taky hodí pro komplexní scénáře, jako je riziko modelování finančních služeb, zpracování obrázků a vykreslování a Genomické analýzy.

Diagram znázorňuje, jak Data Factory orchestruje přesuny dat a zpracování. Také ukazuje, jak Batch zpracovává data paralelní způsobem. Stáhnout a vytisknout diagram referenční (11 × 17 palců nebo velikost A3). Pro přístup k diagramu tak, aby jej vytisknout, najdete v části [orchestration HPC a data pomocí Batch a objektu pro vytváření dat](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram zpracování velkých dat](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Následující seznam uvádí základní kroky procesu. Řešení obsahuje kód a vysvětlení, k vytvoření řešení začátku do konce.

* **Batch nakonfigurujte fond výpočetních uzlů (VM).** Můžete zadat počet uzlů a velikost každého uzlu.

* **Vytvoření instance služby Data Factory** nakonfigurovaný s entitami, které představují úložiště objektů blob, výpočetní služby Batch, vstupní a výstupní data a pracovní postup nebo kanálu s aktivitami, které přesunout a transformovat data.

* **Vytvořte vlastní .NET aktivitu v kanálu Data Factory.** Aktivita je váš kód uživatele, který běží ve fondu Batch.

* **Ukládat velké množství vstupních dat jako objekty BLOB ve službě Azure Storage.** Data je rozdělena do logické řezy (obvykle čas).

* **Objekt pro vytváření dat zkopíruje data, která zpracovává paralelně** do sekundárního umístění.

* **Objekt pro vytváření dat se spustí vlastní aktivita fondu přidělené dávkou.** Objekt pro vytváření dat mohou být aktivity současně. Každá aktivita zpracovává řez data. Výsledky jsou uloženy v úložišti.

* **Objekt pro vytváření dat přesune konečných výsledků do třetí umístění,** pro distribuci přes aplikace nebo pro další zpracování jiných nástrojů.

## <a name="implementation-of-the-sample-solution"></a>Implementace ukázkové řešení
Ukázkové řešení je záměrně jednoduchá. Je určený ke ukazují, jak používat společně s datovými sadami proces služby Data Factory a Batch. Řešení spočítá počet výskytů hledaný termín "Microsoft" v vstupní soubory, které jsou uspořádány do časové řady. Výstupy pak počet, který má výstupní soubory.

**Čas:** Pokud jste se seznámili se základy Azure Data Factory a Batch a dokončili následující předpoklady, toto řešení trvá jednu až dvě hodiny.

### <a name="prerequisites"></a>Požadavky
#### <a name="azure-subscription"></a>Předplatné Azure
Pokud nemáte předplatné Azure, můžete rychle vytvořit Bezplatný zkušební účet. Další informace najdete v tématu [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Účet služby Azure Storage
Použít účet úložiště pro ukládání dat v tomto kurzu. Pokud nemáte účet úložiště, najdete v části [vytvořit účet úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Ukázkové řešení používá úložiště objektů blob.

#### <a name="azure-batch-account"></a>Účet Azure Batch
Vytvoření účtu Batch pomocí [portál Azure](http://portal.azure.com/). Další informace najdete v tématu [vytvořit a spravovat účet Batch](../../batch/batch-account-create-portal.md). Všimněte si klíč účet a název účtu Batch. Můžete taky použít [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) rutiny k vytvoření účtu Batch. Pokyny pro použití této rutiny najdete v tématu [Začínáme pomocí rutin prostředí PowerShell Batch](../../batch/batch-powershell-cmdlets-get-started.md).

Ukázkové řešení Batch (nepřímo prostřednictvím kanálu objekt pro vytváření dat) používá k datům procesu paralelní způsobem ve fondu výpočetních uzlů (spravované kolekce virtuálních počítačů).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch fondu virtuálních počítačů
Vytvoření fondu služby Batch s alespoň dvěma výpočetní uzly.

1. V [portál Azure](https://portal.azure.com), vyberte **Procházet** v levé nabídce a vyberte **účty Batch**.

2. Vyberte svůj účet Batch **účtu Batch** okno.

3. Vyberte **fondy** dlaždici.

4. Na **fondy** okně, vyberte **přidat** tlačítka na panelu nástrojů na Přidat fond.

   a. Zadejte ID fondu (**ID fondu**). Poznamenejte si ID fondu. Budete ho potřebovat při vytváření řešení data factory.

   b. Zadejte **Windows Server 2012 R2** pro **operační systém řady** nastavení.

   c. Vyberte **cenovou úroveň uzlu**.

   d. Zadejte **2** hodnotu **cíl vyhrazené** nastavení.

   e. Zadejte **2** hodnotu **maximální počet úloh na uzel** nastavení.

   f. Vyberte **OK** vytvoříte fond.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Používáte [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) nebo [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (z ClumsyLeaf Software), aby kontrolovat a upravte data ve vašich projektů úložiště. Také můžete zkontrolovat a změnit data v protokolech aplikací hostovaných v cloudu.

1. Vytvořit kontejner s názvem **můj_kontejner** s přístupem k privátní (bez anonymního přístupu).

2. Pokud používáte CloudXplorer, vytvořte složky a podsložky s následující strukturou:

   ![Struktura složky a podsložky](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` a `outputfolder` jsou nejvyšší úrovně složky v `mycontainer`. `inputfolder` Složka obsahuje podsložky razítka data a času (rrrr-MM-DD-HH).

   Pokud používáte Storage Explorer, v dalším kroku, můžete odeslat soubory s těmito názvy: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`a tak dále. Tento krok automaticky vytvoří složky.

3. Vytvořte textový soubor **soubor.txt** na počítači s obsahem, který obsahuje klíčové slovo **Microsoft**. Je například "testovací vlastní aktivity Microsoft test vlastní aktivity společnosti Microsoft."

4. Nahrajte soubor do následující složky vstupní v úložišti objektů blob:

   ![Vstupní složky](./media/data-factory-data-processing-using-batch/image4.png)

   Pokud používáte Storage Explorer, nahrajte **soubor.txt** do souboru **můj_kontejner**. Vyberte **kopie** na panelu nástrojů vytvořit kopii objektu blob. V **kopírovat objekt Blob** dialogové okno, změny **název cílového objektu blob** k `inputfolder/2015-11-16-00/file.txt`. Opakujte tento krok k vytvoření `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`a tak dále. Tato akce automaticky vytvoří složky.

5. Vytvořte jiný kontejner s názvem `customactivitycontainer`. Nahrajte soubor zip vlastní aktivitu do tohoto kontejneru.

#### <a name="visual-studio"></a>Visual Studio
Nainstalujte Visual Studio 2012 nebo novějším k vytvoření vlastní dávkové aktivity, který se má použít v řešení data factory.

### <a name="high-level-steps-to-create-the-solution"></a>Základní kroky pro vytvoření řešení
1. Vytvořte vlastní aktivity, která obsahuje logiku zpracování dat.

2. Vytvořte objekt pro vytváření dat, která používá vlastní aktivity.

### <a name="create-the-custom-activity"></a>Vytvořit vlastní aktivitu
Vlastní aktivity objekt pro vytváření dat je jádrem této ukázkové řešení. Ukázkové řešení Batch používá ke spuštění vlastní aktivity. Informace o tom, jak vyvíjet vlastní aktivity a použijte je v kanálů služby data factory najdete v tématu [použít vlastní aktivity v kanálu objekt pro vytváření dat](data-factory-use-custom-activities.md).

Pokud chcete vytvořit vlastní aktivity rozhraní .NET, který můžete použít v datovém kanálu objekt pro vytváření, vytvoříte s třídu, která implementuje rozhraní IDotNetActivity projektu knihovny tříd rozhraní .NET. Toto rozhraní obsahuje pouze jednu metodu: spuštění. Tady je podpis metody:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoda má několik klíčové komponenty, které je třeba porozumět:

* Tato metoda přebírá čtyř parametrů:

  * **linkedServices**. Tento parametr je vyčíslitelná seznam propojené služby, které odkazují vstupní a výstupní datové zdroje (například úložiště objektů blob) k objektu pro vytváření dat. V této ukázce je pouze jeden propojené služby typu úložiště Azure pro vstup a výstup.
  * **datové sady**. Tento parametr je vyčíslitelná seznam datových sad. Tento parametr můžete získat umístění a schémata definované vstupní a výstupní datové sady.
  * **aktivita**. Tento parametr představuje aktuální výpočetní entitu. V takovém případě je služba Batch.
  * **protokolovač**. Protokolovacího nástroje můžete použít pro zapsání ladění komentáře tento prostor jako protokol "User" pro kanál.
* Metoda vrátí slovník, který slouží k řetězu vlastní aktivity společně v budoucnu. Tato funkce není dosud implementována, tak, aby metoda vrátí prázdný slovník.

#### <a name="procedure-create-the-custom-activity"></a>Postup: Vytvoření vlastní aktivity
1. Vytvoření projektu knihovny tříd rozhraní .NET v sadě Visual Studio.

   a. Spusťte Visual Studio 2012/2013 nebo 2015.

   b. Vyberte **Soubor** > **Nový** > **Projekt**.

   c. Rozbalte položku **šablony**a vyberte **Visual C\#**. V tomto návodu můžete použít C\#, ale můžete použít kterémkoli jazyce platformy .NET pro vývoj vlastní aktivity.

   d. Vyberte **knihovny tříd** ze seznamu typů projektu na pravé straně.

   e. Zadejte **MyDotNetActivity** pro **název**.

   f. Vyberte **C:\\ADF** pro **umístění**. Vytvořit složku **ADF** Pokud neexistuje.

   g. Vyberte **OK** a vytvořte tak projekt.

2. Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

3. V konzole Správce balíčků spustíte následující příkaz pro import Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Import **Azure Storage** balíček NuGet do projektu. Tento balíček je třeba, protože v této ukázce použijete rozhraní API úložiště objektů Blob:

    ```powershell
    Install-Package Azure.Storage
    ```
5. Přidejte následující direktivy using ke zdrojovému souboru v projektu:

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
6. Změňte název oboru názvů **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Změnit název třídy pro **MyDotNetActivity**a odvodí z **IDotNetActivity** rozhraní, jak je znázorněno:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementace (Přidat) **Execute** metodu **IDotNetActivity** rozhraní k **MyDotNetActivity** – třída. Zkopírujte následující vzorový kód do metody. Vysvětlení logikou používanou v této metodě naleznete v tématu [provést metodu](#execute-method) části.

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
9. Přidejte následující pomocné metody pro třídu. Tyto metody jsou vyvolány **Execute** metoda. Nejdůležitější, **Calculate** metoda izoluje kód, který iteruje v rámci jednotlivých objektů blob.

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
    Metoda GetFolderPath vrací cestu ke složce, která odkazuje datovou sadu na a vrátí název objektu blob nebo soubor, který datová sada odkazuje na metodu GetFileName.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoda Calculate vypočítá počet instancí – klíčové slovo "Microsoft" v vstupní soubory (objekty BLOB ve složce). Hledaný termín "Microsoft" je pevně zakódovaná v kódu.

10. Kompilace projektu. Vyberte **sestavení** z nabídky a pak vyberte **sestavit řešení**.

11. Spusťte program Průzkumník Windows a přejděte na **bin\\ladění** nebo **bin\\verze** složky. Výběr složky závisí na typu sestavení.

12. Vytvořte soubor zip **MyDotNetActivity.zip** obsahující všechny binární soubory v  **\\bin\\ladění** složky. Můžete chtít zahrnout MyDotNetActivity. **pdb** tak, aby získat další podrobnosti, jako je například číslo řádku ve zdrojovém kódu, která způsobila problém, když dojde k chybě.

   ![Seznam bin\Debug složek](./media/data-factory-data-processing-using-batch/image5.png)

13. Nahrát **MyDotNetActivity.zip** jako objekt blob do kontejneru objektů blob `customactivitycontainer` ve službě blob storage, aby StorageLinkedService propojená služba v ADFTutorialDataFactory používá. Vytvoření kontejneru objektů blob `customactivitycontainer` Pokud ještě neexistuje.

#### <a name="execute-method"></a>Execute – metoda
Tato část obsahuje další podrobnosti o kód v Metoda Execute.

1. Členy pro iterace v rámci vstupní kolekce jsou součástí [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) oboru názvů. K iteraci v rámci kolekce objektů blob, budete vyzváni k použití **BlobContinuationToken** třídy. V podstatě, je nutné použít DNT-při smyčky pomocí tokenu jako mechanismus pro ukončení opakování. Další informace najdete v tématu [použití objektů Blob storage pomocí technologie .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Zobrazí se zde základní smyčka:

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
   Další informace najdete v dokumentaci pro [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) metoda.

2. Kód pro práci prostřednictvím sady objektů BLOB logicky přejde v rámci aplikace-při smyčky. V **Execute** metoda, do-při smyčky předá seznam objektů BLOB metodu s názvem **Calculate**. Metoda vrátí řetězec proměnné s názvem **výstup** tedy výsledek s vstupní prostřednictvím všech objektů BLOB v segmentu.

   Vrátí počet výskytů hledaný termín "Microsoft" v objektu blob předaný **Calculate** metoda.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Po **Calculate** dokončení metody musí být zapsané do nového objektu blob. Pro každou sadu objektů BLOB zpracovat nový objekt blob může být napsán s výsledky. Zápis do nového objektu blob, nejdřív najdete výstupní datovou sadu.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. Kód také volá metodu helper **GetFolderPath** načíst cesta ke složce (název kontejneru úložiště).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoda GetFolderPath vrhá objekt datové sady, který má AzureBlobDataSet, který má vlastnost s názvem FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Volání kódu **GetFileName** metoda pro načtení názvu souboru (název objektu blob). Kód je podobná předchozí kód, který se použije k získání cesty ke složce.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Název souboru je zapsán vytvořením objektu URI. Konstruktor identifikátoru URI používá **BlobEndpoint** vlastnost vrátit název kontejneru. Název složky a cesta k souboru se přidají k vytvoření identifikátor URI objektu blob výstup.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Po zapsání název souboru, můžete napsat výstupní řetězec z **Calculate** metoda do nového objektu blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Vytvoření objektu pro vytváření dat
V [vytvořit vlastní aktivitu](#create-the-custom-activity) části jste vytvořili vlastní aktivity a nahrát soubor zip binární soubory služby a souboru PDB na kontejner objektů blob. V této části vytvoříte objekt pro vytváření dat kanál, který používá vlastní aktivity.

Vstupní datové sady pro vlastní aktivita představuje objekty BLOB (soubory) ve vstupní složky (`mycontainer\\inputfolder`) v úložišti objektů blob. Představuje výstupní datovou sadu aktivity výstup objektů BLOB v zadané výstupní složce (`mycontainer\\outputfolder`) v úložišti objektů blob.

Vyřaďte jeden nebo více souborů do vstupní složky:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Do každé ze složky například vyřaďte jeden soubor (soubor.txt) s následujícím obsahem:

```
test custom activity Microsoft test custom activity Microsoft
```

Každé vstupní složky odpovídá řez v objektu pro vytváření dat i v případě, že složka obsahuje dvě nebo více souborů. Při zpracování každý řez v kanálu vlastní aktivita iteruje všechny objekty BLOB ve složce vstupní pro tento řez.

Uvidíte pět výstupní soubory se stejným obsahem. Například výstupní soubor z zpracování souboru ve složce 2015 11 16 00 má následující obsah:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Pokud je do vstupní složky vyřadit více souborů (soubor.txt, Soubor2.txt, file3.txt) se stejným obsahem, zobrazí se následující obsah ve výstupním souboru. Každé složky (2015-11-16-00 atd.) odpovídá řez v této ukázce to i v případě, že složka má více vstupních souborů.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Výstupní soubor má tři řádky, jeden pro každý vstupní soubor (binární rozsáhlý objekt) ve složce přidružené řezu (2015-11-16-00).

Úloha se vytvoří pro každou aktivitu spustit. V této ukázce je jenom jedna aktivita v kanálu. Při zpracování řezu v kanálu vlastní aktivita běží na Batch ke zpracování řezu. Protože nejsou k dispozici pět řezy (každý řez může mít více objektů BLOB nebo soubor), jsou ve službě Batch vytvořit pět úloh. Když úloha běží na Batch, je vlastní aktivity, která je spuštěná.

Následující názorný postup obsahuje další podrobnosti.

#### <a name="step-1-create-the-data-factory"></a>Krok 1: Vytvoření objektu pro vytváření dat
1. Po přihlášení na [portál Azure](https://portal.azure.com/), proveďte následující kroky:

   a. Vyberte **nový** v levé nabídce.

   b. Vyberte **Data + analýzy** na **nový** okno.

   c. Vyberte **Data Factory** na **analýzy dat** okno.

2. Na **nový objekt pro vytváření dat** okno, zadejte **CustomActivityFactory** pro název. Název datové továrny musí být globálně jedinečný. Pokud se zobrazí chyba "název objektu pro vytváření dat CustomActivityFactory není k dispozici", změňte název objektu pro vytváření dat. Například použijte yournameCustomActivityFactory a objektu pro vytváření dat vytvořit znovu.

3. Vyberte **název skupiny prostředků**a vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků.

4. Ověřte správnost předplatného a oblasti, kde chcete objekt pro vytváření dat vytvořit.

5. Vyberte **vytvořit** na **nový objekt pro vytváření dat** okno.

6. Vytvoří objekt pro vytváření dat se v řídicím panelu portálu.

7. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí **objekt pro vytváření dat** stránku, která zobrazuje obsah objektu pro vytváření dat.

   ![Stránka objektu pro vytváření dat](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Krok 2: Vytvoření propojených služeb
Propojené služby propojují úložiště dat nebo výpočetní služby s služby data factory. V tomto kroku propojíte svůj účet úložiště a účtu Batch pro vytváření dat.

#### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. Vyberte **vytvořit a nasadit** na dlaždici **objekt pro vytváření dat** okno pro **CustomActivityFactory**. Zobrazí se editoru služby Data Factory.

2. Vyberte **nové datové úložiště** na panelu příkazů a zvolte **úložiště Azure.** Skript JSON, který použijete k vytvoření se zobrazí v editoru propojené služby úložiště.

   ![Nové datové úložiště](./media/data-factory-data-processing-using-batch/image7.png)

3. Nahraďte **název účtu** názvem vašeho účtu úložiště. Nahraďte **klíč účtu** přístupovým klíčem účtu úložiště. Zjistěte, jak získat přístupový klíč k úložišti, najdete v tématu [zobrazení, kopírování a opětovné vytváření úložiště přístupové klíče](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

   ![Nasazení](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Vytvoření služby Azure Batch propojené
V tomto kroku vytvoříte propojené služby pro účet Batch, který se používá ke spuštění aktivity vlastní data factory.

1. Vyberte **nový výpočet** na panelu příkazů a zvolte **Azure Batch.** Skript JSON, který použijete k vytvoření se zobrazí v editoru propojené služby Batch.

2. Ve skriptu JSON:

   a. Nahraďte **název účtu** s názvem vašeho účtu Batch.

   b. Nahraďte **přístupový klíč** přístupovým klíčem k účtu Batch.

   c. Zadejte ID fondu **poolName** vlastnost. Pro tuto vlastnost můžete zadat název fondu nebo ID fondu.

   d. Zadejte batch identifikátor URI pro **batchUri** vlastnost JSON.

      > [!IMPORTANT]
      > Adresu URL z **účtu Batch** okno je v následujícím formátu: \<accountname\>.\< oblast\>. batch.azure.com. Pro **batchUri** vlastnost skript JSON, je třeba odebrat a88 "accountname." ** z adresy URL. Příklad: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Okno účtu batch](./media/data-factory-data-processing-using-batch/image9.png)

      Pro **poolName** vlastnosti také můžete zadat ID fondu namísto názvu fondu.

      > [!NOTE]
      > Služba Data Factory nepodporuje možnost na vyžádání pro dávku stejně pro HDInsight. V datové továrně můžete jenom vlastní fondu služby Batch.
      >
      >
   
   e. Zadejte **StorageLinkedService** pro **linkedServiceName** vlastnost. Tuto propojenou službu jste vytvořili v předchozím kroku. Toto úložiště se používá jako pracovní oblast pro soubory a protokoly.

3. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

#### <a name="step-3-create-datasets"></a>Krok 3: Vytvoření datové sady
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data.

#### <a name="create-the-input-dataset"></a>Vytvoření vstupní datové sady
1. V editoru služby Data Factory, vyberte **nová datová sada** tlačítka na panelu nástrojů. Vyberte **úložiště objektů Azure Blob** z rozevíracího seznamu.

2. Nahraďte skript JSON v pravém podokně následujícím fragmentem kódu JSON:

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

    Vytvoření kanálu dále v tomto návodu se časem spuštění 2015-11-16T00:00:00Z a end čas 2015-11-16T05:00:00Z. Má naplánované nevytvořila data každou hodinu, takže se pět řezy vstupní a výstupní (mezi **00**: 00:00 -\> **05**: 00:00).

    **Frekvence** a **interval** pro vstupní datové sady, které jsou nastaveny na **hodinu** a **1**, což znamená, že vstupní řez je k dispozici každou hodinu.

    Čas zahájení pro každý řez je reprezentována **SliceStart** systémové proměnné v předchozím fragmentu kódu JSON. Tady jsou časy zahájení pro každý řez.

    | **Řez** | **Čas spuštění**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    **FolderPath** se vypočítává pomocí rok, měsíc, den a hodina součástí řez čas spuštění (**SliceStart**). Zde je, jak je vstupní složky mapované na řez.

    | **Řez** | **Čas spuštění**          | **Vstupní složky**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3. Vyberte **nasadit** na panelu nástrojů vytvořit a nasadit **InputDataset** tabulky.

#### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
V tomto kroku vytvoříte jinou datovou sadu typu AzureBlob, která bude představovat výstupní data.

1. V editoru služby Data Factory, vyberte **nová datová sada** tlačítka na panelu nástrojů. Vyberte **úložiště objektů Azure Blob** z rozevíracího seznamu.

2. Nahraďte skript JSON v pravém podokně následujícím fragmentem kódu JSON:

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

    Objekt blob nebo soubor výstupu se generuje pro každý vstupní řez. Zde je, jak je výstupní soubor s názvem pro každý řez. Výstupní soubory jsou generovány v jednu výstupní složce `mycontainer\\outputfolder`.

    | **Řez** | **Čas spuštění**          | **Výstupní soubor**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Mějte na paměti, že všechny soubory v vstupní složky (například 2015 11 16 00) jsou součástí řez se časem spuštění 2015 11 16 00. Při zpracování této řezu se vlastní aktivita prohledává každý soubor a vytvoří řádek ve výstupním souboru s počtem výskytů hledaný termín "Microsoft". Pokud existují tři soubory ve složce 2015 11 16 00, existují tři řádky v výstupní soubor 2015-11-16-00.txt.

3. Vyberte **nasadit** na panelu nástrojů vytvořit a nasadit **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Krok 4: Vytvoření a spuštění kanálu s aktivitou vlastní
V tomto kroku vytvoříte kanál s jedna aktivita, vlastní aktivitu, kterou jste vytvořili dříve.

> [!IMPORTANT]
> Pokud ještě jste neodeslali **soubor.txt** jako vstup složek v kontejneru objektů blob, tak učinit před vytvořením kanálu. **IsPaused** vlastnost nastavena na hodnotu false v kanálu formát JSON, takže se kanál spustí okamžitě, protože **spustit** je datum v minulosti.
>
>

1. V editoru služby Data Factory, vyberte **nový kanál** na panelu příkazů. Pokud nevidíte příkaz, vyberte symbol tří teček můžete ho zobrazit.

2. Nahraďte skript JSON v pravém podokně následujícím fragmentem kódu JSON:

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

   * Je jenom jedna aktivita v kanálu, a je typu **DotNetActivity**.
   * **AssemblyName** nastavena na název knihovny DLL **MyDotNetActivity.dll**.
   * **EntryPoint** je nastaven na **MyDotNetActivityNS.MyDotNetActivity**. Je v podstatě \<obor názvů\>.\< Název třídy\> ve vašem kódu.
   * **PackageLinkedService** je nastaven na **StorageLinkedService**, který odkazuje na úložiště objektů blob, který obsahuje soubor zip vlastní aktivity. Pokud používáte jiným účtům úložiště pro soubory vstupu a výstupu a soubor zip vlastní aktivity, musíte vytvořit jiný propojená služba úložiště. Tento článek předpokládá, že používáte stejný účet úložiště.
   * **PackageFile** je nastaven na **customactivitycontainer/MyDotNetActivity.zip**. Je ve formátu \<containerforthezip\>/\<nameofthezip.zip\>.
   * Vlastní aktivita přijímá **InputDataset** jako vstup a **OutputDataset** jako výstup.
   * **LinkedServiceName** vlastnost vlastní aktivity odkazuje na **AzureBatchLinkedService**, která sděluje Data Factory, který vlastní aktivita je potřeba spustit na dávky.
   * **Souběžnosti** nastavení je důležité. Pokud použijete výchozí hodnotu, která je 1, i v případě, že máte dva nebo více výpočetních uzlů ve fondu Batch, řezy jsou zpracovávány jeden po druhém. Proto nejsou využívat výhod paralelní zpracování funkce služby Batch. Pokud nastavíte **souběžnosti** na vyšší hodnotu, například 2, znamená to, že dva řezy (odpovídá dvě úlohy ve službě Batch) může být zpracována ve stejnou dobu. V takovém případě jsou použity oba virtuální počítače ve fondu Batch. Nastavte vlastnost souběžnosti správně.
   * Pouze jednu úlohu (řez) je spustit na virtuálním počítači v libovolném bodě ve výchozím nastavení. Ve výchozím nastavení **maximální počet úloh na virtuální počítač** je nastaven na hodnotu 1 pro fondu služby Batch. Jako součást požadavky jste vytvořili fond se tato vlastnost nastavena na hodnotu 2. Proto dvě datové řezy factory můžete spustit na virtuálním počítači ve stejnou dobu.
    - **IsPaused** je nastavena na hodnotu false ve výchozím nastavení. Kanál se spustí okamžitě v tomto příkladu protože řezy spustit v minulosti. Tuto vlastnost lze nastavit **true** pozastavit do kanálu a nastavte ji zpět do **false** restartovat.
    -   **Spustit** a **end** časy jsou od sebe pět hodin. Řezy vytváří každou hodinu, takže pět řezy vytváří v kanálu.

3. Vyberte **Nasadit** na panelu příkazů a nasaďte kanál.

#### <a name="step-5-test-the-pipeline"></a>Krok 5: Testování kanálu
V tomto kroku otestovat kanálu přetažením souborů do vstupní složky. Spuštění testování kanál s jedním souborem pro každý vstupní složky.

1. Na **objekt pro vytváření dat** okno na portálu Azure, vyberte **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

2. V **Diagram** klikněte dvakrát na vstupní datové sady **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

3. **InputDataset** okno se zobrazí se všechny pět řezy, které jsou připravené. Upozornění **čas spuštění ŘEZU** a **ŘEZ KONCOVÝ čas** pro každý řez.

   ![Vstupní řez spuštění a ukončení](./media/data-factory-data-processing-using-batch/image12.png)

4. V **Diagram** zobrazit, vyberte možnost **OutputDataset**.

5. Řezy pět Výstup objeví v **připraven** stavu, pokud byly vytvořeny.

   ![Výstupní řez spuštění a ukončení](./media/data-factory-data-processing-using-batch/image13.png)

6. Zobrazit úlohy spojené s řezy pomocí portálu a najdete v části co virtuálního počítače byla spuštěna na každý řez. Další informace najdete v tématu [integraci služby Data Factory a Batch](#data-factory-and-batch-integration) části.

7. Výstupní soubory se zobrazí pod `mycontainer` v `outputfolder` ve službě blob storage.

   ![Výstupní soubory v úložišti](./media/data-factory-data-processing-using-batch/image15.png)

   Pět výstupní soubory nejsou uvedeny, jeden pro každý vstupní řez. Každý výstupních souborů má obsah podobná následující výstup:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Následující diagram znázorňuje, jak datové řezy factory mapování na úkoly ve službě Batch. V tomto příkladu má řez spustit pouze jeden.

   ![Diagram mapování řez](./media/data-factory-data-processing-using-batch/image16.png)

8. Nyní opakujte s více soubory ve složce. Vytvoření souborů **Soubor2.txt**, **file3.txt**, **file4.txt**, a **file5.txt** se stejným obsahem jako soubor.txt ve složce **2015-11-06-01**.

9. V zadané výstupní složce odstranit výstupní soubor **2015 11 16 01.txt**.

10. Na **OutputDataset** okno, klikněte pravým tlačítkem na řez s **čas spuštění ŘEZU** nastavena na **11/16/2015 01:00:00 AM**. Vyberte **spustit** na opětovné spuštění řezu proces opakovat. Řez teď má pět souborů místo jeden soubor.

    ![Spusťte](./media/data-factory-data-processing-using-batch/image17.png)

11. Po spuštění řezu a její stav je **připraven**, ověřte obsah ve výstupním souboru pro tento řez (**2015 11 16 01.txt**). Výstupní soubor se zobrazí pod `mycontainer` v `outputfolder` ve službě blob storage. Měla by existovat řádek pro každý soubor řezu.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Pokud odstraníte nebylo výstupní soubor 2015-11-16-01.txt předtím, než se pokusila s pěti vstupní soubory, zobrazí jeden řádek z předchozího spuštění řezu a pěti řádcích z aktuální spustit řez. Ve výchozím nastavení se připojí obsah do výstupního souboru, pokud již existuje.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integrace služby Data Factory a Batch
Služba Data Factory vytvoří úlohu v dávce s názvem `adf-poolname:job-xxx`.

![Úlohy batch](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Úloha v dané úloze se vytvoří při každém spuštění aktivity řezu. Když 10 řezy jsou připravené ke zpracování, vytvoří se 10 úkoly v úloze. Můžete mít více než jeden řez spouštět současně, pokud máte několika výpočetních uzlech ve fondu. Pokud je maximální počet úkolů na výpočetním uzlu nastavené na větší než 1, na stejnou výpočetní můžete spustit více než jeden řez.

V tomto příkladu nejsou pět řezů, takže se pět úlohy v dávce. S **souběžnosti** nastavena na **5** v kanálu JSON v datové továrně a **maximální počet úloh na virtuální počítač** nastavena na **2** ve fondu Batch s **2** virtuálních počítačů, úlohy běží rychleji. (Zkontrolujte, zda počáteční a koncový čas pro úlohy.)

Zobrazit dávkové úlohy a její úkoly, které jsou přidruženy řezy pomocí portálu a najdete v části co virtuálního počítače byla spuštěna na každý řez.

![Úlohy batch](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Ladění kanálu
Ladění se skládá z několika základní techniky.

1. Pokud vstupní řez není nastaven na **připraven**, potvrďte, že vstupní složky struktura je správný a že soubor.txt existuje ve vstupní složkách.

   ![Struktura vstupních složek](./media/data-factory-data-processing-using-batch/image3.png)

2. V **Execute** metoda vlastní aktivity, použijte **IActivityLogger** objektu k protokolování informací, které vám pomůže vyřešit problémy. Zprávy zaznamenané v uživatele zobrazí\_souboru protokolu 0.

   Na **OutputDataset** okně vyberte řez zobrazíte **datový řez** okno pro tento řez. V části **aktivita spuštěna**, najdete v části jednu aktivitu spustit řez. Pokud vyberete **spustit** na panelu příkazů můžete spustit jiné aktivity při spuštění stejné řez.

   Když vyberete aktivity při spuštění, se zobrazí **podrobnosti o spuštění aktivit** okno se seznamem souborů protokolu. Zobrazí zprávy zaznamenané v uživatele\_souboru protokolu 0. Když dojde k chybě, zobrazí tři běh aktivit, protože počet opakování je nastavena na hodnotu 3 v kódu JSON kanálu nebo aktivity. Vyberete-li spustit aktivitu, zobrazí se soubory protokolů, které můžete zkontrolovat, chcete-li vyřešit chyby.

   ![Okna OutputDataset a datový řez](./media/data-factory-data-processing-using-batch/image18.png)

   V seznamu souborů protokolu, vyberte **uživatele 0.log**. V pravém panelu, výsledky pomocí **IActivityLogger.Write** metoda zobrazí.

   ![Spusťte okno Podrobnosti aktivity](./media/data-factory-data-processing-using-batch/image19.png)

   Zkontrolujte 0.log systému, pro všechny chybové zprávy systému a výjimky.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Zahrnout **PDB** souborů v souboru zip tak, aby podrobnosti o chybě informace, jako je zásobníku volání při výskytu chyby.

4. Všechny soubory v souboru zip vlastní aktivity musí být na nejvyšší úrovni s ne v podsložkách.

   ![Seznam souborů zip vlastní aktivity](./media/data-factory-data-processing-using-batch/image20.png)

5. Ujistěte se, že **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), a **packageLinkedService** (by měla odkazovat na úložiště objektů blob, který obsahuje soubor zip) jsou nastaveny na správný hodnoty.

6. Pokud jste opravit k chybě a chcete znovu zpracovat řez, klikněte pravým tlačítkem na je řez ve **OutputDataset** a vyberte **spustit**.

   ![OutputDataset okna spustit](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Kontejner je ve službě blob storage s názvem `adfjobs`. Tento kontejner není automaticky odstraněn, ale můžete ji po dokončení testování řešení bezpečně odstranit. Podobně řešení data factory vytvoří dávkovou úlohu s názvem `adf-\<pool ID/name\>:job-0000000001`. Po dokončení testu řešení Pokud chcete, můžete odstranit tuto úlohu.
   >
   >
7. Vlastní aktivity nepoužívá **app.config** soubor ze svého balíčku. Proto pokud váš kód čte libovolné púřipojovací řetězce z konfiguračního souboru, nefunguje za běhu. Osvědčeným postupem při použití služby Batch je pro uložení všech tajných klíčů v Azure Key Vault. Potom použijte objekt na základě certifikátu služby k ochraně trezoru klíčů a distribuovat certifikát, který chcete fondu Batch. Vlastní aktivity rozhraní .NET můžete přistupovat tajné klíče z trezoru klíčů za běhu. Toto obecné řešení můžete škálovat k libovolnému typu tajný klíč, ne jenom připojovací řetězec.

    Je snazší alternativní řešení, ale se nejedná o osvědčený postup. Služby propojené databáze SQL můžete vytvořit připojení nastavení řetězce. Potom můžete vytvořit datovou sadu, která používá propojené služby a řetězu datovou sadu jako fiktivní vstupní datové sady do vlastní aktivity .NET. Máte přístup připojovací řetězec propojené služby v kódu vlastní aktivity. By měla fungovat bez problémů za běhu.  

#### <a name="extend-the-sample"></a>Ukázka rozšíření
Tato ukázka se dozvíte více o funkce pro vytváření dat a Batch můžete rozšířit. Například ke zpracování řezů v jiné časové rozmezí, proveďte následující kroky:

1. Přidejte následující podsložky v `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07, 2011 11 16 08 a 2015--11-16 09. Vstupní soubory umístíte do příslušné složky. Změnit koncový čas pro kanál z `2015-11-16T05:00:00Z` k `2015-11-16T10:00:00Z`. V **Diagram** klikněte dvakrát na **InputDataset** a potvrďte, že vstupní řezy jsou připraveny. Klikněte dvakrát na **OutputDataset** zobrazíte stav řezy výstup. V případě, že máte **připraven** stavu, zkontrolujte výstupní složku pro výstupní soubory.

2. Zvětšit nebo zmenšit **souběžnosti** nastavení pochopit, jak ovlivňuje výkon vašeho řešení, zejména zpracování, k níž dojde v dávce. Další informace o **souběžnosti** nastavení, najdete v části "krok 4: vytvoření a spuštění kanálu s aktivitou vlastní."

3. Vytvoření fondu s vyšší nebo nižší **maximální počet úloh na virtuální počítač**. Pokud chcete používat nový fond, kterou jste vytvořili, aktualizujte službu Batch propojené v řešení data factory. Další informace o **maximální počet úloh na virtuální počítač** nastavení, najdete v části "krok 4: vytvoření a spuštění kanálu s aktivitou vlastní."

4. Vytvoření fondu služby Batch s **škálování** funkce. Automatické škálování výpočetních uzlů ve fondu Batch je dynamické přizpůsobení výpočetní výkon, které používá vaše aplikace. 

    Vzorec ukázka tady dosahuje toto chování. Při vytvoření fondu, začíná jeden virtuální počítač. Metrika $PendingTasks definuje počet úloh v provozu a stavy aktivní (zařazených do fronty). Vzorec najde průměrný počet úkolů čekajících na zpracování v posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy překročí 25 virtuálních počítačů. Nové úkoly jsou předloženy, fondu automaticky rozšiřovat. Jako dokončení úkolů virtuální počítače se stanou volné jeden po druhém a automatické škálování zmenšuje těchto virtuálních počítačů. Můžete upravit startingNumberOfVMs a maxNumberofVMs vašim potřebám.
 
    Vzorec škálování:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Další informace najdete v tématu [automatické škálování výpočetních uzlů ve fondu služby Batch](../../batch/batch-automatic-scaling.md).

   Pokud fondu používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může trvat 15 až 30 minut Příprava virtuálního počítače před spuštěním vlastní aktivity. Pokud fondu používá jiný autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval plus 10 minut.

5. V ukázkové řešení **Execute** metoda vyvolá **Calculate** metoda, která zpracuje řez vstupní data k vytvoření řez výstupních dat. Můžete napsat vlastní metodu ke zpracování vstupních dat a nahraďte **Calculate** volání metody **Execute** metoda pomocí volání pro metodu.

### <a name="next-steps-consume-the-data"></a>Další kroky: využívat data
Po při zpracování dat, budete moct pracovat s online nástrojů, jako je Power BI. Tady jsou odkazy, které vám pomohou pochopit Power BI a způsobu jeho použití v Azure:

* [Prozkoumejte datovou sadu v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Začínáme s Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Aktualizovat data v Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure a Power BI: základní přehled](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Odkazy
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Úvod do služby Data Factory](data-factory-introduction.md)
  * [Začínáme s Data Factory](data-factory-build-your-first-pipeline.md)
  * [Použít vlastní aktivity v kanálu pro vytváření dat](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Základy služby Batch](../../batch/batch-technical-overview.md)
  * [Přehled funkcí Batch](../../batch/batch-api-basics.md)
  * [Vytvoření a Správa účtu Batch na portálu Azure](../../batch/batch-account-create-portal.md)
  * [Začínáme s knihovnou klienta Batch pro .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
