---
title: Směrovat událostí Azure Blob storage do koncový bod vlastní webové – prostředí Powershell | Microsoft Docs
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 05/24/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: b6764ffa0e7cfbc888f11c22af855d48d8160372
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650498"
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Události úložiště objektů Blob trasy pro koncový bod vlastní webové pomocí prostředí PowerShell

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku pomocí prostředí Azure PowerShell přihlásit k odběru událostí úložiště objektů Blob, aktivační události a zobrazit výsledek. 

Obvykle odesílat události pro koncový bod, který zpracovává data události a provede akce. Ale zjednodušit v tomto článku, odeslání události do webové aplikace, která shromažďuje a zobrazuje zprávy.

Pokud budete hotovi, uvidíte, že data události byl odeslán do webové aplikace.

![Zobrazit výsledky](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Nastavení

Tento článek vyžaduje použití nejnovější verze Azure PowerShellu. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a při ověřování postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

> [!NOTE]
> Dostupnost pro události úložiště je vázaný na události mřížky [dostupnosti](../../event-grid/overview.md) a bude dostupná v jiných oblastech, stejně jako událost mřížky.

Tento příklad používá **westus2** a ukládá v proměnné pro použití v rámci výběr.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

Následující příklad vytvoří skupinu prostředků **gridResourceGroup** v umístění **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Pokud chcete použít události úložiště objektů Blob, je nutné buď [účtem služby Blob storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) nebo [účtem úložiště pro obecné účely v2](../common/storage-account-options.md#general-purpose-v2). **Obecné účely v2 (GPv2)** jsou účty úložiště, které podporují všechny funkce pro všechny služby úložiště, včetně objektů BLOB, soubory, fronty a tabulky. A **účtem služby Blob storage** je specializovaný účet úložiště pro ukládání nestrukturovaných dat jako blobů (objektů) ve službě Azure Storage. Účty úložiště BLOB jsou podobné účtům úložiště pro obecné účely a sdílejí všechny skvělé odolnost, dostupnost, škálovatelnost a výkon funkce používají. jednotlivá včetně 100 % konzistentnost rozhraní API pro objekty BLOB bloku a doplňovací objekty BLOB. V případě aplikací, které vyžadují jenom úložiště objektů blob bloku nebo objektů blob doporučujeme používat účty úložiště objektů blob.  

Vytvoření účtu úložiště Blob pomocí replikace LRS [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), následně načíst kontext účtu úložiště, který definuje účet úložiště, který se má použít. Když používáte účet úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext. Tento příklad vytvoří účet úložiště s názvem **gridstorage** s místně redundantní úložiště (LRS). 

> [!NOTE]
> Názvy účtů úložiště jsou v oboru globální názvů, takže budete muset připojit k názvu zadanému v tento skript některé náhodných znaků.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Obvykle se koncový bod provede akce podle data události. Pro zjednodušení tento rychlý start, nasazení [předem připravené webové aplikace](https://github.com/dbarkol/azure-event-grid-viewer) který zobrazí zprávy událostí. Nasazené řešení zahrnuje plán služby App Service, webové aplikace App Service a zdrojového kódu z Githubu.

Nahraďte `<your-site-name>` s jedinečným názvem pro vaši webovou aplikaci. Název webové aplikace musí být jedinečný, protože je součástí položky DNS.

```powershell
$sitename="<your-site-name>"

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/dbarkol/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Nasazení může trvat několik minut. Po nasazení proběhla úspěšně, zobrazit vaší webové aplikace na Ujistěte se, zda je spuštěná. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Měli byste vidět web bez zpráv aktuálně zobrazený.

## <a name="subscribe-to-your-storage-account"></a>Přihlásit k účtu úložiště

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat. V následujícím příkladu se přihlásí k účtu úložiště, které jste vytvořili a předá adresu URL z vaší webové aplikace jako koncový bod pro oznámení o události. Koncový bod pro webové aplikace musí obsahovat příponu `/api/updates/`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Znovu zobrazit vaší webové aplikace a Všimněte si, že k němu byl odeslán událost ověření předplatného. Vyberte ikonu oka rozbalte data události. Událost mřížky odešle událost ověření, koncový bod, můžete ověřit, jestli chce přijímat data události. Webová aplikace obsahuje kód pro ověření předplatného.

![Zobrazení odběru událostí](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Aktivace události ze služby Blob Storage

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Nejdříve vytvoříme kontejner a objekt. Pak můžeme nahrát objekt do kontejneru.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Zobrazení vaší webové aplikace na výskyt události, které jste poslali.

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s tímto účtem úložiště a odběru událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

Když teď víte, jak vytvářet témata a odběry událostí, zjistěte více o událostech služby Blob Storage a s čím vám služba Event Grid ještě může pomoct:

- [Reakce na události služby Blob Storage](storage-blob-event-overview.md)
- [Informace o službě Event Grid](../../event-grid/overview.md)
