---
title: Poradce při potížích s připojením – azure cli
titleSuffix: Azure Network Watcher
description: Zjistěte, jak pomocí azure cli používat funkci řešení potíží s připojením služby Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 842e58de8dbc06d3f045b0e9d0dc6b99e6b1e2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842882"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-cli"></a>Poradce při potížích s připojením pomocí Azure Network Watcher pomocí azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Zjistěte, jak pomocí řešení potíží s připojením ověřit, zda lze navázat přímé připojení TCP z virtuálního počítače k danému koncovému bodu.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující zdroje:

* Instance sledovacího programu sítě v oblasti, ve které chcete vyřešit potíže s připojením.
* Virtuální počítače pro řešení potíží s připojením.

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, `AzureNetworkWatcherExtension` aby v ym, který řešíte z má rozšíření virtuálního zařízení nainstalován. Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a pro virtuální počítač SIP na stránce Rozšíření [virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozšíření není vyžadováno v cílovém koncovém bodě.

## <a name="check-connectivity-to-a-virtual-machine"></a>Kontrola připojení k virtuálnímu počítači

Tento příklad kontroluje připojení k cílovému virtuálnímu počítači přes port 80.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Odpověď

Následující odpověď je z předchozího příkladu.  V této odpovědi `ConnectionStatus` je **nedostupný**. Můžete vidět, že všechny odeslané sondy selhaly. Připojení ve virtuálním zařízení se nezdařilo `NetworkSecurityRule` z důvodu uživatelem nakonfigurovaného s názvem **UserRule_Port80**, který je nakonfigurován tak, aby blokoval příchozí provoz na portu 80. Tyto informace lze použít k výzkumu problémů s připojením.

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

## <a name="validate-routing-issues"></a>Ověřit problémy se směrováním

Tento příklad kontroluje připojení mezi virtuálním počítačem a vzdáleným koncovým bodem.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Odpověď

V následujícím příkladu `connectionStatus` je zobrazen jako **Nedostupný**. V `hops` podrobnostech můžete vidět `issues` pod tím, že `UserDefinedRoute`provoz byl zablokován z důvodu .

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

## <a name="check-website-latency"></a>Kontrola latence webu

Následující příklad kontroluje připojení k webu.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://bing.com --dest-port 80
```

### <a name="response"></a>Odpověď

V následující odpovědi můžete vidět `connectionStatus` ukazuje jako **dosažitelné**. Při úspěšném připojení jsou k dispozici hodnoty latence.

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

## <a name="check-connectivity-to-a-storage-endpoint"></a>Kontrola připojení ke koncovému bodu úložiště

Následující příklad kontroluje připojení z virtuálního počítače k účtu úložiště blogu.

### <a name="example"></a>Příklad

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Odpověď

Následující json je příklad odpovědi ze spuštění předchozí rutiny. Jakmile je kontrola úspěšná, vlastnost se `connectionStatus` zobrazí jako **dosažitelná**.  Jsou k dispozici podrobnosti týkající se počtu směrování potřebné k dosažení objektu blob úložiště a latence.

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

Zjistěte, jak automatizovat zachytávání paketů pomocí upozornění virtuálního počítače zobrazením [zobrazení vytvoření záznamu aktivovaného zachycení paketu](network-watcher-alert-triggered-packet-capture.md)

Zjistěte, jestli je určitý provoz povolený do nebo z vašeho virtuálního počítače, a to najdete na [stránce Kontrola toku IP](diagnose-vm-network-traffic-filtering-problem.md)
