---
title: Rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Linux | Microsoft Docs
description: Nasaďte agenta sledovací proces sítě na virtuální počítač s Linuxem pomocí rozšíření virtuálního počítače.
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
ms.openlocfilehash: db508e2311602a66a2c252ffaa842f8bfb4f670b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Sítě rozšíření virtuálního počítače sledovacích procesů agenta pro Linux

## <a name="overview"></a>Přehled

[Azure sledovací proces sítě](/azure/network-watcher/) je sítě výkonu monitorování, diagnostiku a analýzy služba, která umožňuje monitorování pro sítě Azure. Rozšíření virtuálního počítače (VM) sítě sledovacích procesů agenta je požadavek pro některé funkce sledovací proces sítě na virtuálních počítačích Azure, jako je například Zachytávání síťových přenosů na vyžádání a další pokročilé funkce.

Tento článek podrobné informace o podporovaných platformách a možnosti nasazení pro rozšíření sítě sledovacích procesů agenta virtuálního počítače pro Linux. Instalace agenta není přerušit nebo vyžadovat restartování virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta sledovací proces sítě lze nakonfigurovat pro následující distribuce Linux:

| Distribuce | Verze |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS a 12.04 LTS |
| Debian | 7 a 8 |
| RedHat | 6 a 7 |
| Oracle Linux | 6.8 + a 7 |
| SUSE Linux Enterprise Server | 11 a 12 |
| OpenSUSE přestupného | 42.3 + |
| CentOS | 6.5 + a 7 |
| CoreOS | 899.17.0+ |

Jádro operačního systému není podporována.

### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce sítě sledovacích procesů agenta vyžaduje, aby virtuální počítač je připojený k Internetu. Bez možnosti navázat odchozí připojení některé funkce sítě sledovacích procesů agenta může selhání nebo nedostupná. Další informace o funkcích sledovací proces sítě, která vyžaduje agenta najdete v tématu[sledovací proces sítě dokumentaci](/azure/network-watcher/).

## <a name="extension-schema"></a>Schéma rozšíření

Následujícím kódu JSON znázorňuje schéma pro rozšíření sítě sledovacích procesů agenta. Rozšíření není vyžadují nebo podporu, nastavení zadaný uživatelem. Rozšíření spoléhá na jeho výchozí konfigurace.

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

| Název | Hodnota nebo příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Vydavatele | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Nasazení šablon

Můžete nasadit rozšíření virtuálního počítače Azure pomocí šablony Azure Resource Manager. K nasazení rozšíření sítě sledovacích procesů agenta, použijte předchozí schématu json ve vaší šabloně.

## <a name="azure-cli-10-deployment"></a>Nasazení Azure CLI 1.0

Následující příklad nasadí rozšíření sítě sledovacích procesů agenta virtuálního počítače do existující virtuální počítač nasadit v rámci modelu nasazení classic:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-20-deployment"></a>Nasazení Azure CLI 2.0

Následující příklad nasadí rozšíření sítě sledovacích procesů agenta virtuálního počítače do existující virtuální počítač nasadit prostřednictvím Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

### <a name="troubleshooting"></a>Řešení potíží

Můžete získat data o stavu nasazení rozšíření pomocí portálu Azure nebo rozhraní příkazového řádku Azure.

Následující příklad ukazuje stav nasazení rozšíření pro virtuální počítač nasadit v rámci modelu nasazení classic pomocí Azure CLI 1.0:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Výstupu spuštění rozšíření se zaznamenává soubory, které jsou v následujícím adresáři:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

Následující příklad ukazuje stav nasazení NetworkWatcherAgentLinux rozšíření pro virtuální počítač nasadit pomocí Správce prostředků pomocí Azure CLI 2.0:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete se podívat do [sledovací proces sítě dokumentace](/azure/network-watcher/), nebo se obraťte na Azure odborníky [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o používání podporu Azure najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/support/faq/).
