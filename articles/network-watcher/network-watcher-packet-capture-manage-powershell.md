---
title: Správa zachytávání paketů – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat funkci zachycení paketů sledovacího modulu sítě pomocí prostředí PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 06263f85f7d6ad6cc80724baab01124833498739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129649"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Správa zachytávání paketů pomocí sledování sítě Azure pomocí Prostředí PowerShell

> [!div class="op_single_selector"]
> - [Portál Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Zachycení paketu Sledování sítě umožňuje vytvářet relace sběru pro sledování provozu do a z virtuálního počítače. Filtry jsou k dispozici pro relaci zachycení, které zajistí, že zachytíte pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě reaktivně i proaktivně. Mezi další použití patří shromažďování síťových statistik, získávání informací o síťových vniknutích, ladění komunikace mezi klientem a serverem a mnoho dalšího. Tím, že je schopna vzdáleně aktivovat zachycení paketů, tato schopnost zmírňuje zátěž ručního spuštění sběru paketů a na požadovaném počítači, což šetří cenný čas.

Tento článek vás provede různými úkoly správy, které jsou aktuálně k dispozici pro sběr paketů.

- [**Spuštění sběru paketů**](#start-a-packet-capture)
- [**Zastavení sběru paketů**](#stop-a-packet-capture)
- [**Odstranění zachycení paketu**](#delete-a-packet-capture)
- [**Stažení zachytávání paketů**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující zdroje:

* Instance sledování sítě v oblasti, kterou chcete vytvořit sběr paketů

* Virtuální počítač s povoleným rozšířením pro sběr paketů.

> [!IMPORTANT]
> Zachytávání paketů `AzureNetworkWatcherExtension`vyžaduje rozšíření virtuálního počítače . Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md) a pro virtuální počítač SIP na stránce Rozšíření [virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálních počítačů

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

Následující příklad načte informace o rozšíření `Set-AzVMExtension` potřebné ke spuštění rutiny. Tato rutina nainstaluje agenta pro sběr paketů do hostovaného virtuálního počítače.

> [!NOTE]
> Dokončení `Set-AzVMExtension` rutiny může trvat několik minut.

Pro virtuální počítače s Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Pro linuxové virtuální počítače:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Následující příklad je úspěšná odpověď `Set-AzVMExtension` po spuštění rutiny.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Chcete-li zajistit, že je `Get-AzVMExtension` agent nainstalován, spusťte rutinu a předat název virtuálního počítače a název rozšíření.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Následující ukázka je příkladem odpovědi z běhu`Get-AzVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Spuštění sběru paketů

Po dokončení předchozích kroků je agent pro sběr paketů nainstalován ve virtuálním počítači.

### <a name="step-1"></a>Krok 1

Dalším krokem je načtení instance Sledování sítě. Tato proměnná je `New-AzNetworkWatcherPacketCapture` předána rutině v kroku 4.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Krok 2

Načíst účet úložiště. Tento účet úložiště se používá k uložení souboru sběru paketů.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Krok 3

Filtry lze použít k omezení dat, která jsou uložena při sběru paketů. Následující příklad nastaví dva filtry.  Jeden filtr shromažďuje odchozí přenosy Protokolu TCP pouze z místní ip adresy 10.0.0.3 do cílových portů 20, 80 a 443.  Druhý filtr shromažďuje pouze přenosy UDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Pro sběr paketů lze definovat více filtrů.

### <a name="step-4"></a>Krok 4

Spusťte rutinu `New-AzNetworkWatcherPacketCapture` a spusťte proces sběru paketů a předavěte požadované hodnoty načtené v předchozích krocích.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Následující příklad je očekávaný výstup `New-AzNetworkWatcherPacketCapture` ze spuštění rutiny.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Získání zachycení paketu

Spuštěním `Get-AzNetworkWatcherPacketCapture` rutiny načtete stav aktuálně spuštěného nebo dokončeného sběru paketů.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Následující příklad je výstup `Get-AzNetworkWatcherPacketCapture` z rutiny. Následující příklad je po dokončení sběru. Hodnota PacketCaptureStatus je zastavena s hodnotou StopReason of TimeExceeded. Tato hodnota ukazuje, že zachycení paketu bylo úspěšné a běžel jeho čas.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Zastavení sběru paketů

Spuštěním `Stop-AzNetworkWatcherPacketCapture` rutiny, pokud probíhá relace sběru, je zastavena.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Rutina vrátí žádnou odpověď při spuštění v aktuálně spuštěné relaci sběru nebo existující relaci, která již byla zastavena.

## <a name="delete-a-packet-capture"></a>Odstranění zachycení paketu

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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














