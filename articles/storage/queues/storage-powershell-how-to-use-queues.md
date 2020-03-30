---
title: Provádění akcí úložiště fronty Azure v PowerShellu
description: Jak provádět operace v úložišti Azure Queue pomocí PowerShellu
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: bd2f372bdcb949b64f748d186a9b060bb9cbec4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087068"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Provádění operací Azure Queue Storage s využitím Azure PowerShellu

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke kterým se dá přistupovat z libovolného místa na světě prostřednictvím protokolu HTTP nebo HTTPS. Podrobné informace najdete [v tématu Úvod do fronty Azure](storage-queues-introduction.md). Tento článek s postupem popisuje běžné operace úložiště fronty. Získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vytvoření fronty
> * Načtení fronty
> * Přidání zprávy
> * Přečtení zprávy
> * Odstranění zprávy
> * Odstranění fronty

Tento postup vyžaduje modul Azure PowerShell Az verze 0.7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

Pro rovinu dat pro fronty neexistují žádné rutiny prostředí PowerShell. Chcete-li provádět operace roviny dat, jako je například přidání zprávy, čtení zprávy a odstranění zprávy, musíte použít klientskou knihovnu úložiště .NET tak, jak je vystavena v prostředí PowerShell. Vytvoříte objekt zprávy a potom můžete použít příkazy, jako je Například AddMessage k provádění operací na této zprávě. Tento článek vám ukáže, jak to udělat.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. Toto cvičení bude používat **eastus**. Uložte to v **umístění** proměnné pro budoucí použití.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Uložte název skupiny prostředků do proměnné pro budoucí použití. V tomto příkladu je vytvořena skupina prostředků s názvem *howtoqueuesrg* v oblasti *eastus.*

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořte standardní účet úložiště pro obecné účely s místně redundantním úložištěm (LRS) pomocí [účtu New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Získejte kontext účtu úložiště, který definuje účet úložiště, který má být použit. Když používáte účet úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Vytvoření fronty

Následující příklad nejprve naváže připojení k Azure Storage pomocí kontextu účtu úložiště, který zahrnuje název účtu úložiště a jeho přístupový klíč. Dále volá [rutinu New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) k vytvoření fronty s názvem howtoqueue.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Informace o konvencích pojmenování pro službu Front Azure naleznete v [tématu Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Načtení fronty

Můžete dotazovat a načítat konkrétní frontu nebo seznam všech front v účtu úložiště. Následující příklady ukazují, jak načíst všechny fronty v účtu úložiště a konkrétní fronty; oba příkazy používají rutinu [Get-AzStorageQueue.](/powershell/module/az.storage/Get-AzStorageQueue)

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Operace, které mají vliv na skutečné zprávy ve frontě, používají klientskou knihovnu úložiště .NET jako vystavenou v prostředí PowerShell. Chcete-li přidat zprávu do fronty, vytvořte novou instanci objektu zprávy, třídu [Microsoft.Azure.Storage.Queue.CloudQueueMessage.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue_message) Pak zavolejte metodu [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue.addmessage). CloudQueueMessage je možné vytvořit buď z řetězce (ve formátu UTF-8), nebo z bajtového pole.

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

Pokud používáte [Průzkumník a disketu Azure](https://storageexplorer.com), můžete se připojit ke svému účtu Azure a zobrazit fronty v účtu úložiště a přejít k podrobnostem do fronty a zobrazit zprávy ve frontě.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Čtení zprávy z fronty a její odstranění

Zprávy jsou čteny v pořadí nejlepší vyzkoušet první-in-first-out. To není zaručeno. Při čtení zprávy z fronty se stane neviditelné pro všechny ostatní procesy při pohledu na frontu. Tím je zajištěno, že pokud váš kód nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiné instance kódu může získat stejnou zprávu a zkuste to znovu.  

Tento **časový limit neviditelnosti** definuje, jak dlouho zůstane zpráva neviditelná, než bude znovu k dispozici pro zpracování. Výchozí hodnota je 30 sekund.

Váš kód přečte zprávu z fronty ve dvou krocích. Při volání [metody Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) se zobrazí další zpráva ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Chcete-li dokončit odebrání zprávy z fronty, zavolejte metodu [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)

V následujícím příkladu si přečtete tři zprávy fronty a počkejte 10 sekund (časový limit neviditelnosti). Pak si znovu přečtete tři zprávy a po přečtení je odstraníte voláním **DeleteMessage**. Pokud se pokusíte přečíst frontu po odstranění zpráv, $queueMessage budou vráceny jako NULL.

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

Chcete-li odstranit frontu a všechny zprávy v ní obsažené, zavolejte rutinu Remove-AzStorageQueue. Následující příklad ukazuje, jak odstranit konkrétní frontu použitou v tomto cvičení pomocí rutiny Remove-AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat všechny prostředky, které jste v tomto cvičení vytvořili, odeberte skupinu prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere vytvořený účet úložiště a samotnou skupinu prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste se dozvěděli o základní správě úložiště fronty pomocí Prostředí PowerShell, včetně toho, jak:

> [!div class="checklist"]
>
> * Vytvoření fronty
> * Načtení fronty
> * Přidání zprávy
> * Přečtěte si další zprávu
> * Odstranění zprávy
> * Odstranění fronty

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Rutiny úložiště Microsoft Azure PowerShell

* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
