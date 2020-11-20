---
title: Aktualizace rozšíření Network Watcher na nejnovější verzi
description: Přečtěte si, jak aktualizovat rozšíření Azure Network Watcher na nejnovější verzi.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c427a206e0422e66cb526a29a462d8b6bdf6818e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965931"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Aktualizace rozšíření Network Watcher na nejnovější verzi

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je služba pro sledování, diagnostiku a analýzu výkonu sítě, která monitoruje sítě Azure. Rozšíření virtuálních počítačů s Network Watcher agentem je požadavek na zachytávání síťového provozu na vyžádání a používání dalších pokročilých funkcí na virtuálních počítačích Azure. Rozšíření Network Watcher se používají funkcemi, jako je monitorování připojení, monitorování připojení (Preview), řešení potíží s připojením a zachytávání paketů.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte na svém VIRTUÁLNÍm počítači nainstalovanou příponu Network Watcher.

## <a name="latest-version"></a>Nejnovější verze

Nejnovější verze rozšíření Network Watcher je aktuálně `1.4.1654.1` .

## <a name="update-your-extension"></a>Aktualizace rozšíření

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

Pokud je vaše verze starší než verze `1.4.1654.1` , která je aktuální nejnovější verzí, aktualizujte rozšíření pomocí kterékoli z následujících možností.

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
