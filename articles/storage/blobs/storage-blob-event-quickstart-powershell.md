---
title: Odesílání událostí služby Azure Blob storage na web koncového bodu – Powershell | Dokumentace Microsoftu
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
services: storage,event-grid
author: david-stanford
ms.author: dastanfo
ms.date: 08/23/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.custom: seodec18
ms.openlocfilehash: 3e1a482cc59d951fbe6b25fcf0bfc4a967222839
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246411"
---
# <a name="quickstart-route-storage-events-to-web-endpoint-with-powershell"></a>Rychlý start: Směrování událostí služby storage do webového koncového bodu pomocí Powershellu

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku pomocí Azure Powershellu k odběru událostí služby Blob storage, aktivační událost a viděli výsledek. 

Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto článku však budete události odesílat do webové aplikace, která shromažďuje a zobrazuje zprávy.

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

![Zobrazení výsledků](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Nastavení

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vyžaduje, že používáte nejnovější verzi Azure Powershellu. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkaz a postupujte podle pokynů na obrazovce pokynů k ověření.

```powershell
Connect-AzAccount
```

Tento příklad používá **westus2** a uloží do proměnné pro použití v rámci výběr.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu.

Následující příklad vytvoří skupinu prostředků **gridResourceGroup** v umístění **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Události služby Blob Storage jsou dostupné v účtech úložiště pro obecné účely verze 2 a v účtech Blob Storage. Účty úložiště pro **obecné účely verze 2** podporují všechny funkce ve všech službách úložiště, včetně objektů blob, souborů, front a tabulek. **Účet úložiště objektů blob** je specializovaný účet úložiště pro ukládání nestrukturovaných dat v podobě objektů blob do služby Azure Storage. Účty úložiště objektů blob jsou podobné účtům úložiště pro obecné účely a mají stejně vysokou odolnost, dostupnost, škálovatelnost a výkonnost, a navíc mají 100% konzistentnost rozhraní API pro objekty blob bloku a doplňovací objekty blob. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Vytvoření účtu úložiště objektů Blob s replikací LRS pomocí [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount), pak načíst kontext účtu úložiště, který definuje účet úložiště, který se má použít. Když používáte účet úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext. Tento příklad vytvoří účet úložiště s názvem **gridstorage** s místně redundantním úložištěm (LRS). 

> [!NOTE]
> Názvy účtů úložiště jsou v oboru názvů globální, takže budete muset přidat některé náhodných znaků názvu zadanému v tento skript.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Nahraďte `<your-site-name>` jedinečným názvem vaší webové aplikace. Název webové aplikace musí být jedinečný, protože je součástí položky DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Měli byste vidět web aktuálně bez zobrazených zpráv.

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>Přihlášení k odběru účtu úložiště

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat. Následující příklad se přihlásí k odběru účtu úložiště, který jste vytvořili, a předá adresu URL z webové aplikace jako koncový bod pro oznámení události. Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

![Zobrazení události odběru](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Aktivace události ze služby Blob Storage

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Nejdřív vytvoříme kontejner a objekt. Potom Zkusíme nahrát objekt do kontejneru.

```powershell
$containerName = "gridcontainer"
New-AzStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Podívejte se na webovou aplikaci, abyste si zobrazili událost, kterou jste právě odeslali.

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
Pokud budete chtít pokračovat v práci s tímto odběrem účtu a události úložiště, není nevyčišťujte prostředky vytvořené v tomto článku. Pokud nechcete pokračovat, pomocí následujícího příkazu odstraňte prostředky, které jste vytvořili v tomto článku.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

Když teď víte, jak vytvářet témata a odběry událostí, zjistěte více o událostech služby Blob Storage a s čím vám služba Event Grid ještě může pomoct:

- [Reakce na události služby Blob Storage](storage-blob-event-overview.md)
- [Informace o službě Event Grid](../../event-grid/overview.md)
