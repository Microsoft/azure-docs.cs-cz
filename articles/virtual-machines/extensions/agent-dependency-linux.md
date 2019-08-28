---
title: Rozšíření virtuálního počítače závislosti Azure Monitor pro Linux | Microsoft Docs
description: Nasaďte agenta závislostí Azure Monitor na virtuálním počítači se systémem Linux pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 416b0c89105f97514efdfcc859a630d78f7ba7f5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084833"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Rozšíření virtuálního počítače závislosti Azure Monitor pro Linux

Azure Monitor pro funkci mapování virtuálních počítačů získává data od agenta Microsoft Dependency. Rozšíření virtuálního počítače Azure VM Dependency Agent pro Linux se zveřejňuje a podporuje Microsoft. Rozšíření nainstaluje agenta závislostí na virtuální počítače Azure. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače služby Azure VM Dependency Agent pro Linux.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta závislostí virtuálních počítačů Azure pro Linux lze spustit s podporovanými operačními systémy uvedenými v části [podporované operační systémy](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) v článku věnovaném nasazení Azure monitor pro virtuální počítače.

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta závislostí virtuálních počítačů Azure na virtuálním počítači Azure Linux. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Name | Hodnota/příklad |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure můžete nasadit pomocí šablon Azure Resource Manager. Pomocí schématu JSON popsaného v předchozí části šablony Azure Resource Manager můžete během nasazování Azure Resource Manager šablony spustit rozšíření agenta závislostí virtuálních počítačů Azure.

JSON pro rozšíření virtuálního počítače může být vnořený do prostředku virtuálního počítače. Nebo ho můžete umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění formátu JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/child-resource-name-type.md).

Následující příklad předpokládá, že rozšíření agenta závislosti je vnořené uvnitř prostředku virtuálního počítače. Při vnořování prostředku rozšíření je kód JSON umístěn v `"resources": []` objektu virtuálního počítače.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Když umístíte JSON rozšíření do kořenového adresáře šablony, název prostředku obsahuje odkaz na nadřazený virtuální počítač. Typ odráží vnořenou konfiguraci. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nasadit rozšíření virtuálního počítače agenta závislostí do existujícího virtuálního počítače.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, obraťte se na odborníky na Azure na [webu MSDN Azure a Stack Overflow fórech](https://azure.microsoft.com/support/forums/). Nebo můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o tom, jak používat podporu Azure, najdete v části [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
