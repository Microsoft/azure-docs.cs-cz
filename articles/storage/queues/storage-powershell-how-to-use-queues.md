---
title: Provádění operací ve službě Azure Queue storage pomocí Powershellu | Dokumentace Microsoftu
description: Jak k provádění operací v Azure Queue storage s využitím Powershellu
services: storage
author: robinsh
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.component: queues
ms.openlocfilehash: 0b78f822bec2bd545331ed52f3339a07b5dfb466
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521043"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Provádění operací Azure Queue storage pomocí Azure Powershellu

Azure Queue storage je služba pro ukládání velkého počtu zpráv, které můžete přistupovat odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS. Podrobné informace najdete v tématu [seznámení s frontami Azure](storage-queues-introduction.md). Tento článek popisuje běžné operacemi Queue storage. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření fronty
> * Načíst do fronty
> * Přidat zprávu
> * Přečíst zprávu
> * Odstranění zprávy 
> * Odstranění fronty

Tento návod vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Neexistují žádné rutiny Powershellu pro rovinu dat front. K provedení data ploché operace, jako přidat zprávu, přečtěte si zprávu a odstraní zprávu, budete muset použít klientskou knihovnu pro úložiště .NET, jako je přístupný v prostředí PowerShell. Vytvořte objekt zprávu a pak můžete použít příkazech, jako je AddMessage k provádění operací na tuto zprávu. V tomto článku se dozvíte, jak to provést.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. V tomto cvičení použijete **eastus**. To Store v proměnné **umístění** pro budoucí použití.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Název skupiny prostředků Store do proměnné pro budoucí použití. V tomto příkladu skupina prostředků s názvem *howtoqueuesrg* se vytvoří v *eastus* oblasti.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořit účet úložiště úrovně standard pro obecné účely s využitím místně redundantního úložiště (LRS) [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Získáte kontext účtu úložiště, který definuje účet úložiště, který se má použít. Když používáte účet úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Vytvoření fronty

Následující příklad nejprve vytvoří připojení k Azure Storage pomocí kontext účtu úložiště, který obsahuje název účtu úložiště a jeho přístupový klíč. Dále volá [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) rutiny můžete vytvořit frontu s názvem "queuename".

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Informace o vytváření názvů pro službu front Azure, najdete v části [pojmenování front a Metadata](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Načíst do fronty

Můžete vyhledat a načíst konkrétní frontu nebo seznam všech front v účtu úložiště. Následující příklady ukazují, jak načíst všechny fronty v účtu úložiště a konkrétní fronty; Oba příkazy používají [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) rutiny.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Operace, které mají vliv skutečné zprávy ve frontě pomocí klientské knihovny úložiště .NET jako viditelné v prostředí PowerShell. Přidat zprávu do fronty, vytvořte novou instanci objektu zpráva [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) třídy. Pak zavolejte metodu [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). CloudQueueMessage lze vytvořit z řetězce (ve formátu UTF-8) nebo s polem bajtů.

Následující příklad ukazuje, jak přidat zprávu do fronty.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Pokud používáte [Průzkumníka služby Azure Storage](http://storageexplorer.com), můžete připojit ke svému účtu Azure a zobrazit front v účtu úložiště a podrobnostem do fronty k zobrazení zpráv ve frontě. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Čtení zpráv z fronty, odstraňte ji.

Zprávy jsou čteny nejlepší – zkuste first-in-first-out pořadí. To není zaručeno. Při čtení zprávy z fronty, stane neviditelnou pro všechny ostatní procesy, které jsou ve frontě. Tím se zajistí, že pokud váš kód se nepodařilo zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiná instance vašeho kódu můžete získat stejná zpráva a zkuste to znovu.  

To **časový limit neviditelnosti** definuje, jak dlouho zpráva zůstává neviditelná dřív, než bude opět k dispozici pro zpracování. Výchozí hodnota je 30 sekund. 

Váš kód načítá zprávy z fronty ve dvou krocích. Při volání [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metoda, získáte další zprávu ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. K dokončení odebrání zprávy z fronty, volání [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metody. 

V následujícím příkladu, přečtěte si tři fronty zpráv a potom počkejte 10 sekund (vypršel časový limit neviditelnosti). Číst tři zprávy znovu, odstranění zprávy po přečtení voláním **DeleteMessage**. Pokud se pokusíte načíst fronty po odstranění zprávy, $queueMessage budou vráceny jako hodnota NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Odstranění fronty
Pokud chcete odstranit frontu se všemi zprávami, které v ní, zavolejte rutinu Remove-AzureStorageQueue. Následující příklad ukazuje, jak odstranit použité v tomto cvičení pomocí rutiny Remove-AzureStorageQueue konkrétní fronty.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat všechny prostředky, které jste vytvořili v tomto cvičení, odeberte skupinu prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere účet úložiště, který jste vytvořili a samotnou skupinu prostředků.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto článku s postupy jste se dozvěděli o základních fronty úložiště Powershellu ke správě, také se naučíte:

> [!div class="checklist"]
> * Vytvoření fronty
> * Načíst do fronty
> * Přidat zprávu
> * Přečtěte si další zprávy
> * Odstranění zprávy 
> * Odstranění fronty

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Rutiny Powershellu pro úložiště Microsoft Azure
* [Rutiny PowerShellu pro úložiště](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
