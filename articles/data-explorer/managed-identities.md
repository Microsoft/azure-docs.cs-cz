---
title: Postup konfigurace spravovaných identit pro cluster Azure Průzkumník dat
description: Naučte se konfigurovat spravované identity pro cluster Azure Průzkumník dat.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373370"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurace spravovaných identit pro cluster Azure Průzkumník dat

[Spravovaná identita z Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) umožňuje vašemu clusteru snadný přístup k dalším prostředkům chráněným pomocí AAD, jako je Azure Key Vault. Identita je spravovaná platformou Azure a nevyžaduje zřízení ani střídání tajných kódů. V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro clustery Azure Průzkumník dat. Konfigurace spravované identity se v současné době podporuje jenom pro [Povolení klíčů spravovaných zákazníkem pro váš cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Spravované identity pro Azure Průzkumník dat se nebudou chovat podle očekávání, pokud je vaše aplikace migrována napříč předplatnými nebo klienty. Aplikace bude muset získat novou identitu, kterou je možné provést tak, že ji zakážete a znovu povolíte pomocí možnosti [Odebrat identitu](#remove-an-identity). Zásady přístupu k prostředkům pro příjem dat se také musí aktualizovat, aby používaly novou identitu.

## <a name="add-a-system-assigned-identity"></a>Přidat identitu přiřazenou systémem

K vašemu clusteru můžete přiřadit **identitu přiřazenou systémem** , která je vázaná na váš cluster, a když se váš cluster odstraní, odstraní se. Cluster může mít pouze jednu identitu přiřazenou systémem. Vytvoření clusteru s identitou přiřazenou systémem vyžaduje další vlastnost, která má být nastavena v clusteru.

### <a name="add-a-system-assigned-identity-using-c"></a>Přidejte identitu přiřazenou systémem pomocíC#

Pokud chcete nastavit spravovanou identitu pomocí klienta Azure Průzkumník dat C# , udělejte toto:

* Nainstalujte [balíček NuGet pro Azure Průzkumník dat (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Nainstalujte [balíček NuGet Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pro ověřování.
* Chcete-li spustit následující příklad, [vytvořte aplikaci služby Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) a instanční objekt, který má přístup k prostředkům. Můžete přidat přiřazení role v oboru předplatného a získat požadované `Directory (tenant) ID`, `Application ID`a `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Vytvořit nebo aktualizovat cluster

1. Vytvořte nebo aktualizujte cluster pomocí vlastnosti `Identity`:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Spusťte následující příkaz a ověřte, jestli se cluster úspěšně vytvořil nebo aktualizoval pomocí identity:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Pokud výsledek obsahuje `ProvisioningState` s hodnotou `Succeeded`, cluster se vytvořil nebo aktualizoval a měl by mít následující vlastnosti:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` a `TenantId` se nahrazují identifikátory GUID. Vlastnost `TenantId` identifikuje tenanta AAD, ke kterému patří identita. `PrincipalId` je jedinečný identifikátor pro novou identitu clusteru. V rámci služby AAD má instanční objekt stejný název, jaký jste zadali App Service nebo Azure Functions instanci.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Přidání identity přiřazené systémem pomocí šablony Azure Resource Manager

K automatizaci nasazení prostředků Azure můžete použít šablonu Azure Resource Manager. Další informace o nasazení do Azure Průzkumník dat najdete v tématu [Vytvoření clusteru a databáze azure Průzkumník dat pomocí šablony Azure Resource Manager](create-cluster-database-resource-manager.md).

Když přidáte typ přiřazený systémem, dáte službě Azure pokyn k vytvoření a správě identity pro váš cluster. Libovolný prostředek typu `Microsoft.Kusto/clusters` lze vytvořit s identitou, a to zahrnutím následující vlastnosti do definice prostředků: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Příklad:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Když je cluster vytvořený, má následující další vlastnosti:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` a `<PRINCIPALID>` se nahrazují identifikátory GUID. Vlastnost `TenantId` identifikuje tenanta AAD, ke kterému patří identita. `PrincipalId` je jedinečný identifikátor pro novou identitu clusteru. V rámci služby AAD má instanční objekt stejný název, jaký jste zadali App Service nebo Azure Functions instanci.

## <a name="remove-an-identity"></a>Odebrání identity

Odebráním identity přiřazené systémem se odstraní i z AAD. Identity přiřazené systémem se taky při odstranění prostředku clusteru automaticky odeberou z AAD. Identitu přiřazenou systémem je možné odebrat zakázáním této funkce:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Další kroky

* [Zabezpečení clusterů Azure Průzkumník dat v Azure](security.md)
* [Zabezpečte svůj cluster v Azure Průzkumník dat-Azure Portal](manage-cluster-security.md) tím, že povolíte šifrování v klidovém prostředí.
 * [Konfigurace klíčů spravovaných zákazníkem pomocíC#](customer-managed-keys-csharp.md)
 * [Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager](customer-managed-keys-resource-manager.md)
