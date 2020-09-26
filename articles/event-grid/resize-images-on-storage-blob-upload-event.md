---
title: 'Kurz: použití Azure Event Grid k automatizaci změny velikosti nahraných obrázků'
description: 'Kurz: Azure Event Grid se můžou aktivovat při nahrávání objektů BLOB v Azure Storage. Díky tomu můžete odesílat soubory obrázků nahrané do Azure Storage do jiných služeb, třeba Azure Functions, ke změně velikosti a dalším vylepšením.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: 47ac8cad6d7c2ead8d25aa3525aafdab735f9e71
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326554"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Kurz: automatizace změny velikosti nahraných imagí pomocí Event Grid

[Azure Event Grid](overview.md) je služba Eventing Service pro Cloud. Event Grid umožňuje vytvářet odběry událostí vyvolaných službami Azure nebo prostředky třetích stran.  

Tento kurz je druhou částí série kurzů o službě Storage. Navazuje na [předchozí kurz o službě Storage][previous-tutorial] a přidává automatické vytváření miniatur bez serveru s využitím služeb Azure Event Grid a Azure Functions. Event Grid umožňuje službě [Azure Functions](../azure-functions/functions-overview.md) reagovat na události služby [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) a vytvářet miniatury nahraných obrázků. K události vytvoření ve službě Blob Storage se vytvoří odběr události. Při přidání objektu blob do určitého kontejneru služby Blob Storage dojde k volání koncového bodu funkce. K přístupu k objektu blob se použití data předaná do vazby funkce ze služby Event Grid a vygeneruje se obrázek miniatury.

Funkce změny velikosti se do existující aplikace pro nahrávání obrázků přidává pomocí rozhraní Azure CLI a webu Azure Portal.

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

