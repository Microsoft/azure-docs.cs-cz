---
title: Zpracování rozsáhlých datových sad pomocí Data Factory a Batch | Dokumentace Microsoftu
description: Popisuje, jak zpracovat obrovská množství dat v kanálu Azure Data Factory pomocí paralelní zpracování funkce služby Azure Batch.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: adb9fb649d934d08ea546759bcf4733a1c6d9080
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822744"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Zpracování rozsáhlých datových sad pomocí Data Factory a Batch
> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [vlastní aktivity ve službě Data Factory](../transform-data-using-dotnet-custom-activity.md).

Tento článek popisuje architekturu ukázkové řešení, která přesunu a zpracování rozsáhlých datových sad automatické a naplánovaným způsobem. Také poskytuje návod začátku do konce implementovat řešení pomocí služby Data Factory a Azure Batch.

Tento článek je delší než typické článek, protože obsahuje návod, celé ukázkové řešení. Pokud jste služby Batch a Data Factory teprve začínáte, můžete další informace o těchto službách, a jak spolu fungují. Pokud něco o službách, které znáte a jsou návrh/aplikační architektura založená na řešení, můžete se zaměřit na architekturu části tohoto článku. Pokud vytváříte prototyp nebo řešení, můžete chtít vyzkoušet podrobných pokynů v tomto návodu. Zveme komentář k tomuto obsahu a jak je používáte.

Nejprve Podívejme se na jak služby Data Factory a Batch vám může pomoci zpracování velkých datových sad v cloudu.     

## <a name="why-azure-batch"></a>Proč Azure Batch?
 Můžete použít Batch umožňuje efektivně spouštět rozsáhlé paralelní a vysoce výkonné aplikace výpočetního prostředí (HPC) v cloudu. Je služba platformy, která plánuje výpočetně náročné práce ke spuštění ve spravované kolekci virtuálních počítačů (VM). Může automaticky škálovat výpočetní prostředky, které splňují potřeby vašich úloh.

Pomocí služby Batch definujete výpočetní prostředky, které vaše aplikace spustí paralelně a škálovaně. Můžete spustit na vyžádání nebo naplánované úlohy. Není nutné ručně vytvářet, konfigurovat a spravovat cluster prostředí HPC, jednotlivé virtuální počítače, virtuální sítě, nebo komplexních úloh a plánování úloh infrastruktury.

 Pokud nejste obeznámeni s Batch, tyto články vám pomůže porozumět architektuře/implementace řešení popsaných v tomto článku:   

* [Základy služby Batch](../../batch/batch-technical-overview.md)
* [Přehled funkcí Batch](../../batch/batch-api-basics.md)

