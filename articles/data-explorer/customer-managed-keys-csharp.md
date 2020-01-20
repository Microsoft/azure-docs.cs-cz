---
title: Konfigurace klíčů spravovaných zákazníkem pomocíC#
description: Tento článek popisuje, jak nakonfigurovat šifrování klíčů spravovaných zákazníkem pro vaše data v Azure Průzkumník dat.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: ac37c0e1f1f7d769a881c5965744b29d40468486
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276685"
---
# <a name="configure-customer-managed-keys-using-c"></a>Konfigurace klíčů spravovaných zákazníkem pomocíC#

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Šablona Azure Resource Manageru](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

V této části se dozvíte, jak nakonfigurovat šifrování klíčů spravovaných zákazníkem pomocí C# klienta Azure Průzkumník dat. 

### <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="install-c-nuget"></a>Nainstalovat C# NuGet

* Nainstalujte [balíček NuGet pro Azure Průzkumník dat (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Nainstalujte [balíček NuGet Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pro ověřování.

### <a name="authentication"></a>Ověření

Chcete-li spustit příklady v tomto článku, vytvořte aplikaci a instanční objekt služby [Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) , který má přístup k prostředkům. Můžete přidat přiřazení role v oboru předplatného a získat požadované `Directory (tenant) ID`, `Application ID`a `Client Secret`.

### <a name="configure-cluster"></a>Konfigurace clusteru

Ve výchozím nastavení používá šifrování Azure Průzkumník dat klíče spravované společností Microsoft. Nakonfigurujte cluster Azure Průzkumník dat tak, aby používal klíče spravované zákazníkem, a zadejte klíč, který se má přidružit ke clusteru.

1. Aktualizujte cluster pomocí následujícího kódu:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Spusťte následující příkaz a ověřte, jestli se cluster úspěšně aktualizoval:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Pokud výsledek obsahuje `ProvisioningState` s hodnotou `Succeeded`, cluster se úspěšně aktualizoval.

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytváříte novou verzi klíče, budete muset cluster aktualizovat, aby používal novou verzi. Nejdřív zavolejte `Get-AzKeyVaultKey`, abyste získali nejnovější verzi klíče. Pak aktualizujte vlastnosti trezoru klíčů clusteru tak, aby používaly novou verzi klíče, jak je znázorněno v části [Konfigurace clusteru](#configure-cluster).

## <a name="next-steps"></a>Další kroky

* [Zabezpečení clusterů Azure Průzkumník dat v Azure](security.md)
* [Konfigurace spravovaných identit pro cluster Azure Průzkumník dat](managed-identities.md)
* [Zabezpečte svůj cluster v Azure Průzkumník dat-Azure Portal](manage-cluster-security.md) tím, že povolíte šifrování v klidovém prostředí.
* [Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager](customer-managed-keys-resource-manager.md)


