---
title: Rozšíření ovladače GPU NVIDIA – virtuální počítače Azure pro Windows
description: Rozšíření Microsoft Azure pro instalaci ovladačů GPU NVIDIA na výpočetní virtuální počítače řady N se systémem Windows
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250540"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Rozšíření ovladače GPU NVIDIA pro Windows

## <a name="overview"></a>Přehled

Toto rozšíření nainstaluje ovladače NVIDIA GPU na virtuální chod windows n-series. V závislosti na rodině virtuálních počítačů rozšíření nainstaluje ovladače CUDA nebo GRID. Při instalaci ovladačů NVIDIA pomocí tohoto rozšíření přijímáte a souhlasíte s podmínkami [licenční smlouvy s koncovým uživatelem NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Během procesu instalace může virtuální počítač restartovat k dokončení nastavení ovladače.

Pokyny k ruční instalaci ovladačů a aktuálních podporovaných verzí jsou k dispozici [zde](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup).
Rozšíření je také k dispozici pro instalaci ovladačů NVIDIA GPU na [Linux N-series VMs](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující operační týmy:

| Distribuce | Version |
|---|---|
| Windows 10 | Jádro |
| Windows Server 2016 | Jádro |
| Windows Server 2012 R2 | Jádro |

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Microsoft Azure pro ovladače GPU NVIDIA vyžaduje, aby cílový virtuální počítač byl připojený k internetu a měl přístup.

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON ukazuje schéma rozšíření.

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Vlastnosti

| Name (Název) | Hodnota / Příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| vydavatel | Microsoft.HpcCompute | řetězec |
| type | NvidiaGpuDriverWindows | řetězec |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Nasazení

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru 

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Šablony jsou ideální při nasazování jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení.

Konfigurace JSON pro rozšíření virtuálního počítače může být vnořena uvnitř prostředku virtuálního počítače nebo umístěna na kořenové nebo nejvyšší úrovni šablony JSON správce prostředků. Umístění konfigurace JSON ovlivňuje hodnotu názvu a typu prostředku. Další informace naleznete v tématu [Nastavení názvu a typu podřízených prostředků](../../azure-resource-manager/resource-manager-template-child-resource.md). 

Následující příklad předpokládá, že rozšíření je vnořené uvnitř prostředku virtuálního počítače. Při vnoření prostředku rozšíření JSON je `"resources": []` umístěn v objektu virtuálního počítače.

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
    "type": "NvidiaGpuDriverWindows",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z webu Azure Portal a pomocí Azure PowerShell a Azure CLI. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítače, spusťte následující příkaz.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření je zaznamenán do následujícího adresáře:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 0 | Operace byla úspěšná. |
| 1 | Operace byla úspěšná. Je nutné restartovat počítač. |
| 100 | Operace není podporována nebo nelze dokončit. | Možné příčiny: PowerShell verze není podporována, velikost virtuálního počítače není N-series vs, Selhání stahování dat. Zkontrolujte soubory protokolu a zjistěte příčinu chyby. |
| 240, 840 | Časový čas operace. | Opakujte operaci. |
| -1 | Došlo k výjimce. | Zkontrolujte soubory protokolu a zjistěte příčinu výjimky. |
| -5x | Operace byla přerušena z důvodu čekajícího restartování. | Restartování virtuálního počítače. Instalace bude pokračovat i po restartování počítače. Odinstalace by měla být vyvolána ručně. |


### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/community/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních najdete [v tématu Rozšíření a funkce virtuálních strojů pro Windows](features-windows.md).

Další informace o virtuálních počítačích řady N najdete v [tématu velikosti virtuálních strojů optimalizované pro GPU](../windows/sizes-gpu.md).
