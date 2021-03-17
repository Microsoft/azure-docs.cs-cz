---
title: Rozšíření virtuálního počítače služby Azure Network Watcher Agent pro Linux
description: Nasaďte agenta Network Watcher na virtuálním počítači se systémem Linux pomocí rozšíření virtuálního počítače.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/14/2017
ms.openlocfilehash: bc252e560df782625d795b30c6688a34f5c2bd79
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563587"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozšíření virtuálního počítače o agenta Network Watcher pro Linux

## <a name="overview"></a>Přehled

[Azure Network Watcher](../../network-watcher/index.yml) je služba pro sledování, diagnostiku a analýzu výkonu sítě, která umožňuje monitorování sítí Azure. Rozšíření virtuálního počítače s Network Watcher agentem je požadavek na některé z Network Watcher funkcí na virtuálních počítačích Azure, jako je třeba zachycení síťového provozu na vyžádání a další pokročilé funkce.

Tento článek podrobně popisuje podporované platformy a možnosti nasazení pro rozšíření virtuálního počítače Network Watcher agenta pro Linux. Instalace agenta neruší nebo vyžaduje restart virtuálního počítače. Rozšíření můžete nasadit do virtuálních počítačů, které nasadíte. Pokud je virtuální počítač nasazený službou Azure, Projděte si dokumentaci ke službě, kde zjistíte, jestli umožňuje nainstalovat rozšíření na virtuálním počítači.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření agenta Network Watcher lze nakonfigurovat pro následující distribuce systému Linux:

| Distribuce | Verze |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 a 8 |
| Red Hat | 6 a 7 |
| Oracle Linux | 6.8 + a 7 |
| SUSE Linux Enterprise Server | 11 a 12 |
| OpenSUSE přestupnost | 42.3 + |
| CentOS | verze 6.5 + a 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>Připojení k internetu

Některé funkce agenta Network Watcher vyžadují, aby byl virtuální počítač připojený k Internetu. Bez možnosti navázání odchozích připojení nemusí některé funkce agenta Network Watcher fungovat správně nebo nebudou k dispozici. Další informace o funkcích Network Watcher, které vyžadují agenta, najdete v[dokumentaci k Network Watcher](../../network-watcher/index.yml).

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření agenta Network Watcher. Rozšíření nevyžaduje nebo nepodporuje žádná nastavení zadaná uživatelem. Rozšíření spoléhá na jeho výchozí konfiguraci.

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

| Name | Hodnota/příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatel | Microsoft. Azure. NetworkWatcher |
| typ | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure můžete nasadit pomocí šablony Azure Resource Manager. Pokud chcete nasadit rozšíření agenta Network Watcher, použijte v šabloně předchozí schéma JSON.

## <a name="azure-classic-cli-deployment"></a>Nasazení rozhraní příkazového řádku Azure Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Následující příklad nasadí rozšíření virtuálního počítače agenta Network Watcher pro existující virtuální počítač nasazený prostřednictvím modelu nasazení Classic:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Následující příklad nasadí rozšíření virtuálního počítače agenta Network Watcher pro existující virtuální počítač nasazený pomocí Správce prostředků:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

### <a name="troubleshooting"></a>Řešení potíží

Data o stavu nasazení rozšíření můžete načíst pomocí Azure Portal nebo prostřednictvím rozhraní příkazového řádku Azure.

Následující příklad ukazuje stav nasazení rozšíření NetworkWatcherAgentLinux pro virtuální počítač nasazený prostřednictvím Správce prostředků pomocí rozhraní příkazového řádku Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Podpora

Pokud potřebujete další informace v jakémkoli bodě tohoto článku, můžete si přečtěte v dokumentaci k [Network Watcher](../../network-watcher/index.yml)nebo se obraťte na odborníky na Azure na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
