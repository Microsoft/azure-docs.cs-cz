---
title: Použití spravovaných identit ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat spravované identity ve správě rozhraní API
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249630"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Použití spravovaných identit ve správě Azure API

Tento článek ukazuje, jak vytvořit spravovanou identitu pro instanci služby správy rozhraní API a jak získat přístup k dalším prostředkům. Spravovaná identita generovaná službou Azure Active Directory (Azure AD) umožňuje vaší instanci správy rozhraní API snadný a bezpečný přístup k dalším prostředkům chráněným azure ad, jako je Azure Key Vault. Tato identita je spravována Azure a nevyžaduje, abyste zřídit nebo otočit žádné tajné klíče. Další informace o spravovaných identitách najdete v tématu [Co je spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Vytvoření spravované identity pro instanci správy rozhraní API

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Chcete-li nastavit spravovanou identitu na portálu, nejprve vytvoříte instanci správy rozhraní API jako normální a poté tuto funkci povolíte.

1. Vytvořte instanci správy rozhraní API na portálu jako obvykle. Přejděte na něj na portálu.
2. Vyberte **identity spravované služby**.
3. Přepněte registraci pomocí služby Azure Active Directory na Zapnuto. Klikněte na Uložit.

![Povolení MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

Můžete vytvořit instanci správy rozhraní API s identitou zahrnutím následující vlastnosti do definice prostředku:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

To říká Azure k vytvoření a správě identity pro instanci api managementu.

Například kompletní šablona Azure Resource Manager může vypadat takto:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Použití identity spravované služby pro přístup k jiným prostředkům

> [!NOTE]
> V současné době spravované identity lze získat certifikáty z Azure Key Vault pro správu rozhraní API vlastní názvy domén. Brzy budou podporovány další scénáře.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Získání certifikátu z trezoru klíčů Azure

#### <a name="prerequisites"></a>Požadavky
1. Trezor klíčů obsahující certifikát pfx musí být ve stejném předplatném Azure a ve stejné skupině prostředků jako služba API Management. Toto je požadavek šablony Azure Resource Manager.
2. Typ obsahu tajného klíče musí být *application/x-pkcs12*. K nahrání certifikátu můžete použít následující skript:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Pokud není k dispozici verze objektu certifikátu, správa rozhraní API automaticky získá novější verzi certifikátu po jeho odeslání do trezoru klíčů.

Následující příklad ukazuje šablonu Azure Resource Manager, která obsahuje následující kroky:

1. Vytvořte instanci správy rozhraní API se spravovanou identitou.
2. Aktualizujte zásady přístupu instance Azure Key Vault a povolte instanci api Management získat z ní tajné klíče.
3. Aktualizujte instanci správy rozhraní API nastavením vlastního názvu domény prostřednictvím certifikátu z instance Trezoru klíčů.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných identitách pro prostředky Azure:

* [Co se spravuje identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates)
* [Ověření pomocí spravované identity v zásadách](./api-management-authentication-policies.md#ManagedIdentity)
