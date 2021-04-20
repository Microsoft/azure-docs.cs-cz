---
title: Vytvoření trezoru klíčů Azure a zásad přístupu k trezoru pomocí šablony ARM
description: Tento článek ukazuje, jak vytvořit trezory klíčů Azure a zásady přístupu k trezoru pomocí šablony Azure Resource Manager.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: e70906cbf26c899744bfbe137da4ce9cfa651b20
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753141"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Vytvoření trezoru klíčů Azure a zásad přístupu k trezoru pomocí šablony Správce prostředků

[Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště pro tajné klíče, jako jsou klíče, hesla a certifikáty. Tento článek popisuje proces nasazení šablony Azure Resource Manager (šablona ARM) pro vytvoření trezoru klíčů.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Postup dokončení kroků v tomto článku:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="create-a-key-vault-resource-manager-template"></a>Vytvoření šablony Key Vault Správce prostředků

Následující šablona ukazuje základní způsob vytvoření trezoru klíčů. V šabloně jsou zadány některé hodnoty.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Další informace o Key Vault nastavení šablon naleznete v tématu [Key Vault na šablonu ARM](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Pokud se šablona znovu nasadí, budou se všechny existující zásady přístupu v trezoru klíčů přepsat. `accessPolicies`Pro zamezení ztráty přístupu k trezoru klíčů doporučujeme naplnit vlastnost stávajícími zásadami přístupu. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Přidání zásady přístupu do šablony Key Vault Správce prostředků

Zásady přístupu můžete nasadit do existujícího trezoru klíčů, aniž byste museli znovu nasazovat celou šablonu trezoru klíčů. Následující šablona ukazuje základní způsob vytváření zásad přístupu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

Další informace o nastavení šablon Key Vault naleznete v tématu [Key Vault na šablonu ARM](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Další šablony Key Vault Správce prostředků

K dispozici jsou jiné šablony Správce prostředků pro objekty Key Vault:

| Tajné kódy | Klíče | Certifikáty |
|--|--|--|
|<ul><li>[Rychlý start](../secrets/quick-create-template.md)<li>[Odkaz](/azure/templates/microsoft.keyvault/vaults/secrets)|N/A|N/A|

Další Key Vault šablony můžete najít tady: [Key Vault správce prostředků odkazem](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Nasazení šablon

Pomocí Azure Portal můžete nasadit předchozí šablony pomocí možnosti **vytvořit vlastní šablonu v editoru** , jak je popsáno zde: [nasazení prostředků z vlastní šablony](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

Předchozí šablony můžete také uložit do souborů a použít tyto příkazy:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat s dalšími rychlými starty a kurzy, můžete tyto prostředky ponechat na místě. Když už prostředky nepotřebujete, odstraňte skupinu prostředků. Pokud skupinu odstraníte, odstraní se taky Trezor klíčů a související prostředky. Pokud chcete odstranit skupinu prostředků pomocí Azure CLI nebo Azure PowerShell, proveďte tyto kroky:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Zdroje informací

- Přečtěte si [přehled Azure Key Vault](../general/overview.md).
- Přečtěte si další informace o [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Přečtěte si [Přehled zabezpečení Azure Key Vault](security-overview.md)

## <a name="next-steps"></a>Další kroky

- [Zabezpečení přístupu k trezoru klíčů](security-overview.md)
- [Ověřování pro Trezor klíčů](authentication.md)
- [Průvodce vývojáře pro službu Azure Key Vault](developers-guide.md)
