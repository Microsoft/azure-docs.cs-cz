---
title: Rozšíření ovladače NVIDIA GPU – virtuální počítače Azure s Windows | Microsoft Docs
description: Microsoft Azure rozšíření pro instalaci ovladačů NVIDIA GPU na výpočetní virtuální počítače řady N-Series s Windows
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
ms.openlocfilehash: 4526456b608f0920c73ae28446a822661995acab
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173986"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Rozšíření ovladače NVIDIA GPU pro Windows

## <a name="overview"></a>Přehled

Toto rozšíření nainstaluje ovladače NVIDIA GPU na virtuální počítače řady Windows N-Series. V závislosti na rodině virtuálních počítačů rozšíření nainstaluje ovladače CUDA nebo GRIDu. Když instalujete ovladače NVIDIA pomocí tohoto rozšíření, přijímáte podmínky [licenční smlouvy pro koncové uživatele NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330)a souhlasíte s nimi. Během procesu instalace může být virtuální počítač restartován, aby bylo možné dokončit instalaci ovladače.

Pokyny k ruční instalaci ovladačů a aktuálně podporované verze jsou k dispozici [zde](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup).
K dispozici je také rozšíření pro instalaci ovladačů NVIDIA GPU pro [virtuální počítače řady Linux N-Series](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující OSs:

| Distribuce | Version |
|---|---|
| Windows 10 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012R2 | Core |

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
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>properties

| Name | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft. HpcCompute | string |
| type | NvidiaGpuDriverWindows | string |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Nasazení

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru 

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení.

JSON konfigurace pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění konfigurace JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-manager-template-child-resource.md). 

Následující příklad předpokládá, že rozšíření je vnořeno do prostředku virtuálního počítače. Při vnoření rozšíření prostředků, ve formátu JSON je umístěn v `"resources": []` objekt virtuálního počítače.

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

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí Azure PowerShell a Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření je protokolován do následujícího adresáře:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Je to možné akce |
| :---: | --- | --- |
| 0 | Operace byla úspěšná. |
| 1 | Operace byla úspěšná. Je vyžadován restart. |
| 100 | Operace není podporována nebo ji nelze dokončit. | Možné příčiny: Verze PowerShellu není podporovaná, velikost virtuálního počítače není virtuální počítač řady N-Series, při stahování dat došlo k chybě. Zkontrolujte soubory protokolu a určete příčinu chyby. |
| 240, 840 | Časový limit operace. | Zkuste operaci zopakovat. |
| -1 | Došlo k výjimce. | Zkontrolujte soubory protokolu a určete příčinu výjimky. |
| –5x | Operace byla přerušena z důvodu nedokončeného restartování. | Restartujte virtuální počítač. Po restartování bude instalace pokračovat. Odinstalaci je třeba vyvolat ručně. |


### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních najdete v tématu [rozšíření virtuálních počítačů a funkce pro Windows](features-windows.md).

Další informace o virtuálních počítačích řady N-Series najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../windows/sizes-gpu.md).
