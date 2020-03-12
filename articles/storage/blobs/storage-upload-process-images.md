---
title: Odeslání dat obrázků do cloudu v Azure Storage | Microsoft Docs
description: Použití úložiště objektů Blob v Azure pomocí služby web app pro ukládání dat aplikací
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: 49078d2f374203a9fab4fe0f5e3881f6b1b22959
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130326"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Kurz: Odeslání dat obrázků do cloudu pomocí služby Azure Storage

Tento kurz je první částí série. V tomto kurzu se naučíte, jak nasadit webovou aplikaci, která pomocí klientské knihovny Azure Blob Storage odesílá image do účtu úložiště. Jakmile budete hotovi, budete mít webovou aplikaci, která ukládá a zobrazuje obrázky ze služby Azure storage.

# <a name="net-v12-sdk"></a>[sada SDK pro \.NET V12](#tab/dotnet)
![Aplikace pro změně velikosti obrázků v .NET](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[V10 za účelem SDK pro Node. js](#tab/nodejsv10)
![Aplikace se změněnou velikostí obrázku v Node. js v10 za účelem](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště
> * Vytvořit kontejner a nastavit oprávnění
> * Načtení přístupového klíče
> * Nasazení webové aplikace do Azure
> * Konfigurace nastavení aplikace
> * Interakce s webovou aplikací

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete předplatné Azure. Než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Chcete-li nainstalovat a používat rozhraní příkazového řádku místně, tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Ukázka nahrává obrázky do kontejneru objektů BLOB v účtu úložiště Azure. Účet služby Storage poskytuje jedinečný obor názvů pro ukládání datových objektů Azure Storage a přístup k nim. Ve skupině prostředků, kterou jste vytvořili vytvořte účet úložiště pomocí příkazu [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> V části 2 tohoto kurzu použijete úložiště objektů Blob v Azure Event Grid. Ujistěte se, že k vytvoření účtu úložiště v oblasti Azure, která podporuje služby Event Grid. Seznam podporovaných oblastí najdete v tématu [produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

V následujícím příkazu nahraďte vlastní globálně jedinečný název účtu služby Blob Storage, kde vidíte zástupný symbol `<blob_storage_account>`.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Vytvořit kontejnery úložiště objektů Blob

Aplikace používá v účtu služby Blob Storage dva kontejnery. Kontejnery jsou podobné složkám a ukládání objektů BLOB. Aplikace nahrává obrázky v plném rozlišení do kontejneru *images*. V pozdější části série aplikace funkcí Azure nahraje miniatury obrázků se změněnou velikostí do kontejneru *thumbnails*.

Pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys) zjistěte klíč účtu úložiště. Potom pomocí tohoto klíče vytvořte dva kontejnery pomocí příkazu [AZ Storage Container Create](/cli/azure/storage/container) .

Veřejný přístup kontejneru *images* je nastavený na `off`. Veřejný přístup kontejneru *miniatur* je nastaven na `container`. Nastavení `container` veřejný přístup umožňuje uživatelům, kteří navštíví webovou stránku, zobrazit miniatury.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Poznamenejte si název účtu služby Blob storage a klíč. Ukázková aplikace používá tato nastavení pro připojení k účtu úložiště k nahrání obrázku. 

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[Plán služby App Service](../../app-service/overview-hosting-plans.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace.

Pomocí příkazu [az appservice plan create](/cli/azure/appservice/plan) vytvořte plán služby App Service.

Následující příklad vytvoří plán služby App Service s názvem `myAppServicePlan` v cenové úrovni **Free**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Webová aplikace poskytuje prostor hostitele pro kód ukázkové aplikace, která se nasadí z ukázkového úložiště Githubu. Pomocí příkazu [az webapp create](../../app-service/overview.md) vytvořte `myAppServicePlan`webovou aplikaci[ v plánu služby App Service ](/cli/azure/webapp).  

V následujícím příkazu nahraďte `<web_app>` jedinečným názvem. Platné znaky jsou `a-z`, `0-9` a `-`. Pokud není název `<web_app>` jedinečný, zobrazí se chybová zpráva *Web se zadaným názvem `<web_app>` už existuje.* Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Nasazení ukázkové aplikace z úložiště GitHubu

# <a name="net-v12-sdk"></a>[sada SDK pro \.NET V12](#tab/dotnet)

Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Vzorový projekt obsahuje aplikaci [ASP.NET MVC](https://www.asp.net/mvc) . Aplikace přijme obrázek, uloží ho do účtu úložiště a zobrazí obrázky z kontejneru miniatur. Tato webová aplikace používá obory názvů [Azure. Storage](/dotnet/api/azure.storage), [Azure. Storage. BLOBs](/dotnet/api/azure.storage.blobs)a [Azure. Storage. BLOBs. Models](/dotnet/api/azure.storage.blobs.models) pro interakci se službou Azure Storage.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v10-sdk"></a>[V10 za účelem SDK pro Node. js](#tab/nodejsv10)
Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

---

## <a name="configure-web-app-settings"></a>Konfigurace nastavení webové aplikace

# <a name="net-v12-sdk"></a>[sada SDK pro \.NET V12](#tab/dotnet)

Ukázková webová aplikace používá rozhraní [api Azure Storage pro technologii .NET](/dotnet/api/overview/azure/storage) k nahrávání imagí. Přihlašovací údaje účtu úložiště se nastavují v nastavení aplikace pro webovou aplikaci. Do nasazené aplikace přidejte nastavení aplikace pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings) .

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="nodejs-v10-sdk"></a>[V10 za účelem SDK pro Node. js](#tab/nodejsv10)

Ukázková webová aplikace zadává požadavek na přístupové tokeny, které slouží k nahrávání obrázků, pomocí [klientské knihovny Azure Storage](https://github.com/Azure/azure-storage-js). Přihlašovací údaje účtu úložiště používat sadou Storage SDK se nastavují v nastavení aplikace pro webovou aplikaci. Do nasazené aplikace přidejte nastavení aplikace pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings) .

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Po nasazení a konfigurace webové aplikace, můžete v aplikaci otestovat funkci nahrávání obrázků.

## <a name="upload-an-image"></a>Nahrání image

Pokud chcete otestovat webovou aplikaci, přejděte na adresu URL publikované aplikace. Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[sada SDK pro \.NET V12](#tab/dotnet)

Vyberte oblast **nahrát fotografie** a určete a nahrajte soubor nebo přetáhněte soubor do oblasti. Obrázek po úspěšném nahrání zmizí. Oddíl **vygenerované miniatury** zůstane prázdný, dokud ho neotestujete dále v tomto tématu.

![Nahrávání fotek v .NET](media/storage-upload-process-images/figure1.png)

V ukázkovém kódu se `UploadFileToStorage` úloha v souboru *Storagehelper.cs* slouží k nahrání imagí do kontejneru *images* v rámci účtu úložiště pomocí metody [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) . Úlohu `UploadFileToStorage` obsahuje následující vzorek kódu.

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

| Třída    | Metoda   |
|----------|----------|
| [Identifikátor URI](/dotnet/api/system.uri) | [Konstruktor URI](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (String; String) – konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="nodejs-v10-sdk"></a>[V10 za účelem SDK pro Node. js](#tab/nodejsv10)

Vyberte možnost **zvolit soubor** a vyberte soubor a pak klikněte na **Odeslat obrázek**. Oddíl **vygenerované miniatury** zůstane prázdný, dokud ho neotestujete dále v tomto tématu. 

![Nahrávání fotek v Node. js v10 za účelem](media/storage-upload-process-images/upload-app-nodejs.png)

Ve vzorovém kódu je za nahrání obrázku do kontejneru objektů blob zodpovědná trasa `post`. Tato trasa při zpracování nahrávání využívá následující moduly:

- [multer](https://github.com/expressjs/multer) implementuje strategii nahrávání pro obslužnou rutinu trasy.
- [do datového proudu](https://github.com/sindresorhus/into-stream) převede vyrovnávací paměť na datový proud, jak to vyžaduje [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

Soubor je odeslané na trasu, zůstávají obsah souboru v paměti, dokud se soubor nahraje do kontejneru objektů blob.

> [!IMPORTANT]
> Načítání velkých souborů do paměti může mít negativní vliv na výkon vaší webové aplikace. Pokud očekáváte, že uživatelé k odeslání velkých souborů, můžete zvážit pracovní soubory v systému souborů webového serveru a plánování nahrávání do úložiště objektů Blob. Jakmile jsou soubory v úložišti objektů Blob, můžete ho odebrat ze systému souborů serveru.

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Kontrola zobrazení obrázku v účtu úložiště

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. Vyberte **kontejnery**a pak vyberte kontejner **imagí** .

Zkontrolujte, jestli se obrázek v kontejneru zobrazuje.

![Seznam kontejnerů imagí na webu Azure Portal](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test zobrazení miniatury

Chcete-li otestovat zobrazení miniatur, Nahrajte obrázek do kontejneru **miniatury** , abyste zkontrolovali, jestli aplikace může číst kontejner **miniatur** .

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. Vyberte **kontejnery**a pak vyberte kontejner **miniatury** . Výběrem položky **Nahrát** otevřete podokno **Nahrát objekt blob**.

Zvolte soubor pomocí nástroje pro výběr souborů a vyberte **Odeslat**.

Vraťte se do své aplikace a zkontrolujte, jestli je viditelný obrázek nahraný do kontejneru **thumbnails**.

# <a name="net-v12-sdk"></a>[sada SDK pro \.NET V12](#tab/dotnet)
![Aplikace pro změně velikosti obrázku .NET s zobrazeným novým obrázkem](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[V10 za účelem SDK pro Node. js](#tab/nodejsv10)
![Aplikace pro obnovení velikosti obrázku v10 za účelem Node. js s novým zobrazeným obrázkem](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Ve druhé části série můžete automatizovat vytváření obrázků miniatur, takže není nutné tuto bitovou kopii. V kontejneru **thumbnails** na webu Azure Portal vyberte obrázek, který jste nahráli, a výběrem možnosti **Odstranit** ho odstraňte. 

Můžete povolit Content Delivery Network (CDN) pro ukládání obsahu do mezipaměti z vašeho účtu úložiště Azure. Další informace o tom, jak povolit CDN s účtem služby Azure Storage, najdete v tématu [integrace účtu Azure Storage s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Další kroky

V první části této série jste zjistili, jak nakonfigurovat webovou aplikaci pro interakci s úložištěm.

Přejděte k druhé části se dozvíte, jak pomocí Event gridu aktivovat funkci Azure pro změnu velikosti obrázku.

> [!div class="nextstepaction"]
> [Aktivace funkce Azure pro změnu velikosti obrázku pomocí Event Gridu](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
