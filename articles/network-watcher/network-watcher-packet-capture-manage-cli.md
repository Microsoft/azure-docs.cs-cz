---
title: Správa zachytávání paketů pomocí služby Azure Network Watcher – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak spravovat funkce zachytávání paketů služby Network Watcher pomocí Azure CLI
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9b40a85cf3c4edd26f2fc15045f3d6862d4ac1ff
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090482"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Správa zachytávání paketů pomocí služby Azure Network Watcher pomocí Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Rozhraní Azure REST API](network-watcher-packet-capture-manage-rest.md)

Zachytávání paketů Network Watcher umožňuje vytvořit relace zachycení sledujte provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relace zachytávání zajistit, že zachytíte jenom provoz, který chcete. Zachytávání paketů pomáhá diagnostikovat sítě anomálie reaktivně a proaktivně. Mezi další použití patří shromažďování statistických údajů sítě získat informace o síti vniknutí, chcete-li ladit komunikaci klienta se serverem a spoustu dalších věcí. Díky možnosti vzdáleně spustit zachytávání paketů, tato funkce usnadňuje si museli dělat starosti s zachycení paketů ručně a na požadovaný počítač, což šetří cenný čas.

Tento článek používá naše nová generace rozhraní příkazového řádku pro model nasazení resource management, Azure CLI 2.0, které je dostupné pro Windows, Mac a Linux.

K provedení kroků v tomto článku, budete muset [instalace rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

Tento článek vás provede jiné úlohy, které jsou aktuálně k dispozici pro zachycení paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavit zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranit zachycení paketů**](#delete-a-packet-capture)
- [**Stáhněte si zachytávání paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující prostředky:

- Instance služby Network Watcher v oblasti, kterou chcete vytvořit zachytávání paketů
- Virtuální počítač s příponou zachycení paketů povoleno.

> [!IMPORTANT]
> Zachytávání paketů vyžaduje agenta běží na virtuálním počítači. Agent je nainstalován jako rozšíření. Pokyny k rozšíření virtuálních počítačů, navštivte [funkce a rozšíření virtuálních počítačů](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálních počítačů

### <a name="step-1"></a>Krok 1

Spustit `az vm extension set` rutiny pro instalaci agenta zachytávání paketů na virtuálním počítači hosta.

Pro virtuální počítače s Windows:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Pro virtuální počítače s Linuxem:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
````

### <a name="step-2"></a>Krok 2

Chcete-li zajistit, že je agent nainstalovaný, spusťte `vm extension show` rutiny a předejte mu název virtuálního počítače a skupiny prostředků. Zkontrolujte výsledný seznam a ověřit, že je nainstalovaný agent.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Následující příklad je příkladem odpověď od spuštění `az vm extension show`

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

Po dokončení předchozích kroků je nainstalován agent zachytávání paketů na virtuálním počítači.

### <a name="step-1"></a>Krok 1

Dalším krokem je načtení instance Network Watcheru. Název služby Network Watcher je předán `az network watcher show` rutiny v kroku 4.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>Krok 2

Načtěte účet úložiště. Tento účet úložiště se používá k uložení souboru zachytávání paketů.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Krok 3

Filtry je možné omezit data, která se ukládá pomocí zachytávání paketů. Následující příklad nastaví zachytávání paketů se několik filtrů.  První tři filtry shromažďovat odchozí provoz TCP jenom z místní IP 10.0.0.3 na cílové porty 20, 80 a 443.  Poslední filtr shromažďuje pouze provoz UDP.

```azurecli
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

V následujícím příkladu je očekávaný výstup spuštění `az network watcher packet-capture create` rutiny.

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

## <a name="get-a-packet-capture"></a>Získat zachytávání paketů

Spuštění `az network watcher packet-capture show-status` rutiny, načítá stav aktuálně spuštěné nebo dokončené zachytávání paketů.

```azurecli
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

V následujícím příkladu je výstup `az network watcher packet-capture show-status` rutiny. V následujícím příkladu je při zastavení zaznamenávání pomocí Důvoduzastavení TimeExceeded. 

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

## <a name="stop-a-packet-capture"></a>Zastavit zachytávání paketů

Spuštěním `az network watcher packet-capture stop` rutiny, pokud relace zachytávání se v průběhu je zastavená.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Žádná odpověď vrátí rutina při spuštěné v probíhající relace zachytávání nebo existující relaci, která již byla zastavena.

## <a name="delete-a-packet-capture"></a>Odstranit zachycení paketů

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Odstraňuje se zachycení paketů nedojde k odstranění souboru v účtu úložiště.

## <a name="download-a-packet-capture"></a>Stáhněte si zachytávání paketů

Po dokončení relace zachycení paketů zachytávací soubor je možné uložit do úložiště objektů blob nebo do místního souboru na virtuálním počítači. Umístění úložiště pro zachytávání paketů je definován při vytvoření relace. Praktický nástroj pro přístup k těmto zachycení soubory uložené na účet úložiště je Microsoft Azure Storage Explorer, které je možné stáhnout tady:  http://storageexplorer.com/

Pokud je určen účet úložiště, jsou uloženy soubory zachytávání paketů do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak automatizovat zachytávání paketů pomocí virtuálního počítače výstrahy zobrazením [vytvořit zachytávání paketů upozornění aktivovaných](network-watcher-alert-triggered-packet-capture.md)

Najít, jestli některé je povolený provoz do nebo ze svého virtuálního počítače návštěvou [ověření toku protokolu IP zkontrolujte](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
