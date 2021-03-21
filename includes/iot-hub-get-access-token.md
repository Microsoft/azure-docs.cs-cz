---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "70803002"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Získání tokenu Azure Resource Manager
Azure Active Directory musí ověřovat všechny úlohy, které provedete na prostředky pomocí Azure Resource Manager. Zde uvedený příklad používá ověřování hesla pro jiné přístupy, viz [ověřování Azure Resource Manager požadavků][lnk-authenticate-arm].

1. Přidejte následující kód do metody **Main** v programu program. cs, abyste načetli token z Azure AD pomocí ID a hesla aplikace.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Vytvořte objekt **ResourceManagementClient** , který používá token přidáním následujícího kódu na konec metody **Main** :
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Vytvořte nebo získejte odkaz na skupinu prostředků, kterou používáte:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx