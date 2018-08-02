---
title: Rozšíření ovladačů NVIDIA GPU – virtuální počítače Azure s Linuxem | Dokumentace Microsoftu
description: Rozšíření Microsoft Azure k instalaci ovladačů NVIDIA GPU do řady N-series výpočetní virtuální počítače s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: roiyz
ms.openlocfilehash: d95a1b510411f913a05762494dd48d6a5b6f84fd
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413667"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozšíření ovladačů NVIDIA GPU pro Linux

## <a name="overview"></a>Přehled

Toto rozšíření nainstaluje ovladačů NVIDIA GPU na virtuálních počítačích s Linuxem řady N-series. V závislosti na řadu virtuálních počítačů nainstaluje rozšíření ovladače CUDA nebo MŘÍŽKA. Při instalaci NVIDIA ovladače, které používají toto rozšíření přijetí a vyjádření souhlasu s těmito podmínkami licenční smlouvy s koncovým uživatelem NVIDIA. Váš virtuální počítač může během procesu instalace restartovat k dokončení instalace ovladačů.

Rozšíření je také dostupná k instalaci ovladačů NVIDIA GPU na [virtuální počítače řady N-series s Windows](hpccompute-gpu-windows.md).

Podmínky licenční smlouvy s koncovým uživatelem NVIDIA jsou umístěny zde- https://go.microsoft.com/fwlink/?linkid=874330

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující OSs:

| Distribuce | Verze |
|---|---|
| Linux: Ubuntu | 16.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4 |
| Linux: CentOS | 7.3, 7.4 |

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| vydavatele | Microsoft.HpcCompute | řetězec |
| type | NvidiaGpuDriverLinux | řetězec |
| typeHandlerVersion | 1.0 | int |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí Azure Powershellu a rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kódy ukončení

| Kód ukončení | Význam | Je to možné akce |
| :---: | --- | --- |
| 0 | Úspěšná operace |
| 1 | Nesprávné použití rozšíření. | Obraťte se na podporu protokolu výstup spuštění. |
| 10 | Integrační služby Linuxu pro Hyper-V a Azure není k dispozici nebo nainstalované. | Zkontrolujte výstup lspci. |
| 11 | Grafický procesor NVIDIA nebyla nalezena na velikosti tohoto virtuálního počítače. | Použití [podporovaná velikost virtuálního počítače a operačního systému](../linux/n-series-driver-setup.md). |
| 14 | Neúspěšné operace | |
| 21 | Aktualizace se nezdařila na Ubuntu | Zkontrolujte výstup "aktualizace apt-get sudo" |


### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další postup
Další informace o rozšířeních najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Linux](features-linux.md).

Další informace o virtuálních počítačích řady N-series najdete v tématu [GPU optimalizované velikosti virtuálních počítačů](../linux/sizes-gpu.md).