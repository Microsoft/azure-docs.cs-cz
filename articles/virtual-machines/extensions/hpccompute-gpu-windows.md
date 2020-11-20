---
title: Rozšíření ovladače NVIDIA GPU – virtuální počítače Azure s Windows
description: Microsoft Azure rozšíření pro instalaci ovladačů NVIDIA GPU na výpočetní virtuální počítače řady N-Series s Windows
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: 82ed1d57d72a4479005d8bfd2234c124a97096ee
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965982"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Rozšíření ovladače NVIDIA GPU pro Windows

## <a name="overview"></a>Přehled

Toto rozšíření nainstaluje ovladače NVIDIA GPU na virtuální počítače řady Windows N-Series. V závislosti na rodině virtuálních počítačů rozšíření nainstaluje ovladače CUDA nebo GRIDu. Když instalujete ovladače NVIDIA pomocí tohoto rozšíření, přijímáte a souhlasíte s podmínkami [licenční smlouvy NVIDIA End-User](https://go.microsoft.com/fwlink/?linkid=874330). Během procesu instalace může být virtuální počítač restartován, aby bylo možné dokončit instalaci ovladače.

Pokyny k ruční instalaci ovladačů a aktuálně podporované verze jsou k dispozici [zde](../windows/n-series-driver-setup.md).
K dispozici je také rozšíření pro instalaci ovladačů NVIDIA GPU pro [virtuální počítače řady Linux N-Series](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující OSs:

| Distribuce | Verze |
|---|---|
| Windows 10 | Jádro |
| Windows Server 2016 | Jádro |
| Windows Server 2012 R2 | Jádro |

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Microsoft Azure pro ovladače GPU NVIDIA vyžaduje, aby byl cílový virtuální počítač připojený k Internetu a měl přístup.

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.3",
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
| typ | NvidiaGpuDriverWindows | řetězec |
| typeHandlerVersion | 1.3 | int |


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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.3",
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
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
  }'
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

Výstup spuštění rozšíření je protokolován do následujícího adresáře:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\
```

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 0 | Operace byla úspěšná. |
| 1 | Operace byla úspěšná. Je vyžadován restart. |
| 100 | Operace není podporována nebo ji nelze dokončit. | Možné příčiny: verze PowerShellu není podporovaná, velikost virtuálního počítače není virtuální počítač řady N-Series, při stahování dat došlo k chybě. Zkontrolujte soubory protokolu a určete příčinu chyby. |
| 240, 840 | Časový limit operace. | Zkuste operaci zopakovat. |
| -1 | Došlo k výjimce. | Zkontrolujte soubory protokolu a určete příčinu výjimky. |
| – pětinásobné | Operace byla přerušena z důvodu nedokončeného restartování. | Restartujte virtuální počítač. Po restartování bude instalace pokračovat. Odinstalaci je třeba vyvolat ručně. |


### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních najdete v tématu [rozšíření virtuálních počítačů a funkce pro Windows](features-windows.md).

Další informace o virtuálních počítačích řady N-Series najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../sizes-gpu.md).
