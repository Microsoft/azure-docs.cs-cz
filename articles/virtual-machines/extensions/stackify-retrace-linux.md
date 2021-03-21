---
title: Stackify znovu trasovat rozšíření agenta Azure Linux
description: Nasaďte agenta Stackify Retrace pro Linux na virtuálním počítači se systémem Linux.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 04/12/2018
ms.openlocfilehash: 1fc437637fde524da125af9191bf9de79a2e9c37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558997"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Rozšíření agenta Stackify retracee pro Linux

## <a name="overview"></a>Přehled

Stackify poskytuje produkty, které sledují podrobnosti o vaší aplikaci, abyste mohli rychle najít a opravit problémy. Pro vývojářské týmy je opětovné trasování plně integrované, vysoce výkonné prostředí pro více prostředí a výkon aplikace. Kombinuje několik nástrojů, které každý vývojový tým potřebuje.

Přetrasování je jediný nástroj, který poskytuje všechny následující funkce napříč všemi prostředími v jedné platformě.

* Správa výkonu aplikací (APM)
* Protokolování aplikací a serverů
* Sledování a sledování chyb
* Server, aplikace a vlastní metriky

**O rozšíření agenta systému Stackify Linux**

Toto rozšíření poskytuje instalační cestu pro agenta pro Linux pro opětovné trasování. 

## <a name="prerequisites"></a>Předpoklady

### <a name="operating-system"></a>Operační systém 

Agenta přetrasování lze spustit pro tyto distribuce systému Linux.

| Distribuce | Verze |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 a 17,04 |
| Debian | 7.9 + a 8.2 +, 9 |
| Red Hat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření agenta Stackify pro Linux vyžaduje, aby byl cílový virtuální počítač připojený k Internetu. 

Možná budete muset upravit konfiguraci sítě, aby povolovala připojení k Stackify, viz https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall . 


## <a name="extension-schema"></a>Schéma rozšíření

---

Následující JSON zobrazuje schéma pro rozšíření agenta Stackify Retrace. Přípona vyžaduje `environment` a `activationKey` .

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

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části se dá použít v šabloně Azure Resource Manager ke spuštění rozšíření agenta Linux Retrace Stackify během nasazování šablony Azure Resource Manager.  

JSON pro rozšíření virtuálního počítače se dá vnořit do prostředku virtuálního počítače nebo umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění formátu JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu Nastavení názvu a typu pro podřízené prostředky.

Následující příklad předpokládá, že rozšíření Stackify Retrace pro Linux je vnořeno do prostředku virtuálního počítače. Při vnořování prostředku rozšíření je kód JSON umístěn do objektu Resources: [] virtuálního počítače.

Přípona vyžaduje `environment` a `activationKey` .

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

Při umístění JSON rozšíření v kořenovém adresáři šablony obsahuje název prostředku odkaz na nadřazený virtuální počítač a typ odráží vnořenou konfiguraci.

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

`Set-AzVMExtension`Příkaz se dá použít k nasazení rozšíření virtuálního počítače Stackify Retrace agenta pro Linux do existujícího virtuálního počítače. Před spuštěním příkazu musí být veřejné a privátní konfigurace uložené v zatřiďovací tabulce PowerShellu.

Přípona vyžaduje `environment` a `activationKey` .

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

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI 

Nástroj rozhraní příkazového řádku Azure je možné použít k nasazení rozšíření virtuálního počítače Stackify Retrace agenta pro Linux do existujícího virtuálního počítače.  

Přípona vyžaduje `environment` a `activationKey` .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="error-codes"></a>Kódy chyb

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 10 | Chyba instalace | Wget je povinný údaj. |
| 20 | Chyba instalace | Python je povinný. |
| 30 | Chyba instalace | sudo je povinný údaj. |
| 40 | Chyba instalace | activationKey je povinný údaj. |
| 51 | Chyba instalace | Distribuce OS se nepodporuje. |
| 60 | Chyba instalace | prostředí je povinné. |
| 70 | Chyba instalace | Neznámý |
| 80 | Povolit chybu | Instalace služby se nezdařila |
| 90 | Povolit chybu | Spuštění služby se nezdařilo |
| 100 | Zakázat chybu | Zastavení služby se nezdařilo |
| 110 | Zakázat chybu | Nepovedlo se odebrat službu. |
| 120 | Chyba odinstalace | Zastavení služby se nezdařilo |

Pokud potřebujete další pomoc, můžete kontaktovat podporu Stackify na adrese https://support.stackify.com .