---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: b6ea8c7b3a6374572c8bd31e3c62b788efbafcbc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59052015"
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