---
title: 'Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure V8 pro jazyk Java'
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijte Azure Storage klientské knihovny V8 pro jazyk Java k nahrání objektu blob do Azure Storage, stažení objektu BLOB a výpisu objektů BLOB v kontejneru.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b3bb8736eb7a8e24f47812fc4feecbf96628dfb6
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599819"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Rychlý Start: Správa objektů BLOB pomocí sady Java V8 SDK

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Java. Objekty blob jsou objekty, které mohou obsahovat velké objemy textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a dat archivu. Budete nahrávat, stahovat a vypisovat objekty blob. Můžete také vytvořit, nastavit oprávnění pro a odstranit kontejnery.

> [!NOTE]
> V tomto rychlém startu se používá starší verze klientské knihovny pro úložiště objektů BLOB v Azure. Pokud chcete začít používat nejnovější verzi, přečtěte si téma [rychlý Start: Správa objektů BLOB pomocí sady Java V12 SDK](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- Integrované vývojové prostředí (IDE), které má integraci Maven. Tato příručka používá [zatmění](https://www.eclipse.org/downloads/) s konfigurací "zatmění IDE pro vývojáře v jazyce Java".

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-java-quickstart) je základní Konzolová aplikace.

Pomocí [Gitu](https://git-scm.com/) si stáhněte kopii aplikace do vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete projekt otevřít, spusťte Eclipse a zavřete uvítací obrazovku. Vyberte **File** (Soubor) a pak **Open Projects from File System** (Otevřít projekty ze systému souborů). Ujistěte se, že je zaškrtnuté políčko **Detect and configure project natures** (Detekovat a nakonfigurovat povahu projektu). Vyberte **Directory** (Adresář) a pak přejděte do umístění, kam jste uložili naklonované úložiště. V naklonovaném úložišti vyberte složku **blobAzureApp**. Ujistěte se, že se projekt **blobAzureApp** zobrazí jako projekt Eclipse, a pak vyberte **Finish** (Dokončit).

Po dokončení importu projektu otevřete **soubor azureapp. Java** (umístěný v **blobQuickstart. blobAzureApp** uvnitř elementu **Src/Main/Java**) a nahraďte `accountname` a `accountkey` uvnitř `storageConnectionString` řetězce. Pak aplikaci spusťte. Konkrétní pokyny k dokončení těchto úloh jsou popsané v následujících částech.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Otevřete soubor **AzureApp.Java**. Vyhledejte proměnnou `storageConnectionString` a vložte do ní hodnotu připojovacího řetězce, kterou jste zkopírovali v předchozí části. Vaše proměnná `storageConnectionString` by měla vypadat podobně jako v následujícím příkladu kódu:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Spuštění ukázky

Tato ukázková aplikace vytvoří testovací soubor ve výchozím adresáři (*C:\Users \<user> \AppData\Local\Temp*, pro uživatele Windows), nahraje ho do úložiště objektů blob, vypíše objekty BLOB v kontejneru a pak soubor stáhne s novým názvem, abyste mohli porovnat Starý a nový soubor.

Na příkazovém řádku spusťte ukázku pomocí nástroje Maven. Otevřete prostředí a přejděte k **blobAzureApp** v naklonovaném adresáři. Potom zadejte `mvn compile exec:java`.

Následuje příklad ukazuje výstup při spuštění aplikace ve Windows.

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

Než budete pokračovat, vyhledejte ukázkový soubor ve výchozím adresáři (*C:\Users \<user> \AppData\Local\Temp* pro uživatele Windows). Obsah souboru v úložišti objektů blob můžete zobrazit zkopírováním adresy URL objektu blob z okna konzoly a jejím vložením do prohlížeče. Pokud porovnáte ukázkový soubor ve vašem adresáři s obsahem uloženým v úložišti objektů blob, uvidíte, že se shodují.

  >[!NOTE]
  >K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště.

Po ověření souborů stiskněte klávesu **ENTER** a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor **AzureApp.java** a prohlédněte si kód.

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

První věc, kterou je potřeba udělat, je vytvořit odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) ukazující na účet úložiště.

    Objekt **CloudStorageAccount** je reprezentace vašeho účtu úložiště a umožňuje nastavení a přístup k vlastnostem účtu úložiště prostřednictvím kódu programu. Pomocí objektu **CloudStorageAccount** můžete vytvořit instanci objektu **CloudBlobClient**, která je nezbytná pro přístup ke službě Blob.

* Vytvořte instanci objektu **CloudBlobClient**, která odkazuje na [službu Blob service](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) ve vašem účtu úložiště.

    **CloudBlobClient** poskytuje přístupový bod ke službě Blob service a díky tomu umožňuje nastavení a přístup k vlastnostem úložiště objektů blob prostřednictvím kódu programu. Pomocí objektu **CloudBlobClient** můžete vytvořit instanci objektu **CloudBlobContainer**, která je nezbytná pro vytváření kontejnerů.

* Vytvořte instanci objektu [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer), která představuje kontejner, ke kterému přistupujete. Kontejnery můžete použít k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

    Jakmile budete mít **CloudBlobContainer**, můžete vytvořit instanci objektu [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob) , která odkazuje na konkrétní objekt blob, který vás zajímá, a provádět nahrávání, stahování, kopírování nebo jiné operace.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o kontejnerech najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Vytvoření kontejneru

V této části vytvoříte instance objektů, vytvoříte nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné a přístupné přes pouhou adresu URL. Kontejner má název **quickstartcontainer**.

Tento příklad používá [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists), protože chceme při každém spuštění ukázky vytvořit nový kontejner. V produkčním prostředí, kde používáte stejný kontejner v celé aplikaci, je lepší volat **CreateIfNotExists** pouze jednou. Případně můžete kontejner vytvořit předem, abyste ho nemuseli vytvářet v kódu.

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

Pokud chcete nahrát soubor do objektu blob bloku, získejte odkaz na objekt BLOB v cílovém kontejneru. Jakmile budete mít tento odkaz na objekt blob, můžete do něj nahrát data pomocí [CloudBlockBlob.Upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload). Tato operace vytvoří objekt blob, pokud ještě neexistuje, nebo objekt blob přepíše, pokud už existuje.

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor k nahrání uloží do proměnné **source** a název objektu blob do proměnné **blob**. Následující příklad nahraje soubor do kontejneru **quickstartcontainer**.

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

V případě úložiště objektů blob můžete použít několik metod `upload`, včetně metod [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) a [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext). Například pokud máte řetězec, můžete místo metody `Upload` použít metodu `UploadText`.

Objekty blob bloku můžou být jakýmkoli typem textového nebo binárního souboru. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob můžete použít k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina objektů uložených v úložišti objektů blob je objekty blob bloku.

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Seznam souborů v kontejneru můžete získat pomocí [CloudBlobContainer.ListBlobs](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs). Následující kód načte seznam objektů blob, pak je ve smyčce projde a zobrazí identifikátory URI nalezených objektů blob. Soubor můžete zobrazit zkopírováním identifikátoru URI z příkazového okna a jeho vložením do prohlížeče.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Objekty blob můžete stáhnout na místní disk pomocí [CloudBlob.DownloadToFile](/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile).

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

Pokud už nepotřebujete objekty blob, které jste nahráli, můžete celý kontejner odstranit pomocí [CloudBlobContainer. DeleteIfExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists). Tato metoda odstraní také soubory v kontejneru.

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

V tomto článku jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů BLOB v Azure pomocí jazyka Java. Další informace o práci s Javou najdete v našem úložišti zdrojového kódu na GitHubu.

> [!div class="nextstepaction"]
> Reference k rozhraní [Java API](/java/api/overview/azure/storage?view=azure-java-legacy&preserve-view=true) 
>  [Ukázky kódu pro Java](../common/storage-samples-java.md)