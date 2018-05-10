---
title: Stackify vrátit rozšíření Azure Linux Agent | Microsoft Docs
description: Nasaďte agenta Stackify vrátit Linux na virtuální počítač s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: danis
ms.openlocfilehash: 376c5a087f74fbe087db9fa2df38b2ba4e6cf1ff
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify vrátit rozšíření agenta systému Linux

## <a name="overview"></a>Přehled
Stackify poskytuje produkty, které sledují podrobnosti o vaše aplikace a pomáhá najít a opravit problémy rychle. Pro vývojáře týmy vrátit je nadtypem napájení výkonu aplikace plně integrované, více prostředí. Kombinuje několik nástrojů, musí každý vývojový tým.

Vrátit je pouze nástroj, který zajišťuje všechny tyto možnosti ve všech prostředích v jedné platformě.

* Správa výkonu aplikací (APM)
* Protokolování serveru a aplikace
* Monitorování a sledování chyb
* Server, aplikace a vlastní metriky

**O Stackify rozšíření agenta systému Linux**

Toto rozšíření nabízí Instalační cesta pro Linux agenta pro vrátit. 

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém 
Pro tyto Linuxových distribucích můžete spouštět vrátit agenta

| Distribuce | Verze |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 a č. 17.04 |
| Debian | 7.9 + a 8.2 +, 9 |
| RedHat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Připojení k internetu
Rozšíření Stackify agenta pro Linux vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

Budete muset upravit konfiguraci sítě povolit připojení k Stackify, najdete v článku https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Schéma rozšíření
---

Následujícím kódu JSON znázorňuje schéma pro rozšíření Stackify vrátit agenta. Vyžaduje rozšíření `environment` a `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Nasazení šablon 

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Schéma JSON, které jsou popsané v předchozí části lze použít v šablonu Azure Resource Manager ke spuštění rozšíření Stackify vrátit agenta systému Linux při nasazení šablony Azure Resource Manager.  

JSON pro rozšíření virtuálního počítače můžete vnořit prostředek virtuálního počítače nebo umístěn na kořenový server WSUS nebo nejvyšší úrovně šablony JSON Resource Manager. Umístění formátu JSON, ovlivňuje hodnota název prostředku a typem. Další informace najdete v tématu sady názvu a typu pro podřízené prostředky.

V následujícím příkladu se předpokládá, že rozšíření Stackify vrátit Linux je vnořit prostředek virtuálního počítače. Při vnoření rozšíření prostředků, JSON je umístěn v "zdroje": objekt [] virtuálního počítače.

Vyžaduje rozšíření `environment` a `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Při vkládání rozšíření JSON v kořenovém adresáři šablony, názvu prostředku obsahuje odkaz na nadřazený virtuální počítač a typ odráží vnořené konfigurace.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>Nasazení prostředí PowerShell

`Set-AzureRmVMExtension` Příkaz lze použít k nasazení rozšíření virtuálního počítače Stackify agenta systému Linux vrátit do existujícího virtuálního počítače. Před spuštěním příkazu, veřejné a privátní konfigurace muset být uložena v tabulce hash prostředí PowerShell.

Vyžaduje rozšíření `environment` a `activationKey`.

```
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzureRmVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI 

Nástroj příkazového řádku Azure CLI můžete použít k nasazení rozšíření virtuálního počítače Stackify agenta systému Linux vrátit do existujícího virtuálního počítače.  

Vyžaduje rozšíření `environment` a `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Možné akce |
| :---: | --- | --- |
| 10 | Instalaci došlo k chybě | je vyžadován wget |
| 20 | Instalaci došlo k chybě | je vyžadován jazyk Python |
| 30 | Instalaci došlo k chybě | je vyžadován sudo |
| 40 | Instalaci došlo k chybě | je vyžadován activationKey |
| 51 | Instalaci došlo k chybě | Distro operačního systému není podporována |
| 60 | Instalaci došlo k chybě | je vyžadován prostředí |
| 70 | Instalaci došlo k chybě | Neznámé |
| 80 | Povolit chyby | Instalace služby se nezdařila. |
| 90 | Povolit chyby | Spuštění služby se nezdařilo |
| 100 | Zakázat zasílání zpráv | Zastavení služby se nezdařilo |
| 110 | Zakázat zasílání zpráv | Odebírání služby se nezdařilo. |
| 120 | Odinstalace | Zastavení služby se nezdařilo |

Pokud potřebujete další pomoc kontaktovat podporu Stackify v https://support.stackify.com.