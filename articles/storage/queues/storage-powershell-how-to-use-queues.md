---
title: Provádění akcí Azure Queue Storage v PowerShellu
description: Pomocí PowerShellu proveďte operace s úložištěm Azure Queue. Pomocí služby Azure Queue Storage můžete ukládat velké počty zpráv, které jsou přístupné přes HTTP/HTTPS.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d1f758390a270f072bc08e13d1d542e08e4df553
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425544"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Provádění operací Azure Queue Storage s využitím Azure PowerShellu

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke kterým se dá dostat odkudkoli na světě přes HTTP nebo HTTPS. Podrobné informace najdete v tématu [Úvod do front Azure](storage-queues-introduction.md). Tento článek s návody popisuje běžné operace s úložištěm front. Získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vytvoření fronty
> * Načtení fronty
> * Přidat zprávu
> * Přečíst zprávu
> * Odstranění zprávy
> * Odstranění fronty

Tento postup vyžaduje Azure PowerShell modul AZ verze 0,7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

Pro tuto rovinu dat pro fronty neexistují žádné rutiny prostředí PowerShell. Chcete-li provádět operace roviny dat, například přidat zprávu, přečíst zprávu a odstranit zprávu, je nutné použít klientskou knihovnu úložiště .NET, protože je vystavena v prostředí PowerShell. Vytvoříte objekt zprávy a potom můžete k provádění operací s touto zprávou použít příkazy, jako je například AddMessage. V tomto článku se dozvíte, jak to provést.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. Toto cvičení použije **eastus**. Uložte ho do **umístění** proměnné pro budoucí použití.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Uložte název skupiny prostředků do proměnné pro budoucí použití. V tomto příkladu se vytvoří skupina prostředků s názvem *howtoqueuesrg* v oblasti *eastus* .

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořte standardní účet úložiště pro obecné účely s místně redundantním úložištěm (LRS) pomocí [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Získejte kontext účtu úložiště, který definuje účet úložiště, který se má použít. Když používáte účet úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Vytvoření fronty

Následující příklad napřed vytvoří připojení k Azure Storage pomocí kontextu účtu úložiště, který zahrnuje název účtu úložiště a jeho přístupový klíč. Dále volá rutinu [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) , která vytvoří frontu s názvem "howtoqueue".

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Informace o zásadách vytváření názvů pro službu Azure Queue najdete v tématu [pojmenování front a metadat](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Načtení fronty

Můžete zadávat dotazy a načítat konkrétní frontu nebo seznam všech front v účtu úložiště. Následující příklady ukazují, jak načíst všechny fronty v účtu úložiště a konkrétní frontu. Oba příkazy používají rutinu [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) .

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Přidat zprávu do fronty

Operace, které mají vliv na skutečné zprávy ve frontě, používají klientskou knihovnu pro úložiště .NET jako zveřejněnou v prostředí PowerShell. Chcete-li přidat zprávu do fronty, vytvořte novou instanci objektu zprávy, třídy [Microsoft. Azure. Storage. Queue. CloudQueueMessage](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) . Pak zavolejte metodu [AddMessage](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage). CloudQueueMessage je možné vytvořit buď z řetězce (ve formátu UTF-8), nebo z bajtového pole.

Následující příklad ukazuje, jak přidat zprávu do fronty.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Pokud používáte [Průzkumník služby Azure Storage](https://storageexplorer.com), můžete se připojit k účtu Azure a zobrazit fronty v účtu úložiště a přejít k podrobnostem o frontě a zobrazit tak zprávy ve frontě.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Přečtěte si zprávu z fronty a pak ji odstraňte.

Zprávy jsou čteny nejlépe při prvním prvním vyzkoušení. To není zaručeno. Když si přečtete zprávu z fronty, bude se neviditelná pro všechny ostatní procesy, které prohlíží ve frontě. Tím je zajištěno, že pokud váš kód nedokáže zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a opakovat akci.  

Tento **časový limit neviditelnosti** definuje, jak dlouho zpráva zůstane neviditelná, než se znovu zpřístupní pro zpracování. Výchozí hodnota je 30 sekund.

Kód přečte zprávu z fronty ve dvou krocích. Když zavoláte metodu [Microsoft. Azure. Storage. Queue. CloudQueue. GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) , dostanete další zprávu ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Chcete-li dokončit odebrání zprávy z fronty, zavoláte metodu [Microsoft. Azure. Storage. Queue. CloudQueue. DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) .

V následujícím příkladu si přečtete tři zprávy fronty a pak počkáte 10 sekund (časový limit neviditelnosti). Pak znovu přečtete tři zprávy a po jejich přečtení je zavoláte pomocí **DeleteMessage**. Pokud se po odstranění zpráv pokusíte tuto frontu přečíst, $queueMessage bude vrácena jako NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Odstranění fronty

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte rutinu Remove-AzStorageQueue. Následující příklad ukazuje, jak odstranit specifickou frontu použitou v tomto cvičení pomocí rutiny Remove-AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste v tomto cvičení vytvořili, odeberte skupinu prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě se odebere účet úložiště, který je vytvořený, a skupinu prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto článku se seznámíte se základní správou úložiště front pomocí PowerShellu, včetně toho, jak:

> [!div class="checklist"]
>
> * Vytvoření fronty
> * Načtení fronty
> * Přidat zprávu
> * Přečtěte si další zprávu.
> * Odstranění zprávy
> * Odstranění fronty

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell rutiny úložiště

* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
