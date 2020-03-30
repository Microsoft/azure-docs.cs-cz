---
title: Rozšíření ovladače GPU NVIDIA – virtuální počítače Azure Linux
description: Rozšíření Microsoft Azure pro instalaci ovladačů GPU NVIDIA na výpočetní virtuální počítače řady N se systémem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: 6ea61acfc2db3c8f1f5c9c0ac8da8f19897d441e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250566"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Rozšíření ovladače GPU NVIDIA pro Linux

## <a name="overview"></a>Přehled

Toto rozšíření nainstaluje ovladače NVIDIA GPU na virtuální počítače řady Linux N. V závislosti na rodině virtuálních počítačů rozšíření nainstaluje ovladače CUDA nebo GRID. Při instalaci ovladačů NVIDIA pomocí tohoto rozšíření přijímáte a souhlasíte s podmínkami [licenční smlouvy s koncovým uživatelem NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Během procesu instalace může virtuální počítač restartovat k dokončení nastavení ovladače.

Pokyny k ruční instalaci ovladačů a aktuálních podporovaných verzí jsou k dispozici [zde](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup).
Rozšíření je také k dispozici pro instalaci ovladačů NVIDIA GPU na [Windows N-series Virtuální počítač .](hpccompute-gpu-windows.md)

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Toto rozšíření podporuje následující distribuce operačního systému v závislosti na podpoře ovladačů pro konkrétní verzi operačního systému.

| Distribuce | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

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
    "type": "NvidiaGpuDriverLinux",
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
| type | NvidiaGpuDriverLinux | řetězec |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Nastavení

Všechna nastavení jsou volitelná. Výchozí chování je neaktualizovat jádro, pokud není požadováno pro instalaci ovladače, nainstalujte nejnovější podporovaný ovladač a cuda toolkit (podle potřeby).

| Name (Název) | Popis | Výchozí hodnota | Platné hodnoty | Typ dat |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Aktualizace jádra, i když není vyžadována pro instalaci ovladače | false (nepravda) | pravda, nepravdivé | Boolean |
| driverVersion | NV: Verze ovladače GRID<br> NC/ND: Verze sady nástrojů CUDA. Nejnovější ovladače pro vybranou CUDA jsou nainstalovány automaticky. | nejnovější | MŘÍŽKA: "430.30", "418.70", "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | řetězec |
| installCUDA | Nainstalujte sadu nástrojů CUDA. Relevantní pouze pro virtuální měny řady NC/ND. | true | pravda, nepravdivé | Boolean |


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
    "type": "NvidiaGpuDriverLinux",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Následující příklad odráží výše uvedené příklady Azure Resource Manager a PowerShell a také přidá vlastní nastavení jako příklad pro instalaci ovladače, která není výchozí. Konkrétně aktualizuje jádro operačního systému a nainstaluje konkrétní ovladač verze cuda toolkit.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
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

Výstup spuštění rozšíření je zaznamenán do následujícího souboru:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Ukončovací kódy

| Ukončovací kód | Význam | Možná akce |
| :---: | --- | --- |
| 0 | Operace byla úspěšná. |
| 1 | Nesprávné použití rozšíření | Zkontrolovat výstupní protokol spuštění |
| 10 | Linux Integration Services pro Hyper-V a Azure není k dispozici nebo nainstalován | Zkontrolujte výstup lspci |
| 11 | GRAFICKÝ PROCESOR NVIDIA nebyl u této velikosti virtuálního počítače nalezen. | Použití [podporované velikosti virtuálního počítače a operačního systému](../linux/n-series-driver-setup.md) |
| 12 | Nabídka obrázků není podporována |
| 13 | Velikost virtuálního počítače není podporovaná | Nasazení virtuálního virtuálního montovak řady N |
| 14 | Operace nebyla úspěšná. | Zkontrolovat výstupní protokol spuštění |


### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/community/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních najdete [v tématu Rozšíření virtuálních strojů a funkce pro Linux](features-linux.md).

Další informace o virtuálních počítačích řady N najdete v [tématu velikosti virtuálních strojů optimalizované pro GPU](../linux/sizes-gpu.md).
