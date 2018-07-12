---
title: Tajný kód trezoru klíčů pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Ukazuje, jak předat tajného klíče ze služby key vault jako parametr během nasazení.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: 3a29319a0d478537dfc4905ee77865b8fea64587
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598403"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení

Pokud potřebujete předat jako parametr zabezpečenou hodnotu (jako jsou hesla) během nasazení, můžete načíst hodnotu z [Azure Key Vault](../key-vault/key-vault-whatis.md). Načtení hodnoty pomocí odkazu na trezor klíčů a tajný klíč v souboru parametrů. Hodnota se nikdy vystavena, protože pouze odkazujete na jeho ID služby key vault. Trezor klíčů může existovat v jiném předplatném než skupina prostředků, které nasazujete.

## <a name="enable-access-to-the-secret"></a>Povolení přístupu k tajný kód

Existují dvě důležité podmínky, které musí existovat pro přístup k trezoru klíčů během nasazování šablony:

1. Vlastnosti trezoru klíčů `enabledForTemplateDeployment` musí být `true`.
2. Uživatel nasazení šablony musí mít přístup k tajný kód. Uživatel musí mít `Microsoft.KeyVault/vaults/deploy/action` oprávnění pro key vault. [Vlastníka](../role-based-access-control/built-in-roles.md#owner) a [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) obě role udělit přístup.

Při použití služby Key Vault se šablonou pro [spravované aplikace](../managed-applications/overview.md), je nutné udělit přístup k **poskytovatele prostředků zařízení** instančního objektu. Další informace najdete v tématu [tajného kódu trezoru klíčů přístup při nasazování služby Azure Managed Applications](../managed-applications/key-vault-access.md).


## <a name="deploy-a-key-vault-and-secret"></a>Nasazení služby key vault a tajný klíč

Pokud chcete vytvořit trezor klíčů a tajný klíč, pomocí Azure CLI nebo Powershellu. Všimněte si, že služby key vault je povoleno pro šablonu nasazení. 

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Pokud používáte PowerShell, použijte:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="reference-a-secret-with-static-id"></a>Odkazovat na tajný kód se statické ID

Stejně jako jakékoli jiné šablony je šablona, která přijímá tajný kód trezoru klíčů. Důvodem je, že **odkazujete službě key vault v souboru parametrů není šablona.** Následující obrázek ukazuje, jak soubor parametrů odkazuje na tajný kód a předá tuto hodnotu do šablony.

![Statické ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Například [následující šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) nasadí, který obsahuje heslo správce databáze SQL. Parametr hesla je nastaven na zabezpečený řetězec. Ale šablona neurčuje, kde tato hodnota pochází.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Teď vytvořte soubor parametrů pro předchozí šablonu postupem. V souboru parametrů zadejte parametr, který odpovídá názvu parametru v šabloně. Pro hodnotu parametru odkazujte tajný kód trezoru klíčů. Tajný kód odkazujete předáním identifikátor prostředku trezoru klíčů a název tajného kódu. V [následující parametr soubor](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json), již musí existovat tajný kód trezoru klíčů, a zadejte statickou hodnotu pro jeho ID prostředku. Zkopírujte tento soubor místně a nastavit ID předplatného, název trezoru a název systému SQL server.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Pokud je potřeba použít verzi tajného kódu, než je aktuální verzi, použijte `secretVersion` vlastnost.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Nyní nasaďte šablonu a předejte parametr soubor. Můžete použít šablonu příkladu z Githubu, ale je nutné použít souboru místní parametrů s hodnot nastavených pro vaše prostředí.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Odkazovat na tajný kód se dynamické ID

V předchozí části jsme si ukázali, jak předat ID statických prostředků pro tajný kód trezoru klíčů. Nicméně v některých případech budete muset odkaz tajný kód trezoru klíčů, který se liší podle aktuální nasazení. V takovém případě nemůžete pevně zakódovat ID prostředku v souboru parametrů. Bohužel nelze generovat dynamicky ID prostředku v souboru parametrů vzhledem k tomu, že šablona výrazy nejsou povoleny v souboru parametrů.

K dynamickému generování ID prostředku pro tajný kód trezoru klíčů, musíte přesunout prostředek, který se vyžaduje tajný kód na propojenou šablonu. V nadřazené šablony můžete přidat propojené šablony a předejte parametr, který obsahuje ID dynamicky generované prostředku. Následující obrázek ukazuje, jak v propojené šablony parametr odkazuje na tajný kód.

![Dynamické ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Propojené šablony musí být k dispozici prostřednictvím externí identifikátor URI. Obvykle, přidejte šablonu do účtu úložiště a přistupujte k němu prostřednictvím identifikátoru URI jako `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

[Následující šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) dynamicky vytvoří ID služby key vault a předá jej jako parametr. Odkazy na [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) v Githubu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Nasadit předchozí šablonu a zadejte hodnoty pro parametry. Můžete použít šablonu příkladu z Githubu, ale je nutné zadat hodnoty parametrů pro vaše prostředí.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>Další postup
* Obecné informace o trezorů klíčů najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md).
* Kompletní příklady odkazující na klíče tajných kódů, najdete v článku [služby Key Vault příklady](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
