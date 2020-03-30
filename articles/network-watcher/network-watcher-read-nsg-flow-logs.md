---
title: Čtení protokolů toku nsg | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak analyzovat protokoly toku nsg
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: damendo
ms.openlocfilehash: 47d927f9f17580767526ec6683e819256fc5e994
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619917"
---
# <a name="read-nsg-flow-logs"></a>Čtení protokolů toku NSG

Zjistěte, jak číst položky protokolů toku nsg pomocí prostředí PowerShell.

Protokoly toku nsg jsou uloženy v účtu úložiště v [blobů bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). Objekty BLOB bloku se shodnou s menšími bloky. Každý protokol je samostatný objekt blob bloku, který je generován každou hodinu. Nové protokoly jsou generovány každou hodinu, protokoly jsou aktualizovány s novými položkami každých pár minut s nejnovějšími daty. V tomto článku se dozvíte, jak číst části protokolů toku.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scénář

V následujícím scénáři máte ukázkový protokol toku, který je uložen v účtu úložiště. Dozvíte se, jak selektivně číst nejnovější události v protokolech toku nsg. V tomto článku však použijete prostředí PowerShell, koncepty popsané v článku se však neomezují na programovací jazyk a platí pro všechny jazyky podporované rozhraními API úložiště Azure.

## <a name="setup"></a>Nastavení

Než začnete, musíte mít v účtu povoleno protokolování toku skupiny zabezpečení sítě v jedné nebo více skupinách zabezpečení sítě. Pokyny k povolení protokolů toku zabezpečení sítě naleznete v následujícím článku: [Úvod do protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Načíst seznam blokování

Následující prostředí PowerShell nastaví proměnné potřebné k dotazování objektu blob protokolu toku nsg a seznam bloků v rámci objektu blob bloku [CloudBlockBlob.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) Aktualizujte skript tak, aby obsahoval platné hodnoty pro vaše prostředí.

```powershell
function Get-NSGFlowLogCloudBlockBlob {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.Azure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.Azure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        #Return the Cloud Block Blob
        $CloudBlockBlob
    }
}

function Get-NSGFlowLogBlockList  {
    [CmdletBinding()]
    param (
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob
    )
    process {
        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockListAsync()

        # Return the Block List
        $blockList
    }
}


$CloudBlockBlob = Get-NSGFlowLogCloudBlockBlob -subscriptionId "yourSubscriptionId" -NSGResourceGroupName "FLOWLOGSVALIDATIONWESTCENTRALUS" -NSGName "V2VALIDATIONVM-NSG" -storageAccountName "yourStorageAccountName" -storageAccountResourceGroup "ml-rg" -macAddress "000D3AF87856" -logTime "11/11/2018 03:00" 

$blockList = Get-NSGFlowLogBlockList -CloudBlockBlob $CloudBlockBlob
```

Proměnná `$blockList` vrátí seznam bloků v objektu blob. Každý objekt blob bloku obsahuje alespoň dva bloky.  První blok má délku `12` bajtů, tento blok obsahuje otevírací závorky protokolu json. Druhý blok je uzavírací závorky `2` a má délku bajtů.  Jak můžete vidět v následujícím příkladu protokolu má sedm položek v něm, z nichž každá je jednotlivá položka. Všechny nové položky v protokolu jsou přidány na konec těsně před posledním blokem.

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

## <a name="read-the-block-blob"></a>Čtení objektu blob bloku

Dále je třeba `$blocklist` přečíst proměnnou pro načtení dat. V tomto příkladu iterate prostřednictvím seznamu blokovaných, číst bajty z každého bloku a příběh je v poli. K načtení dat použijte metodu [DownloadRangeToByteArray.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadrangetobytearray)

```powershell
function Get-NSGFlowLogReadBlock  {
    [CmdletBinding()]
    param (
        [System.Array] [Parameter(Mandatory=$true)] $blockList,
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob

    )
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
        $CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index, $($blockList[$i].Length)) | Out-Null

        # Increment the index by adding the current block length to the previous index
        $index = $index + $blockList[$i].Length

        # Retrieve the string from the byte array

        $value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

        # Add the log entry to the value array
        $valuearray += $value
    }
    #Return the Array
    $valuearray
}
$valuearray = Get-NSGFlowLogReadBlock -blockList $blockList -CloudBlockBlob $CloudBlockBlob
```

Nyní `$valuearray` pole obsahuje hodnotu řetězce každého bloku. Chcete-li položku ověřit, získejte druhou poslední `$valuearray[$valuearray.Length-2]`hodnotu z pole spuštěním . Nechcete poslední hodnotu, protože se jedná o uzavírací závorku.

Výsledky této hodnoty jsou uvedeny v následujícím příkladu:

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

Tento scénář je příkladem čtení položek v protokolech toku nsg bez nutnosti analyzovat celý protokol. Můžete číst nové položky v protokolu, jak jsou zapsány pomocí ID bloku nebo sledováním délky bloků uložených v objektu blob bloku. To umožňuje číst pouze nové položky.

## <a name="next-steps"></a>Další kroky


Další informace o způsobech zobrazení protokolů toku nsg najdete na stránce [Použití elastického zásobníku](network-watcher-visualize-nsg-flow-logs-open-source-tools.md), [Použijte grafanu](network-watcher-nsg-grafana.md)a [pomocí graylogu.](network-watcher-analyze-nsg-flow-logs-graylog.md) Open Source Azure funkce přístup ke spotřebovávání objektů BLOB přímo a vyzařující různé klienty analýzy protokolů, které lze nalézt zde: [Azure Network Watcher NSG Flow Logs Connector](https://github.com/Microsoft/AzureNetworkWatcherNSGFlowLogsConnector).

[Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) můžete použít k získání přehledů o svých dopravních tocích. Traffic Analytics používá [Log Analytics,](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) aby váš tok provozu dotazován.

Další informace o objektech BLOB úložiště najdete na adrese: [Vazby úložiště Azure Functions Blob](../azure-functions/functions-bindings-storage-blob.md)
