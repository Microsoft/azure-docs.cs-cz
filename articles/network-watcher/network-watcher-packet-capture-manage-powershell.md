---
title: Správa zachycení paketů – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat funkci zachytávání paketů Network Watcher pomocí prostředí PowerShell.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 100a8ed1987b2edbc0aea1708c8a60b48bf391b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84737916"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Správa zachytávání paketů pomocí Azure Network Watcher pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher Capture paketů umožňuje vytvářet relace zachycení pro sledování provozu do a z virtuálního počítače. K dispozici jsou filtry pro relaci zachycení, aby bylo možné zachytit pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě interaktivně a aktivně. Mezi další použití patří shromažďování statistik sítě a získání informací o neoprávněných vniknutích k síti, k ladění komunikace mezi klientem a serverem a mnohem více. Díky tomu, že je možné vzdáleně aktivovat zachycení paketů, tato schopnost usnadňuje zátěžové zachycení paketů ručně a na požadovaném počítači, který šetří cenný čas.

Tento článek vás provede různými úlohami správy, které jsou aktuálně k dispozici pro zachytávání paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavení zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranění zachytávání paketů**](#delete-a-packet-capture)
- [**Stažení zachytávání paketů**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte následující zdroje:

* Instance Network Watcher v oblasti, ve které chcete vytvořit zachytávání paketů

* Virtuální počítač s povoleným rozšířením zachytávání paketů.

> [!IMPORTANT]
> Zachytávání paketů vyžaduje rozšíření virtuálního počítače `AzureNetworkWatcherExtension` . Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, přejděte na web [azure Network Watcher Agent Virtual Machine Extension for Windows](../virtual-machines/windows/extensions-nwa.md) a pro Linux VM, navštivte [rozšíření Azure Network Watcher Agent Virtual Machine pro Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálních počítačů

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

Následující příklad načte informace o rozšíření potřebné ke spuštění `Set-AzVMExtension` rutiny. Tato rutina nainstaluje agenta zachytávání paketů do hostovaného virtuálního počítače.

> [!NOTE]
> `Set-AzVMExtension`Dokončení rutiny může trvat několik minut.

Pro virtuální počítače s Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Pro virtuální počítače se systémem Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Následující příklad je úspěšná odpověď po spuštění `Set-AzVMExtension` rutiny.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Abyste měli jistotu, že je agent nainstalovaný, spusťte `Get-AzVMExtension` rutinu a předejte jí název virtuálního počítače a název rozšíření.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Následující ukázka je příkladem reakce na spuštění `Get-AzVMExtension`

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

## <a name="start-a-packet-capture"></a>Spustit zachytávání paketů

Po dokončení předchozích kroků se na virtuálním počítači nainstaluje agent zachytávání paketů.

### <a name="step-1"></a>Krok 1

Dalším krokem je načtení instance Network Watcher. Tato proměnná je předána `New-AzNetworkWatcherPacketCapture` rutině v kroku 4.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Krok 2

Načtěte účet úložiště. Tento účet úložiště se používá k uložení souboru zachycení paketů.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Krok 3

Filtry lze použít k omezení dat uložených zachytáváním paketů. Následující příklad nastaví dva filtry.  Jeden filtr shromažďuje odchozí přenosy TCP pouze z místních IP 10.0.0.3 na cílové porty 20, 80 a 443.  Druhý filtr shromažďuje jenom přenosy UDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Pro zachytávání paketů lze definovat více filtrů.

### <a name="step-4"></a>Krok 4

Spuštěním `New-AzNetworkWatcherPacketCapture` rutiny spusťte proces zachytávání paketů a předejte požadované hodnoty načtené v předchozích krocích.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Následující příklad je očekávaný výstup z běhu `New-AzNetworkWatcherPacketCapture` rutiny.

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

## <a name="get-a-packet-capture"></a>Získání zachytávání paketů

Spuštěním `Get-AzNetworkWatcherPacketCapture` rutiny se načte stav aktuálně spuštěného nebo dokončeného zachytávání paketů.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Následující příklad je výstupem z `Get-AzNetworkWatcherPacketCapture` rutiny. Následující příklad je po dokončení zachycení. Hodnota PacketCaptureStatus je zastavena s důvoduZastavení TimeExceeded. Tato hodnota ukazuje, že zachycení paketů bylo úspěšné a běželo v čase.
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

## <a name="stop-a-packet-capture"></a>Zastavení zachytávání paketů

Spuštěním `Stop-AzNetworkWatcherPacketCapture` rutiny, pokud probíhá relace zachycení, je zastavena.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Rutina nevrátí žádnou odpověď, pokud byla spuštěna v aktuálně spuštěné relaci zachycení nebo existující relaci, která je již zastavena.

## <a name="delete-a-packet-capture"></a>Odstranění zachytávání paketů

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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














