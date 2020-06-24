---
title: Použití spravovaných identit v Azure API Management | Microsoft Docs
description: Naučte se používat spravované identity v API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 028b26537c9fe8a976dbc68a4776b2ea4101d811
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789397"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Použití spravovaných identit v Azure API Management

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro instanci API Management a jak získat přístup k dalším prostředkům. Spravovaná identita generovaná službou Azure Active Directory (Azure AD) umožňuje vaší instanci API Management snadno a bezpečně přistupovat k dalším prostředkům chráněným službou Azure AD, jako je třeba Azure Key Vault. Tuto identitu spravuje Azure a nevyžaduje zřizování ani navýšení tajných kódů. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

Instanci API Management lze udělit dvěma typům identit:

- **Identita přiřazená systémem** je vázaná na vaši službu a při odstranění vaší služby se odstraní. Služba může mít pouze jednu identitu přiřazenou systémem.
- **Uživatelsky přiřazená identita** je samostatný prostředek Azure, který je možné přiřadit ke službě. Služba může mít více uživatelsky přiřazených identit (*).

## <a name="create-a-system-assigned-managed-identity-for-an-api-management-instance"></a>Vytvoření spravované identity přiřazené systémem pro instanci API Management

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte instanci API Management jako normální a pak tuto funkci povolte.

1. Na portálu vytvořte instanci API Management, normálně by to bylo. Přejděte na portál na portálu.
2. Vyberte **spravované identity**.
3. V rámci karty **přiřazené systémem** přepněte **stav** na **zapnuto**. Klikněte na **Uložit**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Povolte spravovanou identitu přiřazenou systémem." border="true":::


### <a name="using-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením instance API Management a přiřazením identity pomocí Azure PowerShell. 

1. V případě potřeby nainstalujte Azure PowerShell pomocí pokynů uvedených v [příručce Azure PowerShell](/powershell/azure/install-az-ps)a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

