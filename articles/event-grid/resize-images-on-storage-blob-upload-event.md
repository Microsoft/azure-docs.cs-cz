---
title: Automatizace změny velikosti nahraných obrázků pomocí Azure Event Gridu | Microsoft Docs
description: Při každém nahrání objektu blob v Azure Storage se může aktivovat Azure Event Grid. Díky tomu můžete odesílat soubory obrázků nahrané do Azure Storage do jiných služeb, třeba Azure Functions, ke změně velikosti a dalším vylepšením.
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: e19d8b1b6eb06f78908238969a4f6e90e42bb564
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55301454"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Kurz: Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid

[Azure Event Grid](overview.md) je služba zpracování událostí pro cloud. Event Grid umožňuje vytvářet odběry událostí vyvolaných službami Azure nebo prostředky třetích stran.  

Tento kurz je druhou částí série kurzů o službě Storage. Navazuje na [předchozí kurz o službě Storage][previous-tutorial] a přidává automatické vytváření miniatur bez serveru s využitím služeb Azure Event Grid a Azure Functions. Event Grid umožňuje službě [Azure Functions](../azure-functions/functions-overview.md) reagovat na události služby [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) a vytvářet miniatury nahraných obrázků. K události vytvoření ve službě Blob Storage se vytvoří odběr události. Při přidání objektu blob do určitého kontejneru služby Blob Storage dojde k volání koncového bodu funkce. K přístupu k objektu blob se použití data předaná do vazby funkce ze služby Event Grid a vygeneruje se obrázek miniatury.

Funkce změny velikosti se do existující aplikace pro nahrávání obrázků přidává pomocí rozhraní Azure CLI a webu Azure Portal.

