---
title: Provádění operací na Azure Queue storage pomocí prostředí PowerShell | Microsoft Docs
description: Jak provádět operace v Azure Queue storage pomocí prostředí PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: bad9f1f3fd5737e865a8f4d1d15ab3d5eb68b4cb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Provedení operace úložiště Azure Queue pomocí prostředí Azure PowerShell

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Podrobné informace najdete v tématu [Úvod do fronty Azure](storage-queues-introduction.md). Tento článek popisuje běžné operace fronty úložiště. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření fronty
> * Načtení do fronty
> * Přidat zprávu
> * Čtení zprávy
> * Odstranění zprávy 
> * Odstranění fronty

Tento postup vyžaduje prostředí Azure PowerShell verze modulu 3,6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Neexistují žádné rutiny prostředí PowerShell pro roviny data pro fronty. Před provedením datové roviny operací, jako přidat zprávu, přečtěte si zprávu a odstraní zprávu, budete muset použít klientské knihovny .NET úložiště, jako je vystaven v prostředí PowerShell. Můžete vytvořit objekt zprávu a pak můžete použít příkazy jako AddMessage k provádění operací v této zprávě. Tento článek ukazuje, jak to provést.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. Toto cvičení použijete **eastus**. Uložit v proměnné **umístění** pro budoucí použití.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Název skupiny prostředků uložte jako proměnnou pro budoucí použití. V tomto příkladu skupinu prostředků s názvem *howtoqueuesrg* je vytvořen v *eastus* oblast.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořte účet standardního úložiště pro obecné účely s místně redundantní úložiště (LRS) pomocí [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Získá kontext účtu úložiště, který definuje účet úložiště, který se má použít. Když používáte účet úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Vytvoření fronty

Následující příklad nejprve vytvoří připojení k Azure Storage pomocí kontext účtu úložiště, která zahrnuje název účtu úložiště a jeho přístupový klíč. Dále volá [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) rutiny vytvořit frontu s názvem 'queuename'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Informace o vytváření názvů pro službu front Azure, najdete v části [pojmenování front a Metadata](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Načtení do fronty

Můžete zadat dotaz a načíst konkrétní fronty nebo seznam všech front, které jsou v účtu úložiště. Následující příklady ukazují, jak získat všechny fronty v účtu úložiště a konkrétní fronty; Oba příkazy používají [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) rutiny.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Operace s dopadem na skutečné zprávy ve frontě pomocí klientské knihovny .NET úložiště jako vystavený v prostředí PowerShell. Pokud chcete přidat zprávu do fronty, vytvořte novou instanci objektu zpráva [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) třídy. Pak zavolejte metodu [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). CloudQueueMessage můžete vytvořit z řetězce (ve formátu UTF-8) nebo pole bajtů.

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

Pokud použijete [Azure Storage Explorer](http://storageexplorer.com), můžete připojit k účtu Azure a zobrazit fronty v účtu úložiště a přejděte do fronty k zobrazení zpráv pro frontu. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Čtení zpráv z fronty, odstraňte ji.

Zprávy se čtou v pořadí objektů first in first out zkuste nejvhodnější. Toto není zaručena. Při čtení zprávy z fronty, stane neviditelnou pro všechny ostatní procesy prohlížení fronty. To zajišťuje, že pokud kódu se nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiná instance vašeho kódu můžete stejnou zprávu získat a zkuste to znovu.  

To **časový limit neviditelnosti** definuje, jak dlouho zpráva zůstává neviditelná předtím, než je k dispozici pro zpracování znovu. Výchozí hodnota je 30 sekund. 

Váš kód přečte zprávu z fronty ve dvou krocích. Při volání [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metoda, získáte další zprávu ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Chcete-li dokončit odebere ji z fronty, zavolejte [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metoda. 

V následujícím příkladu, můžete si přečíst tři fronty zpráv, a potom počkejte 10 sekund (časový limit neviditelnosti). Pak čtení tři zpráv znovu, odstranění zprávy po přečtení je voláním **DeleteMessage**. Pokud se pokusíte číst fronty po odstranění zprávy, $queueMessage, bude vrácen jako hodnota NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte rutinu Remove-AzureStorageQueue. Následující příklad ukazuje, jak odstranit konkrétní fronty použít v tomto cvičení pomocí rutiny Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat všechny prostředky, které jste vytvořili v tomto cvičení, odeberte skupinu prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere účtu úložiště vytvořeném a skupině prostředků, sám sebe.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto postupu článku jste se dozvěděli o základní správu fronty úložiště pomocí prostředí PowerShell, včetně postup:

> [!div class="checklist"]
> * Vytvoření fronty
> * Načtení do fronty
> * Přidat zprávu
> * Přečtěte si další zprávy
> * Odstranění zprávy 
> * Odstranění fronty

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Rutiny prostředí PowerShell služby Microsoft Azure Storage
* [Rutiny PowerShellu pro úložiště](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
