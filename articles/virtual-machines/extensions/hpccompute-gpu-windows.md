---
title: Rozšíření ovladače NVIDIA GPU – virtuální počítače s Windows v Azure | Dokumentace Microsoftu
description: Rozšíření Microsoft Azure k instalaci ovladačů NVIDIA GPU do řady N-series výpočetní virtuální počítače se systémem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: roiyz
ms.openlocfilehash: 5adc86b161770f2502b6ef9cf5ec2189ec3d4f99
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977622"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Grafický procesor NVIDIA ovladač rozšíření pro Windows

## <a name="overview"></a>Přehled

Toto rozšíření nainstaluje ovladačů NVIDIA GPU na virtuálních počítačích řady N-series s Windows. V závislosti na řadu virtuálních počítačů nainstaluje rozšíření ovladače CUDA nebo MŘÍŽKA. Při instalaci NVIDIA ovladače, které používají toto rozšíření přijímáte a vyjádření souhlasu s těmito podmínkami [licenční smlouva s koncovým uživatelem NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Virtuální počítač může během procesu instalace restartovat k dokončení instalace ovladačů.

Rozšíření je také dostupná k instalaci ovladačů NVIDIA GPU na [virtuální počítače s Linuxem řady N-series](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující OSs:

| Distribuce | Verze |
|---|---|
| Windows 10 (až do verze 1803)| Jádro |
| Windows Server 2016 | Jádro |
| Windows Server 2012R2 | Jádro |

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Microsoft Azure pro ovladače GPU NVIDIA vyžaduje, aby cílový virtuální počítač je připojený k Internetu a máte přístup.

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schématu pro rozšíření.

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

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| vydavatele | Microsoft.HpcCompute | řetězec |
| type | NvidiaGpuDriverWindows | řetězec |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Nasazení

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru 

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony jsou ideální při nasazování jedné nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení.

JSON konfigurace pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění konfigurace JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-manager-template-child-resource.md). 

V následujícím příkladu se předpokládá, že rozšíření je vnořená do prostředku virtuálního počítače. Při vnoření rozšíření prostředků, ve formátu JSON je umístěn v `"resources": []` objekt virtuálního počítače.

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

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí Azure Powershellu a rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Rozšíření provádění výstup je zaznamenán v následujícím adresáři:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Je to možné akce |
| :---: | --- | --- |
| 0 | Úspěšná operace |
| 1 | Operace byla úspěšná. Vyžaduje se restartování. |
| 100 | Operace není podporována nebo nebylo možné dokončit. | Možné příčiny: Nepodporovaná verze prostředí PowerShell, velikost virtuálního počítače není virtuálního počítače s N-series, stahování dat selhání. Zkontrolujte soubory protokolu a zjistěte příčinu chyby. |
| 240, 840 | Časový limit operace. | Zkuste operaci znovu. |
| -1 | Došlo k výjimce. | Zkontrolujte soubory protokolu a zjistěte příčinu výjimky. |
| -5x | Operace byla přerušena z důvodu čeká na restartování. | Restartování virtuálního počítače. Instalace bude pokračovat po restartování počítače. Odinstalace by mělo být vyvoláno ručně. |


### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další postup
Další informace o rozšířeních najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Windows](features-windows.md).

Další informace o virtuálních počítačích řady N-series najdete v tématu [GPU optimalizované velikosti virtuálních počítačů](../windows/sizes-gpu.md).
