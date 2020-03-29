---
title: Jak nakonfigurovat spravované identity pro cluster Azure Data Explorer
description: Zjistěte, jak nakonfigurovat spravované identity pro cluster Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f9592f5d2666684e0cf5eef687b1e69cfb55066c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065571"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurace spravovaných identit pro cluster Azure Data Explorer

[Spravovaná identita ze služby Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) umožňuje vašemu clusteru snadný přístup k dalším prostředkům chráněným aad, jako je azure key vault. Identita je spravovaná platformou Azure a nevyžaduje, abyste zmiřování nebo střídání žádné tajné klíče. Tento článek ukazuje, jak vytvořit spravovanou identitu pro clustery Azure Data Explorer. Konfigurace spravované identity je aktuálně podporována pouze pro [povolení klíčů spravovaných zákazníky pro váš cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Spravované identity pro Azure Data Explorer se nebudou chovat podle očekávání, pokud se vaše aplikace migruje mezi předplatnými nebo tenanty. Aplikace bude muset získat novou identitu, kterou lze provést [zakázáním](#remove-a-system-assigned-identity) a [opětovným povolením](#add-a-system-assigned-identity) funkce. Přístup zásady navazujících prostředků bude také muset být aktualizovány používat novou identitu.

## <a name="add-a-system-assigned-identity"></a>Přidání systémově přiřazené identity
                                                                                                    
Přiřaďte systémem přiřazenou identitu, která je svázána s vaším clusterem a je odstraněna, pokud je váš cluster odstraněn. Cluster může mít pouze jednu identitu přiřazenou systému. Vytvoření clusteru se systémem přiřazenou identitou vyžaduje nastavení další vlastnosti v clusteru. Systémem přiřazená identita se přidává pomocí c#, arm šablon nebo portálu Azure, jak je podrobně popsáno níže.

# <a name="azure-portal"></a>[Portál Azure](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Přidání systémově přiřazené identity pomocí portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Nový cluster Průzkumníka dat Azure

1. [Vytvoření clusteru Průzkumníka dat Azure](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. Na kartě **Zabezpečení** > **přiřazenou identitu systému**vyberte **možnost Zapnuto**. Chcete-li odebrat přiřazenou identitu systému, vyberte **možnost Vypnuto**.
2. Vyberte **Další:Značky>** nebo **Revize + vytvořit** pro vytvoření clusteru.

    ![Přidání systémové přiřazené identity do nového clusteru](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Existující cluster Průzkumníka dat Azure

1. Otevřete existující cluster Průzkumníka dat Azure.
1. V levém podokně portálu vyberte**Identita** **nastavení.** > 
1. V podokně **identit** > **přiřazený systém:**
   1. Posuňte jezdec **Stav** **na Zapnuto**.
   1. Vyberte **Uložit**.
   1. V rozbalovacím okně vyberte **Ano.**

    ![Přidání přiřazené identity systému](media/managed-identities/turn-system-assigned-identity-on.png)

1. Po několika minutách se na obrazovce zobrazí: 
  * **ID objektu** – používá se pro klíče spravované zákazníkem 
  * **Přiřazení rolí** – kliknutím na odkaz přiřadíte příslušné role

    ![Systém přiřazenou identitu na](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Přidání systémově přiřazené identity pomocí c #

#### <a name="prerequisites"></a>Požadavky

Nastavení spravované identity pomocí klienta Azure Data Explorer C#:

* Nainstalujte [balíček NuGet aplikace Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Nainstalujte [balíček Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pro ověřování.
* [Vytvořte azure ad aplikace](/azure/active-directory/develop/howto-create-service-principal-portal) a instanční objekt, který má přístup k prostředkům. Přiřazení role přidáte do oboru předplatného `Directory (tenant) ID`a `Application ID`získáte `Client Secret`požadované , a .

#### <a name="create-or-update-your-cluster"></a>Vytvoření nebo aktualizace clusteru

1. Vytvořte nebo aktualizujte `Identity` cluster pomocí vlastnosti:

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
    
2. Spuštěním následujícího příkazu zkontrolujte, zda byl cluster úspěšně vytvořen nebo aktualizován s identitou:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Pokud výsledek `ProvisioningState` obsahuje `Succeeded` s hodnotou, pak cluster byl vytvořen nebo aktualizován a měl by mít následující vlastnosti:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`a `TenantId` jsou nahrazeny identifikátory GUID. Vlastnost `TenantId` identifikuje klienta AAD, ke kterému patří identita. Jedná `PrincipalId` se o jedinečný identifikátor nové identity clusteru. V rámci Služby AAD má instanční objekt stejného názvu, který jste přiřadili instanci služby App Service nebo Azure Functions.

# <a name="arm-template"></a>[Arm šablona](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Přidání systémově přiřazené identity pomocí šablony Azure Resource Manageru

Šablonu Azure Resource Manager umíte automatizovat nasazení prostředků Azure. Další informace o nasazení do Průzkumníka dat Azure najdete [v tématu Vytvoření clusteru a databáze Azure Data Explorer pomocí šablony Azure Resource Manageru](create-cluster-database-resource-manager.md).

Přidání systémově přiřazeného typu říká Azure k vytvoření a správě identity pro váš cluster. Libovolný prostředek `Microsoft.Kusto/clusters` typu lze vytvořit s identitou zahrnutím následující vlastnosti do definice prostředku: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Například:

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

Po vytvoření clusteru má následující další vlastnosti:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`a `<PRINCIPALID>` jsou nahrazeny identifikátory GUID. Vlastnost `TenantId` identifikuje klienta AAD, ke kterému patří identita. Jedná `PrincipalId` se o jedinečný identifikátor nové identity clusteru. V rámci Služby AAD má instanční objekt stejného názvu, který jste přiřadili instanci služby App Service nebo Azure Functions.

---

## <a name="remove-a-system-assigned-identity"></a>Odebrání systémově přiřazené identity

Odebráním identity přiřazené systému ji také odstraníte z aad. Systémem přiřazené identity jsou také automaticky odebrány z aad při odstranění prostředku clusteru. Systémově přiřazená identita může být odebrána zakázáním funkce.  Systémem přiřazená identita se odebere pomocí c#, arm šablony nebo portál Azure, jak je podrobně popsáno níže.

# <a name="azure-portal"></a>[Portál Azure](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>Odebrání systémově přiřazené identity pomocí portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. V levém podokně portálu vyberte**Identita** **nastavení.** > 
1. V podokně **identit** > **přiřazený systém:**
    1. Posuňte jezdec **Stav** na **Vypnuto**.
    1. Vyberte **Uložit**.
    1. V rozbalovacím okně vyberte **Ano,** chcete-li zakázat identitu přiřazenou systému. Podokno **Identity** se vrátí do stejného stavu jako před přidáním systémově přiřazené identity.

    ![Systém přiřazená identita vypnuta](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Odebrání systémově přiřazené identity pomocí c #

Chcete-li odebrat systémově přiřazenou identitu, spusťte následující:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Arm šablona](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Odebrání systémově přiřazené identity pomocí šablony Azure Resource Manageru

Chcete-li odebrat systémově přiřazenou identitu, spusťte následující:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Další kroky

* [Zabezpečené clustery Azure Data Explorer v Azure](security.md)
* [Zabezpečte svůj cluster v Azure Data Explorer – Portál Azure](manage-cluster-security.md) povolením šifrování v klidovém stavu.
 * [Konfigurace klíčů spravovaných zákazníkem pomocí c #](customer-managed-keys-csharp.md)
 * [Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager](customer-managed-keys-resource-manager.md)