![Publikovaná webová aplikace v prohlížeči Microsoft Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit obecný účet služby Azure Storage
> * Nasadit kód bez serveru pomocí služby Azure Functions
> * Vytvořit odběr události služby Blob Storage ve službě Event Grid

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

Je nutné dokončit předchozí kurz o službě Blob storage: [Odeslání dat obrázků do cloudu pomocí služby Azure Storage][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pokud jste ve svém předplatném ještě nezaregistrovali poskytovatele prostředků Event Grid, zaregistrujte ho.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.14 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

Pokud nepoužíváte cloudové prostředí, musíte se nejdřív přihlásit pomocí `az login`.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Azure Functions vyžaduje obecný účet úložiště. V rámci skupiny prostředků vytvořte samostatný obecný účet úložiště pomocí příkazu [az storage account create](/cli/azure/storage/account#az-storage-account-create).

Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. 

V následujícím příkazu nahraďte zástupný symbol `<general_storage_account>` vlastním globálně jedinečným názvem obecného účtu úložiště. 

1. Nastavte proměnnou, která bude uchovávat název skupiny prostředků, kterou jste vytvořili v předchozím kurzu. 

    ```azurecli-interactive
    resourceGroupName=<Name of the resource group that you created in the previous tutorial>
    ```
2. Nastavte proměnnou pro název účtu úložiště, který vyžaduje funkci Azure. 

    ```azurecli-interactive
    functionstorage=<name of the storage account to be used by function>
    ```
3. Vytvořte účet úložiště pro funkce Azure functions. Tím se liší od úložiště, který obsahuje Image. 

    ```azurecli-interactive
    az storage account create --name $functionstorage --location eastus --resource-group $resourceGroupName --sku Standard_LRS --kind storage
    ```

## <a name="create-a-function-app"></a>Vytvoření Function App  

K hostování provádění funkcí potřebujete aplikaci Function App. Function App poskytuje prostředí pro provádění kódu funkce bez serveru. Aplikaci Function App vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

V následujícím příkazu nahraďte zástupný symbol `<function_app>` vlastním jedinečným názvem aplikace Function App. Název aplikace funkcí se použije jako výchozí doména DNS pro příslušnou aplikaci funkcí, a proto musí být název mezi všemi aplikacemi v Azure jedinečný. Hodnotu `<general_storage_account>` nahraďte názvem obecného účtu úložiště, který jste vytvořili.

1. Zadejte název pro aplikaci function app, který se má vytvořit. 

    ```azurecli-interactive
    functionapp=<name of the function app>
    ```
2. Vytvoření funkce Azure functions. 

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account  $functionstorage --resource-group $resourceGroupName --consumption-plan-location eastus
    ```

Teď je potřeba nakonfigurovat připojení aplikace funkcí k účtu služby Blob Storage, který jste vytvořili v [předchozím kurzu][previous-tutorial].

## <a name="configure-the-function-app"></a>Konfigurace aplikace Function App

Funkce potřebuje k připojení k účtu služby Blob Storage připojovací řetězec. Kód funkce, který v následujícím kroku nasadíte do Azure, hledá připojovací řetězec v nastavení aplikace myblobstorage_STORAGE a název kontejneru obrázků miniatur v nastavení aplikace myContainerName. Připojovací řetězec zobrazíte pomocí příkazu [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Pomocí příkazu [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) nastavte nastavení aplikace.

V následujících příkazech rozhraní příkazového řádku je `<blob_storage_account>` název účtu služby Blob Storage, který jste vytvořili v předchozím kurzu.

1. Získá připojovací řetězec pro účet úložiště, který obsahuje Image. 

    ```azurecli-interactive
    storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)
    ```
2. Konfigurace aplikace function app. 

    ```azurecli-interactive
    az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
    ```

    Nastavení `FUNCTIONS_EXTENSION_VERSION=~2` způsobí, že bude aplikace funkcí používat verzi 2.x modulu runtime služby Azure Functions.

Teď můžete nasadit do této aplikace Function App nasadit projekt projektu kódu funkce.

## <a name="deploy-the-function-code"></a>Nasazení kódu funkce 

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Ukázkový skript jazyka C# (.csx) pro změnu velikosti je k dispozici na [GitHubu](https://github.com/Azure-Samples/function-image-upload-resize). Pomocí příkazu [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) nasaďte tento projekt kódu funkce do aplikace Function App. 

V následujícím příkazu je `<function_app>` název aplikace funkcí, kterou jste vytvořili dříve.

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName --branch master --manual-integration --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
Ukázková funkce změny velikosti v Node.js je k dispozici na [GitHubu](https://github.com/Azure-Samples/storage-blob-resize-function-node). Pomocí příkazu [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) nasaďte tento projekt kódu funkce do aplikace Function App.

V následujícím příkazu je `<function_app>` název aplikace funkcí, kterou jste vytvořili dříve.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```
---

Funkce změny velikosti obrázků se aktivuje požadavky HTTP, které se do ní odesílají ze služby Event Grid. Službě Event Grid můžete sdělit, že chcete přijímat tato oznámení na adrese URL vaší funkce, vytvořením odběru událostí. Pro účely tohoto kurzu se přihlásíte k odběru událostí vytvářených objekty blob.

Data předávaná do funkce z oznámení služby Event Grid zahrnují adresu URL objektu blob. Tato adresa URL se pak předá do vstupní vazby za účelem získání nahraného obrázku ze služby Blob Storage. Funkce vygeneruje obrázek miniatury a zapíše výsledný datový proud do samostatného kontejneru ve službě Blob Storage. 

Tento projekt používá aktivační události typu `EventGridTrigger`. Použití aktivační události Event Grid je vhodnější než obecné aktivační události HTTP. Event Grid automaticky ověřuje aktivační události funkcí Event Grid. U obecných aktivačních událostí HTTP je potřeba implementovat [odpověď ověření](security-authentication.md#webhook-event-delivery).

Další informace o této funkci najdete v [souborech function.json a run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/imageresizerfunc).
 
Kód projektu funkce se nasadí přímo z veřejného úložiště ukázek. Další informace o možnostech nasazení pro Azure Functions najdete v článku[Průběžné nasazování se službou Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Vytvoření odběru událostí

Odběr událostí udává, které události vygenerované zprostředkovatelem chcete odeslat do určitého koncového bodu. V tomto případě zveřejňuje koncový bod vaše funkce. Pomocí následujících kroků vytvořte na webu Azure Portal odběr událostí, který odesílá oznámení do vaší funkce: 

1. V [webu Azure portal](https://portal.azure.com)vyberte **všechny služby** v nabídce vlevo a pak vyberte **aplikace Function App**. 

    ![Přechod na aplikace Function App na webu Azure Portal](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Rozbalte aplikaci function app, vyberte **miniaturu** pracovat a pak vyberte **přidat předplatné Event gridu**.

    ![Přechod na aplikace Function App na webu Azure Portal](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Použijte nastavení odběru událostí uvedená v tabulce.
    
    ![Vytvoření odběru událostí z funkce na webu Azure Portal](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | imageresizersub | Název identifikující nový odběr událostí. | 
    | **Typ tématu** |  Účty úložiště | Vyberte zprostředkovatele událostí Účty úložiště. | 
    | **Předplatné** | Vaše předplatné Azure | Ve výchozím nastavení je vybrané vaše aktuální předplatné Azure.   |
    | **Skupina prostředků** | myResourceGroup | Vyberte **Použít existující** a zvolte skupinu prostředků, které jste už používali v tomto kurzu.  |
    | **Prostředek** |  Váš účet služby Blob Storage |  Vyberte účet služby Blob Storage, který jste vytvořili. |
    | **Typy událostí** | Vytvoření objektu blob | Zrušte zaškrtnutí všech typů komě **Vytvoření objektu blob**. Do funkce se předají jenom události typu `Microsoft.Storage.BlobCreated`.| 
    | **Typ odběratele** |  automaticky generovaný |  Předdefinovaný jako webhook. |
    | **Koncový bod odběratele** | automaticky generovaný | Použijte adresu URL koncového bodu, která se vygeneruje. | 
4. *Volitelné:* V případě, že je potřeba vytvořit další kontejnery ve stejném úložišti objektů blob pro jiné účely v budoucnu, můžete použít **na základě práv subjektů filtrování** funkce v **filtry** kartu pro podrobnější cílení na objektu blob události, aby vaše aplikace function app je volána, pouze pokud objekty BLOB jsou přidány do **imagí** kontejneru zvlášť. 
5. Přidejte odběr událostí kliknutím na **Vytvořit**. Vytvoří se odběr událostí, která aktivuje `Thumbnail` fungovat v případě, že objekt blob se přidá do *imagí* kontejneru. Tato funkce změní velikost obrázků a přidá je do kontejneru *thumbnails*.

Teď máte nakonfigurované back-endové služby a můžete funkci změny velikosti obrázků otestovat v ukázkové webové aplikaci. 

## <a name="test-the-sample-app"></a>Testování ukázkové aplikace

Pokud chcete ve webové aplikaci otestovat změnu velikosti obrázků, přejděte na adresu URL publikované aplikace. Výchozí adresa URL webové aplikace je `https://<web_app>.azurewebsites.net`.

Klikněte na oblast **Nahrát fotografie** a vyberte a nahrajte soubor. Do této oblasti také můžete fotografii přetáhnout. 

Všimněte si, že po zmizení nahraného obrázku se na karuselu **Vygenerované miniatury** zobrazí kopie nahraného obrázku. Funkce změnila velikost tohoto obrázku, přidala miniaturu do kontejneru *thumbnails* a webový klient ji stáhl.

![Publikovaná webová aplikace v prohlížeči Microsoft Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Další postup

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
