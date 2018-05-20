---
title: Směrovat událostí Azure Blob storage do koncový bod vlastní webové – prostředí Powershell | Microsoft Docs
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 9ea51f6ea55c62fdd01efb155d26fade3941ce41
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Události úložiště objektů Blob trasy pro koncový bod vlastní webové pomocí prostředí PowerShell

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku pomocí prostředí Azure PowerShell přihlásit k odběru událostí úložiště objektů Blob, aktivační události a zobrazit výsledek. 

Obvykle odesíláte události do koncového bodu, který na událost reaguje například webhookem nebo funkcí Azure Functions. Pro zjednodušení z příkladu v tomto článku, jsou události odeslané na adresu URL, jenom shromažďující zprávy. Tuto adresu URL vytvoříte pomocí nástroje třetí strany z webu [Hookbin](https://hookbin.com/).

> [!NOTE]
> **Hookbin** není určen pro použití vysoké propustnosti. Použití tohoto nástroje je čistě demonstrativní. Pokud najednou nabídnete více než jednu událost, možná se v nástroji nezobrazí všechny. Navíc mějte na paměti, **Hookbin** získá [zvláštní zacházení](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-grid#create-a-requestbin-endpoint) pomocí Azure událostí mřížky. Usnadňuje testování mřížky událostí odesílá události existuje bez nutnosti správné odpovědí na požadavky na ověření předplatného (což by mohlo dojít [jinak](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication#validation-details)).

Po dokončení kroků popsaných v tomto článku uvidíte, že se data událostí odeslala do koncového bodu.

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

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Místo psaní kódu, který by na událost reagoval, vytvoříme koncový bod, který bude shromažďovat zprávy, abyste je mohli zobrazit. Hookbin je nástroj třetí strany, který umožňuje vytvořit koncový bod a zobrazit požadavky, které se do něj odesílají. Přejděte na web [Hookbin](https://hookbin.com/) a klikněte na **Create New Endpoint** (Vytvořit nový koncový bod). Zkopírujte adresu URL Koš a nahraďte `<bin URL>` v následující skript.

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>Přihlásit k účtu úložiště

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat. V následujícím příkladu se přihlásí k účtu úložiště, které jste vytvořili a předává adresu URL z Hookbin jako koncový bod pro oznámení o události. 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>Aktivace události ze služby Blob Storage

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Nejdříve vytvoříme kontejner a objekt. Pak můžeme nahrát objekt do kontejneru.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Přejděte na adresu URL koncového bodu, kterou jste vytvořili dříve. Nebo v otevřeném prohlížeči klikněte na tlačítko pro obnovení. Zobrazí se událost, kterou jste právě odeslali. 

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
