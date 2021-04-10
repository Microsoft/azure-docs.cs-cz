---
title: Aktualizace rozšíření Network Watcher na nejnovější verzi
description: Přečtěte si, jak aktualizovat rozšíření Azure Network Watcher na nejnovější verzi.
services: virtual-machines
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: 42efe2927b4d711f7fa66a96ebd25f1a62bf654a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563604"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Aktualizace rozšíření Network Watcher na nejnovější verzi

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je služba pro sledování, diagnostiku a analýzu výkonu sítě, která monitoruje sítě Azure. Rozšíření virtuálních počítačů s Network Watcher agentem je požadavek na zachytávání síťového provozu na vyžádání a používání dalších pokročilých funkcí na virtuálních počítačích Azure. Rozšíření Network Watcher se používají funkcemi, jako je monitorování připojení, monitorování připojení (Preview), řešení potíží s připojením a zachytávání paketů.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte na svém VIRTUÁLNÍm počítači nainstalovanou příponu Network Watcher.

## <a name="latest-version"></a>Nejnovější verze

Nejnovější verze rozšíření Network Watcher je aktuálně `1.4.1693.1` .

## <a name="update-your-extension-using-a-powershell-script"></a>Aktualizace rozšíření pomocí skriptu PowerShellu
Zákazníci s velkými nasazeními, kteří potřebují aktualizovat víc virtuálních počítačů najednou Pokud chcete ručně aktualizovat vybrané virtuální počítače, přečtěte si další část. 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1654.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>Ruční aktualizace rozšíření

Chcete-li aktualizovat rozšíření, je třeba znát verzi rozšíření.

### <a name="check-your-extension-version"></a>Ověřit verzi rozšíření

Verzi rozšíření můžete zjistit pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu.

#### <a name="usetheazureportal"></a>Použití Azure Portal

1. V Azure Portal otevřete podokno **rozšíření** vašeho virtuálního počítače.
1. Pokud chcete zobrazit podokno podrobností, vyberte rozšíření **AzureNetworkWatcher** .
1. V poli **verze** vyhledejte číslo verze.  

#### <a name="use-the-azure-cli"></a>Použití Azure CLI

Z příkazového řádku Azure CLI spusťte následující příkaz:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Ve výstupu vyhledejte **"AzureNetworkWatcherExtension"** a Identifikujte číslo verze z pole *"TypeHandlerVersion"* ve výstupu.  Poznámka: informace o rozšíření se zobrazí ve výstupu JSON víckrát. Podívejte se prosím na blok "přípony" a měli byste vidět plné číslo verze rozšíření. 

Měl by se zobrazit něco podobného jako na obrázku ![ Azure CLI snímek obrazovky.](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>Použití PowerShellu

Z příkazového řádku PowerShellu spusťte následující příkazy:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Ve výstupu Najděte rozšíření Azure Network Watcher a určete číslo verze z pole *TypeHandlerVersion* ve výstupu.   

Měl by se zobrazit něco jako v následujícím příkladu: ![ snímek obrazovky PowerShellu](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Aktualizace rozšíření

Pokud je vaše verze nižší než nejnovější výše uvedená verze, aktualizujte rozšíření pomocí kterékoli z následujících možností.

#### <a name="option-1-use-powershell"></a>Možnost 1: použití PowerShellu

Spusťte následující příkazy:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Pokud to nefunguje. Pomocí níže uvedeného postupu odeberte rozšíření a nainstalujte ho znovu. Tím se automaticky přidá nejnovější verze.

Odebrání rozšíření 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

Opětovné instalace rozšíření

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>Možnost 2: použití rozhraní příkazového řádku Azure

Vynutit upgrade.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Pokud to nepomůže, odeberte a nainstalujte rozšíření znovu a pomocí následujících kroků automaticky přidejte nejnovější verzi.

Odeberte rozšíření.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Nainstalujte znovu rozšíření.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Možnost 3: restartování virtuálních počítačů

Pokud jste pro rozšíření Network Watcher nastavili automatický upgrade na hodnotu true, restartujte instalaci virtuálního počítače do nejnovějšího rozšíření.

## <a name="support"></a>Podpora

Pokud potřebujete další pomoc v jakémkoli bodě tohoto článku, přečtěte si dokumentaci k rozšíření Network Watcher pro [Linux](./network-watcher-linux.md) nebo [Windows](./network-watcher-windows.md). Můžete se také obrátit na odborníky na Azure na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete také zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
