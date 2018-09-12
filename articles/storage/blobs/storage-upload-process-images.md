---
title: Odeslání dat obrázků do cloudu v Azure Storage | Microsoft Docs
description: Uložení dat webové aplikace pomocí služby Azure Blob Storage
services: storage
documentationcenter: ''
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 2537e7cd6d59dfd38cac6b18009ce7d6a311ed10
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027142"
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Odeslání dat obrázků do cloudu v Azure Storage

Tento kurz je první částí série. V tomto kurzu se dozvíte, jak nasadit webovou aplikaci, která nahrává obrázky do účtu služby Storage pomocí klientské knihovny Azure Storage. Po dokončení budete mít webovou aplikaci, která ukládá a zobrazuje obrázky ze služby Azure Storage.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

V první části této série se naučíte:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Vytvoření kontejneru a nastavení oprávnění
> * Načtení přístupového klíče
> * Konfigurace nastavení aplikace
> * Nasazení webové služby do Azure
> * Provádět interakci s webovou aplikací

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků 

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.
 
Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
 
Ukázka nahrává obrázky do kontejneru objektů blob v účtu služby Azure Storage. Účet služby Storage poskytuje jedinečný obor názvů pro ukládání datových objektů Azure Storage a přístup k nim. Ve skupině prostředků, kterou jste vytvořili vytvořte účet úložiště pomocí příkazu [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

> [!IMPORTANT] 
> Ve druhé části kurzu použijete odběry událostí pro službu Blog Storage. Odběry událostí se v současné době podporují pouze pro účty služby Blob Storage v následujících oblastech: Austrálie – jihovýchod, Austrálie – východ, Evropa – sever, Evropa – západ, Japonsko – východ, Japonsko – západ, Jihovýchodní Asie, USA – střed, USA – středozápad, USA – východ, USA – východ 2, USA – západ, USA – západ 2 a Východní Asie. Z důvodu tohoto omezení je potřeba vytvořit účet služby Blob Storage, který bude ukázková aplikace používat k ukládání obrázků a miniatur.   

V následujícím příkazu nahraďte zástupný symbol `<blob_storage_account>` vlastním globálně jedinečným názvem účtu služby Blob Storage.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Vytvoření kontejnerů služby Blob Storage

Aplikace používá v účtu služby Blob Storage dva kontejnery. Kontejnery jsou podobné složkám a používají se k ukládání objektů blob. Aplikace nahrává obrázky v plném rozlišení do kontejneru _images_. V pozdější části série aplikace funkcí Azure nahraje miniatury obrázků se změněnou velikostí do kontejneru _thumbnails_. 

Pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) zjistěte klíč účtu úložiště. Tento klíč potom použijete k vytvoření dvou kontejnerů pomocí příkazu [az storage container create](/cli/azure/storage/container#az_storage_container_create).  
 
V tomto případě je `<blob_storage_account>` název účtu služby Blob Storage, který jste vytvořili. Veřejný přístup ke kontejneru _images_ je nastavený na hodnotu `off` a veřejný přístup ke kontejneru _thumbnails_ na hodnotu `container`. Nastavení veřejného přístupu `container` umožňuje zobrazení miniatur návštěvníky dané webové stránky.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Název svého účtu služby Blob Storage a klíč si poznamenejte. Ukázková aplikace používá tato nastavení k připojení k účtu úložiště, aby mohla nahrávat obrázky. 

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service 

[Plán služby App Service](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace. 

Pomocí příkazu [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) vytvořte plán služby App Service. 

Následující příklad vytvoří plán služby App Service s názvem `myAppServicePlan` v cenové úrovni **Free**: 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace 

Webová aplikace poskytuje hostitelský prostor pro kód ukázkové aplikace nasazené z úložiště ukázek GitHubu. Pomocí příkazu [az webapp create](/cli/azure/webapp#az_webapp_create) vytvořte [webovou aplikaci](../../app-service/app-service-web-overview.md) v plánu služby App Service `myAppServicePlan`.  
 
V následujícím příkazu nahraďte položku `<web_app>` jedinečným názvem (platné znaky jsou `a-z`, `0-9` a `-`). Pokud není název `<web_app>` jedinečný, zobrazí se chybová zpráva _Web se zadaným názvem `<web_app>` už existuje._ Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Nasazení ukázkové aplikace z úložiště GitHubu

# <a name="nettabnet"></a>[\.NET](#tab/net)

Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Položku `<web_app>` nahraďte názvem webové aplikace, kterou jste vytvořili v předchozím kroku.

Ukázkový projekt obsahuje aplikaci [ASP.NET MVC](https://www.asp.net/mvc), která přijme obrázek, uloží ho do účtu služby Storage a zobrazí obrázky z kontejneru miniatur. Webová aplikace používá k interakci se službou Azure Storage tyto obory názvů z klientské knihovny Azure Storage: [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) a [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet). 

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
Služba App Service podporuje několik způsobů nasazení obsahu do webové aplikace. V tomto kurzu nasadíte webovou aplikaci z [veřejného úložiště ukázek GitHubu](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Položku `<web_app>` nahraďte názvem webové aplikace, kterou jste vytvořili v předchozím kroku.

---

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Konfigurace nastavení webové aplikace 

Ukázková webová aplikace zadává požadavek na přístupové tokeny, které slouží k nahrávání obrázků, pomocí [klientské knihovny Azure Storage](/dotnet/api/overview/azure/storage?view=azure-dotnet). Přihlašovací údaje úložiště pro webovou aplikaci používané sadou Storage SDK se nastavují v nastavení aplikace. Pomocí příkazu [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) přidejte do nasazené aplikace nastavení aplikace. 

V následujícím příkazu je `<blob_storage_account>` název vašeho účtu služby Blob Storage a `<blob_storage_key>` je přidružený klíč. Položku `<web_app>` nahraďte názvem webové aplikace, kterou jste vytvořili v předchozím kroku.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Po nasazení a konfiguraci webové aplikace můžete v aplikaci otestovat funkci nahrávání obrázků.   

## <a name="upload-an-image"></a>Nahrání image 

Pokud chcete otestovat webovou aplikaci, přejděte na adresu URL publikované aplikace. Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`. Vyberte oblast **Nahrát fotografie**, abyste mohli vybrat a nahrát soubor, nebo na tuto oblast přetáhněte požadovaný soubor. Obrázek po úspěšném nahrání zmizí.

# <a name="nettabnet"></a>[\.NET](#tab/net)

![Aplikace ImageResizer](media/storage-upload-process-images/figure1.png)

Ve vzorku kódu úloha `UploadFiletoStorage` v souboru `Storagehelper.cs` slouží k nahrání obrázku do kontejneru `images` v rámci daného účtu úložiště pomocí metody [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). Úlohu `UploadFiletoStorage` obsahuje následující vzorek kódu. 

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
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

![Aplikace pro nahrávání obrázků](media/storage-upload-process-images/upload-app-nodejs.png)

Ve vzorovém kódu je za nahrání obrázku do kontejneru objektů blob zodpovědná trasa `post`. Tato trasa při zpracování nahrávání využívá následující moduly:

- [multer](https://github.com/expressjs/multer) implementuje strategii nahrávání pro obslužnou rutinu trasy.
- [into-stream](https://github.com/sindresorhus/into-stream) převádí vyrovnávací paměť na datový proud, jak to vyžaduje [createBlockBlobFromStream](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream).

Když se soubor odešle do trasy, obsah souboru zůstane v paměti, dokud se soubor nenahraje do kontejneru objektů blob.

> [!IMPORTANT]
> Načítání velmi velkých souborů do paměti může mít negativní vliv na výkon webové aplikace. Pokud očekáváte, že uživatelé budou odesílat velké soubory, pravděpodobně byste měli zvážit přípravu souborů v systému souborů webového serveru a následné naplánování jejich nahrání do úložiště objektů blob. Jakmile budou soubory v úložišti objektů blob, můžete je odebrat ze systému souborů serveru.

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
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Kontrola zobrazení obrázku v účtu úložiště

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. V části **Přehled** vyberte kontejner **images**.

Zkontrolujte, jestli se obrázek v kontejneru zobrazuje.

![Zobrazení kontejneru obrázků](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test zobrazení miniatury

K otestování zobrazení miniatury nahrajete obrázek do kontejneru miniatur, abyste mohli zkontrolovat, jestli aplikace umí kontejner miniatur přečíst.

Přihlaste se k webu [Azure Portal](https://portal.azure.com). V nabídce vlevo vyberte **Účty úložiště** a potom vyberte název svého účtu úložiště. Vyberte **Kontejnery** v části **Blob Service** a pak vyberte kontejner **thumbnails**. Výběrem položky **Nahrát** otevřete podokno **Nahrát objekt blob**.

Vyberte soubor pomocí nástroje pro výběr souborů a vyberte položku **Nahrát**.

Vraťte se do své aplikace a zkontrolujte, jestli je viditelný obrázek nahraný do kontejneru **thumbnails**.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Zobrazení kontejneru obrázků](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

V kontejneru **thumbnails** na webu Azure Portal vyberte obrázek, který jste nahráli, a výběrem možnosti **Odstranit** ho odstraňte. Ve druhé části kurzu nastavíte automatizované vytváření obrázků miniatur, takže už tento testovací obrázek nebudete potřebovat.

Síť CDN se dá nastavit tak, aby ukládala obsah vašeho účtu služby Azure Storage do mezipaměti. Tento kurz se tím nezabývá, ale pokud se chcete dozvědět, jak ve svém účtu služby Azure Storage povolit síť CDN, navštivte stránku [Integrace účtu služby Azure Storage se sítí Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Další kroky

V první části kurzu jste se dozvěděli o konfiguraci webové aplikace pro interakci s úložištěm, třeba o těchto krocích:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Vytvoření kontejneru a nastavení oprávnění
> * Načtení přístupového klíče
> * Konfigurace nastavení aplikace
> * Nasazení webové služby do Azure
> * Interakce s webovou aplikací

Ve druhé části kurzu se dozvíte, jak pomocí Event Gridu aktivovat funkci Azure pro změnu velikosti obrázku.

> [!div class="nextstepaction"]
> [Aktivace funkce Azure pro změnu velikosti obrázku pomocí Event Gridu](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
