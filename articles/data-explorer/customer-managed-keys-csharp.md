---
title: 'Konfigurace klíčů spravovaných zákazníkem pomocí c #'
description: Tento článek popisuje, jak nakonfigurovat šifrování klíčů spravovaných zákazníkem na vašich datech v Průzkumníku dat Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297951"
---
# <a name="configure-customer-managed-keys-using-c"></a>Konfigurace klíčů spravovaných zákazníkem pomocí c #

> [!div class="op_single_selector"]
> * [Portál](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Šablona Azure Resource Manageru](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování pomocí klíčů spravovaných zákazníkem

Tato část ukazuje, jak nakonfigurovat šifrování klíčů spravovaných zákazníkem pomocí klienta Azure Data Explorer C#. 

### <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="install-c-nuget"></a>Instalace jazyka C# NuGet

* Nainstalujte [balíček NuGet aplikace Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)

* Nainstalujte [balíček Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pro ověřování.

### <a name="authentication"></a>Ověřování

Chcete-li spustit příklady v tomto článku, [vytvořte instanční objekt azure a](/azure/active-directory/develop/howto-create-service-principal-portal) služby, které mají přístup k prostředkům. Přiřazení role můžete přidat v oboru předplatného `Directory (tenant) ID` `Application ID`a `Client Secret`získat požadované , a .

### <a name="configure-cluster"></a>Konfigurace clusteru

Ve výchozím nastavení používá šifrování Azure Data Explorer u klíčů spravovaných společností Microsoft. Nakonfigurujte cluster Průzkumníka dat Azure tak, aby používal klíče spravované zákazníky, a zadejte klíč, který chcete k clusteru přidružit.

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

1. Spuštěním následujícího příkazu zkontrolujte, zda byl cluster úspěšně aktualizován:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Pokud výsledek `ProvisioningState` obsahuje `Succeeded` s hodnotou, byl cluster úspěšně aktualizován.

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když vytvoříte novou verzi klíče, budete muset aktualizovat cluster, aby se používala nová verze. Nejprve `Get-AzKeyVaultKey` zavolejte a získejte nejnovější verzi klíče. Potom aktualizujte vlastnosti trezoru klíčů clusteru tak, aby používaly novou verzi klíče, jak je znázorněno v [části Konfigurovat cluster](#configure-cluster).

## <a name="next-steps"></a>Další kroky

* [Zabezpečené clustery Azure Data Explorer v Azure](security.md)
* [Konfigurace spravovaných identit pro cluster Azure Data Explorer](managed-identities.md)
* [Zabezpečte svůj cluster v Azure Data Explorer – Portál Azure](manage-cluster-security.md) povolením šifrování v klidovém stavu.
* [Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager](customer-managed-keys-resource-manager.md)


