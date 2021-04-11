---
title: Odeslání dat obrázků do cloudu v Azure Storage | Microsoft Docs
description: Pomocí služby Azure Blob Storage s webovými aplikacemi můžete ukládat data aplikací do účtu úložiště. V tomto kurzu vytvoříte webovou aplikaci, která ukládá a zobrazuje obrázky z Azure Storage.
author: twooley
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: twooley
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f751b93a7d298763da5a37a54f400d6fc56a58e3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277959"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Kurz: nahrání obrazových dat v cloudu pomocí Azure Storage

Tento kurz je první částí série. V tomto kurzu se dozvíte, jak nasadit webovou aplikaci. Webová aplikace používá klientskou knihovnu služby Azure Blob Storage k nahrání imagí do účtu úložiště. Až budete hotovi, budete mít webovou aplikaci, která bude ukládat a zobrazovat obrázky z Azure Storage.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

![Aplikace pro změně velikosti obrázků v .NET](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

![Aplikace se změněnou velikostí obrázku v JavaScriptu](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

V první části tohoto kurzu se naučíte:

> [!div class="checklist"]

> * Vytvoření účtu úložiště
> * Vytvoření kontejneru a nastavení oprávnění
> * Načtení přístupového klíče
> * Nasazení webové aplikace do Azure
> * Konfigurace nastavení aplikace
> * Interakce s webovou aplikací

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud chcete nainstalovat a používat rozhraní příkazového řádku místně, spusťte Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.

```azurecli
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Ukázka nahrává obrázky do kontejneru objektů BLOB v účtu úložiště Azure. Účet služby Storage poskytuje jedinečný obor názvů pro ukládání datových objektů Azure Storage a přístup k nim. Ve skupině prostředků, kterou jste vytvořili vytvořte účet úložiště pomocí příkazu [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> V části 2 tohoto kurzu používáte Azure Event Grid s úložištěm objektů BLOB. Ujistěte se, že jste svůj účet úložiště vytvořili v oblasti Azure, která podporuje Event Grid. Seznam podporovaných oblastí najdete v tématu [produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

V následujícím příkazu nahraďte zástupný symbol vlastním globálně jedinečným názvem účtu služby Blob Storage `<blob_storage_account>` .

```azurecli
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Vytvoření kontejnerů úložiště objektů BLOB

Aplikace používá v účtu služby Blob Storage dva kontejnery. Kontejnery jsou podobné složkám a objektům blob úložiště. Aplikace nahrává obrázky v plném rozlišení do kontejneru *images*. V pozdější části série aplikace funkcí Azure nahraje miniatury obrázků se změněnou velikostí do kontejneru *thumbnails*.

Pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys) zjistěte klíč účtu úložiště. Potom pomocí tohoto klíče vytvořte dva kontejnery pomocí příkazu [AZ Storage Container Create](/cli/azure/storage/container) .

Veřejný přístup kontejneru *images* je nastavený na `off` . Veřejný přístup kontejneru *miniatur* je nastavený na `container` . `container`Nastavení veřejný přístup umožňuje uživatelům, kteří navštíví webovou stránku, zobrazit miniatury.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Poznamenejte si název a klíč účtu služby Blob Storage. Ukázková aplikace používá tato nastavení pro připojení k účtu úložiště pro nahrání imagí. 

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[Plán služby App Service](../../app-service/overview-hosting-plans.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace.

Pomocí příkazu [az appservice plan create](/cli/azure/appservice/plan) vytvořte plán služby App Service.

Následující příklad vytvoří plán služby App Service s názvem `myAppServicePlan` v cenové úrovni **Free**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Webová aplikace poskytuje prostor pro hostování kódu ukázkové aplikace, který je nasazený z ukázkového úložiště GitHubu. Pomocí příkazu [az webapp create](/cli/azure/webapp) vytvořte [webovou aplikaci](../../app-service/overview.md) v plánu služby App Service `myAppServicePlan`.  

V následujícím příkazu nahraďte `<web_app>` jedinečným názvem. Platné znaky jsou `a-z`, `0-9` a `-`. Pokud `<web_app>` není jedinečný, zobrazí se chybová zpráva: *web se zadaným názvem `<web_app>` již existuje.* Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.  

```azurecli
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Nasazení ukázkové aplikace z úložiště GitHubu

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Vzorový projekt obsahuje aplikaci [ASP.NET MVC](https://www.asp.net/mvc) . Aplikace akceptuje obrázek, uloží ho do účtu úložiště a zobrazí obrázky z kontejneru miniatur. Tato webová aplikace používá obory názvů [Azure. Storage](/dotnet/api/azure.storage), [Azure. Storage. BLOBs](/dotnet/api/azure.storage.blobs)a [Azure. Storage. BLOBs. Models](/dotnet/api/azure.storage.blobs.models) pro interakci se službou Azure Storage.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Konfigurace nastavení webové aplikace

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Ukázková webová aplikace používá rozhraní [api Azure Storage pro technologii .NET](/dotnet/api/overview/azure/storage) k nahrávání imagí. Přihlašovací údaje účtu úložiště se nastavují v nastavení aplikace pro webovou aplikaci. Do nasazené aplikace přidejte nastavení aplikace pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings) .

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Ukázková webová aplikace používá [Azure Storage klientské knihovny pro JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) k nahrávání obrázků. Přihlašovací údaje účtu úložiště se nastavují v nastavení aplikace pro webovou aplikaci. Do nasazené aplikace přidejte nastavení aplikace pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings) .

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Po nasazení a konfiguraci webové aplikace můžete testovat funkci nahrávání obrázků v aplikaci.

## <a name="upload-an-image"></a>Nahrání image

Pokud chcete otestovat webovou aplikaci, přejděte na adresu URL publikované aplikace. Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Vyberte oblast **nahrát fotografie** a určete a nahrajte soubor nebo přetáhněte soubor do oblasti. Obrázek po úspěšném nahrání zmizí. Oddíl **vygenerované miniatury** zůstane prázdný, dokud ho neotestujeme dále v tomto kurzu.

![Nahrávání fotek v .NET](media/storage-upload-process-images/figure1.png)

V ukázkovém kódu se `UploadFileToStorage` úloha v souboru *Storagehelper. cs* používá k nahrání imagí do kontejneru *images* v rámci účtu úložiště pomocí metody [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) . Úlohu `UploadFileToStorage` obsahuje následující vzorek kódu.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

Předchozí úloha využívá následující třídy a metody:

| Třída | Metoda |
|-------|--------|
| [Identifikátor URI](/dotnet/api/system.uri) | [Konstruktor URI](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (String; String) – konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Vyberte možnost **zvolit soubor** a vyberte soubor a pak klikněte na **Odeslat obrázek**. Oddíl **vygenerované miniatury** zůstane prázdný, dokud ho neotestujeme dále v tomto kurzu.

![Nahrávání fotek v Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

Ve vzorovém kódu je za nahrání obrázku do kontejneru objektů blob zodpovědná trasa `post`. Tato trasa při zpracování nahrávání využívá následující moduly:

- [multer](https://github.com/expressjs/multer) implementuje strategii nahrávání pro obslužnou rutinu trasy.
- [do datového proudu](https://github.com/sindresorhus/into-stream) převede vyrovnávací paměť na datový proud, jak to vyžaduje [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-).

Jakmile se soubor pošle do trasy, obsah souboru zůstane v paměti, dokud se soubor neodešle do kontejneru objektů BLOB.

> [!IMPORTANT]
> Načítání velkých souborů do paměti může mít negativní vliv na výkon vaší webové aplikace. Pokud očekáváte, že uživatelé budou posílat velké soubory, zvažte, že budete chtít zvážit pracovní soubory v systému souborů webového serveru a pak naplánujete nahrávání do úložiště objektů BLOB. Jakmile jsou soubory v úložišti objektů blob, můžete je odebrat ze systému souborů serveru.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Kontrola zobrazení obrázku v účtu úložiště

Přihlaste se na [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. Vyberte **kontejnery** a pak vyberte kontejner **imagí** .

Zkontrolujte, jestli se obrázek v kontejneru zobrazuje.

![Azure Portal výpisu kontejneru imagí](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test zobrazení miniatury

Chcete-li otestovat zobrazení miniatur, Nahrajte obrázek do kontejneru **miniatury** , abyste zkontrolovali, jestli aplikace může číst kontejner **miniatur** .

Přihlaste se na [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. Vyberte **kontejnery** a pak vyberte kontejner **miniatury** . Výběrem položky **Nahrát** otevřete podokno **Nahrát objekt blob**.

Zvolte soubor pomocí nástroje pro výběr souborů a vyberte **Odeslat**.

Vraťte se do své aplikace a zkontrolujte, jestli je viditelný obrázek nahraný do kontejneru **thumbnails**.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

![Aplikace pro změně velikosti obrázku .NET s zobrazeným novým obrázkem](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

![Aplikace pro obnovení velikosti obrázku Node.js s nově zobrazeným obrázkem](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Ve druhé části série můžete automatizovat vytváření obrázků miniatur, takže tuto image nebudete potřebovat. V kontejneru **miniatury** vyberte obrázek, který jste nahráli, a kliknutím na **Odstranit** odeberte obrázek.

Můžete povolit Content Delivery Network (CDN) pro ukládání obsahu do mezipaměti z vašeho účtu úložiště Azure. Další informace najdete v tématu [integrace účtu Azure Storage s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Další kroky

V první části série jste se dozvěděli, jak nakonfigurovat webovou aplikaci pro práci s úložištěm.

Pokud se chcete dozvědět víc o použití Event Grid k aktivaci funkce Azure pro změnu velikosti obrázku, přečtěte si část dvě série.

> [!div class="nextstepaction"]
> [Aktivace funkce Azure pro změnu velikosti obrázku pomocí Event Gridu](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
