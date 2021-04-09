---
title: Použití spravovaných identit v Azure API Management | Microsoft Docs
description: Naučte se vytvářet identity přiřazené systémem a uživatelem v API Management pomocí Azure Portal, PowerShellu a šablony pro Správce prostředků.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 98237efae89e7d88dd23cb7e8fc9f7e9f05bca70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521539"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Použití spravovaných identit v Azure API Management

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro instanci Azure API Management a jak získat přístup k dalším prostředkům. Spravovaná identita generovaná službou Azure Active Directory (Azure AD) umožňuje vaší instanci API Management snadno a bezpečně přistupovat k dalším prostředkům chráněným službou Azure AD, jako je třeba Azure Key Vault. Azure spravuje tuto identitu, takže nemusíte zřizovat ani střídat tajné klíče. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).

API Management instanci můžete udělit dva typy identit:

- *Identita přiřazená systémem* je vázaná na vaši službu a při odstranění vaší služby se odstraní. Služba může mít pouze jednu identitu přiřazenou systémem.
- *Uživatelsky přiřazená identita* je samostatný prostředek Azure, který je možné přiřadit ke službě. Služba může mít více uživatelsky přiřazených identit.

## <a name="create-a-system-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené systémem

### <a name="azure-portal"></a>portál Azure

Pokud chcete nastavit spravovanou identitu v Azure Portal, vytvoříte nejprve instanci API Management a pak tuto funkci povolíte.

1. Na portálu vytvořte instanci API Management, normálně by to bylo. Přejděte na portál na portálu.
2. Vyberte **spravované identity**.
3. Na kartě **přiřazeno systému** přepněte **stav** na **zapnuto**. Vyberte **Uložit**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Výběry pro povolení spravované identity přiřazené systémem" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením instance API Management a přiřazením identity pomocí Azure PowerShell. 

1. V případě potřeby nainstalujte Azure PowerShell pomocí pokynů v [příručce Azure PowerShell](/powershell/azure/install-az-ps). Pak spusťte příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

