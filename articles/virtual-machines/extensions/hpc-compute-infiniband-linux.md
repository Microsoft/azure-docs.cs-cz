---
title: Rozšíření ovladače InfiniBand – virtuální počítače Azure Linux
description: Microsoft Azure rozšíření pro instalaci ovladačů InfiniBand na výpočetní virtuální počítače H-a N-Series se systémem Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 1219f5befb4de81850681a53c10f03dc81249d53
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559727"
---
# <a name="infiniband-driver-extension-for-linux"></a>Rozšíření ovladače InfiniBand pro Linux

Toto rozšíření nainstaluje ovladače InfiniBand OFED na InfiniBand a virtuální počítače s podporou SR-IOV (velikosti r) [H-Series](../sizes-hpc.md) a [N-Series](../sizes-gpu.md) s operačním systémem Linux. V závislosti na rodině virtuálních počítačů rozšíření nainstaluje příslušné ovladače pro síťovou kartu Connect-X.

Pokyny k ruční instalaci ovladačů OFED jsou k dispozici [zde](../workloads/hpc/enable-infiniband.md#manual-installation).

K dispozici je také rozšíření pro instalaci ovladačů InfiniBand pro [virtuální počítače s Windows](hpc-compute-infiniband-windows.md).

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující distribuce operačního systému, v závislosti na podpoře ovladačů pro konkrétní verzi operačního systému.

| Distribuce | Verze |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS, 20,04 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Vlastnosti

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| vydavatel | Microsoft. HpcCompute | řetězec |
| typ | InfiniBandDriverLinux | řetězec |
| typeHandlerVersion | 1.1 | int |



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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Přidat rozšíření do sady škálování virtuálních počítačů

Následující příklad nainstaluje nejnovější InfiniBandDriverLinux rozšíření verze 1,1 na všechny virtuální počítače podporující RDMA ve stávající sadě virtuálních počítačů s názvem *myVMSS* nasazené ve skupině prostředků s názvem *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
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

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Ukončovací kódy

Následující tabulka popisuje význam a doporučenou akci na základě ukončovacích kódů procesu instalace rozšíření.

| Ukončovací kód | Význam | Možná akce |
| :---: | --- | --- |
| 0 | Operace byla úspěšná. |
| 1 | Nesprávné použití rozšíření | Kontrolovat výstupní protokol spuštění |
| 10 | Integrační služby Linux pro Hyper-V a Azure nejsou dostupné nebo nainstalované | Kontrolovat výstup lspci |
| 11 | Mellanox InfiniBand se v této velikosti virtuálního počítače nenašel. | Použít [podporovanou velikost virtuálního počítače a operační systém](../sizes-hpc.md) |
| 12 | Nabídka Image není podporovaná. |
| 13 | Velikost virtuálního počítače se nepodporuje. | Použití virtuálního počítače s podporou InfiniBand (r-Size) [H-](../sizes-hpc.md) Series a [n](../sizes-gpu.md)-Series n-Series pro nasazení |
| 14 | Operace neproběhla úspěšně. | Kontrolovat výstupní protokol spuštění |


### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). Alternativně můžete podávat incidenty podpory prostřednictvím [webu podpory Azure](https://azure.microsoft.com/support/options/). Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o povolených InfiniBand (velikosti r) najdete v tématu virtuální počítače řady [H-Series](../sizes-hpc.md) a [N-Series](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Další informace o rozšířeních a funkcích virtuálních počítačů se systémem Linux](features-linux.md)