---
title: Správa zachytávání paketů pomocí Azure Network Watcher – Azure CLI | Dokumenty společnosti Microsoft
description: Tato stránka vysvětluje, jak spravovat funkci zachytávání paketů sledovacího programu služby Network Watcher pomocí příkazového příkazového příkazu Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7a69610d1ac176354a9d7e388a12ccc7f064d848
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382711"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Správa zachytávání paketů pomocí Azure Network Watcher pomocí azure cli

> [!div class="op_single_selector"]
> - [Portál Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Zachycení paketu Sledování sítě umožňuje vytvářet relace sběru pro sledování provozu do a z virtuálního počítače. Filtry jsou k dispozici pro relaci zachycení, které zajistí, že zachytíte pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě reaktivně i proaktivně. Mezi další použití patří shromažďování síťových statistik, získávání informací o síťových vniknutích, ladění komunikace mezi klientem a serverem a mnoho dalšího. Tím, že je schopna vzdáleně aktivovat zachycení paketů, tato schopnost zmírňuje zátěž ručního spuštění sběru paketů a na požadovaném počítači, což šetří cenný čas.

Chcete-li provést kroky v tomto článku, je třeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)](/cli/azure/install-azure-cli).

Tento článek vás provede různými úkoly správy, které jsou aktuálně k dispozici pro sběr paketů.

- [**Spuštění sběru paketů**](#start-a-packet-capture)
- [**Zastavení sběru paketů**](#stop-a-packet-capture)
- [**Odstranění zachycení paketu**](#delete-a-packet-capture)
- [**Stažení zachytávání paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující zdroje:

- Instance sledování sítě v oblasti, kterou chcete vytvořit sběr paketů
- Virtuální počítač s povoleným rozšířením pro sběr paketů.

> [!IMPORTANT]
> Sběr paketů vyžaduje agenta, který má být spuštěn ve virtuálním počítači. Agent je nainstalován jako rozšíření. Pokyny k rozšířením virtuálních zařízení najdete v [rozšířeních a funkcích virtuálních strojů](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálních počítačů

### <a name="step-1"></a>Krok 1

Spusťte `az vm extension set` příkaz a nainstalujte agenta pro digitalizaci paketů do hostovaného virtuálního počítače.

Pro virtuální počítače s Windows:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Pro linuxové virtuální počítače:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Krok 2

Chcete-li zajistit, aby byl `vm extension show` agent nainstalován, spusťte příkaz a předajte mu skupinu prostředků a název virtuálního počítače. Zkontrolujte výsledný seznam a ujistěte se, že je agent nainstalován.

Pro virtuální počítače s Windows:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Pro linuxové virtuální počítače:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Následující ukázka je příkladem odpovědi z běhu`az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Spuštění sběru paketů

Po dokončení předchozích kroků je agent pro sběr paketů nainstalován ve virtuálním počítači.

### <a name="step-1"></a>Krok 1

Načíst účet úložiště. Tento účet úložiště se používá k uložení souboru sběru paketů.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Krok 2

V tomto okamžiku jste připraveni vytvořit sběr paketů.  Nejprve prozkoumáme parametry, které chcete konfigurovat. Filtry jsou jedním z takových parametrů, které lze použít k omezení dat, která jsou uložena při sběru paketů. Následující příklad nastaví sběr paketů s několika filtry.  První tři filtry shromažďují odchozí přenosy Protokolu TCP pouze z místní ip 10.0.0.3 na cílové porty 20, 80 a 443.  Poslední filtr shromažďuje pouze přenosy UDP.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Následující příklad je očekávaný výstup `az network watcher packet-capture create` ze spuštění příkazu.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Získání zachycení paketu

Spuštěním `az network watcher packet-capture show-status` příkazu načtete stav aktuálně spuštěného nebo dokončeného sběru paketů.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Následující příklad je výstup `az network watcher packet-capture show-status` z příkazu. Následující příklad je, když je zastaveno zachycení s StopReason of TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Zastavení sběru paketů

Spuštěním `az network watcher packet-capture stop` příkazu, pokud probíhá relace sběru, je zastavena.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Příkaz vrátí žádnou odpověď při spuštění v aktuálně spuštěné relaci sběru nebo existující relaci, která již byla zastavena.

## <a name="delete-a-packet-capture"></a>Odstranění zachycení paketu

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Odstraněním sběru paketů neodstraníte soubor v účtu úložiště.

## <a name="download-a-packet-capture"></a>Stažení zachytávání paketů

Po dokončení relace sběru paketů lze soubor sběru nahrát do úložiště objektů blob nebo do místního souboru na virtuálním počítači. Umístění úložiště sběru paketů je definováno při vytváření relace. Pohodlným nástrojem pro přístup k těmto souborům pro digitalizaci uloženým do účtu úložiště je Microsoft Azure Storage Explorer, který si můžete stáhnout zde:https://storageexplorer.com/

Pokud je zadán účet úložiště, soubory pro digitalizaci paketů jsou uloženy do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak automatizovat zachytávání paketů pomocí upozornění virtuálního počítače zobrazením [zobrazení vytvoření záznamu aktivovaného zachycení paketu](network-watcher-alert-triggered-packet-capture.md)

Zjistěte, jestli je určitý provoz povolený do nebo z vašeho virtuálního počítače, a to najdete na [stránce Kontrola toku IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
