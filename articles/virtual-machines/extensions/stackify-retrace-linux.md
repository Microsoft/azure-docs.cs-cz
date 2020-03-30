---
title: Stackify Retrace Rozšíření agenta Azure Linux
description: Nasazení agenta Stackify Retrace Linuxu na virtuálním počítači s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 5914947bd994ee405f253e34c3dd919dd6561898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253790"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux Agent Rozšíření

## <a name="overview"></a>Přehled

Stackify poskytuje produkty, které sledují podrobnosti o vaší aplikaci, které pomáhají rychle najít a opravit problémy. Pro vývojářské týmy je Retrace plně integrovaný supervýkon aplikací s více prostředími. Kombinuje několik nástrojů, které každý vývojový tým potřebuje.

Retrace je jediný nástroj, který poskytuje všechny následující funkce ve všech prostředích v jedné platformě.

* Správa výkonu aplikací (APM)
* Protokolování aplikací a serverů
* Sledování a sledování chyb
* Server, aplikace a vlastní metriky

**Rozšíření stackify linuxového agenta**

Toto rozšíření poskytuje instalační cestu pro agenta Linuxu pro retrace. 

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém 

Agent retrace lze spustit proti těmto linuxovým distribucím

| Distribuce | Version |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 a 17,04 |
| Debian | 7,9+ a 8,2+, 9 |
| Red Hat | 6,7+, 7,1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Stackify Agent pro Linux vyžaduje, aby byl cílový virtuální počítač připojen k internetu. 

Možná budete muset upravit konfiguraci sítě tak, aby https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewallumožňovala připojení k Stackify, viz . 


## <a name="extension-schema"></a>Schéma rozšíření

---

Následující JSON ukazuje schéma pro Stackify retrace agent rozšíření. Rozšíření vyžaduje `environment` a `activationKey`.

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

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Schéma JSON popsané v předchozí části lze použít v šabloně Správce prostředků Azure ke spuštění rozšíření Stackify Retrace Linux Agent během nasazení šablony Azure Resource Manager.  

JSON pro rozšíření virtuálního počítače může být vnořené uvnitř prostředku virtuálního počítače nebo umístěny na kořenové nebo nejvyšší úrovni šablony Správce prostředků JSON. Umístění JSON ovlivňuje hodnotu názvu a typu prostředku. Další informace naleznete v tématu Nastavení názvu a typu podřízených prostředků.

Následující příklad předpokládá Stackify Retrace Linux rozšíření je vnořené uvnitř prostředku virtuálního počítače. Při vnoření prostředku rozšíření json je umístěn v "prostředky": [] objekt virtuálního počítače.

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

Při umístění rozšíření JSON v kořenovém adresáři šablony obsahuje název prostředku odkaz na nadřazený virtuální počítač a typ odráží vnořenou konfiguraci.

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


## <a name="powershell-deployment"></a>Nasazení PowerShellu

Příkaz `Set-AzVMExtension` lze použít k nasazení Stackify Retrace Linux Agent rozšíření virtuálního počítače do existujícího virtuálního počítače. Před spuštěním příkazu je třeba uložit veřejné a soukromé konfigurace v tabulce hash prostředí PowerShell.

Rozšíření vyžaduje `environment` a `activationKey`.

```powershell
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

## <a name="azure-cli-deployment"></a>Nasazení azure cli 

Nástroj Azure CLI se dá použít k nasazení rozšíření virtuálního počítače Stackify Retrace Linux Agent do existujícího virtuálního počítače.  

Rozšíření vyžaduje `environment` a `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 10 | Chyba instalace | wget je vyžadováno |
| 20 | Chyba instalace | python je vyžadován |
| 30 | Chyba instalace | sudo je nutné |
| 40 | Chyba instalace | je vyžadován aktivační klíč. |
| 51 | Chyba instalace | Distribuce operačního systému není podporována. |
| 60 | Chyba instalace | prostředí je vyžadováno |
| 70 | Chyba instalace | Není známo |
| 80 | Povolit chybu | Nastavení služby se nezdařilo. |
| 90 | Povolit chybu | Spuštění služby se nezdařilo. |
| 100 | Zakázat chybu | Zastavení služby se nezdařilo. |
| 110 | Zakázat chybu | Odebrání služby se nezdařilo. |
| 120 | Chyba odinstalace | Zastavení služby se nezdařilo. |

Pokud potřebujete další pomoc, můžete kontaktovat https://support.stackify.compodporu Stackify na adrese .