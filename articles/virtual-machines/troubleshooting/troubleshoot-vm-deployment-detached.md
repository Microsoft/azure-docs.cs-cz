---
title: Poradce při potížích s nasazením virtuálního počítače z důvodu odpojených disků | Dokumenty společnosti Microsoft
description: Poradce při potížích s nasazením virtuálního počítače kvůli odpojené disky
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486817"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>Poradce při potížích s nasazením virtuálního počítače kvůli odpojené disky

## <a name="symptom"></a>Příznak

Při pokusu o aktualizaci virtuálního počítače, jehož předchozí odpojení datového disku se nezdařilo, můžete narazit na tento kód chyby.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Příčina

K této chybě dojde, když se pokusíte znovu připojit datový disk, jehož poslední operace odpojení se nezdařila. Nejlepší způsob, jak se dostat z tohoto stavu, je odpojit selhávající disk.

## <a name="solution-1-powershell"></a>Řešení 1: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>Krok 1: Získání podrobností o virtuálním počítači a disku

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Krok 2: Nastavte příznak pro selhání disky na "true".

Získejte index pole selhání disku a nastavte příznak **BeDetached** pro selhání disku (pro které Došlo k chybě **AttachDiskWhileBeingDetached)** na "true". Toto nastavení znamená odpojení disku od virtuálního počítače. Název disku se selháním naleznete v **errorMessage**.

> ! Poznámka: Verze rozhraní API určená pro volání Get and Put musí být 2019-03-01 nebo vyšší.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

Alternativně můžete také odpojit tento disk pomocí níže uvedeného příkazu, který bude užitečný pro uživatele, kteří používají verze ROZHRANÍ API před březnem 01, 2019.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>Krok 3: Aktualizace virtuálního počítače

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>Řešení 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>Krok 1: Získejte datové zatížení virtuálního počítače.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Krok 2: Nastavte příznak pro selhání disky na "true".

Nastavte příznak **toBeDetached** pro selhání disku na hodnotu true v datové části vrácené v kroku 1. Poznámka: Verze rozhraní API určená pro volání `2019-03-01` Get and Put musí být nebo vyšší.

**Tělo požadavku vzorku**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Alternativně můžete také odstranit selhávající datový disk z výše uvedené datové části, což je užitečné pro uživatele, kteří používají verze ROZHRANÍ API před březnem 01, 2019.

### <a name="step-3-update-the-virtual-machine"></a>Krok 3: Aktualizace virtuálního počítače

Použijte datovou část těla požadavku nastavenou v kroku 2 a aktualizujte virtuální počítač následujícím způsobem:

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Odpověď vzorku:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k virtuálnímu počítači, [přečtěte si článek Poradce při potížích s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md).

Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete [v tématu Řešení problémů s připojením aplikací na virtuálním počítači se systémem Windows](troubleshoot-app-connection.md).
