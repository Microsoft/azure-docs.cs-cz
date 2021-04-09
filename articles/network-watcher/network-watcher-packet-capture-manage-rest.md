---
title: Správa zachytávání paketů pomocí Azure Network Watcher-REST API | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat funkci zachytávání paketů Network Watcher pomocí Azure REST API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: bcdb91d7516acb8f987a3f302c04fbd2d8594fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019767"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Správa zachytávání paketů pomocí Azure Network Watcher s využitím Azure REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher Capture paketů umožňuje vytvářet relace zachycení pro sledování provozu do a z virtuálního počítače. K dispozici jsou filtry pro relaci zachycení, aby bylo možné zachytit pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě interaktivně a aktivně. Mezi další použití patří shromažďování statistik sítě a získání informací o neoprávněných vniknutích k síti, k ladění komunikace mezi klientem a serverem a mnohem více. Díky tomu, že je možné vzdáleně aktivovat zachycení paketů, tato schopnost usnadňuje zátěžové zachycení paketů ručně a na požadovaném počítači, který šetří cenný čas.

Tento článek vás provede různými úlohami správy, které jsou aktuálně k dispozici pro zachytávání paketů.

- [**Získání zachytávání paketů**](#get-a-packet-capture)
- [**Vypsat všechna zachytávání paketů**](#list-all-packet-captures)
- [**Dotaz na stav zachytávání paketů**](#query-packet-capture-status)
- [**Spustit zachytávání paketů**](#start-packet-capture)
- [**Zastavení zachytávání paketů**](#stop-packet-capture)
- [**Odstranění zachytávání paketů**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři zavoláte rozhraní Network Watcher REST API, které spustí ověřování toku IP. ARMclient se používá k volání REST API s využitím PowerShellu. ARMClient se nachází v čokoládě na [ARMClient při čokoládě](https://chocolatey.org/packages/ARMClient) .

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher.

> Zachytávání paketů vyžaduje rozšíření virtuálního počítače `AzureNetworkWatcherExtension` . Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, přejděte na web [azure Network Watcher Agent Virtual Machine Extension for Windows](../virtual-machines/extensions/network-watcher-windows.md) a pro Linux VM, navštivte [rozšíření Azure Network Watcher Agent Virtual Machine pro Linux](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="log-in-with-armclient"></a>Přihlášení pomocí ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Načíst virtuální počítač

Spusťte následující skript, který vrátí virtuální počítač. Tyto informace jsou potřeba ke spuštění zachytávání paketů.

Následující kód vyžaduje proměnné:

- **SubscriptionId** – ID předplatného se dá načíst taky pomocí rutiny **Get-AzSubscription** .
- **resourceGroupName** – název skupiny prostředků, která obsahuje virtuální počítače.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Z následujícího výstupu se v dalším příkladu použije ID virtuálního počítače.

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


## <a name="get-a-packet-capture"></a>Získání zachytávání paketů

V následujícím příkladu se načte stav jednoho zachytávání paketů.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Následující odpovědi jsou příklady typické odpovědi vracené při dotazování na stav zachytávání paketů.

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

## <a name="list-all-packet-captures"></a>Vypsat všechna zachytávání paketů

Následující příklad získá všechny relace zachytávání paketů v oblasti.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

Následující odpověď je příkladem typické odpovědi vracené při získávání všech zachycení paketů.

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

## <a name="query-packet-capture-status"></a>Stav zachytávání paketů dotazů

Následující příklad získá všechny relace zachytávání paketů v oblasti.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Následující odpověď je příkladem typické odpovědi vracené při dotazování na stav zachytávání paketů.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Spustit zachytávání paketů

Následující příklad vytvoří zachytávání paketů na virtuálním počítači.  Příklad je parametrizovaný tak, aby umožňoval flexibilitu při vytváření příkladu.

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
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
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

Následující příklad zastaví zachytávání paketů na virtuálním počítači.  Příklad je parametrizovaný tak, aby umožňoval flexibilitu při vytváření příkladu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Odstranit zachytávání paketů

Následující příklad odstraní zachytávání paketů na virtuálním počítači.  Příklad je parametrizovaný tak, aby umožňoval flexibilitu při vytváření příkladu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> Odstraněním zachytávání paketů se soubor neodstraní v účtu úložiště.

## <a name="next-steps"></a>Další kroky

Pokyny ke stahování souborů z účtů Azure Storage najdete v tématu [Začínáme s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Průzkumník služby Storage se dá použít jiný nástroj. Další informace o Průzkumník služby Storage najdete na následujícím odkazu: [Průzkumník služby Storage](https://storageexplorer.com/)

Podívejte se, jak automatizovat zachycení paketů s výstrahami virtuálních počítačů zobrazením [Vytvoření výstrahy aktivované zachytávání paketů](network-watcher-alert-triggered-packet-capture.md) .