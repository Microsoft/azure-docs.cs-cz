---
title: Rozšíření Azure VM Network Watcher Agent pro Linux | Dokumentace Microsoftu
description: Nasazení agenta sledovací proces sítě na virtuálním počítači s Linuxem pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: efd512395b49d3c274bb5aa409d1cbd527673659
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781883"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Síť rozšíření virtuálních počítačů sledovacích procesů agenta pro Linux

## <a name="overview"></a>Přehled

[Azure Network Watcher](/azure/network-watcher/) je síť výkonu monitorování, Diagnostika a analýza služby, který umožňuje monitorování sítě Azure. Rozšíření virtuálního počítače (VM) Network Watcher Agent představuje požadavek pro některé funkce sledovací proces sítě na virtuálních počítačích Azure, jako je zachytávání síťového provozu na vyžádání a další pokročilé funkce.

Tento článek podrobně popisuje podporované platformy a možnosti nasazení pro rozšíření virtuálního počítače síťových sledovacích procesů agenta pro Linux. Instalace agenta není narušit nebo vyžadují restartování virtuálního počítače. Nasazení rozšíření do virtuálních počítačů, které nasadíte. Pokud je nasazený virtuální počítač pomocí služby Azure, naleznete v dokumentaci k service k určení, zda it specialistovi instalace rozšíření ve virtuálním počítači.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Network Watcher Agent rozšíření lze nakonfigurovat pro následující distribuce Linuxu:

| Distribuce | Verze |
|---|---|
| Ubuntu | 12 + |
| Debian | 7 a 8 |
| Red Hat | 6 a 7 |
| Oracle Linux | 6.8 + a 7 |
| SUSE Linux Enterprise Server | 11 a 12 |
| Přestupné OpenSUSE | 42.3 + |
| CentOS | 6.5 + a 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce Network Watcher Agent vyžaduje, aby virtuální počítač je připojený k Internetu. Kdybychom neměli možnost navázat odchozí připojení některé funkce Network Watcher Agent může selhání nebo není dostupná. Další informace o Network Watcheru funkce, které vyžadují agenta, najdete v článku[dokumentace ke službě Network Watcher](/azure/network-watcher/).

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro rozšíření Network Watcher Agent. Rozšíření nebude vyžadovat nebo podporu, žádné uživatelem zadané nastavení. Rozšíření se spoléhá na její výchozí konfiguraci.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatele | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure pomocí šablony Azure Resource Manageru můžete nasadit. Pokud chcete nasadit rozšíření Network Watcher Agent, v šabloně použijte předchozí schématu json.

## <a name="azure-cli-10-deployment"></a>Nasazení v Azure CLI 1.0

Následující příklad nasadí rozšíření síťových sledovacích procesů agenta virtuálního počítače do existující virtuální počítač nasadit prostřednictvím modelu nasazení classic:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-20-deployment"></a>Nasazení v Azure CLI 2.0

Následující příklad nasadí rozšíření síťových sledovacích procesů agenta virtuálního počítače do existujícího virtuálního počítače nasazeného pomocí nástroje Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

### <a name="troubleshooting"></a>Řešení potíží

Můžete načíst data o stavu nasazení rozšíření pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.

Následující příklad ukazuje stav nasazení rozšíření pro virtuální počítače nasazené prostřednictvím modelu nasazení classic pomocí rozhraní příkazového řádku Azure CLI 1.0:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Rozšíření provádění výstup je zaznamenán soubory nalezené v následujícím adresáři:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

Následující příklad ukazuje stav nasazení NetworkWatcherAgentLinux rozšíření pro virtuální počítače nasazené prostřednictvím Resource Manageru pomocí rozhraní příkazového řádku Azure CLI 2.0:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku najdete [dokumentace ke službě Network Watcher](/azure/network-watcher/), nebo se obraťte na Azure odborníků [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o používání podpory Azure najdete v tématu [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
