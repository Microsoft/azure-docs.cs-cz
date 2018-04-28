---
title: Spravovat zachycení paketů s sledovací proces sítě Azure – prostředí PowerShell | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat funkci zachycení paketu sledovací proces sítě pomocí prostředí PowerShell
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
ms.openlocfilehash: 6ffb1aec91899b54a153e264e346910caee84cc0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Spravovat zachycení paketů s sledovací proces sítě Azure pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Rozhraní API Azure REST](network-watcher-packet-capture-manage-rest.md)

Zachytáváním paketů sledovací proces sítě vám umožní vytvořit relace zachytávání sledovat provoz do a z virtuálního počítače. Filtry jsou k dispozici pro relaci zachytávání zajistit, že zaznamenáte pouze provoz, který chcete. Při diagnostice sítě anomálií reaktivně a proaktivně pomáhá zachytáváním paketů. Jiné účely zahrnují shromažďování statistiku sítě, získá informace o síti vniknutí, k ladění komunikaci klienta se serverem a mnoho dalšího. Díky vzdáleně aktivovat paketu zachycení, tato funkce snižuje zátěž spuštěných zachytáváním paketů ručně a na požadované počítače, který úspora času.

Tento článek vás provede úloh jiný správy, které jsou aktuálně dostupné pro zachytávání paketů.

- [**Spustit zachytávání paketů**](#start-a-packet-capture)
- [**Zastavit zachytávání paketů**](#stop-a-packet-capture)
- [**Odstranit zachytávání paketů**](#delete-a-packet-capture)
- [**Stáhnout zachytáváním paketů**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte v následujících zdrojích informací:

* Instance sledovací proces sítě v oblasti, kterou chcete vytvořit zachytávání paketů

* Virtuální počítač s příponou zachytávání paketů povoleno.

> [!IMPORTANT]
> Rozšíření virtuálního počítače vyžaduje zachytáváním paketů `AzureNetworkWatcherExtension`. Instalaci rozšíření na virtuální počítač s Windows najdete v článku [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Windows](../virtual-machines/windows/extensions-nwa.md) a u virtuálního počítače s Linuxem, navštivte [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalace rozšíření virtuálního počítače

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzureRmVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

Následující příklad načte informace o rozšíření, které jsou potřebné ke spuštění `Set-AzureRmVMExtension` rutiny. Tato rutina nainstaluje agenta zachytávání paketů na virtuálním počítači hosta.

> [!NOTE]
> `Set-AzureRmVMExtension` Rutiny může trvat několik minut na dokončení.

Pro virtuální počítače s Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Pro virtuální počítače Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
````

Následující příklad je úspěšné odpovědi po spuštění `Set-AzureRmVMExtension` rutiny.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Chcete-li zajistit, že je agent nainstalovaný, spusťte `Get-AzureRmVMExtension` rutiny a předejte ji název virtuálního počítače a název rozšíření.

```powershell
Get-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Následující příklad je příklad odpovědi spuštění `Get-AzureRmVMExtension`

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

Po dokončení předchozích kroků se agent zachytávání paketů je nainstalován na virtuálním počítači.

### <a name="step-1"></a>Krok 1

Dalším krokem je pro získání instance sledovací proces sítě. Tato proměnná je předána `New-AzureRmNetworkWatcherPacketCapture` rutiny v kroku 4.

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

Chcete-li omezit data, která je uložená ve zachytáváním paketů použít filtry. Následující příklad nastaví dva filtry.  Jeden filtr shromažďuje odchozí přenosy TCP pouze z místní IP 10.0.0.3 do cílové porty 20, 80 a 443.  Druhý filtr shromažďuje jenom provoz UDP.

```powershell
$filter1 = New-AzureRmPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzureRmPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Více filtrů lze definovat pro zachytávání paketů.

### <a name="step-4"></a>Krok 4

Spustit `New-AzureRmNetworkWatcherPacketCapture` rutiny spustit proces zachycení paketu předávání požadované hodnoty načíst v předchozích krocích.
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

Spuštění `Get-AzureRmNetworkWatcherPacketCapture` rutina načte stav paketu zachycení aktuálně spuštěné, nebo pokud byla dokončena.

```powershell
Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

V následujícím příkladu je výstup z `Get-AzureRmNetworkWatcherPacketCapture` rutiny. V následujícím příkladu je po dokončení zachytávání. Hodnota PacketCaptureStatus je zastavena, s StopReason TimeExceeded. Tato hodnota ukazuje, že zachytáváním paketů byla úspěšná a jeho spuštění.
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

Spuštěním `Stop-AzureRmNetworkWatcherPacketCapture` rutiny, pokud zachycení relace je v průběhu je zastavena.

```powershell
Stop-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Žádná odpověď vrátí rutina při spuštěné v relaci aktuálně spuštěné zachycení nebo existující relaci, která již byla zastavena.

## <a name="delete-a-packet-capture"></a>Odstranit zachytávání paketů

```powershell
Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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

Najít, pokud určité provoz je povolený v orr mimo váš virtuální počítač navštivte stránky [zkontrolujte IP tok ověření](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->














