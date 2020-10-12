---
title: Aktualizovat rozšíření Network Watcher na nejnovější verzi
description: Naučte se aktualizovat rozšíření Network Watcher na nejnovější verzi.
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
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410313"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Jak aktualizovat rozšíření Network Watcher na nejnovější verzi 

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je služba pro sledování, diagnostiku a analýzu výkonu sítě, která umožňuje monitorování sítí Azure. Rozšíření virtuálního počítače Network Watcher agentem je požadavek na zachytávání síťového provozu na vyžádání a další pokročilé funkce na virtuálních počítačích Azure. Rozšíření Network Watcher se používají funkcemi, jako je monitorování připojení, monitorování připojení (Preview), řešení potíží s připojením a zachytávání paketů.   

## <a name="prerequisites"></a>Požadavky
V tomto dokumentu se předpokládá, že máte na svém virtuálním počítači nainstalovanou příponu Network Watcher a máte pokyny k její aktualizaci na nejnovější verzi. 

## <a name="latest-version"></a>Nejnovější verze
Nejnovější verze rozšíření Network Watcher je aktuálně `1.4.1654.1` .

## <a name="updating-your-extension"></a>Aktualizace rozšíření 

### <a name="check-your-extension-version"></a>Ověřit verzi rozšíření  

**Použití Azure Portal**

1. V Azure Portal otevřete okno rozšíření vašeho virtuálního počítače.   
2. Kliknutím na rozšíření AzureNetworkWatcher zobrazíte podokno podrobností.  
3. V poli Version (verze) vyhledejte číslo verze.  

Použití rozhraní příkazového **řádku Azure** Z příkazového řádku Azure CLI spusťte níže uvedený příkaz.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Ve výstupu Najděte rozšíření AzureNetworkWatcher a Identifikujte číslo verze z pole "TypeHandlerVersion" ve výstupu.  


**Použití PowerShellu** Z příkazového řádku PowerShellu spusťte následující příkazy:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Ve výstupu Najděte rozšíření AzureNetworkWatcher a Identifikujte číslo verze z pole "TypeHandlerVersion" ve výstupu.   


### <a name="update-your-extension"></a>Aktualizace rozšíření

V případě, že je verze nižší než `1.4.1654.1` (aktuální nejnovější verze), aktualizujte rozšíření pomocí kterékoli z následujících možností. 

**Možnost 1: použití PowerShellu**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Možnost 2: použití rozhraní příkazového řádku Azure**  

Vynutit upgrade 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Pokud to nefunguje. Pomocí níže uvedeného postupu odeberte rozšíření a nainstalujte ho znovu. Tím se automaticky přidá nejnovější verze. 

Odebrání rozšíření 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Opětovné instalace rozšíření

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Možnost 3: restartování virtuálních počítačů**

Pokud jste pro rozšíření NetworkWatcher nastavili automatický upgrade na hodnotu true. Restartování virtuálního počítače nainstalujete nejnovější rozšíření.


## <a name="support"></a>Podpora

Pokud potřebujete další pomoc kdykoli v tomto článku, můžete se podívat na dokumentaci k rozšíření Network Watcher ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux), [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)) nebo se obrátit na odborníky na Azure na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
