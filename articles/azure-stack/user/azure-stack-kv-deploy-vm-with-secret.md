---
title: Nasazení virtuálního počítače s heslem bezpečně uložené v zásobníku Azure | Microsoft Docs
description: Informace o nasazení virtuálního počítače pomocí hesla uložená v Azure zásobníku Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: 4239eb31afd4abc8b3555f0ee353f5d96716d623
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068971"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Vytvoření virtuálního počítače pomocí zabezpečeného hesla uložená v Azure zásobníku Key Vault

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Tento článek obsahuje kroky prostřednictvím nasazení virtuálního počítače Windows serveru použít heslo uložené v Azure zásobníku Key Vault. Pomocí hesla trezoru klíčů je bezpečnější než předávání hesla v podobě prostého textu.

## <a name="overview"></a>Přehled

Hodnoty jako například heslo můžete uložit jako tajný klíč v trezoru klíčů služby Azure zásobníku. Po vytvoření tajného klíče, můžete ho odkazovat v šablonách Azure Resource Manager. Pomocí tajných klíčů s Resource Manager poskytuje následující výhody:

* Nemusíte ručně zadejte tajný klíč pokaždé, když nasazujete prostředku.
* Můžete určit, kteří uživatelé nebo objekty služby můžete přístup k tajný klíč.

## <a name="prerequisites"></a>Požadavky

* Musí se přihlásíte na nabídku obsahující službu Key Vault.
* [Instalace prostředí PowerShell pro Azure zásobníku.](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele.](azure-stack-powershell-configure-user.md)

Následující kroky popisují proces nutný k vytvoření virtuálního počítače načtením heslo uložené v Key Vault:

1. Vytvořte tajný klíč trezoru.
2. Aktualizujte soubor azuredeploy.parameters.json.
3. Nasazení šablony.

>[POZNÁMKA] Tyto kroky ze Development Kit zásobník Azure nebo z externích klientských můžete použít, pokud jsou připojené prostřednictvím sítě VPN.

## <a name="create-a-key-vault-secret"></a>Vytvoření tajný klíč trezoru

Následující skript vytvoří trezoru klíčů a ukládá heslo v trezoru klíčů jako tajný klíč. Použití `-EnabledForDeployment` parametr při vytváření trezoru klíčů. Tento parametr je zajištěno, že trezor klíčů můžete na něj odkazovat z šablon Azure Resource Manageru.

```powershell

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

Když spustíte předchozí skript, zahrnuje tajný identifikátor URI. Poznamenejte si tento identifikátor URI. Máte na něj v odkazovat [nasazení systému Windows virtuálního počítače s heslem v šabloně trezoru klíčů](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Stažení [101-vm zabezpečené heslo](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) složka na vývojovém počítači. Tato složka obsahuje `azuredeploy.json` a `azuredeploy.parameters.json` soubory, které budete potřebovat v dalších krocích.

Změnit `azuredeploy.parameters.json` soubor na základě hodnoty prostředí. Parametry zajímají jsou název trezoru, skupině prostředků úložiště a tajný klíč identifikátor URI (generovaná předchozí skript). Následující soubor je příklad souboru parametrů:

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizovat soubor azuredeploy.parameters.json

Aktualizujte soubor azuredeploy.parameters.json URI KeyVault, secretName, adminUsername hodnoty virtuálního počítače podle vašeho prostředí. Následující soubor JSON ukazuje příklad souboru parametrů šablony:

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

Nyní nasazení šablony pomocí následujícího skriptu prostředí PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Při nasazení šablony úspěšně výsledkem následující výstup:

![Výstup nasazení](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Další postup

[Nasazení ukázkové aplikace v Key Vault](azure-stack-kv-sample-app.md)

[Nasadit virtuální počítač s certifikátem Key Vault](azure-stack-kv-push-secret-into-vm.md)
