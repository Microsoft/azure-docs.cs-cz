---
title: Použití Azure identita spravované služby ve službě Azure API Management | Microsoft Docs
description: Další informace o použití identita spravované služby Azure API Management
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
ms.openlocfilehash: 98aa70935a3efbbe2edb07aade85fa3ea17ce786
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Použít identitu Azure spravované služby ve službě Azure API Management

Tento článek ukazuje, jak vytvořit identitu spravované služby pro instanci služby API Management a jak přistupovat k dalším prostředkům. Identita spravované služby vytvořené službou Azure Active Directory (Azure AD) umožňuje snadno a bezpečně přistupovat k dalším Azure AD chráněné prostředkům, jako je Azure Key Vault vaší instance služby API Management. Tato identita spravované služby je spravován nástrojem Azure a zřizovat nebo otočit všech tajných klíčů nevyžaduje. Další informace o identita spravované služby Azure najdete v tématu [spravované identita služby pro prostředky Azure](../active-directory/msi-overview.md).

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>Vytvoření identity spravované služby pro instance služby API Management

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete nastavit identita spravované služby v portálu, bude nejprve vytvořit jako normální instance služby API Management a potom povolte funkci.

1. Vytvoření instance API Management na portálu jako obvykle. Přejděte na ni na portálu.
2. Vyberte **identita spravované služby**.
3. Přepnout na registraci v Azure Active Directory. Klikněte na tlačítko Uložit.

![Povolit MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manager

Instance služby API Management můžete vytvořit s identitou, včetně následující vlastnost v definici prostředků: 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Tato hodnota informuje Azure k vytváření a správě identity pro vaše instance služby API Management. 

Například dokončení šablony Azure Resource Manageru, může vypadat následovně:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
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
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Používat pro přístup k dalším prostředkům identita spravované služby

> [!NOTE]
> V současné době identitu spravované služby je možné použít k získání certifikátů z Azure Key Vault pro názvy vlastních domén API Management. Další scénáře bude brzy podporované.
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Získejte certifikát od Azure Key Vault

#### <a name="prerequisites"></a>Požadavky
1. Key Vault, který obsahuje certifikát pfx musí být ve stejném předplatném Azure a stejné skupině prostředků jako služba API Management. Toto je požadavek šablony Azure Resource Manageru. 
2. Typ obsahu tajný klíč musí být *application/x-pkcs12*. Následující skript můžete použít k nahrání certifikátu:

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
> Verze objektu certifikátu není k dispozici, rozhraní API správy obdrží novější verzi certifikát automaticky po odeslání do Key Vault. 

Následující příklad ukazuje šablonu Azure Resource Manager, který obsahuje následující kroky:

1. Vytvoření instance API Management s identitou, spravované služby.
2. Aktualizovat zásady přístupu instance Azure Key Vault a povolit instanci služby API Management získat tajné klíče z něj.
3. Nastavení názvu vlastní domény prostřednictvím certifikát z instance Key Vault aktualizujte instanci služby API Management.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "description": "Reference to the KeyVault certificate."
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

## <a name="next-steps"></a>Další postup

Další informace o identita spravované služby Azure:

* [Identita spravované služby pro prostředky Azure](../active-directory/msi-overview.md)
* [Šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates)

