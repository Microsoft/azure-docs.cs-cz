---
title: Správa zachytávání paketů pomocí služby Azure Network Watcher – rozhraní REST API | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak spravovat funkce zachytávání paketů služby pomocí rozhraní REST API služby Azure Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 53fe0324-835f-4005-afc8-145eeb314aeb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: a429bfae001cad044da9ef729c021c8128cbfefe
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090006"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Správa zachytávání paketů pomocí služby Azure Network Watcher pomocí rozhraní Azure REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Rozhraní Azure REST API](network-watcher-packet-capture-manage-rest.md)

Zachytávání paketů Network Watcher umožňuje vytvořit relace zachycení sledujte provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relace zachytávání zajistit, že zachytíte jenom provoz, který chcete. Zachytávání paketů pomáhá diagnostikovat sítě anomálie reaktivně a proaktivně. Mezi další použití patří shromažďování statistických údajů sítě získat informace o síti vniknutí, chcete-li ladit komunikaci klienta se serverem a spoustu dalších věcí. Díky možnosti vzdáleně spustit zachytávání paketů, tato funkce usnadňuje si museli dělat starosti s zachycení paketů ručně a na požadovaný počítač, což šetří cenný čas.

Tento článek vás provede jiné úlohy, které jsou aktuálně k dispozici pro zachycení paketů.

- [**Získat zachytávání paketů**](#get-a-packet-capture)
- [**Seznam všech zachytávání paketů**](#list-all-packet-captures)
- [**Dotaz na stav zachytávání paketů**](#query-packet-capture-status)
- [**Spustit zachytávání paketů**](#start-packet-capture)
- [**Zastavit zachytávání paketů**](#stop-packet-capture)
- [**Odstranit zachycení paketů**](#delete-packet-capture)

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři volání rozhraní Rest API síťových sledovacích procesů ke spuštění IP tok ověřit. ARMclient slouží k volání rozhraní REST API pomocí Powershellu. ARMClient se nachází na chocolatey na [ARMClient v Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher.

> Zachytávání paketů vyžaduje rozšíření virtuálního počítače `AzureNetworkWatcherExtension`. Instalaci rozšíření na virtuálním počítači s Windows najdete [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md) a pro virtuální počítač s Linuxem, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Načíst virtuální počítač

Spusťte následující skript, který vrátí virtuální počítač. Tyto informace jsou nezbytné pro spouštění zachytávání paketů.

Následující kód potřebuje proměnné:

- **subscriptionId** – id předplatného můžete získat také pomocí **Get-AzureRMSubscription** rutiny.
- **Název skupiny prostředků** – název skupiny prostředků obsahující virtuální počítače.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Id virtuálního počítače následující výstup se používá v dalším příkladu.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Získat zachytávání paketů

Následující příklad získá stav zachycení jednoho paketu

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Příklady typických odpovědi vrácené při dotazování na stav zachytávání paketů, jsou tyto odpovědi.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Seznam všech zachytávání paketů

Následující příklad získá všechny relace zachycení paketů v oblasti.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

Odpověď na následující je příklad typické odpovědi vrácené při získávání všech paketů zachycuje

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Dotazování stavu zachytávání paketů

Následující příklad získá všechny relace zachycení paketů v oblasti.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Odpověď na následující je příklad typické odpovědi vrácené při dotazování na stav zachytávání paketů.

```json
{
    "name": "vm1PacketCapture",     "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatche rName}/packetCaptures/{packetCaptureName}",
   "captureStartTime" : "9/7/2016 12:35:24PM",
   "packetCaptureStatus" : "Stopped",
   "stopReason" : "TimeExceeded"
   "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Spustit zachytávání paketů

Následující příklad vytvoří zachytávání paketů na virtuálním počítači.  V příkladu je parametrizované umožňující flexibilitu při vytváření příklad.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$storagePath = ""
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Zastavit zachytávání paketů

Následující příklad zastaví zachytávání paketů na virtuálním počítači.  V příkladu je parametrizované umožňující flexibilitu při vytváření příklad.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Odstranit zachycení paketů

Následující příklad odstraní zachytávání paketů na virtuálním počítači.  V příkladu je parametrizované umožňující flexibilitu při vytváření příklad.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> Odstraňuje se zachycení paketů nedojde k odstranění souboru v účtu úložiště

## <a name="next-steps"></a>Další postup

Pokyny ke stahování souborů z účtů úložiště azure, najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který je možné je Průzkumníka služby Storage. Další informace o Průzkumníku služby Storage najdete tady na následující odkaz: [Průzkumníka služby Storage](http://storageexplorer.com/)

Zjistěte, jak automatizovat zachytávání paketů pomocí virtuálního počítače výstrahy zobrazením [vytvořit zachytávání paketů upozornění aktivovaných](network-watcher-alert-triggered-packet-capture.md)













