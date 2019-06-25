---
title: Rozšíření virtuálního počítače Azure monitoru závislostí pro Linux | Dokumentace Microsoftu
description: Nasazení agenta Azure Monitor závislost na virtuální počítač s Linuxem pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 5faeebe799bd8cc0ba9a148508ac5b3a6d4b803a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120199"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Rozšíření virtuálního počítače Azure monitoru závislostí pro Linux

Azure Monitor pro funkci mapování virtuálních počítačů získává data od agenta Microsoft Dependency. Rozšíření virtuálního počítače agenta závislosti virtuálních počítačů Azure pro Linux je publikována a podporované společností Microsoft. Instalaci agenta závislostí na virtuálních počítačích Azure. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače agenta závislosti virtuálních počítačů Azure pro Linux.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta závislosti virtuálních počítačů Azure pro Linux lze spustit na podporovaných operačních systémů uvedených v [podporované operační systémy](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) části Azure Monitor pro článek nasazení virtuálních počítačů.

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma rozšíření Azure VM závislost agenta na virtuálním počítači Azure s Linuxem. 

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

| Name | Příklad hodnoty / |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure pomocí šablon Azure Resource Manageru můžete nasadit. Schéma JSON, které jsou podrobně popsané v předchozí části šablony Azure Resource Manageru můžete použít ke spuštění rozšíření agenta závislostí virtuálního počítače Azure při nasazení šablony Azure Resource Manageru.

JSON pro rozšíření virtuálního počítače může být vnořena v prostředku virtuálního počítače. Nebo je možné je umístit na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění ve formátu JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources).

V následujícím příkladu se předpokládá, že rozšíření agenta závislostí je vnořená do prostředku virtuálního počítače. Když je vnořovat rozšíření prostředků, ve formátu JSON najdete v `"resources": []` objekt virtuálního počítače.


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

Umístíte-li v kořenovém adresáři šablony rozšíření JSON, název prostředku obsahuje odkaz na nadřazený virtuální počítač. Typ odráží vnořené konfigurace. 

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

Rozhraní příkazového řádku Azure můžete použít k nasazení agenta závislostí rozšíření virtuálního počítače do existujícího virtuálního počítače.  

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

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz s použitím rozhraní příkazového řádku Azure:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, obraťte se na Azure odborníků na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Nebo můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o tom, jak pomocí podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
