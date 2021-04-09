---
title: Řešení potíží s připojeními – Azure CLI
titleSuffix: Azure Network Watcher
description: Naučte se používat funkce řešení potíží s připojením v Azure Network Watcher pomocí Azure CLI.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e93a9b7ba2c7f801761d33ccacfa3ecdb3c48503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019784"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-cli"></a>Řešení potíží s připojením k Azure Network Watcher pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Naučte se používat řešení potíží s připojením k ověření, jestli je možné navázat přímé připojení TCP z virtuálního počítače do daného koncového bodu.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte následující zdroje:

* Instance Network Watcher v oblasti, ve které chcete řešit potíže s připojením.
* Virtuální počítače pro řešení potíží s připojeními.

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, aby virtuální počítač, ze kterého řešení řešíte, byl `AzureNetworkWatcherExtension` nainstalován rozšíření virtuálního počítače. Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, přejděte na web [azure Network Watcher Agent Virtual Machine Extension for Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a pro Linux VM, navštivte [rozšíření Azure Network Watcher Agent Virtual Machine pro Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). V cílovém koncovém bodě není rozšíření vyžadováno.

## <a name="check-connectivity-to-a-virtual-machine"></a>Ověřte připojení k virtuálnímu počítači.

Tento příklad zkontroluje připojení k cílovému virtuálnímu počítači přes port 80.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Odpověď

Následující odpověď je z předchozího příkladu.  V této odpovědi `ConnectionStatus` je **nedosažitelný**. Vidíte, že se nepovedlo úspěšně odeslat všechny sondy. Připojení na virtuálním zařízení se nepovedlo, protože se `NetworkSecurityRule` nakonfiguroval uživatel s názvem **UserRule_Port80** nakonfigurovaný tak, aby blokoval příchozí provoz na portu 80. Tyto informace se dají použít k tomu, aby se nastudovaly problémy s připojením.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "bb01d336-d881-4808-9fbc-72f091974d68",
      "issues": [],
      "nextHopIds": [
        "f8b074e9-9980-496b-a35e-619f9bcbf648"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "10.1.2.4",
      "id": "f8b074e9-9980-496b-a35e-619f9bcbf648",
      "issues": [],
      "nextHopIds": [
        "8a5857f3-6ab8-4b11-b9bf-a046d66b8696"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fw
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.3.4",
      "id": "8a5857f3-6ab8-4b11-b9bf-a046d66b8696",
      "issues": [
        {
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule"
        }
      ],
      "nextHopIds": [
        "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/au
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.4.4",
      "id": "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/db
Nic0/ipConfigurations/ipconfig1",
      "type": "VnetLocal"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="validate-routing-issues"></a>Ověřit problémy s směrováním

Tento příklad kontroluje připojení mezi virtuálním počítačem a vzdáleným koncovým bodem.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Odpověď

V následujícím příkladu `connectionStatus` je zobrazen jako **nedosažitelný**. V `hops` podrobnostech vidíte, `issues` že provoz byl zablokován z důvodu `UserDefinedRoute` .

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "f2cb1868-2049-4839-b8ed-57a480d06f95",
      "issues": [
        {
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute"
        }
      ],
      "nextHopIds": [
        "da4022db-0ab0-48c4-a507-dd4c03561ca5"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "13.107.21.200",
      "id": "da4022db-0ab0-48c4-a507-dd4c03561ca5",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Unknown",
      "type": "Destination"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="check-website-latency"></a>Kontrolovat latenci webu

Následující příklad zkontroluje připojení k webu.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://bing.com --dest-port 80
```

### <a name="response"></a>Odpověď

V následující reakci vidíte zobrazení `connectionStatus` jako **dostupné**. Po úspěšném připojení se dodávají hodnoty latence.

```json
{
  "avgLatencyInMs": 2,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "639c2d19-e163-4dfd-8737-5018dd1168ae",
      "issues": [],
      "nextHopIds": [
        "fd43a6e7-c758-4f48-90aa-8db99105a4a3"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "204.79.197.200",
      "id": "fd43a6e7-c758-4f48-90aa-8db99105a4a3",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Zkontroluje připojení ke koncovému bodu úložiště.

Následující příklad zkontroluje připojení z virtuálního počítače k účtu úložiště blogu.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Odpověď

Následující JSON je příklad reakce na spuštění předchozí rutiny. Když je ověření úspěšné, zobrazí se `connectionStatus` vlastnost jako **dostupná**.  Zadali jste podrobnosti o počtu směrování, které vyžaduje, aby se dosáhlo objektu BLOB úložiště a latence.

```json
{
  "avgLatencyInMs": 1,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "5136acff-bf26-4c93-9966-4edb7dd40353",
      "issues": [],
      "nextHopIds": [
        "f8d958b7-3636-4d63-9441-602c1eb2fd56"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "1.2.3.4",
      "id": "f8d958b7-3636-4d63-9441-602c1eb2fd56",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak automatizovat zachycení paketů s výstrahami virtuálních počítačů zobrazením [Vytvoření výstrahy aktivované zachytávání paketů](network-watcher-alert-triggered-packet-capture.md) .

Zjistěte, jestli je na VIRTUÁLNÍm počítači povolený určitý provoz, a to tak, že navštíví [kontrolu toku IP](diagnose-vm-network-traffic-filtering-problem.md) .