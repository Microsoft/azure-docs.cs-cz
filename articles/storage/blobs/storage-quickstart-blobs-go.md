---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí Go | Microsoft Docs
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro Go k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: seguler
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: seguler
ms.openlocfilehash: 2939bd5c7b32cc9fe05326ee72dbb7367a72ef7f
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711153"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí Go

V tomto rychlém startu zjistíte, jak pomocí programovacího jazyka Go nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ujistěte se, že máte nainstalované následující další požadavky:
 
* [Go verze 1.8 nebo vyšší](https://golang.org/dl/)
* [Azure Storage Blob SDK for Go](https://github.com/azure/azure-storage-blob-go/), pomocí následujícího příkazu:

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > Ujistěte se, že můžete využít `Azure` v adrese URL, aby nedocházelo k problémům souvisejícím s případ import při práci se sadou SDK. Také využijte `Azure` v příkazech pro import.
    
## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace
[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) použitá v tomto rychlém startu je základní aplikace v jazyce Go.  

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete otevřít ukázku v jazyce Go pro úložiště objektů blob, vyhledejte soubor storage-quickstart.go.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
Toto řešení vyžaduje, aby název a klíč vašeho účtu úložiště byly bezpečně uložené v místních proměnných prostředí počítače, na kterém je ukázka spuštěná. V závislosti na operačním systému vytvořte proměnné prostředí pomocí jednoho z následujících příkladů.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Spuštění ukázky
Ukázka vytvoří v aktuální složce testovací soubor, nahraje ho do úložiště objektů blob, vypíše objekty blob v kontejneru a stáhne soubor do vyrovnávací paměti. 

Ukázku spustíte následujícím příkazem: 

```go run storage-quickstart.go```

Následující výstup je příkladem výstupu vráceného po spuštění aplikace:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Když budete pokračovat stisknutím klávesy, ukázkový program odstraní kontejner úložiště i soubory. 

> [!TIP]
> K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 
>

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="create-containerurl-and-bloburl-objects"></a>Vytvoření objektů ContainerURL a BlobURL
První věc, kterou je potřeba udělat, je vytvořit odkazy na objekty ContainerURL a BlobURL sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty nabízejí rozhraní API nízké úrovně, jako jsou Create, Upload a Download, pro vydávání rozhraní REST API.

* K uložení přihlašovacích údajů použijte strukturu [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential). 

* Vytvořte [**Kanál**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline) s použitím těchto přihlašovacích údajů a možností. Kanál určuje například zásady opakování, protokolování, deserializaci datových částí odpovědí HTTP a další.  

* Vytvořte nové instance objektů [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) a [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL) pro spouštění operací s kontejnery (Create) a objekty blob (Upload a Download).


Jakmile budete mít objekt ContainerURL, můžete vytvořit instanci objektu **BlobURL** odkazující na objekt blob a provádět například operace nahrávání, stahování a kopírování.

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

V této části vytvoříte nový kontejner. Kontejner má název **quickstartblobs-[náhodný_řetězec]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Nejčastěji používané jsou objekty blob bloku, které se používají také v tomto rychlém startu.  

Pokud chcete do objektu blob nahrát soubor, otevřete soubor pomocí příkazu **os.Open**. Pak můžete soubor nahrát do zadané cesty pomocí některého z rozhraní REST API: Upload (PutBlob), StageBlock nebo CommitBlockList (PutBlock nebo PutBlockList). 

Sada SDK případně nabízí [rozhraní API vysoké úrovně](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go) založená na rozhraních REST API nízké úrovně. Příkladem je funkce ***UploadFileToBlockBlob***, která používá operace StageBlock (PutBlock) k souběžnému nahrání souboru po částech za účelem optimalizace propustnosti. Pokud je soubor menší než 256 MB, použije místo toho operaci Upload (PutBlob) k dokončení přenosu v rámci jediné transakce.

Následující příklad nahraje soubor do kontejneru **quickstartblobs-[náhodný_řetězec]**.

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam souborů v kontejneru získáte pomocí metody **ListBlobs** s použitím objektu **ContainerURL**. Metoda ListBlobs vrací jeden segment objektů blob (až 5 000) počínaje zadanou **značkou**. Pokud chcete začít výčet od začátku, použijte prázdnou značku. Názvy objektů blob se vrací ve slovníkovém pořadí. Po získání segmentu ho zpracujte a pak znovu zavolejte metodu ListBlobs a předejte jí dříve vrácenou značku.  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Stažení objektu blob

Objekty blob můžete stáhnout pomocí funkce nízké úrovně **Download** s použitím objektu BlobURL. Tato funkce vrátí strukturu **DownloadResponse**. Spuštěním funkce **Body** pro strukturu získáte datový proud **RetryReader** pro čtení dat. Pokud při čtení selže připojení, odešlou se další požadavky na opětovné navázání připojení a bude se pokračovat ve čtení. Pokud zadáte RetryReaderOptions s vlastností MaxRetryRequests nastavenou na hodnotu 0 (výchozí hodnota), vrátí se původní text odpovědi bez opakování. Alternativně můžete kód zjednodušit použitím rozhraní API vysoké úrovně **DownloadBlobToBuffer** nebo **DownloadBlobToFile**.

Následující kód stáhne objekt blob pomocí funkce **Download**. Obsah objektu blob se zapíše do vyrovnávací paměti a zobrazí se v konzole.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí metody **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací v jazyce Go s využitím objektů blob

Prohlédněte si tyto další zdroje informací o vývoji v jazyce Go s využitím úložiště objektů blob:

- Prohlédněte si a nainstalujte [zdrojový kód klientské knihovny pro jazyk Go](https://github.com/Azure/azure-storage-blob-go) pro službu Azure Storage na GitHubu.
- Prozkoumejte [ukázky pro úložiště objektů blob](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples) napsané s využitím klientské knihovny pro jazyk Go.

## <a name="next-steps"></a>Další postup
 
V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí jazyka Go. Další informace o sadě Azure Storage Blob SDK najdete ve [zdrojovém kódu](https://github.com/Azure/azure-storage-blob-go/) a [referenčních materiálech k rozhraní API](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob).
