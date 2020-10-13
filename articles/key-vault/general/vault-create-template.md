---
title: Průvodce Azure – vytvoření trezoru klíčů Azure a zásad přístupu k trezoru pomocí šablony Azure Resource Manager | Microsoft Docs
description: Ukazuje, jak vytvořit trezory klíčů Azure a zásady přístupu k trezoru pomocí šablony Azure Resource Manager.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804337"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Postup vytvoření Azure Key Vault a zásad přístupu k trezoru pomocí šablony Správce prostředků

[Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště tajných kódů, jako jsou klíče, hesla, certifikáty a další tajné kódy. Tato příručka se zaměřuje na proces nasazení šablony Azure Resource Manager (šablona ARM) pro vytvoření trezoru klíčů.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Postup dokončení tohoto článku:

* Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-key-vault-resource-manager-template"></a>Vytvořit šablonu Key Vault Správce prostředků

Následující šablona ukazuje základní způsob vytvoření trezoru klíčů. Některé hodnoty jsou zadány v rámci šablony.

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

Další informace o Key Vault nastavení šablon naleznete v tématu [Key Vault na šablonu ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Pokud se šablona znovu nasadí, přepíše všechny existující zásady přístupu v trezoru klíčů. Doporučuje se naplnit `accessPolicies` vlastnost existujícími zásadami přístupu, abyste se vyhnuli ztrátě přístupu k trezoru klíčů. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Přidání zásad přístupu k šabloně Key Vault Správce prostředků

Zásady přístupu můžete nasadit do existujícího trezoru klíčů bez opětovného nasazení celé šablony trezoru klíčů. Následující šablona ukazuje základní způsob, jak vytvořit zásady přístupu.

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
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Další informace o Key Vault nastavení šablon naleznete v tématu [Key Vault na šablonu ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Další dostupné Key Vault šablony Správce prostředků

K dispozici jsou jiné šablony Správce prostředků pro objekty Key Vault:

| Tajné kódy | Klíče | Certifikáty |
|--|--|--|
|[Rychlý start](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Odkaz](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N/A|N/A|

Další Key Vault šablony najdete tady: [Key Vault správce prostředků odkazem](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions) .

## <a name="deploy-the-templates"></a>Nasazení šablon

Pomocí Azure Portal můžete nasadit výše uvedené šablony pomocí možnosti sestavit vlastní šablonu v editoru v následující příručce: [nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) .

Můžete také uložit výše uvedené šablony do souborů a použít následující příkazy:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat a pracovat s dalšími rychlými starty a kurzy, můžete tyto prostředky ponechat na místě. Pokud už prostředky nepotřebujete, odstraňte skupinu prostředků, která odstraní Trezor klíčů a související prostředky. Pokud chcete odstranit skupinu prostředků pomocí Azure CLI nebo Azure PowerShell, použijte následující postup.

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

## <a name="resources"></a>Zdroje a prostředky

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace o [Azure Resource Manageru](../../azure-resource-manager/management/overview.md)
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)

## <a name="next-steps"></a>Další kroky

- [Zabezpečený přístup k trezoru klíčů](secure-your-key-vault.md)
- [Ověřování pro Trezor klíčů](authentication.md)
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)
