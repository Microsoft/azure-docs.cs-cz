---
title: Rozšíření ovladače NVIDIA GPU – virtuální počítače Azure Linux
description: Microsoft Azure rozšíření pro instalaci ovladačů NVIDIA GPU na výpočetní virtuální počítače řady N-Series se systémem Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: fa2b82f3246fd87830010f572ba23aa2df075053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566222"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozšíření ovladače NVIDIA GPU pro Linux

## <a name="overview"></a>Přehled

Toto rozšíření nainstaluje ovladače NVIDIA GPU na virtuální počítače se systémem Linux N-Series. V závislosti na rodině virtuálních počítačů rozšíření nainstaluje ovladače CUDA nebo GRIDu. Když instalujete ovladače NVIDIA pomocí tohoto rozšíření, přijímáte a souhlasíte s podmínkami [licenční smlouvy NVIDIA End-User](https://go.microsoft.com/fwlink/?linkid=874330). Během procesu instalace může být virtuální počítač restartován, aby bylo možné dokončit instalaci ovladače.

Pokyny k ruční instalaci ovladačů a aktuálně podporované verze jsou k dispozici [zde](../linux/n-series-driver-setup.md).
K dispozici je také rozšíření pro instalaci ovladačů NVIDIA GPU na [virtuálních počítačích s Windows N-Series](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující distribuce operačního systému, v závislosti na podpoře ovladačů pro konkrétní verzi operačního systému.

| Distribuce | Verze |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |

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
    "type": "NvidiaGpuDriverLinux",
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
| apiVersion | 2015-06-15 | date |
| vydavatel | Microsoft. HpcCompute | řetězec |
| typ | NvidiaGpuDriverLinux | řetězec |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>Nastavení

Všechna nastavení jsou volitelná. Výchozím chováním je neaktualizovat jádro, pokud není pro instalaci ovladače vyžadováno, nainstalujte nejnovější podporovaný ovladač a CUDA Toolkit (podle potřeby).

| Název | Description | Výchozí hodnota | Platné hodnoty | Typ dat |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Aktualizace jádra i v případě, že není potřeba k instalaci ovladače | false (nepravda) | true, false | boolean |
| driverVersion | NV: verze ovladače mřížky<br> NC/ND: verze sady CUDA Toolkit. Nejnovější ovladače pro zvolené CUDA se nainstalují automaticky. | nejnovější | [Seznam](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) podporovaných verzí ovladačů | řetězec |
| installCUDA | Nainstalujte sadu CUDA Toolkit. Platí jenom pro virtuální počítače řady NC/ND. | true | true, false | boolean |


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
    "type": "NvidiaGpuDriverLinux",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Následující příklad zrcadlí výše uvedené Azure Resource Manager a příklady prostředí PowerShell.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

Následující příklad také přidá dvě volitelná vlastní nastavení jako příklad pro jinou než výchozí instalaci ovladače. Konkrétně aktualizuje jádro operačního systému na nejnovější a nainstaluje konkrétní ovladač verze CUDA Toolkit. Znovu si všimněte, že nastavení--Settings jsou volitelná a výchozí. Všimněte si, že aktualizace jádra může prodloužit dobu instalace rozšíření. Také volba konkrétní (starší) tolkit verze CUDA nemusí být vždycky kompatibilní s novějšími jádry.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
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

Výstup spuštění rozšíření je protokolován do následujícího souboru. Pokud chcete sledovat stav (v případě dlouhotrvající) instalace a řešit případné chyby, podívejte se na tento soubor.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Ukončovací kódy

| Ukončovací kód | Význam | Možná akce |
| :---: | --- | --- |
| 0 | Operace byla úspěšná. |
| 1 | Nesprávné použití rozšíření | Kontrolovat výstupní protokol spuštění |
| 10 | Integrační služby Linux pro Hyper-V a Azure nejsou dostupné nebo nainstalované | Kontrolovat výstup lspci |
| 11 | Grafický procesor NVIDIA se v této velikosti virtuálního počítače nenašel. | Použít [podporovanou velikost virtuálního počítače a operační systém](../linux/n-series-driver-setup.md) |
| 12 | Nabídka Image není podporovaná. |
| 13 | Velikost virtuálního počítače se nepodporuje. | Použití virtuálního počítače řady N-Series k nasazení |
| 14 | Operace neproběhla úspěšně. | Kontrolovat výstupní protokol spuštění |


### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních najdete v tématu [rozšíření virtuálních počítačů a funkce pro Linux](features-linux.md).

Další informace o virtuálních počítačích řady N-Series najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../sizes-gpu.md).
