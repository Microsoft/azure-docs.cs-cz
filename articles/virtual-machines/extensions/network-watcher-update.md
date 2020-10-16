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
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: e367c348364d03cec6914c99e7ff112803fc58f6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132427"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Aktualizace rozšíření Network Watcher na nejnovější verzi

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je služba pro sledování, diagnostiku a analýzu výkonu sítě, která monitoruje sítě Azure. Rozšíření virtuálních počítačů s Network Watcher agentem je požadavek na zachytávání síťového provozu na vyžádání a používání dalších pokročilých funkcí na virtuálních počítačích Azure. Rozšíření Network Watcher se používají funkcemi, jako je monitorování připojení, monitorování připojení (Preview), řešení potíží s připojením a zachytávání paketů.

## <a name="prerequisites"></a>Předpoklady

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
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Ve výstupu Najděte rozšíření AzureNetworkWatcher. Identifikujte číslo verze ve výstupu pole TypeHandlerVersion.  

#### <a name="usepowershell"></a>Použití PowerShellu

Z příkazového řádku PowerShellu spusťte následující příkazy:

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Ve výstupu Najděte rozšíření AzureNetworkWatcher. Identifikujte číslo verze ve výstupu pole TypeHandlerVersion.

### <a name="update-your-extension"></a>Aktualizace rozšíření

Pokud je vaše verze starší než verze `1.4.1654.1` , která je aktuální nejnovější verzí, aktualizujte rozšíření pomocí kterékoli z následujících možností.

#### <a name="option-1-use-powershell"></a>Možnost 1: použití PowerShellu

Spusťte následující příkazy:

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
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