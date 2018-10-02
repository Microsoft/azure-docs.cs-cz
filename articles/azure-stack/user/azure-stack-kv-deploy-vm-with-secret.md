---
title: Nasazení virtuálního počítače s heslem bezpečně uloženým ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nasadit virtuální počítač s použitím hesla uložená ve službě Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: e35a63a36a84316815d609afa178f9a896415c2b
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584112"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Vytvoření virtuálního počítače pomocí zabezpečeného hesla uložená ve službě Azure Stack Key Vault

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek prochází jednotlivé kroky nasazení virtuálního počítače s Windows serverem pomocí hesla uložená ve službě Azure Stack Key Vault. Použití služby key vault hesla je bezpečnější než k předání hesla jako prostý text.

## <a name="overview"></a>Přehled

Hodnoty, jako je heslo můžete uložit jako tajný klíč v trezoru klíčů služby Azure Stack. Po vytvoření tajného klíče, které na něj mohli odkazovat v šablonách Azure Resource Manageru. Použití tajných kódů pomocí Resource Manageru poskytuje následující výhody:

* Není nutné ručně zadat tajný klíč pokaždé, když nasazení prostředku.
* Můžete určit, které uživatele nebo instanční objekty přístupné tajného kódu.

## <a name="prerequisites"></a>Požadavky

* Můžete musí přihlásit k odběru nabídky, která obsahuje službu Key Vault.
* [Instalace Powershellu pro Azure Stack.](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell.](azure-stack-powershell-configure-user.md)

Následující kroky popisují proces nutný k vytvoření virtuálního počítače s načtením hesla uložená ve službě Key Vault:

1. Vytvoření tajného kódu Key Vault.
2. Aktualizace souboru azuredeploy.parameters.json.
3. Nasazení šablony.

> ! [POZNÁMKA]  
> Tyto kroky ze sady Azure Stack Development Kit, nebo z externího klienta můžete použít, pokud jsou připojené prostřednictvím sítě VPN.

## <a name="create-a-key-vault-secret"></a>Vytvoření tajného kódu Key Vault

Tento skript vytvoří trezor klíčů a uloží heslo ve službě key vault jako tajný kód. Použití `-EnabledForDeployment` parametr při vytváření trezoru klíčů. Tento parametr zajišťuje, že služby key vault může odkazovat ze šablon Azure Resource Manageru.

```PowerShell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Při spuštění předchozí skript výstup obsahuje identifikátor URI tajného kódu. Poznamenejte si tento identifikátor URI. Je nutné na něj v odkazovat [Windows nasazení virtuálního počítače s heslem v šabloně služby key vault](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Stáhněte si [101hesla virtuálního počítače zabezpečení](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) složky na svém vývojovém počítači. Tato složka obsahuje `azuredeploy.json` a `azuredeploy.parameters.json` soubory, které budete potřebovat v dalších krocích.

Upravit `azuredeploy.parameters.json` souboru souladu s hodnotami prostředí. Parametry zájmovou jsou název trezoru, skupina prostředků trezoru a tajný klíč identifikátoru URI (generovaná předchozí skript). Následující soubor je příkladem souboru parametrů:

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizace souboru azuredeploy.parameters.json

Aktualizace souboru azuredeploy.parameters.json se identifikátor URI trezoru klíčů, secretName, adminUsername hodnoty virtuálního počítače podle vašeho prostředí. Následující soubor JSON ukazuje příklad soubor parametrů šablony:

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Nasazení šablon

Nyní nasaďte šablonu pomocí následujícího skriptu prostředí PowerShell:

```PowerShell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Po úspěšném nasazení šablony, výsledkem následující výstup:

![Výstup nasazení](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Další postup

[Nasaďte ukázkovou aplikaci pomocí služby Key Vault](azure-stack-kv-sample-app.md)

[Nasazení virtuálního počítače s certifikátem Key Vaultu](azure-stack-kv-push-secret-into-vm.md)
