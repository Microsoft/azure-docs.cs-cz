---
title: Stackify obsluhy vrátit rozšíření Azure Linux Agent | Dokumentace Microsoftu
description: Nasazení agenta pro Linux stackify obsluhy vrátit na virtuální počítač s Linuxem.
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
ms.author: roiyz
ms.openlocfilehash: e80134729c33741aa3007deb2d93a2de3e3fe697
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979713"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify obsluhy vrátit rozšíření agenta pro Linux

## <a name="overview"></a>Přehled
Stackify obsluhy poskytuje produkty, které sledují informace o vaší aplikaci, který pomůže najít a opravit problémy rychle. Pro vývojářské týmy vrátit je výkon velmi více prostředí, plně integrovaných aplikací. Kombinuje několik nástrojů, se musí všechny vývojové týmy.

Vrátit se pouze nástroj, který poskytuje všechny tyto možnosti ve všech prostředích na jedné platformě.

* Správa výkonu aplikací (APM)
* Aplikace a protokolování serveru
* Monitorování a sledování chyb
* Server, aplikace a vlastní metriky

**Informace o stackify obsluhy rozšíření agenta pro Linux**

Toto rozšíření nabízí instalační cestu pro agenta pro Linux pro vrátit. 

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém 
Agent vrátit dají spustit pro tyto Linuxové distribuce

| Distribuce | Verze |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 a č. 17.04 |
| Debian | 7.9 + a 8.2 +, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Připojení k internetu
Rozšíření stackify obsluhy agenta pro Linux vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

Může být potřeba upravit konfiguraci sítě povolovat připojení k Stackify, přečtěte si téma https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Schéma rozšíření
---

Následující kód JSON ukazuje schéma pro rozšíření stackify obsluhy vrátit agenta. Rozšíření vyžaduje `environment` a `activationKey`.

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

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru pro spuštění rozšíření stackify obsluhy vrátit agenta pro Linux při nasazení šablony Azure Resource Manageru.  

JSON pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění ve formátu JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu nastavit název a typ pro podřízené prostředky.

V následujícím příkladu se předpokládá, že rozšíření stackify obsluhy vrátit Linux je vnořená do prostředku virtuálního počítače. Při vnoření rozšíření prostředků, ve formátu JSON je umístěn v "resources": [] objekt virtuálního počítače.

Rozšíření vyžaduje `environment` a `activationKey`.

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

Při vkládání rozšíření JSON v kořenovém adresáři šablony, název prostředku obsahuje odkaz na nadřazený virtuální počítač a typ odráží vnořené konfigurace.

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


## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

`Set-AzVMExtension` Příkaz lze použít k nasazení rozšíření stackify obsluhy vrátit linuxového agenta virtuálního počítače do existujícího virtuálního počítače. Před spuštěním příkazu, veřejné a privátní konfigurace muset být uložena v tabulce hash prostředí PowerShell.

Rozšíření vyžaduje `environment` a `activationKey`.

```
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI 

Nástroje Azure CLI slouží k nasazení rozšíření stackify obsluhy vrátit linuxového agenta virtuálního počítače do existujícího virtuálního počítače.  

Rozšíření vyžaduje `environment` a `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Je to možné akce |
| :---: | --- | --- |
| 10 | Chyba instalace | vyžaduje se wget |
| 20 | Chyba instalace | vyžaduje se Python |
| 30 | Chyba instalace | vyžaduje se sudo |
| 40 | Chyba instalace | vyžaduje se klíč activationKey |
| 51 | Chyba instalace | Distribuce operačního systému není podporována |
| 60 | Chyba instalace | prostředí je povinný |
| 70 | Chyba instalace | Neznámé |
| 80 | Povolit chyby | Instalace služby se nezdařila. |
| 90 | Povolit chyby | Spuštění služby se nezdařilo |
| 100 | Vypnout | Zastavení služby se nezdařilo |
| 110 | Vypnout | Odinstalace služby se nezdařila |
| 120 | Chyba odinstalace | Zastavení služby se nezdařilo |

Pokud potřebujete další pomoc můžete požádat podporu Stackify na https://support.stackify.com.