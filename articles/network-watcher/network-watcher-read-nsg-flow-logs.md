---
title: Tok protokolů NSG pro čtení | Microsoft Docs
description: Tento článek ukazuje, jak k analýze protokolů NSG toku
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 58474286352ff3f00b31e65a565c2b64a656a177
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
ms.locfileid: "29149630"
---
# <a name="read-nsg-flow-logs"></a>Tok protokolů NSG pro čtení

Zjistěte, jak číst záznamů protokolů NSG toku pomocí prostředí PowerShell.

Skupina NSG toku protokoly jsou uloženy v účtu úložiště v [objekty BLOB bloků](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs.md#about-block-blobs). Objekty BLOB bloku jsou tvořeny menší bloky. Objekt blob bloku samostatné, aby se vygenerovala každou hodinu je každý protokol. Nové protokoly jsou generovány každou hodinu, protokoly jsou aktualizované o nové položky každých několik minut s nejnovější data. V tomto článku a zjistěte, jak číst části toku protokolů.

## <a name="scenario"></a>Scénář

V následujícím scénáři máte protokol toku příklad, který je uložený v účtu úložiště. jsme projděte jak můžete selektivně přečíst nejnovější události v toku protokolů NSG. V tomto článku budeme používat prostředí PowerShell, ale koncepty popsanou v článku nejsou omezeny na programovací jazyk a platí pro všechny jazyky, které podporuje rozhraní API úložiště Azure

## <a name="setup"></a>Nastavení

Než začnete, musíte mít síťové zabezpečení skupiny toku povoleným protokolováním na jeden nebo více skupin zabezpečení sítě ve vašem účtu. Pokyny k povolení zabezpečení sítě toku protokolů, najdete v následujícím článku: [Úvod do toku protokolování pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Načtení do seznamu zakázaných položek

Následující PowerShell nastavuje proměnné potřebné pro dotaz na objekt blob NSG toku protokolu a seznam bloků v rámci [CloudBlockBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azurestorage-8.1.3) objekt blob bloku. Aktualizujte skript tak, aby obsahovala platné hodnoty pro vaše prostředí.

```powershell
# The SubscriptionID to use
$subscriptionId = "00000000-0000-0000-0000-000000000000"

# Resource group that contains the Network Security Group
$resourceGroupName = "<resourceGroupName>"

# The name of the Network Security Group
$nsgName = "NSGName"

# The storage account name that contains the NSG logs
$storageAccountName = "<storageAccountName>" 

# The date and time for the log to be queried, logs are stored in hour intervals.
[datetime]$logtime = "06/16/2017 20:00"

# Retrieve the primary storage account key to access the NSG logs
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]

# Setup a new storage context to be used to query the logs
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Container name used by NSG flow logs
$ContainerName = "insights-logs-networksecuritygroupflowevent"

# The MAC Address of the Network Interface
$macAddress = "000D3AFA8650"

# Name of the blob that contains the NSG flow log
$BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${nsgName}/y=$($logtime.Year)/m=$(($logtime).ToString("MM"))/d=$(($logtime).ToString("dd"))/h=$(($logtime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

# Gets the storage blog
$Blob = Get-AzureStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

# Gets the block blog of type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from the storage blob
$CloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

# Stores the block list in a variable from the block blob.
$blockList = $CloudBlockBlob.DownloadBlockList()
```

`$blockList` Proměnné vrátí seznam bloků v objektu blob. Každý objekt blob bloku obsahuje alespoň dva bloky.  První blok má délku `21` bajtů, tento blok obsahuje otvírací závorky protokolu json. Další blok není pravé hranaté závorky a má délku `9` bajtů.  Jak je vidět v následujícím příkladu protokolu má sedm položky v něm, každý se jednotlivé položky. Všechny nové položky v protokolu jsou přidány na konec bezprostředně před posledního bloku.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>Čtení objektů blob bloku

Další potřebujeme ke čtení `$blocklist` proměnná načíst data. V tomto příkladu, které jsme iteraci v rámci blocklist čtení bajtů z každého bloku a scénáře je v matici. Používáme [DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadrangetobytearray?view=azurestorage-8.1.3#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadRangeToByteArray_System_Byte___System_Int32_System_Nullable_System_Int64__System_Nullable_System_Int64__Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metoda načíst data.

```powershell
# Set the size of the byte array to the largest block
$maxvalue = ($blocklist | measure Length -Maximum).Maximum

# Create an array to store values in
$valuearray = @()

# Define the starting index to track the current block being read
$index = 0

# Loop through each block in the block list
for($i=0; $i -lt $blocklist.count; $i++)
{

# Create a byte array object to story the bytes from the block
$downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

# Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
$CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index+3,$($blockList[$i].Length-1)) | Out-Null

# Increment the index by adding the current block length to the previous index
$index = $index + $blockList[$i].Length

# Retrieve the string from the byte array

$value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

# Add the log entry to the value array
$valuearray += $value
}
```

Nyní `$valuearray` pole obsahuje hodnotu řetězce každého bloku. Ověření vstupu, získat druhý poslední hodnota z pole spuštěním `$valuearray[$valuearray.Length-2]`. Jsme nechcete, aby poslední hodnotu je právě pravá závorka.

Následující příklad ukazuje výsledky této hodnoty:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

Tento scénář je příklad čtení položek v toku protokolů NSG bez nutnosti analyzovat celý protokol. Nové položky v protokolu může číst, jako jsou zapsané pomocí ID bloku nebo sledování délka bloky, které jsou uložené v objekt blob bloku. To umožňuje číst pouze nové položky.


## <a name="next-steps"></a>Další postup

Navštivte [vizualizovat protokolů toku NSG sledovací proces sítě Azure pomocí nástroje s otevřeným zdrojem](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) Další informace o k zobrazení protokolů NSG toku.

Další informace o úložiště objektů BLOB najdete: [vazby úložiště objektů Blob v Azure funkce](../azure-functions/functions-bindings-storage-blob.md)
