---
title: 'Rychlý start Azure: Vytvoření objektu blob v úložišti objektů pomocí sady Java Storage SDK V10 | Microsoft Docs'
description: V tomto rychlém startu vytvoříte kontejner v úložišti objektů (Azure Blob), nahrajete soubor a vypíšete a stáhnete objekty pomocí sady Java Storage SDK.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: rogarana
ms.openlocfilehash: c675dd17994abaaf6d0eed1934bec8f2220e7435
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955681"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí sady Java Storage SDK V10

V tomto rychlém startu zjistíte, jak pomocí nové sady Java Storage SDK nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure. Nová sada Java SDK používá reaktivní programovací model s implementací RxJava, který umožňuje asynchronní operace. Přečtěte si další informace o [reaktivních rozšířeních pro virtuální počítače s Javou](https://github.com/ReactiveX/RxJava) RxJava. 

## <a name="prerequisites"></a>Požadavky

Nainstalujte a nakonfigurujte tyto aplikace:

* [Maven](http://maven.apache.org/download.cgi) pro práci z příkazového řádku nebo jakékoli integrované vývojové prostředí Java, kterému dáváte přednost
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) použitá v tomto rychlém startu je základní konzolová aplikace. 

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu.

Po dokončení importu projektu otevřete soubor **Quickstart.java**, který se nachází ve složce **src/main/java/quickstart**.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
Toto řešení vyžaduje bezpečné uložení názvu a klíče vašeho účtu úložiště. Uložte je do proměnných prostředí v místním prostředí počítače, na kterém běží ukázka. K vytvoření proměnných prostředí použijte příklad pro Linux nebo Windows podle toho, jaký operační systém používáte.

### <a name="linux-example"></a>Příklad pro Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Příklad pro Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Spuštění ukázky

Pokud používáte W, tato ukázka vytvoří testovací soubor ve výchozím adresáři **AppData\Local\Temp**. Potom se zobrazí výzva k provedení následujících kroků:

1. Zadejte příkazy pro nahrání testovacího souboru do Azure Blob Storage.
2. Zobrazte seznam objektů blob v kontejneru.
3. Stáhněte nahraný soubor pod novým názvem, abyste mohli porovnat staré a nové soubory. 

Pokud chcete ukázku spustit pomocí Mavenu na příkazovém řádku, otevřete prostředí a přejděte do složky **storage-blobs-java-v10-quickstart** v naklonovaném adresáři. Potom zadejte `mvn compile exec:java`.

Tento příklad ukazuje výstup při spuštění aplikace ve Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Máte možnost ovládat tuto ukázku a zadat příkazy, které spustí kód. Při zadávání se rozlišují malá a velká písmena.

K zobrazení souborů v úložišti objektů blob můžete použít taky nástroj, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 

Ověřte soubory. Potom vyberte **E**, stiskněte **Enter**, aby se ukázka dokončila, a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor **Quickstart.java** a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

V následujících částech si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

Nejdřív vytvoříte odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se sestavují jeden podle druhého. Každý další objekt použije ten, který je v seznamu před ním.

1. Vytvořte instanci objektu **StorageURL**, která odkazuje na účet úložiště.

    * Objekt [StorageURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-stable) je reprezentace vašeho účtu úložiště. Použijete ho k vygenerování nového kanálu. 
    * Kanál je sada zásad, které se používají při manipulaci s požadavky a odpověďmi pomocí mechanismů ověřování, protokolování a opakování. Další informace najdete v článku [Kanál HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline).  
    * Pomocí kanálu vytvořte instanci objektu [ServiceURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-stable).
    * Pomocí objektu **ServiceURL** vytvořte instanci objektu [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * Objekt **ContainerURL** je zapotřebí ke spouštění operací v kontejnerech objektů blob.

2. Vytvořte instanci objektu **ContainerURL** reprezentující kontejner, ke kterému získáváte přístup. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

    * Objekt **ContainerURL** poskytuje přístupový bod ke službě kontejneru. 
    * Pomocí objektu [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable) můžete vytvořit instanci objektu [BlobURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-stable).
    * Objekt **BlobURL** slouží k vytváření objektů blob.

3. Vytvořte instanci objektu **BlobURL** odkazující na konkrétní objekt blob, který vás zajímá. 

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Vytvoření kontejneru 

V této části vytvoříte instanci objektu **ContainerURL**. S ní vytvoříte i nový kontejner. Kontejner v ukázce má název **quickstart**. 

Tento příklad používá [ContainerURL.create](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-stable#com_microsoft_azure_storage_blob__container_u_r_l_create_Metadata_PublicAccessType_Context_), abyste mohli při každém spuštění ukázky vytvořit nový kontejner. Kontejner ale můžete vytvořit i předem, abyste ho nemuseli vytvářet v kódu.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Objekty blob bloku se používají nejčastěji. Používáme je i v tomto rychlém startu. 

1. Pokud chcete do objektu blob nahrát soubor, získejte odkaz na objekt blob v cílovém kontejneru. 
2. Jakmile získáte odkaz na objekt blob, můžete do něj nahrát soubor pomocí některého z následujících rozhraní API:

    * Rozhraní API nízké úrovně. Mezi příklady patří rozhraní [BlockBlobURL.upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-stable#com_microsoft_azure_storage_blob__block_blob_u_r_l_upload_Flowable_ByteBuffer__long_BlobHTTPHeaders_Metadata_BlobAccessConditions_Context_) neboli PutBlob a rozhraní [BlockBlobURL.stageBlock](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-stable) neboli PutBLock v instanci objektu **BlockBlobURL**. 

    * Rozhraní API vysoké úrovně, která jsou součástí [třídy TransferManager](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-stable). Mezi příklady patří metoda [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-stable). 

    Tato operace vytvoří objekt blob, pokud ještě neexistuje. Pokud už objekt blob existuje, operace ho přepíše.

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor určený k nahrání se uloží jako **sourceFile** a adresa URL objektu blob jako **blob**. Následující příklad nahraje soubor do kontejneru **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Objekty blob bloku můžou být jakýmkoli typem textového nebo binárního souboru. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob slouží k doplňování dat na konec a často se používají k protokolování. Většina objektů blob ukládaných do úložiště jsou typu blok.

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam objektů v kontejneru můžete získat pomocí metody [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-stable). Tato metoda vrátí najednou až 5 000 objektů, a pokud kontejner obsahuje další objekty, vrátí se spolu s nimi pokračovací neboli další značka. Vytvořte pomocnou funkci, která opakovaně volá sama sebe tak dlouho, dokud se v předchozí odpovědi metody **listBlobsFlatSegment** nachází pokračovací značka.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte objekty blob na místní disk pomocí metody [BlobURL.download](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-stable).

Následující kód stáhne objekt blob nahraný v předchozí části. K názvu objektu blob se přidá přípona **_DOWNLOADED**, takže na místním disku uvidíte oba soubory. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí metody [ContainerURL.delete](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-stable). Tato metoda odstraní také soubory v kontejneru.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a službou Azure Blob Storage pomocí Javy. 

> [!div class="nextstepaction"]
> [Storage SDK V10 pro zdrojový kód Java](https://github.com/Azure/azure-storage-java/)
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/java/api/overview/azure/storage/client?view=azure-java-stable)
> [Další informace o implementaci RxJava](https://github.com/ReactiveX/RxJava)
