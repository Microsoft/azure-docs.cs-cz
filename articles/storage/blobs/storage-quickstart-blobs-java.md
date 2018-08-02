---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí sady Java Storage SDK V7 | Microsoft Docs
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro Javu k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: 7fc5b164792907644a0a41615436806f998a8b1e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398705"
---
# <a name="quickstart-upload-download-and-list-blobs-using-java-sdk-v7"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí sady Java SDK V7

V tomto rychlém startu zjistíte, jak pomocí Javy nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Nainstalovat integrované vývojové prostředí (IDE) s integrovaným Mavenem.

* Případně můžete nainstalovat a nakonfigurovat Maven pro práci z příkazového řádku.

Tento kurz používá [Eclipse](http://www.eclipse.org/downloads/) s konfigurací Eclipse IDE pro vývojáře Java.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-java-quickstart) použitá v tomto rychlém startu je základní konzolová aplikace.  

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete projekt otevřít, spusťte Eclipse a zavřete uvítací obrazovku. Vyberte **File** (Soubor) a pak **Open Projects from File System...** (Otevřít projekty ze systému souborů...). Ujistěte se, že je zaškrtnuté políčko **Detect and configure project natures** (Detekovat a nakonfigurovat povahu projektu). Vyberte **Directory** (Adresář), pak přejděte do umístění, kam jste uložili naklonované úložiště, a v něm vyberte složku **javaBlobsQuickstart**. Ujistěte se, že se projekt **javaBlobsQuickstarts** zobrazí jako projekt Eclipse, a pak vyberte **Finish** (Dokončit).

Po dokončení importování projektu otevřete soubor **AzureApp.java** (umístěný ve složce **blobQuickstart.blobAzureApp** v adresáři **src/main/java**) a nahraďte `accountname` a `accountkey` uvnitř řetězce `storageConnectionString`. Pak aplikaci spusťte.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
    
V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Otevřete soubor **AzureApp.Java**. Vyhledejte proměnnou `storageConnectionString` a vložte do ní hodnotu připojovacího řetězce, kterou jste zkopírovali v předchozí části. Vaše proměnná `storageConnectionString` by měla vypadat nějak takto:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Spuštění ukázky

Tato ukázka vytvoří testovací soubor ve vašem výchozím adresáři (pro uživatele Windows je to složka Dokumenty), nahraje ho do úložiště objektů blob, vypíše objekty blob v kontejneru a pak soubor stáhne s novým názvem, abyste mohli porovnat starý a nový soubor. 

Na příkazovém řádku spusťte ukázku pomocí nástroje Maven. Otevřete prostředí a přejděte k **blobAzureApp** v naklonovaném adresáři. Potom zadejte `mvn compile exec:java`. 

Následuje příklad výstupu při spuštění aplikace ve Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Než budete pokračovat, zkontrolujte, jestli váš výchozí adresář (pro uživatele Windows je to složka Dokumenty) obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické. Obsah souboru v úložišti objektů blob můžete zobrazit zkopírováním adresy URL objektu blob z okna konzoly a jejím vložením do prohlížeče. Když stisknete klávesu Enter, odstraní se kontejner úložiště i soubory. 

K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště.

Po ověření souborů stiskněte klávesu Enter a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor **AzureApp.java** a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

První věc, kterou je potřeba udělat, je vytvořit odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage._storage_account), která odkazuje na účet úložiště.

    Objekt **CloudStorageAccount** je reprezentace vašeho účtu úložiště a umožňuje nastavení a přístup k vlastnostem účtu úložiště prostřednictvím kódu programu. Pomocí objektu **CloudStorageAccount** můžete vytvořit instanci objektu **CloudBlobClient**, která je nezbytná pro přístup ke službě Blob.

* Vytvořte instanci objektu **CloudBlobClient**, která odkazuje na [službu Blob service](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) ve vašem účtu úložiště.

    **CloudBlobClient** poskytuje přístupový bod ke službě Blob a díky tomu umožňuje nastavení a přístup k vlastnostem úložiště objektů blob prostřednictvím kódu programu. Pomocí objektu **CloudBlobClient** můžete vytvořit instanci objektu **CloudBlobContainer**, která je nezbytná pro vytváření kontejnerů.

* Vytvořte instanci objektu [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container), která představuje kontejner, ke kterému přistupujete. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.    

    Jakmile budete mít **CloudBlobContainer**, můžete vytvořit instanci objektu [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob), která odkazuje na konkrétní objekt blob, který vás zajímá, a provést operaci nahrání, stažení, kopírování atd.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Vytvoření kontejneru

V této části vytvoříte instance objektů, vytvoříte nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné a přístupné přes pouhou adresu URL. Kontejner má název **quickstartblobs**. 

Tento příklad používá [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists), protože chceme při každém spuštění ukázky vytvořit nový kontejner. V produkčním prostředí, kde používáte stejný kontejner v celé aplikaci, je lepší volat **CreateIfNotExists** pouze jednou. Případně můžete kontejner vytvořit předem, abyste ho nemuseli vytvářet v kódu.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Nejčastěji používané jsou objekty blob bloku, které se používají také v tomto rychlém startu. 

Pokud chcete do objektu blob nahrát soubor, získejte odkaz na objekt blob v cílovém kontejneru. Jakmile budete mít tento odkaz na objekt blob, můžete do něj nahrát data pomocí [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Tato operace vytvoří objekt blob, pokud ještě neexistuje, nebo ho přepíše, pokud už existuje.

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor určený k nahrání uloží jako **source** (zdroj) a název objektu blob jako **blob**. Následující příklad nahraje soubor do kontejneru **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

V případě úložiště objektů blob můžete k nahrání použít několik metod, včetně metod [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) a [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext). Například pokud máte řetězec, můžete místo metody Upload použít metodu UploadText. 

Objekty blob bloku můžou být jakýmkoli typem textového nebo binárního souboru. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina objektů blob ukládaných do úložiště jsou typu blok.

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam souborů v kontejneru můžete získat pomocí [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Následující kód načte seznam objektů blob, pak je ve smyčce projde a zobrazí identifikátory URI nalezených objektů blob. Soubor můžete zobrazit zkopírováním identifikátoru URI z příkazového okna a jeho vložením do prohlížeče.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Objekty blob můžete stáhnout na místní disk pomocí [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Následující kód stáhne objekt blob nahraný v předchozí části a k názvu objektu blob přidá příponu „_DOWNLOADED“, takže na místním disku uvidíte oba soubory. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Tím se odstraní také soubory v kontejneru.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí Javy. Další informace o práci s Javou najdete v našem úložišti zdrojového kódu na GitHubu.

> [!div class="nextstepaction"]
> [Sada SDK služby Azure Storage pro Javu](https://github.com/azure/azure-storage-java) 
> [Referenční informace k rozhraní API](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-stable)
> [Vzorové kódy pro Javu](../common/storage-samples-java.md)

* Další informace o Průzkumníku služby Storage a objektech blob najdete v tématu [Správa prostředků úložiště objektů blob v Azure pomocí Průzkumníka služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).