---
title: Odeslání dat obrázků do cloudu v Azure Storage | Microsoft Docs
description: Ukládání dat aplikací pomocí azure blob úložiště s webovou aplikací
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: e2ee959fb6fc7e8454919c71cfa20e2bb9055dfb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393846"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Kurz: Nahrávání obrazových dat v cloudu pomocí Azure Storage

Tento kurz je první částí série. V tomto kurzu se dozvíte, jak nasadit webovou aplikaci, která používá klientskou knihovnu úložiště objektů Blob Azure k nahrání ibi do účtu úložiště. Až skončíte, budete mít webovou aplikaci, která ukládá a zobrazuje image z úložiště Azure.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![Aplikace pro rezidui obrázků v rozhraní .NET](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![Aplikace pro rozprašovač obrázků v souboru Node.js V10](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

V první části tohoto kurzu se naučíte:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Vytvoření kontejneru a nastavení oprávnění
> * Načtení přístupového klíče
> * Nasazení webové aplikace do Azure
> * Konfigurace nastavení aplikace
> * Interakce s webovou aplikací

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Než začnete, vytvořte si [bezplatný účet.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Chcete-li nainstalovat a používat příkaz příkazového příkazového příkazového uživatele místně, tento kurz vyžaduje spuštění azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Ukázka nahraje image do kontejneru objektů blob v účtu úložiště Azure. Účet služby Storage poskytuje jedinečný obor názvů pro ukládání datových objektů Azure Storage a přístup k nim. Ve skupině prostředků, kterou jste vytvořili vytvořte účet úložiště pomocí příkazu [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> V části 2 kurzu použijete Azure Event Grid s úložištěm objektů blob. Nezapomeňte si vytvořit účet úložiště v oblasti Azure, která podporuje Event Grid. Seznam podporovaných oblastí najdete v tématu [Produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

V následujícím příkazu nahraďte vlastní globálně jedinečný název pro účet `<blob_storage_account>` úložiště objektů Blob, kde se zobrazí zástupný symbol.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Vytvoření kontejnerů úložiště objektů Blob

Aplikace používá v účtu služby Blob Storage dva kontejnery. Kontejnery jsou podobné složky a objekty BLOB úložiště. Aplikace nahrává obrázky v plném rozlišení do kontejneru *images*. V pozdější části série aplikace funkcí Azure nahraje miniatury obrázků se změněnou velikostí do kontejneru *thumbnails*.

Pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys) zjistěte klíč účtu úložiště. Potom použijte tento klíč k vytvoření dvou kontejnerů s příkazem [vytvořit kontejner az úložiště.](/cli/azure/storage/container)

Veřejný přístup kontejneru *bitových* `off`kopií je nastaven na . Veřejný přístup *kontejneru miniatur* je `container`nastaven na . Nastavení `container` veřejného přístupu umožňuje uživatelům, kteří navštíví webovou stránku, zobrazit miniatury.

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

Poznamenejte si název účtu úložiště objektů Blob a klíč. Ukázková aplikace používá tato nastavení k připojení k účtu úložiště k nahrání obrázků. 

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[Plán služby App Service](../../app-service/overview-hosting-plans.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace.

Pomocí příkazu [az appservice plan create](/cli/azure/appservice/plan) vytvořte plán služby App Service.

Následující příklad vytvoří plán služby App Service s názvem `myAppServicePlan` v cenové úrovni **Free**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Webová aplikace poskytuje hostitelské místo pro ukázkový kód aplikace, který se nasadí z ukázkového úložiště GitHubu. Pomocí příkazu [az webapp create](/cli/azure/webapp) vytvořte [webovou aplikaci](../../app-service/overview.md) v plánu služby App Service `myAppServicePlan`.  

V následujícím příkazu `<web_app>` nahraďte jedinečným názvem. Platné znaky jsou `a-z`, `0-9` a `-`. Pokud není název `<web_app>` jedinečný, zobrazí se chybová zpráva *Web se zadaným názvem `<web_app>` už existuje.* Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Nasazení ukázkové aplikace z úložiště GitHubu

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Ukázkový projekt obsahuje [aplikaci ASP.NET MVC.](https://www.asp.net/mvc) Aplikace přijme obrázek, uloží ho do účtu úložiště a zobrazí obrázky z kontejneru miniatur. Webová aplikace používá k interakci se službou [Azure](/dotnet/api/azure.storage)Storage Storage , [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs)a [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) obory názvů.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>Konfigurace nastavení webové aplikace

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Ukázková webová aplikace používá [rozhraní API úložiště Azure pro rozhraní .NET](/dotnet/api/overview/azure/storage) k nahrávání bitových kopií. Přihlašovací údaje účtu úložiště se nastavují v nastavení aplikace pro webovou aplikaci. Přidejte nastavení aplikace do nasazené aplikace pomocí příkazu [az webapp config appsettings set.](/cli/azure/webapp/config/appsettings)

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

Ukázková webová aplikace zadává požadavek na přístupové tokeny, které slouží k nahrávání obrázků, pomocí [klientské knihovny Azure Storage](https://github.com/Azure/azure-storage-js). Přihlašovací údaje účtu úložiště používané sadou Storage SDK jsou nastaveny v nastavení aplikace pro webovou aplikaci. Přidejte nastavení aplikace do nasazené aplikace pomocí příkazu [az webapp config appsettings set.](/cli/azure/webapp/config/appsettings)

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Po nasazení a konfiguraci webové aplikace můžete otestovat funkci nahrávání obrázků v aplikaci.

## <a name="upload-an-image"></a>Nahrání image

Pokud chcete otestovat webovou aplikaci, přejděte na adresu URL publikované aplikace. Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Vyberte oblast **Nahrát fotografie,** chcete-li určit a nahrát soubor, nebo přetáhněte soubor do oblasti. Obrázek po úspěšném nahrání zmizí. Část **Generované miniatury** zůstane prázdná, dokud ji neotestujeme později v tomto tématu.

![Nahrání fotografií v rozhraní .NET](media/storage-upload-process-images/figure1.png)

V ukázkovém `UploadFileToStorage` kódu se úloha v *souboru Storagehelper.cs* používá k nahrání bitových kopií do kontejneru *obrázků* v rámci účtu úložiště pomocí metody [UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) Úlohu `UploadFileToStorage` obsahuje následující vzorek kódu.

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
| [Uri](/dotnet/api/system.uri) | [Konstruktor Uri](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [Konstruktor StorageSharedKeyCredential(Řetězec, Řetězec)](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [Klient objektů Blob](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

Vyberte soubor, ve které chcete **vybrat soubor,** a pak klepněte na **Nahrát obrázek**. Část **Generované miniatury** zůstane prázdná, dokud ji neotestujeme později v tomto tématu. 

![Nahrání fotografií v souboru Node.js V10](media/storage-upload-process-images/upload-app-nodejs.png)

Ve vzorovém kódu je za nahrání obrázku do kontejneru objektů blob zodpovědná trasa `post`. Tato trasa při zpracování nahrávání využívá následující moduly:

- [multer](https://github.com/expressjs/multer) implementuje strategii nahrávání pro obslužnou rutinu trasy.
- [into-stream](https://github.com/sindresorhus/into-stream) převede vyrovnávací paměť na datový proud podle požadavků [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

Jako soubor je odeslán a trasa, obsah souboru zůstat v paměti, dokud soubor je odeslán do kontejneru objektů blob.

> [!IMPORTANT]
> Načítání velkých souborů do paměti může mít negativní vliv na výkon webové aplikace. Pokud očekáváte, že uživatelé budou účtovat velké soubory, můžete zvážit pracovní soubory v systému souborů webového serveru a potom naplánovat nahrávání do úložiště objektů Blob. Jakmile jsou soubory v úložišti objektů Blob, můžete je odebrat ze systému souborů serveru.

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

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. Vyberte **Kontejnery**a pak vyberte kontejner **obrazů.**

Zkontrolujte, jestli se obrázek v kontejneru zobrazuje.

![Seznam kontejneru s ibi na webu Azure Portal](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test zobrazení miniatury

Chcete-li otestovat zobrazení miniatur, nahrajete obrázek do kontejneru **miniatur** a zkontrolujete, zda aplikace dokáže přečíst kontejner **miniatur.**

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. Vyberte **Kontejnery**a pak vyberte kontejner **miniatur.** Výběrem položky **Nahrát** otevřete podokno **Nahrát objekt blob**.

Vyberte soubor s výběrem souboru a vyberte **Nahrát**.

Vraťte se do své aplikace a zkontrolujte, jestli je viditelný obrázek nahraný do kontejneru **thumbnails**.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![Aplikace pro rezizátor obrázků .NET s novým zobrazeným obrazem](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![Aplikace node.js V10 image resizer app s novým zobrazeným obrázkem](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Ve druhé části série automatizujete vytváření miniatur, takže tento obrázek nepotřebujete. V kontejneru **thumbnails** na webu Azure Portal vyberte obrázek, který jste nahráli, a výběrem možnosti **Odstranit** ho odstraňte. 

Síť pro doručování obsahu (CDN) můžete povolit ukládání obsahu do mezipaměti z vašeho účtu úložiště Azure. Další informace o tom, jak povolit CDN s účtem úložiště Azure, najdete [v tématu Integrace účtu úložiště Azure s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Další kroky

V první části řady jste se naučili, jak nakonfigurovat webovou aplikaci pro interakci s úložištěm.

Přejděte na druhou část série a zjistěte, jak pomocí funkce Event Grid aktivovat funkci Azure pro změna velikosti bitové kopie.

> [!div class="nextstepaction"]
> [Aktivace funkce Azure pro změnu velikosti obrázku pomocí Event Gridu](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