2. Použijte následující kód k vytvoření instance. Další příklady použití Azure PowerShell s instancí API Management najdete v tématu [API Management ukázek PowerShellu](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Aktualizace existující instance pro vytvoření identity:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Můžete vytvořit instanci API Management s identitou, a to zahrnutím následující vlastnosti do definice prostředků:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Tato vlastnost oznamuje službě Azure, aby vytvořila a spravovala identitu vaší instance API Management.

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

`tenantId`Vlastnost určuje, do jakého tenanta Azure AD patří identita. `principalId`Vlastnost je jedinečný identifikátor pro novou identitu instance. V rámci služby Azure AD má instanční objekt stejný název, který jste zadali vaší instanci API Management.

> [!NOTE]
> Instance API Management může současně mít přiřazené i uživatelsky přiřazené identity. V tomto případě `type` by vlastnost byla `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Podporované scénáře využívající identitu přiřazenou systémem

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Získání vlastního certifikátu TLS/SSL pro instanci API Management z Azure Key Vault
K načtení vlastních certifikátů TLS/SSL uložených v Azure Key Vault můžete použít identitu instance API Management přiřazenou systémem. Tyto certifikáty pak můžete přiřadit k vlastním doménám v instanci API Management. Upozorňujeme na tyto skutečnosti:

- Typ obsahu tajného kódu musí být *Application/x-PKCS12*.
- Použijte koncový bod tajného certifikátu Key Vault, který obsahuje tajný klíč.

> [!Important]
> Pokud neposkytnete verzi objektu certifikátu, API Management bude automaticky získávat novější verzi certifikátu do čtyř hodin poté, co se aktualizuje v Key Vault.

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
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
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

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Ověřování pro back-end pomocí API Management identity

Identitu přiřazenou systémem můžete použít k ověření do back-endu prostřednictvím zásad [ověřování spravované-identity](api-management-authentication-policies.md#ManagedIdentity) .

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Připojení k prostředkům Azure za branou firewall protokolu IP pomocí spravované identity přiřazené systémem


API Management je důvěryhodná služba Microsoftu pro následující prostředky. To umožňuje službě připojit se k následujícím prostředkům za bránou firewall. Po explicitním přiřazení příslušné role Azure k [spravované identitě přiřazené systémem](../active-directory/managed-identities-azure-resources/overview.md) pro danou instanci prostředku bude rozsah přístupu instance odpovídat roli Azure přiřazené spravované identitě.


|Služba Azure | Odkaz|
|---|---|
|Azure Storage | [Trusted – přístup k Azure-Storage](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Trusted – přístup k Azure-Service-Bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Trused-přístup-k-Azure – centrum událostí](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

> [!NOTE]
> Instanci API Management můžete přidružit až 10 spravovaných identit přiřazených uživatelem.

### <a name="azure-portal"></a>portál Azure

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte instanci API Management a pak tuto funkci povolte.

1. Na portálu vytvořte instanci API Management, normálně by to bylo. Přejděte na portál na portálu.
2. Vyberte **spravované identity**.
3. Na kartě **přiřazený uživatel** vyberte **Přidat**.
4. Vyhledejte identitu, kterou jste vytvořili dříve, a vyberte ji. Vyberte **Přidat**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Výběry pro povolení spravované identity přiřazené uživatelem" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením instance API Management a přiřazením identity pomocí Azure PowerShell. 

1. V případě potřeby nainstalujte Azure PowerShell pomocí pokynů v [příručce Azure PowerShell](/powershell/azure/install-az-ps). Pak spusťte příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

2. Použijte následující kód k vytvoření instance. Další příklady použití Azure PowerShell s instancí API Management najdete v tématu [API Management ukázek PowerShellu](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Aktualizace existující služby pro přiřazení identity ke službě:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

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

`principalId`Vlastnost je jedinečný identifikátor pro identitu, která se používá pro správu Azure AD. `clientId`Vlastnost je jedinečný identifikátor pro novou identitu aplikace, která se používá k určení identity, která se má použít při volání za běhu.

> [!NOTE]
> Instance API Management může současně mít přiřazené i uživatelsky přiřazené identity. V tomto případě `type` by vlastnost byla `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Podporované scénáře použití spravované identity přiřazené uživatelem

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Získání vlastního certifikátu TLS/SSL pro instanci API Management z Azure Key Vault
K navázání vztahu důvěryhodnosti mezi API Management instancí a trezorem klíčů můžete použít libovolnou uživatelskou identitu. Tento vztah důvěryhodnosti pak můžete použít k načtení vlastních certifikátů TLS/SSL uložených v Azure Key Vault. Tyto certifikáty pak můžete přiřadit k vlastním doménám v instanci API Management. 

Upozorňujeme na tyto skutečnosti:

- Typ obsahu tajného kódu musí být *Application/x-PKCS12*.
- Použijte koncový bod tajného certifikátu Key Vault, který obsahuje tajný klíč.

> [!Important]
> Pokud neposkytnete verzi objektu certifikátu, API Management bude automaticky získávat novější verzi certifikátu do čtyř hodin poté, co se aktualizuje v Key Vault.

Úplnou šablonu najdete v tématu [API Management s protokolem SSL založeném na trezoru klíčů pomocí identity přiřazené uživatelem](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

V této šabloně nasadíte:

* Azure API Management
* Identita přiřazená ke spravovanému uživateli Azure
* Azure webrecovery pro uložení certifikátu SSL/TLS

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Ověřování v back-endu pomocí uživatelsky přiřazené identity

K ověření v back-endu pomocí zásad [ověřování spravovaného pomocí identity](api-management-authentication-policies.md#ManagedIdentity) můžete použít uživatelem přiřazenou identitu.

## <a name="remove-an-identity"></a><a name="remove"></a>Odebrání identity

Identitu přiřazenou systémem můžete odebrat tak, že tuto funkci zakážete prostřednictvím portálu nebo šablony Azure Resource Manager stejným způsobem jako v případě, že byla vytvořena. Uživatelsky přiřazené identity je možné odebrat jednotlivě. Pokud chcete odebrat všechny identity, nastavte typ identity na `"None"` .

Odebrání identity přiřazené systémem tímto způsobem ji odstraní také z Azure AD. Identity přiřazené systémem se při odstranění instance API Management automaticky z Azure AD odeberou.

Pokud chcete odebrat všechny identity pomocí šablony Azure Resource Manager, aktualizujte tuto část:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Pokud je instance API Management nakonfigurovaná s vlastním certifikátem SSL z Key Vault a pokusíte se zakázat spravovanou identitu, požadavek se nezdaří.
>
> Odblokování můžete odblokovat tak, že přepnete z Azure Key Vault certifikátu na vložený kódovaný certifikát a zakážete spravovanou identitu. Další informace najdete v tématu [Konfigurace vlastního názvu domény](configure-custom-domain.md).

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných identitách pro prostředky Azure:

* [Jaké jsou spravované identity prostředků Azure?](../active-directory/managed-identities-azure-resources/overview.md)
* [Šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates)
* [Ověřování pomocí spravované identity v zásadě](./api-management-authentication-policies.md#ManagedIdentity)
