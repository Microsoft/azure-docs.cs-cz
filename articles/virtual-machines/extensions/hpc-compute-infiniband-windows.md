---
title: Rozšíření ovladače InfiniBand – virtuální počítače Azure s Windows
description: Microsoft Azure rozšíření pro instalaci ovladačů InfiniBand ve výpočetních virtuálních počítačích H a N-Series se systémem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: c0ec18ae4a7d6020299660adbeba6f993cd4eeca
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966016"
---
# <a name="infiniband-driver-extension-for-windows"></a>Rozšíření ovladače InfiniBand pro Windows

Toto rozšíření nainstaluje ovladače InfiniBand ND (pro povolený non SR-IOV) a ovladače OFED (pro virtuální počítače s podporou SR-IOV) a [N-Series](../sizes-gpu.md) [s Windows](../sizes-hpc.md) . V závislosti na rodině virtuálních počítačů rozšíření nainstaluje příslušné ovladače pro síťovou kartu Connect-X.

K dispozici je také rozšíření pro instalaci ovladačů InfiniBand pro [virtuální počítače se systémem Linux](hpc-compute-infiniband-linux.md).

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující distribuce operačního systému, v závislosti na podpoře ovladačů pro konkrétní verzi operačního systému.

| Distribuce | Verze |
|---|---|
| Windows 10 | Jádro |
| Windows Server 2019 | Jádro |
| Windows Server 2016 | Jádro |
| Windows Server 2012 R2 | Jádro |
| Windows Server 2012 | Jádro |

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Microsoft Azure pro ovladače InfiniBand vyžaduje, aby byl cílový virtuální počítač připojený k Internetu a měl k němu přístup.

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Vlastnosti

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| vydavatel | Microsoft. HpcCompute | řetězec |
| typ | InfiniBandDriverWindows | řetězec |
| typeHandlerVersion | 1,2 | int |



## <a name="deployment"></a>Nasazení


### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru 

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení.

Konfiguraci JSON pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění konfigurace JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu [Nastavení názvu a typu pro podřízené prostředky](../../azure-resource-manager/templates/child-resource-name-type.md). 

Následující příklad předpokládá, že rozšíření je vnořeno do prostředku virtuálního počítače. Při vnořování prostředku rozšíření je kód JSON umístěný v `"resources": []` objektu virtuálního počítače.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Přidat rozšíření do sady škálování virtuálních počítačů

Následující příklad nainstaluje nejnovější InfiniBandDriverWindows rozšíření verze 1,2 na všechny virtuální počítače podporující RDMA ve stávající sadě virtuálních počítačů s názvem *myVMSS* nasazené ve skupině prostředků s názvem *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí Azure PowerShell a Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření je protokolován do následujícího souboru. Pokud chcete sledovat stav instalace a vyřešit případné selhání, přečtěte si tento soubor.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Ukončovací kódy

Následující tabulka popisuje význam a doporučenou akci na základě ukončovacích kódů procesu instalace rozšíření.

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 0 | Operace byla úspěšná. |
| 3010 | Operace byla úspěšná. Je vyžadován restart. |
| 100 | Operace není podporována nebo ji nelze dokončit. | Možné příčiny: verze PowerShellu není podporovaná, velikost virtuálního počítače není virtuální počítač s povolenou InfiniBand, při stahování dat došlo k chybě. Zkontrolujte soubory protokolu a určete příčinu chyby. |
| 240, 840 | Časový limit operace. | Zkuste operaci zopakovat. |
| -1 | Došlo k výjimce. | Zkontrolujte soubory protokolu a určete příčinu výjimky. |

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). Alternativně můžete podávat incidenty podpory prostřednictvím [webu podpory Azure](https://azure.microsoft.com/support/options/). Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o povolených InfiniBand (velikosti r) najdete v tématu virtuální počítače řady [H-Series](../sizes-hpc.md) a [N-Series](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Další informace o rozšířeních a funkcích virtuálních počítačů se systémem Linux](features-linux.md)