![Snímek obrazovky, který zobrazuje publikovanou webovou aplikaci v prohlížeči pro \. sadu NET V12 SDK.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[ SADANode.js V10 ZA ÚČELEM SDK](#tab/nodejsv10)

![Snímek obrazovky, který zobrazuje publikovanou webovou aplikaci v prohlížeči pro \. sadu NET v10 za účelem SDK.](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu Azure Storage
> * Nasadit kód bez serveru pomocí služby Azure Functions
> * Vytvořit odběr události služby Blob Storage ve službě Event Grid

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro absolvování tohoto kurzu potřebujete:

Nejdřív je potřeba dokončit předchozí kurz o službě Blob Storage: [Odeslání dat obrázků do cloudu v Azure Storage][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.14 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Pokud nepoužíváte cloudové prostředí, musíte se nejdřív přihlásit pomocí `az login`.

Pokud jste ve svém předplatném ještě nezaregistrovali poskytovatele prostředků Event Grid, zaregistrujte ho.

```bash
az provider register --namespace Microsoft.EventGrid
```

```powershell
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu Azure Storage

Azure Functions vyžaduje obecný účet úložiště. Kromě účtu BLOB Storage, který jste vytvořili v předchozím kurzu, vytvořte ve skupině prostředků samostatný obecný účet úložiště pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account) . Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.

1. Nastavte proměnnou tak, aby obsahovala název skupiny prostředků, kterou jste vytvořili v předchozím kurzu.

    ```bash
    resourceGroupName="myResourceGroup"
    ```

    ```powershell
    $resourceGroupName="myResourceGroup"
    ```

1. Nastavte proměnnou tak, aby obsahovala umístění pro prostředky, které se mají vytvořit. 

    ```bash
    location="eastus"
    ```

    ```powershell
    $location="eastus"
    ```

1. Nastavte proměnnou pro název nového účtu úložiště, který Azure Functions vyžaduje.

    ```bash
    functionstorage="<name of the storage account to be used by the function>"
    ```

    ```powershell
    $functionstorage="<name of the storage account to be used by the function>"
    ```

1. Vytvořte účet úložiště pro funkci Azure Functions.

    ```bash
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

    ```powershell
    az storage account create --name $functionstorage --location $location `
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí  

K hostování provádění funkcí potřebujete aplikaci Function App. Function App poskytuje prostředí pro provádění kódu funkce bez serveru. Aplikaci Function App vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp).

V následujícím příkazu zadejte vlastní jedinečný název aplikace Function App. Název aplikace funkcí se použije jako výchozí doména DNS pro příslušnou aplikaci funkcí, a proto musí být název mezi všemi aplikacemi v Azure jedinečný.

1. Zadejte název aplikace Function App, která se má vytvořit.

    ```bash
    functionapp="<name of the function app>"
    ```

    ```powershell
    $functionapp="<name of the function app>"
    ```

1. Vytvořte funkci Azure Functions.

    ```bash
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

    ```powershell
    az functionapp create --name $functionapp --storage-account $functionstorage `
      --resource-group $resourceGroupName --consumption-plan-location $location `
      --functions-version 2
    ```

Teď nakonfigurujte aplikaci Function App, aby se připojovala k účtu úložiště objektů blob, který jste vytvořili v [předchozím kurzu][previous-tutorial].

## <a name="configure-the-function-app"></a>Konfigurace aplikace Function App

Tato funkce potřebuje přihlašovací údaje pro účet úložiště objektů blob, které se přidají do nastavení aplikace Function App pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

```bash
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```powershell
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails `
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[ SADANode.js V10 ZA ÚČELEM SDK](#tab/nodejsv10)

```bash
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName `
  -n $blobStorageAccount --query [0].value --output tsv)

$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails `
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey `
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

Nastavení `FUNCTIONS_EXTENSION_VERSION=~2` způsobí, že bude aplikace funkcí používat verzi 2.x modulu runtime služby Azure Functions.

Teď můžete nasadit do této aplikace Function App nasadit projekt projektu kódu funkce.

## <a name="deploy-the-function-code"></a>Nasazení kódu funkce 

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

Ukázková funkce změny velikosti v C# je k dispozici na [GitHubu](https://github.com/Azure-Samples/function-image-upload-resize). Pomocí příkazu [AZ functionapp Deployment source config](/cli/azure/functionapp/deployment/source) nasaďte tento projekt kódu do aplikace Function App.

```bash
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

```powershell
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[ SADANode.js V10 ZA ÚČELEM SDK](#tab/nodejsv10)

Ukázková funkce změny velikosti v Node.js je k dispozici na [GitHubu](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Pomocí příkazu [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) nasaďte tento projekt kódu funkce do aplikace Function App.

```bash
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

```powershell
az functionapp deployment source config --name $functionapp `
  --resource-group $resourceGroupName --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

Funkce změny velikosti obrázků se aktivuje požadavky HTTP, které se do ní odesílají ze služby Event Grid. Službě Event Grid můžete sdělit, že chcete přijímat tato oznámení na adrese URL vaší funkce, vytvořením odběru událostí. Pro účely tohoto kurzu se přihlásíte k odběru událostí vytvářených objekty blob.

Data předávaná do funkce z oznámení služby Event Grid zahrnují adresu URL objektu blob. Tato adresa URL se pak předá do vstupní vazby za účelem získání nahraného obrázku ze služby Blob Storage. Funkce vygeneruje obrázek miniatury a zapíše výsledný datový proud do samostatného kontejneru ve službě Blob Storage.

Tento projekt používá aktivační události typu `EventGridTrigger`. Použití aktivační události Event Grid je vhodnější než obecné aktivační události HTTP. Event Grid automaticky ověřuje aktivační události funkcí Event Grid. U obecných aktivačních událostí HTTP je potřeba implementovat [odpověď ověření](security-authentication.md).

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

Další informace o této funkci najdete v [souborech function.json a run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[ SADANode.js V10 ZA ÚČELEM SDK](#tab/nodejsv10)

Další informace o této funkci najdete v [ souborechfunction.jszapnuto a index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Kód projektu funkce se nasadí přímo z veřejného úložiště ukázek. Další informace o možnostech nasazení pro Azure Functions najdete v článku[Průběžné nasazování se službou Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Vytvoření odběru událostí

Odběr událostí udává, které události vygenerované zprostředkovatelem chcete odeslat do určitého koncového bodu. V tomto případě zveřejňuje koncový bod vaše funkce. Pomocí následujících kroků vytvořte na webu Azure Portal odběr událostí, který odesílá oznámení do vaší funkce:

1. V [Azure Portal](https://portal.azure.com)v horní části hledání stránky a vyberte `Function App` a zvolte aplikaci funkcí, kterou jste právě vytvořili. Vyberte **funkce** a zvolte funkci **Miniatura** .

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="Volba funkce miniatury na portálu":::

1.  Vyberte vybrat **integraci** a pak zvolte **aktivační událost Event Grid** a vyberte **vytvořit Event Grid předplatné**.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Přejděte k části Přidání předplatného Event Grid v Azure Portal" :::

1. Použijte nastavení odběru událostí uvedená v tabulce.
    
    ![Vytvoření odběru událostí z funkce na webu Azure Portal](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Název** | imageresizersub | Název identifikující nový odběr událostí. |
    | **Typ tématu** | Účty úložiště | Vyberte zprostředkovatele událostí Účty úložiště. |
    | **Předplatné** | Vaše předplatné Azure | Ve výchozím nastavení je vybrané vaše aktuální předplatné Azure. |
    | **Skupina prostředků** | myResourceGroup | Vyberte **Použít existující** a zvolte skupinu prostředků, které jste už používali v tomto kurzu. |
    | **Prostředek** | Váš účet služby Blob Storage | Vyberte účet služby Blob Storage, který jste vytvořili. |
    | **Název systémového tématu** | imagestoragesystopic | Zadejte název systémového tématu. Další informace o systémových tématech najdete v tématu [Přehled systémových témat](system-topics.md). |    
    | **Typy událostí** | Vytvoření objektu blob | Zrušte zaškrtnutí všech typů komě **Vytvoření objektu blob**. Do funkce se předají jenom události typu `Microsoft.Storage.BlobCreated`. |
    | **Typ koncového bodu** | automaticky generovaný | Předem definováno jako **funkce Azure Functions**. |
    | **Koncový bod** | automaticky generovaný | Název funkce V tomto případě je to **Miniatura**. |

1. Přepněte na kartu **filtry** a proveďte následující akce:
    1. Vyberte možnost **Povolit filtrování subjektu** .
    1. Pro **předmět začíná**na zadejte následující hodnotu: **/blobServices/default/Containers/images/BLOBs/**.

        ![Zadat filtr pro odběr události](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. Vyberte **vytvořit** a přidejte odběr událostí. Tím se vytvoří odběr události, který aktivuje `Thumbnail` funkci při přidání objektu blob do `images` kontejneru. Funkce změní velikost obrázků a přidá je do `thumbnails` kontejneru.

Teď máte nakonfigurované back-endové služby a můžete funkci změny velikosti obrázků otestovat v ukázkové webové aplikaci.

## <a name="test-the-sample-app"></a>Testování ukázkové aplikace

Pokud chcete ve webové aplikaci otestovat změnu velikosti obrázků, přejděte na adresu URL publikované aplikace. Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

Klikněte na oblast **Nahrát fotografie** a vyberte a nahrajte soubor. Do této oblasti také můžete fotografii přetáhnout.

Všimněte si, že po ukončení nahraného obrázku se v karuselu **vygenerované miniatury** zobrazí kopie nahraného obrázku. Funkce změnila velikost tohoto obrázku, přidala miniaturu do kontejneru *thumbnails* a webový klient ji stáhl.

![Snímek obrazovky, který zobrazuje publikovanou webovou aplikaci s názvem "ImageResizer" v prohlížeči pro \. sadu NET V12 SDK.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[ SADANode.js V10 ZA ÚČELEM SDK](#tab/nodejsv10)

Klikněte na **zvolit soubor** a vyberte soubor a pak klikněte na **Odeslat obrázek**. Po úspěšném nahrání se v prohlížeči přejde na stránku úspěchu. Klikněte na odkaz a vraťte se na domovskou stránku. V oblasti **vygenerované miniatury** se zobrazí kopie nahraného obrázku. (Pokud se obrázek nezobrazuje napřed, zkuste stránku znovu načíst.) Velikost tohoto obrázku byla změněna funkcí, přidána do kontejneru *miniatur* a stažena webovým klientem.

![Publikovaná webová aplikace v prohlížeči](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit obecný účet služby Azure Storage
> * Nasadit kód bez serveru pomocí služby Azure Functions
> * Vytvořit odběr události služby Blob Storage ve službě Event Grid

Ve třetí části série kurzů o službě Storage se dozvíte, jak zabezpečit přístup k účtu úložiště.

> [!div class="nextstepaction"]
> [Zabezpečený přístup k datům aplikací v cloudu](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Další informace o službě Event Grid najdete v článku [Úvod do služby Azure Event Grid](overview.md).
+ Pokud chcete vyzkoušet jiný kurz, který se týká služby Azure Functions, podívejte se na článek [Vytvoření funkce pro integraci s Azure Logic Apps](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
