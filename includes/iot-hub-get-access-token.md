---
author: robinsh
ms.author: robin.shahan
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: e2d6b6413d1e397eaa3c53f28394dcf321dac729
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011763"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Získání tokenu Azure Resource Manageru
Azure Active Directory se musí ověřovat všechny úkoly, které můžete provádět s prostředky pomocí Azure Resource Manageru. V příkladu je vidět tady používá ověřování pomocí hesla, naleznete v části Další přístupy [požadavků ověřování Azure Resource Manageru][lnk-authenticate-arm].

1. Přidejte následující kód, který **hlavní** metody v souboru Program.cs načíst token ze služby Azure AD pomocí id aplikace a heslo.
   
    ```
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
2. Vytvoření **ResourceManagementClient** objekt, který používá token přidáním následujícího kódu na konec objektu **hlavní** metody:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Vytvoření nebo získání odkazu na skupinu prostředků, které používáte:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx