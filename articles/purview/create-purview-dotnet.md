---
title: Vytvoření účtu dosah pomocí sady .NET SDK
description: Vytvořte účet Azure dosah pomocí sady .NET SDK.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: 04ed5cef351c81355a2390dd0b983c162f2b9532
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387463"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Rychlý Start: vytvoření účtu dosah pomocí sady .NET SDK

V tomto rychlém startu se dozvíte, jak pomocí .NET SDK vytvořit účet Azure dosah. 

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Vlastní [klient Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Váš účet musí mít oprávnění k vytváření prostředků v předplatném.

* Pokud jste **Azure Policy** blokující všechny aplikace při vytváření **účtu úložiště** a **oboru názvů EventHub**, je potřeba vytvořit výjimku zásad pomocí značky, kterou je možné zadat během procesu vytváření účtu dosah. Hlavním důvodem je to, že pro každý vytvořený účet dosah je potřeba vytvořit spravovanou skupinu prostředků a v rámci této skupiny prostředků, účtu úložiště a oboru názvů EventHub. Další informace najdete v tématu [vytvoření portálu katalogu](create-catalog-portal.md) .

### <a name="visual-studio"></a>Visual Studio

Návod v tomto článku používá Visual Studio 2019. Postupy pro Visual Studio 2013, 2015 nebo 2017 se mírně liší.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Stáhněte sadu [Azure .NET SDK](https://azure.microsoft.com/downloads/) a nainstalujte ji do svého počítače.

## <a name="create-an-application-in-azure-active-directory"></a>Vytvoření aplikace v Azure Active Directory

V části *Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům*, postupujte podle pokynů k provedení těchto úloh:

1. V části [vytvořit aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)vytvořte aplikaci, která představuje aplikaci .NET, kterou vytváříte v tomto kurzu. Jako přihlašovací adresu URL můžete poskytnout fiktivní URL, jak ukazuje článek (`https://contoso.org/exampleapp`).
2. V poli [získat hodnoty pro přihlášení](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Získejte **ID aplikace** a **ID tenanta** a poznamenejte si tyto hodnoty, které použijete později v tomto kurzu. 
3. V části [certifikáty a tajné](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)klíče Získejte **ověřovací klíč** a poznamenejte si tuto hodnotu, kterou použijete později v tomto kurzu.
4. V části [přiřadit aplikaci k roli](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)přiřaďte aplikaci roli **Přispěvatel** na úrovni předplatného, aby aplikace mohla vytvářet datové továrny v rámci předplatného.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Dále vytvořte konzolovou aplikaci C# .NET v aplikaci Visual Studio:

1. Spusťte **Visual Studio**.
2. V okně Start vyberte **vytvořit novou**  >  **konzolovou aplikaci projektu (.NET Framework)**. Vyžaduje se .NET verze 4.5.2 nebo novější.
3. Do **název projektu** zadejte **ADFv2QuickStart**.
4. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

1. Vyberte **Nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.
2. V podokně **konzoly Správce balíčků** spusťte následující příkazy pro instalaci balíčků. Další informace najdete v [balíčku NuGet Microsoft. Azure. Management. dosah](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Vytvoření klienta dosah

1. Otevřete soubor **Program.cs** a vložte do něj následující příkazy. Přidáte tak odkazy na obory názvů.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Do metody **Main** přidejte následující kód, který nastaví proměnné. Zástupné symboly nahraďte vlastními hodnotami. Seznam oblastí Azure, ve kterých je dosah aktuálně k dispozici, najdete v **Azure dosah** a vyberte oblasti, které vás zajímají na následující stránce: [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Do metody **Main** přidejte následující kód, který vytvoří instanci třídy **PurviewManagementClient** . Pomocí tohoto objektu vytvoříte účet dosah.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Vytvoření účtu dosah

Do metody **Main** přidejte následující kód, který vytvoří **účet dosah**. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>Spuštění kódu

Sestavte a spusťte aplikaci a pak ověřte spuštění.

Konzola vytiskne průběh vytváření účtu dosah.

### <a name="sample-output"></a>Ukázkový výstup

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Ověření výstupu

Přejít na stránku **účty dosah** v [Azure Portal](https://portal.azure.com) a ověřte účet vytvořený pomocí výše uvedeného kódu. 

## <a name="delete-purview-account"></a>Odstranit účet dosah

K programovému odstranění účtu dosah přidejte do programu následující řádky kódu: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Ověřte, jestli je název účtu dosah k dispozici.

Pokud chcete ověřit dostupnost účtu dosah, použijte následující kód: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

Výše uvedený kód s tiskem ' true ', pokud je název k dispozici a ' false ', pokud název není k dispozici.


## <a name="next-steps"></a>Další kroky

Kód v tomto kurzu vytvoří účet dosah, odstraní účet dosah a ověří dostupnost názvu účtu dosah. Teď si můžete stáhnout sadu .NET SDK a získat informace o dalších akcích poskytovatele prostředků, které můžete provést pro účet dosah.

V dalším článku se dozvíte, jak uživatelům dovolit přístup k vašemu účtu Azure dosah. 

> [!div class="nextstepaction"]
> [Přidání uživatelů k účtu Azure dosah](catalog-permissions.md)
