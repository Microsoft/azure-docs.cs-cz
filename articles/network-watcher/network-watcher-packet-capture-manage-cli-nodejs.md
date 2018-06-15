---
title: Spravovat zachycení paketů s sledovací proces sítě Azure - 1.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat funkci zachycení paketu sledovací proces sítě pomocí Azure CLI 1.0
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
ms.openlocfilehash: c74d1a94971495f7cd5f5bed42b33869fa9710d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185381"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-10"></a>Spravovat zachycení paketů s sledovací proces sítě Azure pomocí Azure CLI 1.0

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Rozhraní API Azure REST](network-watcher-packet-capture-manage-rest.md)

Zachytáváním paketů sledovací proces sítě vám umožní vytvořit relace zachytávání sledovat provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relaci zachytávání zajistit, že zaznamenáte pouze provoz, který chcete. Při diagnostice sítě anomálií reaktivně a proaktivně pomáhá zachytáváním paketů. Jiné účely zahrnují shromažďování statistiku sítě, získá informace o síti vniknutí, k ladění komunikaci klienta se serverem a mnoho dalšího. Díky vzdáleně aktivovat paketu zachycení, tato funkce snižuje zátěž spuštěných zachytáváním paketů ručně a na požadované počítače, který úspora času.

Tento článek používá 1.0 rozhraní příkazového řádku Azure a platformy, která je dostupná pro Windows, Mac a Linux.

Tento článek vás provede úloh jiný správy, které jsou aktuálně dostupné pro zachytávání paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavit zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranit zachytávání paketů**](#delete-a-packet-capture)
- [**Stáhnout zachytáváním paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte v následujících zdrojích informací:

- Instance sledovací proces sítě v oblasti, kterou chcete vytvořit zachytávání paketů
- Virtuální počítač s příponou zachytávání paketů povoleno.

> [!IMPORTANT]
> Zachytáváním paketů vyžaduje agenta, aby byl spuštěn na virtuálním počítači. Agent byl nainstalován jako rozšíření. Pokyny k rozšíření virtuálního počítače, navštivte [rozšíření virtuálního počítače a funkce](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálního počítače

### <a name="step-1"></a>Krok 1

Spustit `azure vm extension set` rutiny pro instalaci agenta zachytávání paketů na virtuálním počítači hosta.

Pro virtuální počítače s Windows:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

Pro virtuální počítače Linux:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>Krok 2

Chcete-li zajistit, že je agent nainstalovaný, spusťte `vm extension get` rutiny a předejte ji názvu prostředku skupiny a virtuální počítač. Zkontrolujte výsledného seznamu ujistěte se, že je agent nainstalován.

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

Následující příklad je příklad odpovědi spuštění `azure vm extension get`

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                        Version  State
data:    ------------------------------  -----------------------     -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentWindows  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>Spustit zachytávání paketů

Po dokončení předchozích kroků se agent zachytávání paketů je nainstalován na virtuálním počítači.

### <a name="step-1"></a>Krok 1

Dalším krokem je pro získání instance sledovací proces sítě. Tato proměnná je předána `network watcher show` rutiny v kroku 4.

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>Krok 2

Načtěte účet úložiště. Tento účet úložiště se používá k uložení souboru zachytávání paketů.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Krok 3

Chcete-li omezit data, která je uložená ve zachytáváním paketů použít filtry. Následující příklad nastaví zachytáváním paketů s několika filtry.  První tři filtry shromažďovat odchozí přenosy TCP pouze z místní IP 10.0.0.3 do cílové porty 20, 80 a 443.  Poslední filtr shromažďuje jenom provoz UDP.

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Více filtrů lze definovat pro zachytávání paketů. Pokud používáte struktura složitějších filtrů, je vhodnější použít filtry jako soubor json, aby se zabránilo chyby syntaxe. Například použijte příznak "-r" (místo "-f") a předejte umístění souboru json, který obsahuje následující filtry:

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


V následujícím příkladu je očekávaný výstup spuštění `network watcher packet-capture create` rutiny.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>Získat zachytávání paketů

Spuštění `network watcher packet-capture show` rutina načte stav paketu zachycení aktuálně spuštěné, nebo pokud byla dokončena.

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

V následujícím příkladu je výstup z `network watcher packet-capture show` rutiny. V následujícím příkladu je po dokončení zachytávání. Hodnota PacketCaptureStatus je zastavena, s StopReason TimeExceeded. Tato hodnota ukazuje, že zachytáváním paketů byla úspěšná a jeho spuštění.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>Zastavit zachytávání paketů

Spuštěním `network watcher packet-capture stop` rutiny, pokud zachycení relace je v průběhu je zastavena.

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> Žádná odpověď vrátí rutina při spuštěné v relaci aktuálně spuštěné zachycení nebo existující relaci, která již byla zastavena.

## <a name="delete-a-packet-capture"></a>Odstranit zachytávání paketů

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> Odstraňuje se zachytáváním paketů nedojde k odstranění souboru v účtu úložiště.

## <a name="download-a-packet-capture"></a>Stáhnout zachytáváním paketů

Po dokončení relace zachytávání paketů můžete zaznamenat soubor odeslat do úložiště objektů blob nebo do místního souboru virtuálního počítače. Umístění úložiště pro zachytávání paketů se definuje při vytvoření relace. Nástroj vhodné pro přístup k těmto zachycení soubory uložené na účet úložiště je Microsoft Azure Storage Explorer, kterou můžete stáhnout tady:  http://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory zachytávání paketů ukládají na účet úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Další postup

Informace o automatizaci paketu zachytává se virtuální počítač výstrahy zobrazením [vytvořit zaznamenání výstrahy spouštěná paketu](network-watcher-alert-triggered-packet-capture.md)

Najít, pokud určité provoz je povolený v nebo z virtuálního počítače navštivte stránky [zkontrolujte IP tok ověření](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
