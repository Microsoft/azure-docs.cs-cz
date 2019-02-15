---
title: Odeslání dat obrázků do cloudu v Azure Storage | Microsoft Docs
description: Použití úložiště objektů Blob v Azure pomocí služby web app pro ukládání dat aplikací
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 01404c89665ebfea62e7bda0e7566289bb15f2ae
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300954"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Kurz: Odeslání dat obrázků do cloudu v Azure Storage

Tento kurz je první částí série. V tomto kurzu se dozvíte, jak nasadit webovou aplikaci, která používá Klientská knihovna pro úložiště Azure můžete nahrávat obrázky do účtu úložiště. Jakmile budete hotovi, budete mít webovou aplikaci, která ukládá a zobrazuje obrázky ze služby Azure storage.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

V první části této série se naučíte:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Vytvoření kontejneru a nastavení oprávnění
> * Načtení přístupového klíče
> * Nasazení webové aplikace do Azure
> * Konfigurace nastavení aplikace
> * Interakce s webovou aplikací

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Vytvoření [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Chcete-li nainstalovat a používat rozhraní příkazového řádku místně, tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků 

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Ukázka nahrává obrázky do kontejneru objektů blob v účtu služby Azure Storage. Účet služby Storage poskytuje jedinečný obor názvů pro ukládání datových objektů Azure Storage a přístup k nim. Ve skupině prostředků, kterou jste vytvořili vytvořte účet úložiště pomocí příkazu [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> V části 2 tohoto kurzu použijete úložiště objektů Blob v Azure Event Grid. Ujistěte se, že k vytvoření účtu úložiště v oblasti Azure, která podporuje služby Event Grid. Seznam podporovaných oblastí najdete v tématu [produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

V následujícím příkazu nahraďte vlastním globálně jedinečným názvem pro účet Blob storage, kde uvidíte `<blob_storage_account>` zástupný symbol.  

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

az storage account create --name $blobStorageAccount \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Vytvořit kontejnery úložiště objektů Blob

Aplikace používá v účtu služby Blob Storage dva kontejnery. Kontejnery jsou podobné složkám a ukládání objektů BLOB. Aplikace nahrává obrázky v plném rozlišení do kontejneru *images*. V pozdější části série aplikace funkcí Azure nahraje miniatury obrázků se změněnou velikostí do kontejneru *thumbnails*.

Pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys) zjistěte klíč účtu úložiště. Potom tento klíč použít k vytvoření dvou kontejnerů pomocí [vytvořit kontejner úložiště az](/cli/azure/storage/container) příkazu.  

*Image* kontejneru veřejného přístupu nastavená na `off`. *Miniatury* kontejneru veřejného přístupu nastavená na `container`. `container` Nastavení veřejného přístupu umožňuje uživatelům, kteří naleznete na webové stránce, chcete-li zobrazit miniatury.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

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

Webová aplikace poskytuje prostor hostitele pro kód ukázkové aplikace, která se nasadí z ukázkového úložiště Githubu. Pomocí příkazu [az webapp create](/cli/azure/webapp) vytvořte [webovou aplikaci](../../app-service/overview.md) v plánu služby App Service `myAppServicePlan`.  

V následujícím příkazu nahraďte `<web_app>` s jedinečným názvem. Platné znaky jsou `a-z`, `0-9` a `-`. Pokud `<web_app>` není jedinečný, zobrazí se chybová zpráva: _Web se zadaným názvem `<web_app>` již existuje._ Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp=<web_app>

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Nasazení ukázkové aplikace z úložiště GitHubu

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Ukázkový projekt obsahuje [ASP.NET MVC](https://www.asp.net/mvc) aplikace. Aplikace přijme obrázek, uloží ho do účtu úložiště a zobrazí obrázky z kontejneru miniatur. Tato webová aplikace používá [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet)a [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) obory názvů Azure storage klientské knihovny pro interakci s Azure storage.

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>Konfigurace nastavení webové aplikace

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Ukázková webová aplikace zadává požadavek na přístupové tokeny, které slouží k nahrávání obrázků, pomocí [klientské knihovny Azure Storage](/dotnet/api/overview/azure/storage?view=azure-dotnet). Přihlašovací údaje účtu úložiště používat sadou Storage SDK se nastavují v nastavení aplikace pro webovou aplikaci. Přidat nastavení aplikace s nasazenou aplikaci [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) příkazu.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=$blobStorageAccountKey  
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Ukázková webová aplikace zadává požadavek na přístupové tokeny, které slouží k nahrávání obrázků, pomocí [klientské knihovny Azure Storage](https://docs.microsoft.com/javascript/api/azure-storage). Přihlašovací údaje účtu úložiště používat sadou Storage SDK se nastavují v nastavení aplikace pro webovou aplikaci. Přidat nastavení aplikace s nasazenou aplikaci [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) příkazu.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__AccountKey=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Ukázková webová aplikace zadává požadavek na přístupové tokeny, které slouží k nahrávání obrázků, pomocí [klientské knihovny Azure Storage](https://github.com/Azure/azure-storage-js). Přihlašovací údaje účtu úložiště používat sadou Storage SDK se nastavují v nastavení aplikace pro webovou aplikaci. Přidat nastavení aplikace s nasazenou aplikaci [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) příkazu.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Po nasazení a konfigurace webové aplikace, můžete v aplikaci otestovat funkci nahrávání obrázků.

## <a name="upload-an-image"></a>Nahrání image

Pokud chcete otestovat webovou aplikaci, přejděte na adresu URL publikované aplikace. Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Vyberte **mohl odesílat fotky** oblasti vybrat a nahrát soubor nebo soubor přetáhněte do oblasti. Obrázek po úspěšném nahrání zmizí. **Vygenerované miniatury** části zůstane prázdná, dokud testujeme dále v tomto tématu.

![Aplikace ImageResizer](media/storage-upload-process-images/figure1.png)

Ve vzorovém kódu `UploadFiletoStorage` úlohy v *Storagehelper.cs* soubor slouží k nahrání obrázku do *image* kontejneru v účtu úložiště pomocí [ UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metody. Úlohu `UploadFiletoStorage` obsahuje následující vzorek kódu.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Předchozí úloha využívá následující třídy a metody:

|Třída  |Metoda  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Vyberte **zvolit soubor** a vyberte soubor, pak klikněte na tlačítko **nahrát obrázek**. **Vygenerované miniatury** části zůstane prázdná, dokud testujeme dále v tomto tématu. 

![Aplikace pro nahrávání obrázků](media/storage-upload-process-images/upload-app-nodejs.png)

Ve vzorovém kódu je za nahrání obrázku do kontejneru objektů blob zodpovědná trasa `post`. Tato trasa při zpracování nahrávání využívá následující moduly:

- [multer](https://github.com/expressjs/multer) implementuje strategii odeslání obslužné rutině trasy.
- [do datového proudu](https://github.com/sindresorhus/into-stream) převede vyrovnávací paměti do datového proudu podle požadavků [Createblockblobfromlocalfile]. ()http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

Soubor je odeslané na trasu, zůstávají obsah souboru v paměti, dokud se soubor nahraje do kontejneru objektů blob.

> [!IMPORTANT]
> Načítání velkých souborů do paměti může mít negativní vliv na výkon vaší webové aplikace. Pokud očekáváte, že uživatelé k odeslání velkých souborů, můžete zvážit pracovní soubory v systému souborů webového serveru a plánování nahrávání do úložiště objektů Blob. Jakmile jsou soubory v úložišti objektů Blob, můžete ho odebrat ze systému souborů serveru.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Vyberte **zvolit soubor** a vyberte soubor, pak klikněte na tlačítko **nahrát obrázek**. **Vygenerované miniatury** části zůstane prázdná, dokud testujeme dále v tomto tématu. 

![Aplikace pro nahrávání obrázků](media/storage-upload-process-images/upload-app-nodejs.png)

Ve vzorovém kódu je za nahrání obrázku do kontejneru objektů blob zodpovědná trasa `post`. Tato trasa při zpracování nahrávání využívá následující moduly:

- [multer](https://github.com/expressjs/multer) implementuje strategii odeslání obslužné rutině trasy.
- [do datového proudu](https://github.com/sindresorhus/into-stream) převede vyrovnávací paměti do datového proudu znamének podle [Createblockblobfromlocalfile](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

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

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. V části **služby Blob Service**vyberte **objekty BLOB**, vyberte **imagí** kontejneru.

Zkontrolujte, jestli se obrázek v kontejneru zobrazuje.

![Zobrazení kontejneru obrázků](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test zobrazení miniatury

K otestování zobrazení miniatury nahrajete obrázek, který se **miniatury** kontejneru ke kontrole, jestli aplikace můžou číst **miniatury** kontejneru.

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. V části **služby Blob Service**vyberte **objekty BLOB**a pak **miniatury** kontejneru. Výběrem položky **Nahrát** otevřete podokno **Nahrát objekt blob**.

Pomocí nástroje pro výběr souborů vyberte soubor a vyberte **nahrát**.

Vraťte se do své aplikace a zkontrolujte, jestli je viditelný obrázek nahraný do kontejneru **thumbnails**.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Ve druhé části série můžete automatizovat vytváření obrázků miniatur, takže není nutné tuto bitovou kopii. V kontejneru **thumbnails** na webu Azure Portal vyberte obrázek, který jste nahráli, a výběrem možnosti **Odstranit** ho odstraňte. 

Můžete povolit CDN k ukládání obsahu do mezipaměti z vašeho účtu úložiště Azure. Další informace o tom, jak povolit CDN pomocí svého účtu Azure storage najdete v tématu [integrace účtu služby Azure storage s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Další postup

V první části této série jste zjistili, jak nakonfigurovat webovou aplikaci pro interakci s úložištěm.

Přejděte k druhé části se dozvíte, jak pomocí Event gridu aktivovat funkci Azure pro změnu velikosti obrázku.

> [!div class="nextstepaction"]
> [Aktivace funkce Azure pro změnu velikosti obrázku pomocí Event Gridu](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