2. Vytvořte instanci API Management pomocí Azure PowerShell. Další příklady použití Azure PowerShell s instancí API Management najdete v tématu [API Management ukázek PowerShellu](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Aktualizace a existující instance pro vytvoření identity:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="using-the-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

Můžete vytvořit instanci API Management s identitou, a to zahrnutím následující vlastnosti do definice prostředků:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

To oznamuje službě Azure, aby vytvořila a spravovala identitu vaší instance API Management.

Například kompletní šablona Azure Resource Manager může vypadat takto:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
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

Při vytvoření instance má následující další vlastnosti:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Vlastnost tenantId určuje, do kterého tenanta Azure AD patří identita. PrincipalId je jedinečný identifikátor instance nové identity. V rámci služby Azure AD má instanční objekt stejný název, který jste zadali vaší instanci API Management.


> [!NOTE]
> Instance API Management může současně mít přiřazené i uživatelsky přiřazené identity. V tomto případě `type` by vlastnost byla`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Podporované scénáře

#### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Získání vlastního certifikátu TLS/SSL pro instanci API Management z Azure Key Vault
Identitu služby API Management přiřazenou systémem můžete použít k načtení vlastních certifikátů TLS/SSL uložených v Azure Key Vault. Tyto certifikáty je možné přiřadit k vlastním doménám v instanci API Management.

1. Typ obsahu tajného kódu musí být *Application/x-PKCS12*.
2. Měl by se použít koncový bod tajného certifikátu Key Vault, který obsahuje skutečný tajný klíč.

> [!Important]
> Pokud není k dispozici verze objektu certifikátu, bude API Management po nahrání do Key Vault během 4 hodin automaticky získat novější verzi certifikátu.

Následující příklad ukazuje šablonu Azure Resource Manager, která obsahuje následující kroky:

1. Vytvořte instanci API Management se spravovanou identitou.
2. Aktualizujte zásady přístupu instance Azure Key Vault a umožněte, aby instance služby API Management z ní získala tajné kódy.
3. Aktualizujte instanci API Management nastavením vlastního názvu domény pomocí certifikátu z instance Key Vault.

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
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
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
        "apiVersion": "2019-01-01",
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

#### <a name="authenticate-using-api-management-identity-to-the-backend"></a>Ověřování pomocí API Management identity k back-endu

Identitu přiřazenou systémem můžete použít k ověření do back-endu pomocí zásad [ověřování spravovaného identity](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="create-a-user-assigned-managed-identity-for-an-api-management-instance"></a>Vytvoření spravované identity přiřazené uživatelem pro instanci API Management

> [!NOTE]
> Instance API Management může být přidružená k spravované identitě přiřazené uživatelem až 10.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte instanci API Management jako normální a pak tuto funkci povolte.

1. Na portálu vytvořte instanci API Management, normálně by to bylo. Přejděte na portál na portálu.
2. Vyberte **spravované identity**.
3. Na kartě **přiřazené uživatelem** klikněte na tlačítko **Přidat**.
4. Vyhledejte identitu, kterou jste vytvořili dříve, a vyberte ji. Klikněte na tlačítko **Add** (Přidat).

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Povolit spravovanou identitu přiřazenou uživatelem" border="true":::

### <a name="using-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením instance API Management a přiřazením identity pomocí Azure PowerShell. 

1. V případě potřeby nainstalujte Azure PowerShell pomocí pokynů uvedených v [příručce Azure PowerShell](/powershell/azure/install-az-ps)a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

2. Vytvořte instanci API Management pomocí Azure PowerShell. Další příklady použití Azure PowerShell s instancí API Management najdete v tématu [API Management ukázek PowerShellu](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Aktualizuje a stávající služba, aby službu přiřadila identitu.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="using-the-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

Můžete vytvořit instanci API Management s identitou, a to zahrnutím následující vlastnosti do definice prostředků:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Když se přidá uživatelem přiřazený typ, sdělí Azure, aby používal identitu přiřazenou uživateli zadanou pro vaši instanci.

Například kompletní šablona Azure Resource Manager může vypadat takto:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
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
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Když je služba vytvořená, má následující další vlastnosti:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

PrincipalId je jedinečný identifikátor pro identitu, která se používá pro správu Azure AD. ClientId je jedinečný identifikátor pro novou identitu aplikace, která se používá k určení identity, která se má použít během volání za běhu.

> [!NOTE]
> Instance API Management může současně mít přiřazené i uživatelsky přiřazené identity. V tomto případě `type` by vlastnost byla`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Podporované scénáře

#### <a name="authenticate-using-user-assigned-identity-to-the-backend"></a>Ověřování pomocí uživatelem přiřazené identity k back-endu

Identita přiřazená uživatelem se dá použít k ověření do back-endu pomocí zásad [ověřování spravovaného identity](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="remove-an-identity"></a><a name="remove"></a>Odebrání identity

Identitu přiřazenou systémem je možné odebrat zakázáním této funkce pomocí portálu nebo šablony Azure Resource Manager stejným způsobem jako v případě, že byla vytvořena. Uživatelsky přiřazené identity je možné odebrat jednotlivě. Pokud chcete odebrat všechny identity, nastavte typ identity na None (žádné).

Odebrání identity přiřazené systémem tímto způsobem ji odstraní také z Azure AD. Identity přiřazené systémem se při odstranění instance API Management automaticky z Azure AD odeberou.

Pokud chcete odebrat všechny identity pomocí šablony Azure Resource Manager, aktualizujte tuto část:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Pokud je instance API Management nakonfigurovaná s vlastním certifikátem SSL z trezoru klíčů a pokusí se o vypnutí spravované identity, požadavek se nezdařil.
> Zákazník může odblokovat samy sebe přepnutím z Azure Key Vault certifikátu na poskytnutí vloženého kódovaného certifikátu a vypnutím spravované identity. Podívejte se na téma [Konfigurace vlastní domény](configure-custom-domain.md) .

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných identitách pro prostředky Azure:

* [Co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates)
* [Ověřování pomocí spravované identity v zásadě](./api-management-authentication-policies.md#ManagedIdentity)
