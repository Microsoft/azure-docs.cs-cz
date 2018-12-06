---
title: Použití Azure se identita spravované služby ve službě Azure API Management | Dokumentace Microsoftu
description: Další informace o použití Identity spravované služby Azure ve službě API Management
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
ms.openlocfilehash: b7208943a27bcd184100ae426721a2fe8f6e1c72
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970480"
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Použití identit spravovaných služeb Azure ve službě Azure API Management

V tomto článku se dozvíte, jak vytvořit identitu spravované služby pro instanci služby API Management a jak získat přístup k dalším prostředkům. Identita spravované služby vygenerované službou Azure Active Directory (Azure AD) umožňuje snadno a bezpečný přístup k jiné AD chráněné prostředky Azure, jako je Azure Key Vault vaší instance služby API Management. Tato identita spravované služby spravuje Azure a není nutné zřizovat nebo otočit jakýchkoli tajných kódů. Další informace o identitu spravované služby Azure najdete v tématu [identita spravované služby pro prostředky Azure](../active-directory/msi-overview.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>Vytvoření identity spravované služby pro instance služby API Management

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Nastavit identitu spravované služby na portálu, nejprve vytvoříte instanci služby API Management jako za normálních okolností a povolte funkci.

1. Vytvoření instance služby API Management na portálu jako obvykle. Přejděte na ni na portálu.
2. Vyberte **se identita spravované služby**.
3. Přepněte na registru pomocí Azure Active Directory. Klikněte na Uložit.

![Povolení MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru

Instance služby API Management s identitou, můžete vytvořit přidáním následující vlastnost v definici prostředků: 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

To říká Azure můžete vytvářet a spravovat identity pro vaše instance služby API Management. 

Například úplnou šablonu Azure Resource Manageru, může vypadat takto:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Použití služby managed service identity pro přístup k dalším prostředkům

> [!NOTE]
> Identita spravované služby je v současné době možné získat certifikáty ze služby Azure Key Vault pro API Management vlastních názvů domén. Brzy bude podporováno více scénářů.
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Získání certifikátu ze služby Azure Key Vault

#### <a name="prerequisites"></a>Požadavky
1. Key Vault, který obsahuje certifikát pfx musí být ve stejném předplatném Azure ve stejné skupině prostředků jako služba API Management. Jde o požadavek šablony Azure Resource Manageru. 
2. Typ obsahu tajného klíče musí být *application/x-pkcs12*. Na kterou odešlete certifikát můžete použít následující skript:

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
> Verze objektu certifikátu není k dispozici, API Management získá novější verzi certifikát automaticky po odeslání do služby Key Vault. 

Následující příklad ukazuje šablonu Azure Resource Manageru, který obsahuje následující kroky:

1. Vytvoření instance API managementu s využitím identity spravované služby.
2. Aktualizovat zásady přístupu instance služby Azure Key Vault a umožnit instance API Management k získání tajné kódy z něj.
3. Aktualizace instance API Management tak, že nastavíte vlastního názvu domény pomocí certifikátu z instance služby Key Vault.

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

Další informace o identitu spravované služby Azure:

* [Identita spravované služby pro prostředky Azure](../active-directory/msi-overview.md)
* [Šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates)

