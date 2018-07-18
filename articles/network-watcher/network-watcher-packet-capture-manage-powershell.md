---
title: Správa zachytávání paketů pomocí služby Azure Network Watcher – PowerShell | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak spravovat funkce zachytávání paketů služby Network Watcher pomocí Powershellu
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: d13c02696c9babae9fd04233ae7d2fdc75fca25f
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090674"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Správa zachytávání paketů pomocí služby Azure Network Watcher pomocí Powershellu

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Rozhraní Azure REST API](network-watcher-packet-capture-manage-rest.md)

Zachytávání paketů Network Watcher umožňuje vytvořit relace zachycení sledujte provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relace zachytávání zajistit, že zachytíte jenom provoz, který chcete. Zachytávání paketů pomáhá diagnostikovat sítě anomálie reaktivně a proaktivně. Mezi další použití patří shromažďování statistických údajů sítě získat informace o síti vniknutí, chcete-li ladit komunikaci klienta se serverem a spoustu dalších věcí. Díky možnosti vzdáleně spustit zachytávání paketů, tato funkce usnadňuje si museli dělat starosti s zachycení paketů ručně a na požadovaný počítač, což šetří cenný čas.

Tento článek vás provede jiné úlohy, které jsou aktuálně k dispozici pro zachycení paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavit zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranit zachycení paketů**](#delete-a-packet-capture)
- [**Stáhněte si zachytávání paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující prostředky:

* Instance služby Network Watcher v oblasti, kterou chcete vytvořit zachytávání paketů

* Virtuální počítač s příponou zachycení paketů povoleno.

> [!IMPORTANT]
> Zachytávání paketů vyžaduje rozšíření virtuálního počítače `AzureNetworkWatcherExtension`. Instalaci rozšíření na virtuálním počítači s Windows najdete [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md) a pro virtuální počítač s Linuxem, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálních počítačů

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzureRmVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

Následující příklad načte rozšíření informací potřebných ke spuštění `Set-AzureRmVMExtension` rutiny. Tato rutina nainstaluje agenta zachytávání paketů na virtuálním počítači hosta.

> [!NOTE]
> `Set-AzureRmVMExtension` Rutiny může trvat několik minut.

Pro virtuální počítače s Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Pro virtuální počítače s Linuxem:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
````

V následujícím příkladu se po spuštění úspěšné odpovědi `Set-AzureRmVMExtension` rutiny.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Chcete-li zajistit, že je agent nainstalovaný, spusťte `Get-AzureRmVMExtension` rutiny a předejte mu název virtuálního počítače a název rozšíření.

```powershell
Get-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Následující příklad je příkladem odpověď od spuštění `Get-AzureRmVMExtension`

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

Po dokončení předchozích kroků je nainstalován agent zachytávání paketů na virtuálním počítači.

### <a name="step-1"></a>Krok 1

Dalším krokem je načtení instance Network Watcheru. Tato proměnná je předána `New-AzureRmNetworkWatcherPacketCapture` rutiny v kroku 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>Krok 2

Načtěte účet úložiště. Tento účet úložiště se používá k uložení souboru zachytávání paketů.

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Krok 3

Filtry je možné omezit data, která se ukládá pomocí zachytávání paketů. Následující příklad nastaví dva filtry.  Jeden filtr shromažďuje odchozí provoz TCP jenom z IP adresy místní 10.0.0.3 do cílové porty 20, 80 a 443.  Druhý filtr shromažďuje pouze provoz UDP.

```powershell
$filter1 = New-AzureRmPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzureRmPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Pro zachytávání paketů je možné definovat více filtrů.

### <a name="step-4"></a>Krok 4

Spustit `New-AzureRmNetworkWatcherPacketCapture` ke spuštění procesu zachycení paketu předáním požadované hodnoty načíst v předchozích krocích.
```powershell

New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

V následujícím příkladu je očekávaný výstup spuštění `New-AzureRmNetworkWatcherPacketCapture` rutiny.

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

## <a name="get-a-packet-capture"></a>Získat zachytávání paketů

Spuštění `Get-AzureRmNetworkWatcherPacketCapture` rutiny, načítá stav aktuálně spuštěné nebo dokončené zachytávání paketů.

```powershell
Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

V následujícím příkladu je výstup `Get-AzureRmNetworkWatcherPacketCapture` rutiny. V následujícím příkladu se po dokončení operace zachycení se. Hodnota PacketCaptureStatus zastavena s Důvoduzastavení TimeExceeded. Tato hodnota ukazuje, že zachytávání paketů byla úspěšná a jeho spuštění.
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

## <a name="stop-a-packet-capture"></a>Zastavit zachytávání paketů

Spuštěním `Stop-AzureRmNetworkWatcherPacketCapture` rutiny, pokud relace zachytávání se v průběhu je zastavená.

```powershell
Stop-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Žádná odpověď vrátí rutina při spuštěné v probíhající relace zachytávání nebo existující relaci, která již byla zastavena.

## <a name="delete-a-packet-capture"></a>Odstranit zachycení paketů

```powershell
Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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

Pokud určitá provoz je povolený v orr mimo váš virtuální počítač si najít [ověření toku protokolu IP zkontrolujte](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->














