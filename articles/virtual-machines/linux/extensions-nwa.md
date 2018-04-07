---
title: Rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Linux | Microsoft Docs
description: Nasaďte agenta sledovací proces sítě na virtuální počítač s Linuxem pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: dennisg
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
ms.openlocfilehash: 487eca98b9be20faaa52c0a8952e84c6027ee1f0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Sítě rozšíření virtuálního počítače sledovacích procesů agenta pro Linux

## <a name="overview"></a>Přehled

[Azure sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/) je sítě výkonu monitorování, diagnostiku a analýzy služba, která umožňuje monitorování pro sítě Azure. Rozšíření sítě sledovacích procesů agenta virtuálního počítače není pro některé funkce sledovací proces sítě na virtuálních počítačích Azure. To zahrnuje Zachytávání síťových přenosů na vyžádání a další pokročilé funkce.

Tento dokument podrobně popisuje možnosti nasazení pro rozšíření sítě sledovacích procesů agenta virtuálního počítače pro Linux a podporované platformy. Instalace agenta není přerušit nebo vyžadovat restartování virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta sledovací proces sítě může být spuštěn proti tyto Linuxových distribucích:

| Distribuce | Verze |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS a 12.04 LTS |
| Debian | 7 a 8 |
| RedHat | 6.x a 7.x |
| Oracle Linux | 7x |
| SuSE | 11 a 12 |
| OpenSuse | 7.0 |
| CentOS | 7.0 |

Všimněte si, že se v tuto chvíli nepodporuje CoreOS.

### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce sítě sledovacích procesů agenta vyžaduje, aby cílový virtuální počítač připojen k Internetu. Bez možnosti navázat odchozí připojení některé funkce sítě sledovacích procesů agenta nebude fungovat správně nebo nedostupná. Další podrobnosti najdete v tématu [sledovací proces sítě dokumentaci](https://review.docs.microsoft.com/azure/network-watcher/).

## <a name="extension-schema"></a>Rozšíření schématu

Následujícím kódu JSON znázorňuje schéma pro rozšíření sítě sledovacích procesů agenta. Rozšíření ani jeden z nich vyžaduje ani podporuje nastavení uživatelem zadané v tuto chvíli a spoléhá na jeho výchozí konfigurace.

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

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Schéma JSON, které jsou popsané v předchozí části lze použít v šablonu Azure Resource Manager ke spuštění rozšíření sítě sledovacích procesů agenta při nasazení šablony Azure Resource Manager.

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Rozhraní příkazového řádku Azure můžete použít k nasazení rozšíření sítě sledovacích procesů agenta virtuálního počítače do existujícího virtuálního počítače.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

### <a name="troubleshooting"></a>Řešení potíží

Z portálu Azure a pomocí rozhraní příkazového řádku Azure je možné načíst data o stavu nasazení rozšíření. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí rozhraní příkazového řádku Azure.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

Výstupu spuštění rozšíření se zaznamenává soubory, které jsou v následujícím adresáři:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete v dokumentaci sledovací proces sítě nebo se obraťte na Azure odborníky [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/en-us/support/options/) a vyberte Get podpory. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/en-us/support/faq/).
