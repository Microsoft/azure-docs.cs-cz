---
title: Správa zachytávání paketů pomocí Azure Network Watcher – Azure CLI | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat funkci zachycení paketů Network Watcher pomocí rozhraní příkazového řádku Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 6e5f2a519564716d426c50bb9cc8dd245774321e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966492"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Správa zachytávání paketů pomocí Azure Network Watcher pomocí Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher Capture paketů umožňuje vytvářet relace zachycení pro sledování provozu do a z virtuálního počítače. K dispozici jsou filtry pro relaci zachycení, aby bylo možné zachytit pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě interaktivně a aktivně. Mezi další použití patří shromažďování statistik sítě a získání informací o neoprávněných vniknutích k síti, k ladění komunikace mezi klientem a serverem a mnohem více. Díky tomu, že je možné vzdáleně aktivovat zachycení paketů, tato schopnost usnadňuje zátěžové zachycení paketů ručně a na požadovaném počítači, který šetří cenný čas.

K provedení kroků v tomto článku je potřeba [nainstalovat rozhraní azure Command-Line pro Mac, Linux a Windows (Azure CLI)](/cli/azure/install-azure-cli).

Tento článek vás provede různými úlohami správy, které jsou aktuálně k dispozici pro zachytávání paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavení zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranění zachytávání paketů**](#delete-a-packet-capture)
- [**Stažení zachytávání paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte následující zdroje:

- Instance Network Watcher v oblasti, ve které chcete vytvořit zachytávání paketů
- Virtuální počítač s povoleným rozšířením zachytávání paketů.

> [!IMPORTANT]
> Zachytávání paketů vyžaduje, aby na virtuálním počítači běžel agent. Agent je nainstalován jako rozšíření. Pokyny k rozšíření virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních](../virtual-machines/extensions/features-windows.md)počítačů.

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálních počítačů

### <a name="step-1"></a>Krok 1

Spuštěním `az vm extension set` příkazu nainstalujte agenta zachytávání paketů do hostovaného virtuálního počítače.

Pro virtuální počítače s Windows:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Pro virtuální počítače se systémem Linux:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Krok 2

Chcete-li zajistit, aby byl agent nainstalován, spusťte `vm extension show` příkaz a předejte mu skupinu prostředků a název virtuálního počítače. Zkontrolujte výsledný seznam a ujistěte se, že je agent nainstalovaný.

Pro virtuální počítače s Windows:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Pro virtuální počítače se systémem Linux:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Následující ukázka je příkladem reakce na spuštění `az vm extension show`

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

## <a name="start-a-packet-capture"></a>Spustit zachytávání paketů

Po dokončení předchozích kroků se na virtuálním počítači nainstaluje agent zachytávání paketů.

### <a name="step-1"></a>Krok 1

Načtěte účet úložiště. Tento účet úložiště se používá k uložení souboru zachycení paketů.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Krok 2

V tuto chvíli jste připraveni vytvořit zachytávání paketů.  Nejdřív se podíváme na parametry, které můžete chtít nakonfigurovat. Filtry jsou jedním z těchto parametrů, které lze použít k omezení dat uložených zachytáváním paketů. Následující příklad nastaví zachytávání paketů s několika filtry.  První tři filtry shromažďují odchozí přenosy TCP pouze z místních IP 10.0.0.3 na cílové porty 20, 80 a 443.  Poslední filtr shromažďuje jenom přenosy UDP.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Následující příklad je očekávaným výstupem ze spuštění `az network watcher packet-capture create` příkazu.

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

## <a name="get-a-packet-capture"></a>Získání zachytávání paketů

Spuštěním `az network watcher packet-capture show-status` příkazu načte stav aktuálně spuštěného nebo dokončeného zachytávání paketů.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Následující příklad je výstupem z `az network watcher packet-capture show-status` příkazu. Následující příklad je po zastavení zachycení s důvoduZastavení TimeExceeded.

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

## <a name="stop-a-packet-capture"></a>Zastavení zachytávání paketů

Spuštěním `az network watcher packet-capture stop` příkazu, pokud probíhá relace zachycení, je zastavena.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Příkaz nevrátí žádnou odpověď, pokud je spuštěn v aktuálně spuštěné relaci zachycení nebo existující relaci, která je již zastavena.

## <a name="delete-a-packet-capture"></a>Odstranění zachytávání paketů

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Odstraněním zachytávání paketů se soubor neodstraní v účtu úložiště.

## <a name="download-a-packet-capture"></a>Stažení zachytávání paketů

Po dokončení relace zachycení paketů se zachytávací soubor dá nahrát do úložiště objektů BLOB nebo do místního souboru na VIRTUÁLNÍm počítači. Umístění úložiště zachycení paketu je definováno při vytváření relace. Pohodlný nástroj pro přístup k těmto sběrným souborům uloženým do účtu úložiště je Průzkumník služby Microsoft Azure Storage, který se dá stáhnout tady:  https://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory zachytávání paketů se uloží do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak automatizovat zachycení paketů s výstrahami virtuálních počítačů zobrazením [Vytvoření výstrahy aktivované zachytávání paketů](network-watcher-alert-triggered-packet-capture.md) .

Zjistěte, jestli je na VIRTUÁLNÍm počítači povolený určitý provoz, a to tak, že navštíví [kontrolu toku IP](diagnose-vm-network-traffic-filtering-problem.md) .

<!-- Image references -->