Volitelně můžete další informace o službě Batch najdete v tématu [Batch documentatnion](https://docs.microsoft.com/azure/batch/).

## <a name="why-azure-data-factory"></a>Proč Azure Data Factory?
Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat. Vytvoření spravované datové kanály, které přesun dat z místních i cloudových úložišť dat do centralizovaného úložiště dat můžete použít Data Factory. Příkladem je úložiště objektů Blob v Azure. Data Factory můžete zpracovat a transformovat data pomocí služeb, jako je Azure HDInsight a Azure Machine Learning. Můžete také naplánovat datové kanály ke spuštění naplánované způsobem (například každou hodinu, každý den a jednou týdně). Můžete monitorovat a spravovat kanály, abyste mohli rozpoznat potíže a provést akci zasáhnout.

  Pokud nejste obeznámeni s Data Factory, tyto články vám pomůže porozumět architektuře/implementace řešení popsaných v tomto článku:  

* [Úvod do služby Data Factory](data-factory-introduction.md)
* [Vytvoření prvního kanálu dat](data-factory-build-your-first-pipeline.md)   

Volitelně můžete další informace o službě Data Factory najdete v tématu [dokumentace ke službě Data Factory](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory a Batch najednou
Data Factory obsahuje zabudované aktivity. Například aktivita kopírování umožňuje kopírování a přesouvání dat ze zdrojového úložiště dat do cílového úložiště dat. Aktivita Hive se používá ke zpracování dat s využitím clusterů Hadoop (HDInsight) v Azure. Seznam aktivit transformace podporované, najdete v části [aktivity transformace dat](data-factory-data-transformation-activities.md).

Můžete také vytvořit vlastní aktivity .NET se přesunout nebo zpracování dat pomocí vlastní logiky. Tyto aktivity můžete spustit v clusteru HDInsight nebo ve fondu Batch virtuálních počítačů. Při použití služby Batch můžete vytvořit fond pro automatické škálování (Přidání nebo odebrání na základě příslušného zatížení virtuálních počítačů) na základě vzorce je zadat.     

## <a name="architecture-of-a-sample-solution"></a>Architektura ukázkové řešení
  Architektura popsaných v tomto článku je jednoduché řešení. Také je relevantní pro komplexní scénáře, jako je modelování finančních služeb, zpracování obrázků a vykreslování a genomický analýzy rizik.

Diagram znázorňuje, jak Data Factory orchestruje přesun dat a zpracování. Profil také ukazuje, jak Batch zpracovává data paralelní způsobem. Stáhnout a vytisknout diagram pro snadné odkazování (11 × 17 palců nebo velikost A3). Pro přístup k diagramu tak, aby jej vytisknout, naleznete v tématu [HPC a Orchestrace dat pomocí služby Batch a Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram rozsáhlé zpracování dat](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Následující seznam uvádí základní kroky procesu. Toto řešení zahrnuje kód a vysvětlení, k vytvoření řešení začátku do konce.

* **Nakonfigurujte službu Batch k fondu výpočetních uzlů (virtuálních počítačů).** Můžete určit počet uzlů a velikost každého uzlu.

* **Vytvoření instance služby Data Factory** , který je nakonfigurovaný s entitami, které představují úložiště objektů blob, služba Batch výpočetní prostředky, vstupní a výstupní data a pracovního postupu a kanál pomocí aktivity, které přesouvají a transformují data.

* **Vytvořte vlastní aktivitu .NET v kanálu služby Data Factory.** Aktivita je váš uživatelský kód, který běží na fondu služby Batch.

* **Store velké množství vstupních dat jako objekty BLOB ve službě Azure Storage.** Data rozdělená do logické kolekce obsahuje nějaké řezy (obvykle podle času).

* **Data Factory kopíruje data, která se zpracovává paralelně** do sekundárního umístění.

* **Data Factory vlastní aktivita běží za použití fondu přidělené službou Batch.** Data Factory můžou běžet souběžně aktivity. Každá aktivita zpracuje určitý řez data. Výsledky jsou uloženy v úložišti.

* **Data Factory přesune konečných výsledků do třetí umístění** buď pro distribuci přes aplikaci nebo pro další zpracování v jiných nástrojích.

## <a name="implementation-of-the-sample-solution"></a>Provádění ukázkové řešení
Ukázkové řešení je záměrně jednoduchá. Je navržena tak, aby ukazují, jak pomocí služby Data Factory a Batch společně s datovými sadami procesu. Řešení se počítá počet výskytů prvku hledaný termín "Microsoft" ve vstupních souborů, které jsou uspořádány v časové řadě. Potom vypíše počet výstupních souborů.

**Čas:** Pokud jste se seznámili se základními funkcemi Azure Data Factory a Batch a dokončili následující požadavky, toto řešení má jedné až dvou hodin.

### <a name="prerequisites"></a>Požadavky
#### <a name="azure-subscription"></a>Předplatné Azure
Pokud nemáte předplatné Azure, můžete rychle vytvořit Bezplatný zkušební účet. Další informace najdete v tématu [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Účet služby Azure Storage
K uložení dat v tomto kurzu použijete účet úložiště. Pokud nemáte účet úložiště, přečtěte si téma [vytvořit účet úložiště](../../storage/common/storage-quickstart-create-account.md). Ukázkové řešení využívá úložiště objektů blob.

#### <a name="azure-batch-account"></a>Účet Azure Batch
Vytvoření účtu Batch pomocí [webu Azure portal](http://portal.azure.com/). Další informace najdete v tématu [vytvořit a spravovat účty Batch](../../batch/batch-account-create-portal.md). Poznámka: Batch účtu název a klíč účtu. Můžete také použít [New-AzureRmBatchAccount](https://docs.microsoft.com/powershell/module/azurerm.batch/new-azurermbatchaccount) rutina pro vytvoření účtu Batch. Pokyny k používání této rutiny najdete v tématu [Začínáme s rutinami Powershellu ve službě Batch](../../batch/batch-powershell-cmdlets-get-started.md).

Ukázkové řešení Batch (nepřímo prostřednictvím kanálu datové továrny) používá ke zpracování dat paralelní způsobem ve fondu výpočetních uzlů (spravované kolekce virtuálních počítačů).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch pool virtuálních počítačů
Vytvoření fondu služby Batch s výpočetními uzly měly aspoň dva.

1. V [webu Azure portal](https://portal.azure.com)vyberte **Procházet** v levé nabídce a vyberte **účty Batch**.

1. Vyberte svůj účet Batch **účtu Batch** okno.

1. Vyberte **fondy** dlaždici.

1. Na **fondy** okno, vyberte **přidat** tlačítko na panelu nástrojů a přidání fondu.

   a. Zadejte ID fondu (**ID fondu**). Poznamenejte si ID fondu. Budete ho potřebovat při vytváření řešení data factory.

   b. Zadejte **systému Windows Server 2012 R2** pro **operační systém řady** nastavení.

   c. Vyberte **cenové úrovně uzlů**.

   d. Zadejte **2** hodnotu **cílové vyhrazené** nastavení.

   e. Zadejte **2** hodnotu **maximální počet úkolů na uzel** nastavení.

   f. Vyberte **OK** vytvoříte fond.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Použijete [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) nebo [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (od společnosti ClumsyLeaf Software) ke kontrole a měnit data ve vašich projektech úložiště. Také můžete zkontrolovat a změnit data v protokolech aplikací hostovaných v cloudu.

1. Vytvořte kontejner s názvem **mycontainer** s přístupem k privátní (bez anonymního přístupu).

1. Pokud používáte CloudXplorer, vytvořte složky a podsložky s následující strukturou:

   ![Struktura složky a podsložky](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` a `outputfolder` jsou složky nejvyšší úrovně v `mycontainer`. `inputfolder` Složka obsahuje podsložky razítka data a času (rrrr-MM-DD-HH).

   Pokud používáte Průzkumníka služby Storage, v dalším kroku, nahrávání souborů s následujícími názvy: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`, a tak dále. Tento krok vytvoří automaticky složky.

1. Vytvořte textový soubor **soubor.txt** na svém počítači s obsahem, který obsahuje klíčové slovo **Microsoft**. Příkladem je "testovací vlastní aktivity Microsoft vlastní aktivity testu Microsoft."

1. Nahrajte soubor do následující vstupní složky v úložišti objektů blob:

   ![Vstupní složky](./media/data-factory-data-processing-using-batch/image4.png)

   Pokud používáte Průzkumníka služby Storage, nahrajte **soubor.txt** do souboru **mycontainer**. Vyberte **kopírování** na panelu nástrojů a vytvořte kopii objektu blob. V **objekt Blob kopírování** dialogovém okně Změnit **název cílové objektů blob** k `inputfolder/2015-11-16-00/file.txt`. Opakujte tento krok k vytvoření `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`, a tak dále. Tato akce automaticky vytvoří složky.

1. Vytvořit jiný kontejner s názvem `customactivitycontainer`. Nahrajte soubor zip vlastní aktivity do tohoto kontejneru.

#### <a name="visual-studio"></a>Visual Studio
Instalace sady Visual Studio 2012 nebo novějším k vytvoření vlastní aktivity služby Batch pro použití v řešení data factory.

### <a name="high-level-steps-to-create-the-solution"></a>Základní kroky pro vytvoření řešení
1. Vytvořte vlastní aktivitu, která obsahuje logiku zpracování dat.

1. Vytvoření datové továrny, který používá vlastní aktivity.

### <a name="create-the-custom-activity"></a>Vytvoření vlastní aktivity
Vlastní aktivita objekt pro vytváření dat je srdcem toto ukázkové řešení. Ukázkové řešení Batch používá ke spuštění vlastní aktivity. Informace o tom, jak vyvinout vlastní aktivity a jejich použití v kanálech data factory najdete v tématu [použití vlastních aktivit v kanálech data factory](data-factory-use-custom-activities.md).

Pokud chcete vytvořit vlastní aktivitu .NET, který vám pomůže v kanálech data factory, vytvoříte projekt knihovny tříd .NET vytvoříte třídou, která implementuje rozhraní IDotNetActivity. Toto rozhraní obsahuje pouze jednu metodu: Spusťte. Následuje podpis metody:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Tato metoda má několik klíčových komponent, které je třeba porozumět:

* Tato metoda přebírá čtyři parametry:

  * **linkedServices**. Tento parametr je výčtový seznam propojených služeb, které jsou propojeny vstupní a výstupní data zdroje (například služby blob storage) do služby data factory. V této ukázce je pouze jeden propojené služby typu Azure Storage používají pro vstup a výstup.
  * **datové sady**. Tento parametr je výčtový seznam datových sad. Tento parametr slouží k získání umístění a schémata definované vstupní a výstupní datové sady.
  * **aktivita**. Tento parametr představuje aktuální entitu výpočetní prostředky. V takovém případě je služba Batch.
  * **Protokolovací nástroj**. Protokolovací nástroj můžete použít k zápisu ladění komentáře povrchu jako protokol "User" pro kanál.
* Metoda vrací slovník, který je možné zřetězit vlastních aktivit v budoucnu. Tato funkce není dosud implementována, tak, aby prázdný slovník vrácení z metody.

#### <a name="procedure-create-the-custom-activity"></a>Postup: Vytvoření vlastní aktivity
1. Vytvořte projekt knihovny tříd .NET v sadě Visual Studio.

   a. Start Visual Studio 2012/2013/2015.

   b. Vyberte **Soubor** > **Nový** > **Projekt**.

   c. Rozbalte **šablony**a vyberte **Visual C\#**. V tomto názorném postupu použijete C\#, ale můžete použít libovolný jazyk .NET k vývoji vlastní aktivity.

   d. Vyberte **knihovny tříd** ze seznamu typů projektů napravo.

   e. Zadejte **MyDotNetActivity** pro **název**.

   f. Vyberte **C:\\ADF** pro **umístění**. Vytvořit složku **ADF** Pokud neexistuje.

   g. Vyberte **OK** a vytvořte projekt.

1. Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

1. V konzole Správce balíčků spustíte následující příkaz k importu Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Import **služby Azure Storage** balíček NuGet do projektu. Tento balíček musíte, protože v této ukázce použijete rozhraní API pro úložiště objektů Blob:

    ```powershell
    Install-Package Azure.Storage
    ```
1. Přidejte následující direktivy using pro zdrojový soubor v projektu:

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
1. Změňte název oboru názvů **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Změnit název třídy, která se **MyDotNetActivity**a jsou odvozeny z **IDotNetActivity** rozhraní, jak je znázorněno:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementace (Přidat) **Execute** metodu **IDotNetActivity** rozhraní při **MyDotNetActivity** třídy. Zkopírujte následující vzorový kód k metodě. Vysvětlení logikou používanou v této metodě, najdete v článku [metodu Execute](#execute-method) oddílu.

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
1. Přidejte následující metody helper pro třídu. Tyto metody jsou vyvolány **Execute** metody. Nejdůležitější, **Calculate** metoda izoluje kód, který prochází každý objekt blob.

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
    Metoda GetFolderPath vrací cestu ke složce, která datová sada odkazuje na a GetFileName metoda vrátí název souboru objektu blob nebo odkazující na datovou sadu.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoda Calculate vypočítá počet instancí – klíčové slovo "Microsoft" ve vstupních souborů (objekty BLOB ve složce). Hledaný termín "Microsoft" je pevně zakódovaný v kódu.

1. Zkompilujte projekt. Vyberte **sestavení** z nabídky a pak vyberte **sestavit řešení**.

1. Spusťte Windows Explorer a přejděte k **bin\\ladění** nebo **bin\\release** složky. Výběr složky závisí na typu sestavení.

1. Vytvořte soubor zip **MyDotNetActivity.zip** , která obsahuje všechny binární soubory v  **\\bin\\ladění** složky. Můžete chtít zahrnout MyDotNetActivity. **pdb** souboru tak, aby získat další podrobnosti, jako je číslo řádku ve zdrojovém kódu, která způsobila problém, když dojde k chybě.

   ![Seznam složek bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Nahrát **MyDotNetActivity.zip** jako objekt blob do kontejneru objektů blob `customactivitycontainer` ve službě blob storage, StorageLinkedService propojenou službu v objektu ADFTutorialDataFactory používá. Vytvořit kontejner objektů blob `customactivitycontainer` Pokud ještě neexistuje.

#### <a name="execute-method"></a>Execute – metoda
Tato část obsahuje bližší informace o kódu v metodě Execute.

1. Členy pro iterace v rámci vstupní kolekce se nacházejí v [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) oboru názvů. Pro iteraci prostřednictvím kolekce objektů blob, je nutné použít **BlobContinuationToken** třídy. V podstatě je nutné použít DNT-při smyčky s tokenem jako mechanismus pro ukončení opakování. Další informace najdete v tématu [použití Blob storage pomocí technologie .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Základní smyčky je znázorněna zde:

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
   Další informace najdete v tématu v dokumentaci [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) metody.

1. Kód pro práci prostřednictvím sady objektů BLOB logicky přejde v rámci provést-smyčku while. V **Execute** metody, provést – zatímco smyčka projde seznam objektů blob pro metodu s názvem **Calculate**. Metoda vrátí řetězcovou proměnnou s názvem **výstup** , který je výsledkem by bylo provést iteraci pomocí všech objektů BLOB v segmentu.

   Vrátí počet výskytů prvku hledaný termín, který je předán "Microsoft" v objektu blob **Calculate** metody.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Po **Calculate** dokončení metody musí být napsané pro nový objekt blob. Pro každou sadu objektů BLOB zpracovat je možné zapsat nový objekt blob s výsledky. Pokud chcete uložit nový objekt blob, vyhledejte nejdříve výstupní datovou sadu.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Kód také volá metodu helper **GetFolderPath** načíst cestu ke složce (název kontejneru úložiště).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoda GetFolderPath Přetypuje objekt datové sady, který má AzureBlobDataSet, který má vlastnost s názvem FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
1. Kód volá **GetFileName** metody k získání názvu souboru (název objektu blob). Kód je podobný jako předchozí kód, který byl použit k získání cesty ke složce.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
1. Název souboru je zapsán tak, že vytvoříte objekt identifikátoru URI. Identifikátor URI konstruktor používá **BlobEndpoint** vlastnost vrátí název kontejneru. Název složky a cesta k souboru se přidají k sestavení kompletních identifikátor URI objektu blob výstup.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Po zapsání název souboru, můžete napsat výstupní řetězec z **Calculate** metodu pro nový objekt blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Vytvoření datové továrny
V [vytvořit vlastní aktivitu](#create-the-custom-activity) oddílu, můžete vytvořit vlastní aktivitu a nahraný soubor zip s binárními soubory a souboru PDB do kontejneru objektů blob. V této části vytvoříte datovou továrnu s kanálem, který používá vlastní aktivity.

Vstupní datová sada pro vlastní aktivity představuje (soubory) objektů BLOB ve vstupní složce (`mycontainer\\inputfolder`) ve službě blob storage. Představuje výstupní datovou sadu aktivity výstupních objektů BLOB ve výstupní složce (`mycontainer\\outputfolder`) ve službě blob storage.

Přetáhněte jeden nebo více souborů do vstupní složky:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Na všech složkách, třeba vyřaďte jeden soubor (soubor.txt) s následujícím obsahem:

```
test custom activity Microsoft test custom activity Microsoft
```

Každá vstupní složky odpovídá řezu v objektu pro vytváření dat i v případě, že složka obsahuje dva nebo více souborů. Když každý řez je zpracován kanálem, vlastní aktivita Iteruje přes všechny objekty BLOB ve vstupní složce pro tento řez.

Uvidíte pět výstupní soubory se stejným obsahem. Například výstupní soubor pro zpracování souboru ve složce 2015-11-16-00 má následující obsah:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Pokud je do vstupní složky vyřadit více souborů (soubor.txt, Soubor2.txt, file3.txt) se stejným obsahem, viz následující obsah do výstupního souboru. Všechny složky (2015-11-16-00 atd.) odpovídá řezu v této ukázce i v případě, že složka obsahuje více vstupních souborů.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Výstupní soubor má tři řádky, jeden pro každý vstupní soubor (blob) ve složce přidružené řez (2015-11-16-00).

Pro každé spuštění aktivity se vytvoří úkol. V této ukázce je pouze jedna aktivita v kanálu. Pokud kanál je zpracován určitý řez, vlastní aktivita spouští v dávkové zpracování řezu. Protože je pět řezů (každý řez může mít více objektů BLOB nebo soubor), se vytvoří pět úkolů ve službě Batch. Když je úkol spuštěn v Batch, je vlastní aktivity, na kterém běží.

Následující návod poskytuje další podrobnosti.

#### <a name="step-1-create-the-data-factory"></a>Krok 1: Vytvoření datové továrny
1. Po přihlášení k [webu Azure portal](https://portal.azure.com/), proveďte následující kroky:

   a. Vyberte **nový** v nabídce vlevo.

   b. Vyberte **Data a analýzy** na **nový** okno.

   c. Vyberte **služby Data Factory** na **Data analytics** okno.

1. Na **nová datová továrna** okně zadejte **CustomActivityFactory** pro název. Název datové továrny musí být globálně jedinečný. Pokud se zobrazí chyba "název objektu pro vytváření dat CustomActivityFactory není k dispozici", změňte název datové továrny. Například použijte yournameCustomActivityFactory a znovu vytvořte datovou továrnu.

1. Vyberte **název skupiny prostředků**a vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků.

1. Ověřte správnost dané předplatné a oblast, ve kterém chcete objekt pro vytváření dat vytvořit.

1. Vyberte **vytvořit** na **nová datová továrna** okno.

1. Vytvoření datové továrny v řídicím panelu portálu.

1. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí **služby Data factory** stránky, která zobrazuje obsah služby data factory.

   ![Stránka datová továrna](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Krok 2: Vytvoření propojených služeb
Propojené služby propojují úložiště dat nebo výpočetní služby spojili s datovou továrnu. V tomto kroku propojíte se svou datovou továrnou účet úložiště a účet Batch.

#### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. Vyberte **Autor a nasadit** na dlaždici **služby Data factory** okno **CustomActivityFactory**. Zobrazí se Editor služby Data Factory.

1. Vyberte **nové datové úložiště** na panelu příkazů a zvolte **úložiště Azure.** Skript JSON, který použijete k vytvoření úložiště, propojené služby v editoru se zobrazí.

   ![Nové datové úložiště](./media/data-factory-data-processing-using-batch/image7.png)

1. Nahraďte **název účtu** názvem vašeho účtu úložiště. Nahraďte **klíč účtu** přístupovým klíčem účtu úložiště. Zjistěte, jak získat přístupový klíč k úložišti, najdete v článku [zobrazení, kopírování a znovu vygenerovat přístupové klíče úložiště](../../storage/common/storage-account-manage.md#access-keys).

1. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

   ![Nasazení](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Vytvoření služby propojené služby Azure Batch
V tomto kroku vytvoříte propojené služby pro účet Batch, který se používá ke spuštění aktivity vlastní data factory.

1. Vyberte **nový výpočet** na panelu příkazů a zvolte **Azure Batch.** Skript JSON, který použijete k vytvoření dávky propojené služby v editoru se zobrazí.

1. Ve skriptu JSON:

   a. Nahraďte **název účtu** s názvem vašeho účtu Batch.

   b. Nahraďte **přístupový klíč** přístupovým klíčem účtu Batch.

   c. Zadejte ID fondu **poolName** vlastnost. Pro tuto vlastnost můžete zadat název fondu nebo ID fondu.

   d. Zadejte identifikátor URI služby batch pro **batchUri** vlastnost JSON.

      > [!IMPORTANT]
      > Adresu URL **účtu Batch** okně je v následujícím formátu: \<accountname\>.\< oblast\>. batch.azure.com. Pro **batchUri** vlastnost ve skriptu JSON, budete muset odebrat a88 "accountname." ** z adresy URL. Příklad: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Okno účtu batch](./media/data-factory-data-processing-using-batch/image9.png)

      Pro **poolName** vlastnosti také můžete zadat ID fondu namísto název fondu.

      > [!NOTE]
      > Služba Data Factory nepodporuje možnost na vyžádání pro službu Batch, stejně jako pro HDInsight. Pouze vlastní fond služby Batch můžete použít ve službě data factory.
      >
      >
   
   e. Zadejte **StorageLinkedService** pro **linkedServiceName** vlastnost. Tuto propojenou službu jste vytvořili v předchozím kroku. Toto úložiště se používá jako pracovní oblast pro soubory a protokoly.

1. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

#### <a name="step-3-create-datasets"></a>Krok 3: Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data.

#### <a name="create-the-input-dataset"></a>Vytvoření vstupní datové sady
1. V editoru služby Data Factory, vyberte **nová datová sada** tlačítko na panelu nástrojů. Vyberte **úložiště objektů Blob v Azure** z rozevíracího seznamu.

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

    Vytvoření kanálu dále v tomto návodu s časem zahájení 2015-11-16T00:00:00Z a konec času 2015-11-16T05:00:00Z. Je naplánováno a generuje data po hodinách, takže máte pět řezy vstupní a výstupní (mezi **00**: 00:00 -\> **05**: 00:00).

    **Frekvence** a **interval** pro vstupní datovou sadu, které jsou nastaveny na **hodinu** a **1**, což znamená, že vstupní řez je dostupný po hodinách.

    Počáteční čas pro každý řez je reprezentována **SliceStart** systémové proměnné v předchozím fragmentu kódu JSON. Tady jsou časy zahájení pro každý řez.

    | **řez** | **Čas spuštění**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    **FolderPath** vypočítat pomocí toho rok, měsíc, den a hodina součástí počáteční čas řezu (**SliceStart**). Zde je, jak je vstupní složky mapované na určitý řez.

    | **řez** | **Čas spuštění**          | **Vstupní složky**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Vyberte **nasadit** na panelu nástrojů můžete vytvářet a nasazovat **InputDataset** tabulky.

#### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
V tomto kroku vytvoříte datovou sadu typu AzureBlob, která bude představovat výstupní data.

1. V editoru služby Data Factory, vyberte **nová datová sada** tlačítko na panelu nástrojů. Vyberte **úložiště objektů Blob v Azure** z rozevíracího seznamu.

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

    Výstupní objekt blob nebo soubor se generuje pro každý vstupní řez. Zde je, jak je výstupní soubor s názvem pro každý řez. Všechny výstupní soubory jsou generovány v jedné složce výstupu `mycontainer\\outputfolder`.

    | **řez** | **Čas spuštění**          | **Výstupní soubor**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Mějte na paměti, že všechny soubory ve vstupní složky (například 2015-11-16-00) jsou součástí řez s časem zahájení 2015--11-16 00. Při zpracování této řez, vlastní aktivita prohledává každý soubor a vytváří čáry ve výstupním souboru s počtem výskytů hledaný termín "Microsoft". Pokud existují tři soubory ve složce 2015-11-16-00, jsou v výstupní soubor 2015-11-16-00.txt tři řádky.

1. Vyberte **nasadit** na panelu nástrojů můžete vytvářet a nasazovat **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Krok 4: Vytvoření a spuštění kanálu s aktivitou vlastní
V tomto kroku vytvoříte kanál s jednou aktivitou, vlastní aktivita, kterou jste vytvořili dříve.

> [!IMPORTANT]
> Pokud jste nenahráli **soubor.txt** do vstupní složky v kontejneru objektů blob, proveďte tak před vytvořením kanálu. **IsPaused** je nastavena na hodnotu false v kanálu JSON, takže spuštění kanálu se okamžitě, protože **start** datum je v minulosti.
>
>

1. V editoru služby Data Factory, vyberte **nový kanál** na panelu příkazů. Pokud se nezobrazí příkaz, vyberte symbol tří teček zobrazte je.

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

   * Je jenom jedna aktivita v kanálu a je typu **DotNetActivity**.
   * **AssemblyName** je nastavena na název knihovny DLL **MyDotNetActivity.dll**.
   * **Vstupní bod** je nastavena na **MyDotNetActivityNS.MyDotNetActivity**. Je v podstatě \<obor názvů\>.\< ClassName\> ve vašem kódu.
   * **PackageLinkedService** je nastavena na **StorageLinkedService**, která odkazuje na úložiště objektů blob, který obsahuje příslušný soubor .zip pro vlastní aktivity. Pokud používáte jiný účet úložiště pro vstupní a výstupní soubory a soubor zip vlastní aktivitu, budete muset vytvořit jinou propojenou službu Storage. Tento článek předpokládá, že používáte stejný účet úložiště.
   * **PackageFile** je nastavena na **customactivitycontainer/MyDotNetActivity.zip**. Je ve formátu \<containerforthezip\>/\<nameofthezip.zip\>.
   * Vlastní aktivita přijímá **InputDataset** jako vstup a **OutputDataset** jako výstup.
   * **LinkedServiceName** vlastnost vlastní aktivity odkazuje na **AzureBatchLinkedService**, který dává pokyn služby Data Factory, který vlastní aktivita je potřeba spustit na služby Batch.
   * **Souběžnosti** nastavení je důležité. Pokud použijete výchozí hodnotu, která je 1, i v případě, že máte dva nebo víc výpočetních uzlů ve fondu služby Batch, se zpracovávají řezy jeden po druhém. Proto nejsou využívat paralelní zpracování funkce služby Batch. Pokud nastavíte **souběžnosti** na vyšší hodnotu, Dejme tomu, že 2, znamená to, že dva řezy (odpovídá možnosti dvě úlohy ve službě Batch) může být zpracována ve stejnou dobu. V takovém případě budou použity oba virtuální počítače ve fondu služby Batch. Odpovídajícím způsobem nastavte vlastnost souběžnosti.
   * Pouze jeden úkol (řezu) je spustit na virtuálním počítači v libovolném bodě ve výchozím nastavení. Ve výchozím nastavení **maximální počet úloh na virtuální počítač** je nastavena na hodnotu 1 pro fond služby Batch. Jako součást požadavků vytvořili fond se tato vlastnost nastavena na hodnotu 2. Proto se dva datové továrny řezy můžete spustit na virtuálním počítači ve stejnou dobu.
    - **IsPaused** je vlastnost nastavena na hodnotu false, ve výchozím nastavení. Kanálu se spustí okamžitě v tomto příkladě vzhledem k tomu, že řezy spustit v minulosti. Tuto vlastnost lze nastavit **true** k pozastavení kanálu a nastavte ji zpět na **false** restartovat.
    -   **Start** a **end** časy jsou od sebe pět hodin. Řezy se tvoří po hodinách, takže pět řezy se tvoří kanál.

1. Vyberte **Nasadit** na panelu příkazů a nasaďte kanál.

#### <a name="step-5-test-the-pipeline"></a>Krok 5: Test kanálu
V tomto kroku otestujte kanál přetažením soubory do vstupní složky. Začněte tím, že kanál ve Visual Basicu s jeden soubor pro každý vstupní složky.

1. Na **služby Data factory** okna na webu Azure Portal, vyberte **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. V **Diagram** dvakrát klikněte na vstupní datová sada **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. **InputDataset** otevře se okno s všech pět řezy, které jsou připravené. Všimněte si, že **čas spuštění ŘEZU** a **ŘEZ KONCOVÝ čas** pro každý řez.

   ![Vstupní řez počáteční a koncové časy](./media/data-factory-data-processing-using-batch/image12.png)

1. V **Diagram** zobrazit, vyberte možnost **OutputDataset**.

1. Pět výstupní řezy se zobrazí v **připravené** stavu, pokud byly vytvořeny.

   ![Výstupní řez počáteční a koncové časy](./media/data-factory-data-processing-using-batch/image13.png)

1. Použití portálu k zobrazení úloh přidružených k řezy a naleznete v tématu co virtuálního počítače, které byly spuštěny každý řez v. Další informace najdete v tématu [integrace služby Data Factory a Batch](#data-factory-and-batch-integration) oddílu.

1. Výstupní soubory se zobrazí pod `mycontainer` v `outputfolder` ve službě blob storage.

   ![Výstupní soubory v úložišti](./media/data-factory-data-processing-using-batch/image15.png)

   Pět výstupní soubory jsou uvedeny, jeden pro každý vstupní řez. Každá z výstupních souborů má obsah, podobně jako následující výstup:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Následující diagram znázorňuje, jak se objekt pro vytváření datové řezy mapují na úkoly ve službě Batch. V tomto příkladu má určitý řez pouze jedno spuštění.

   ![Diagram mapování řezu](./media/data-factory-data-processing-using-batch/image16.png)

1. Zkuste to teď s více soubory ve složce. Vytvoření souborů **Soubor2.txt**, **file3.txt**, **file4.txt**, a **file5.txt** se stejným obsahem stejně jako v soubor.txt ve složce **2015-11-06-01**.

1. Ve výstupní složce odstranit výstupní soubor **2015-11-16-01.txt**.

1. Na **OutputDataset** okna, klikněte pravým tlačítkem na segment s **čas spuštění ŘEZU** nastavena na **11/16/2015 01:00:00 AM**. Vyberte **spustit** na opětovné spuštění/opětovné zpracování řezu. Řez nyní obsahuje pět souborů místo jednoho souboru.

    ![Spusťte](./media/data-factory-data-processing-using-batch/image17.png)

1. Po spuštění řezu a její stav je **připravené**, ověřte obsah do výstupního souboru pro tento řez (**2015-11-16-01.txt**). Výstupní soubor se zobrazí v části `mycontainer` v `outputfolder` ve službě blob storage. Měla by existovat jeden řádek pro každý soubor řezu.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Pokud odstraníte nebyla výstupní soubor 2015-11-16-01.txt před s pěti vstupních souborů, zobrazí jeden řádek z předchozího spuštění řezu a pět řádků z aktuálního spuštění řezu. Ve výchozím nastavení se připojí obsah k výstupnímu souboru, pokud již existuje.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integrace služby Data Factory a Batch
Služba Data Factory vytvoří úlohu ve službě Batch s názvem `adf-poolname:job-xxx`.

![Úlohy služby batch](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Pro každé spuštění aktivity řezu se vytvoří úkol v úloze. Pokud 10 řezy jsou připravené ke zpracování, vytvoří se 10 úkoly v úloze. Můžete mít více než jeden řez paralelnímu spouštění, pokud máte víc výpočetních uzlů ve fondu. Pokud je maximální počet úkolů na výpočetním uzlu nastaven na větší než jedna, můžete spustit více než jeden řez ve stejné výpočetní.

V tomto příkladu je pět řezů, takže máte pět úkolů ve službě Batch. S **souběžnosti** nastavena na **5** v kódu JSON ve službě data factory kanálu a **maximální počet úloh na virtuální počítač** nastavena na **2** ve fondu služby Batch pomocí **2** virtuálních počítačů, úlohy běžet rychle. (Zkontrolujte počátečním a koncovým časem pro úlohy.)

Chcete-li zobrazit úlohy služby Batch a jejích úkolů, které jsou spojeny s řezy použít na portálu a podívat se jaké virtuálního počítače, které byly spuštěny každý řez v.

![Úlohy služby batch](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Ladění kanálu
Ladění zahrnuje několik základních technik.

1. Pokud vstupní řez není nastavený **připravené**, zkontrolujte, jestli je správná struktura vstupní složky a že soubor.txt existuje ve vstupních složkách.

   ![Struktura vstupní složky](./media/data-factory-data-processing-using-batch/image3.png)

1. V **Execute** metoda vaše vlastní aktivity použijte **IActivityLogger** objektu do protokolu informace, které vám pomohou s řešením problémů. Protokolované zprávy zobrazí uživateli\_souboru protokolu 0.

   Na **OutputDataset** okno, vyberte na řez otevřete **datový řez** okno pro tento řez. V části **spuštění aktivit**, se zobrazí jedna aktivita spustit řez. Pokud vyberete **spustit** na příkazovém řádku, můžete spustit jiné aktivity spuštění pro stejný řez.

   Když vyberete spuštění aktivit, uvidíte **podrobnosti o spuštění aktivit** okno se seznamem souborů protokolů. Zobrazí zprávy zaznamenané dříve uživatele\_souboru protokolu 0. Při výskytu chyby se zobrazí tři spuštění aktivit, protože počet opakování je nastavená na 3 v kódu JSON kanálu a aktivity. Vyberete-li spuštění aktivity, zobrazit soubory protokolů obsahující informace pro řešení potíží s chybou.

   ![Okna OutputDataset a datový řez](./media/data-factory-data-processing-using-batch/image18.png)

   V seznamu souborů protokolu, vyberte **user-0.log**. V pravém panelu výsledky použití **IActivityLogger.Write** metoda se zobrazí.

   ![Okno Podrobnosti o spuštění aktivit](./media/data-factory-data-processing-using-batch/image19.png)

   Kontrola systému 0.log pro všechny chybové zprávy systému a výjimky.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Zahrnout **PDB** souboru v souboru zip tak, aby podrobnosti o chybě informace, jako je zásobníku volání při výskytu chyby.

1. Všechny soubory v souboru .zip pro vlastní aktivity musí být na nejvyšší úrovni se žádné podsložky.

   ![Seznam souborů zip vlastní aktivity](./media/data-factory-data-processing-using-batch/image20.png)

1. Ujistěte se, že **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), a **packageLinkedService** (mělo by odkazovat na úložiště objektů blob, který obsahuje příslušný soubor .zip) jsou nastavené na správné hodnoty.

1. Pokud jste opravili chybu a chcete řez zpracovat znovu, klikněte pravým tlačítkem myši výseč **OutputDataset** okna a vyberte **spustit**.

   ![Okno OutputDataset možnost spuštění](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Kontejner je v úložišti objektů blob s názvem `adfjobs`. Tento kontejner není automaticky odstraněn, ale můžete bezpečně odstranit po dokončení testování řešení. Obdobně řešení data factory vytvoří úlohu Batch s názvem `adf-\<pool ID/name\>:job-0000000001`. Po otestování řešení Pokud chcete, můžete odstranit tuto úlohu.
   >
   >
1. Vlastní aktivita nepoužívá **app.config** souborů z vašeho balíčku. Proto pokud váš kód načítá řetězce připojení z konfiguračního souboru, to nebude fungovat v době běhu. Nejosvědčenější při použití služby Batch je uložit všechny tajné klíče ve službě Azure Key Vault. Pak pomocí hlavního názvu služby na základě certifikátů k ochraně služby key vault a distribuovat tento certifikát fondu služby Batch. Vlastní aktivita .NET můžete přístup k tajným klíčům ze služby key vault za běhu. Tato obecná řešení můžete škálovat pro libovolný typ tajného klíče, nejenom na připojovací řetězec.

    Je snazší alternativní řešení, ale se nejedná osvědčený postup. Vytvoříte propojené služby databáze SQL s připojením nastavení řetězce. Potom můžete vytvořit datovou sadu, která používá propojenou službu a zřetězení datovou sadu jako zástupný vstupní datovou sadu, která vlastní aktivitu .NET. Potom můžete přistupovat připojovací řetězec propojené služby v kódu vlastní aktivity. By měl fungovat správně v době běhu.  

#### <a name="extend-the-sample"></a>Rozšíření ukázky
Můžete rozšířit této ukázce se dozvíte více o funkcích služby Data Factory a Batch. Třeba pro zpracování řezů v různých časový rozsah, proveďte následující kroky:

1. Přidání následujících podsložek v `inputfolder`: 2015-11-16-05, 2015-11-16-06 201--11-16 07, 2011--11-16 08 a 2015--11-16 09. Vstupní soubory umístíte do těchto složek. Změňte koncový čas pro kanál z `2015-11-16T05:00:00Z` k `2015-11-16T10:00:00Z`. V **Diagram** dvakrát klikněte na **InputDataset** a ověřte, zda vstupní řezy jsou připravené. Dvakrát klikněte na panel **OutputDataset** zobrazíte stav výstupní řezy. Pokud jsou v **připravené** stavu, zkontrolujte výstupní složku pro výstupní soubory.

1. Zvětšit nebo zmenšit **souběžnosti** nastavení, abyste pochopili, jak ovlivňuje výkon vašeho řešení, hlavně zpracování, který se nachází v Batch. Další informace o **souběžnosti** nastavení, najdete v článku "krok 4: Vytvoření a spuštění kanálu s aktivitou vlastní."

1. Vytvoření fondu s vyšší nebo nižší **maximální počet úloh na virtuální počítač**. Pokud chcete použít nový fond, který jste vytvořili, aktualizujte ve službě Batch propojené v řešení data factory. Další informace o **maximální počet úloh na virtuální počítač** nastavení, najdete v článku "krok 4: Vytvoření a spuštění kanálu s aktivitou vlastní."

1. Vytvoření fondu služby Batch pomocí **automatického škálování** funkce. Automatické škálování výpočetních uzlů ve fondu služby Batch je dynamické přizpůsobení výpočetní výkon, které používá vaše aplikace. 

    Ukázkové vzorce zde dosáhne následující chování. Při počátečním vytvoření fondu začíná jeden virtuální počítač. Metrika $PendingTasks definuje počet úloh ve spuštěném a stavy aktivní (ve frontě). Vzorec najde průměrný počet čekající úlohy za posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy nedostane mimo 25 virtuálních počítačů. Jako nové úkoly se odešlou, fond se automaticky rozšíří. Dokončení úkolů virtuální počítače budou zdarma jednu po druhé a automatickým Škálováním zmenšuje těchto virtuálních počítačů. StartingNumberOfVMs a maxNumberofVMs můžete upravit podle vašich potřeb.
 
    Vzorec automatického škálování:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Další informace najdete v tématu [automatické škálování výpočetních uzlů ve fondu Batch](../../batch/batch-automatic-scaling.md).

   Pokud se fond používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může trvat 15 až 30 minut Příprava virtuálního počítače před spuštěním vlastní aktivity. Pokud fond používá jiný autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval plus 10 minut.

1. V ukázkovém řešení **Execute** metoda vyvolá **Calculate** metody, která zpracovává vstupní výseč k vytvoření řez výstupních dat. Můžete napsat vlastní metodu ke zpracování vstupních dat a nahraďte **Calculate** volání metody **Execute** metoda pomocí volání pro metodu.

### <a name="next-steps-consume-the-data"></a>Další kroky: Tato data využívají
Po zpracování dat můžete použít pomocí online nástrojů, jako je Power BI. Tady jsou odkazy, které vám pomůžou porozumět Power BI a jak ji používat v Azure:

* [Prozkoumejte datová sada v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Začínáme s Power BI Desktopu](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Aktualizovat data v Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure a Power BI: Základní přehled](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Odkazy
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Úvod do služby Data Factory](data-factory-introduction.md)
  * [Začínáme s Data Factory](data-factory-build-your-first-pipeline.md)
  * [Použití vlastních aktivit v kanálech Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Základy služby Batch](../../batch/batch-technical-overview.md)
  * [Přehled funkcí Batch](../../batch/batch-api-basics.md)
  * [Vytvoření a Správa účtu Batch na webu Azure Portal](../../batch/batch-account-create-portal.md)
  * [Začínáme s klientskou knihovnou Batch pro .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
