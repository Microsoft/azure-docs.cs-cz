---
title: Začínáme s knihovnou Azure CDN pro .NET | Microsoft Docs
description: Naučte se psát aplikace .NET pro správu Azure CDN pomocí sady Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: a812704c42a4da5ddf89fe6c5ba0c9a684047f75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919484"
---
# <a name="get-started-with-the-azure-cdn-library-for-net"></a>Začínáme s knihovnou Azure CDN pro .NET
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

Pomocí [knihovny Azure CDN pro .NET](/dotnet/api/overview/azure/cdn) můžete automatizovat vytváření a správu profilů a koncových bodů CDN.  Tento kurz vás provede vytvořením jednoduché konzolové aplikace .NET, která ukazuje několik dostupných operací.  Tento kurz není určený k podrobnému popisu všech aspektů knihovny Azure CDN pro .NET.

K dokončení tohoto kurzu budete potřebovat Visual Studio 2015.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) je volně dostupné ke stažení.

> [!TIP]
> [Dokončený projekt z tohoto kurzu](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) je k dispozici ke stažení na webu MSDN.
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Vytvoření projektu a přidání balíčků NuGet
Teď, když jsme vytvořili skupinu prostředků pro profily CDN a udělili jí oprávnění aplikace Azure AD ke správě profilů a koncových bodů CDN v této skupině, můžeme začít vytvářet aplikace.

V sadě Visual Studio 2015 klikněte na **soubor**, **Nový**, **projekt...** a otevřete dialogové okno Nový projekt.  Rozbalte položku **Visual C#** a potom vyberte možnost **Windows** v levém podokně.  V prostředním podokně klikněte na **Konzolová aplikace** .  Pojmenujte projekt a klikněte na tlačítko **OK**.

![Nový projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Náš projekt bude používat některé knihovny Azure obsažené v balíčcích NuGet.  Pojďme je přidat do projektu.

1. Klikněte na nabídku **nástroje** , **Správce balíčků NuGet**a pak na **Konzola správce balíčků**.

    ![Spravovat balíčky NuGet](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. V konzole správce balíčků spusťte následující příkaz pro instalaci **Active Directory Authentication Library (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Spusťte následující příkaz pro instalaci **knihovny pro správu Azure CDN**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktivy, konstanty, metoda Main a pomocné metody
Pojďme získat základní strukturu našeho programu napsaného.

1. Zpátky na kartě Program.cs nahraďte `using` direktivy v horní části následujícím způsobem:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Musíme definovat některé konstanty, které budou používat naše metody.  Ve `Program` třídě, ale před `Main` metodou, přidejte následující.  Zástupné symboly, včetně ** &lt; lomených závorek &gt; **, nahraďte požadovanými hodnotami.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Také na úrovni třídy definujte tyto dvě proměnné.  Později je použijeme k určení, jestli už náš profil a koncový bod existují.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Metodu nahraďte následujícím `Main` způsobem:

   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();

       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };

       ListProfilesAndEndpoints(cdn);

       // Create CDN Profile
       CreateCdnProfile(cdn);

       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);

       Console.WriteLine();

       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);

       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);

       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);

       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Některé z našich dalších metod se zobrazí uživateli s dotazem "Ano/ne".  Přidejte následující metodu, aby bylo poněkud jednodušší:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Teď, když je napsaná základní struktura našeho programu, bychom měli vytvořit metody, které metoda volá `Main` .

## <a name="authentication"></a>Authentication
Než budeme moct použít knihovnu pro správu Azure CDN, musíme ověřit náš instanční objekt a získat ověřovací token.  Tato metoda načte token pomocí ADAL.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult =
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Pokud používáte ověření individuálního uživatele, bude `GetAccessToken` Metoda vypadat mírně odlišně.

> [!IMPORTANT]
> Tuto ukázku kódu použijte pouze v případě, že se rozhodnete použít individuální ověřování uživatele namísto instančního objektu.
>
>

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Nezapomeňte nahradit `<redirect URI>` identifikátor URI pro přesměrování, který jste zadali při registraci aplikace ve službě Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Výpis profilů a koncových bodů CDN
Teď jsme připraveni provést operace CDN.  První věc naší metody je seznam všech profilů a koncových bodů v naší skupině prostředků, a pokud nalezne shodu pro názvy profilů a koncových bodů zadaných v našich konstantách, poznamenejte si ji později, takže nezkusíme vytvořit duplicity.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Vytvoření profilů a koncových bodů CDN
V dalším kroku vytvoříme profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Po vytvoření profilu vytvoříme koncový bod.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> Výše uvedený příklad přiřadí koncovému bodu zdroj s názvem *Contoso* s názvem hostitele `www.contoso.com` .  Tuto změnu byste měli změnit tak, aby odkazovala na název hostitele vlastního zdroje.
>
>

## <a name="purge-an-endpoint"></a>Vyprázdnit koncový bod
Za předpokladu, že byl koncový bod vytvořen, je možné v našem programu vymezit obsah v našem koncovém bodu jedním běžným úkolem, který bychom mohli provést.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> V předchozím příkladu řetězec `/*` označuje, že chci vymazat vše v kořenu cesty koncového bodu.  To je ekvivalentní k kontrole **Vyprázdnění všech** v dialogovém okně "vyčištění" Azure Portal. V `CreateCdnProfile` metodě jsem vytvořili náš profil jako **Azure CDN z profilu Verizon** pomocí kódu `Sku = new Sku(SkuName.StandardVerizon)` , takže to bude úspěšné.  **Azure CDN z profilů Akamai** ale nepodporují **Vyprázdnit vše**, takže pokud jsem pro tento kurz používal profil Akamai, je nutné zahrnout konkrétní cesty, které se mají vyprázdnit.
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranění profilů a koncových bodů CDN
Poslední metoda odstraní náš koncový bod a profil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Spuštění programu
Nyní můžeme zkompilovat a spustit program kliknutím na tlačítko **Start** v aplikaci Visual Studio.

![Program běží](./media/cdn-app-dev-net/cdn-program-running-1.png)

Když program dosáhne výše uvedené výzvy, měli byste se vrátit do vaší skupiny prostředků v Azure Portal a zjistit, zda byl profil vytvořen.

![Výborně!](./media/cdn-app-dev-net/cdn-success.png)

Potom můžeme potvrdit výzvy ke spuštění zbytku programu.

![Program se dokončuje.](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Další kroky
Pokud si chcete zobrazit dokončený projekt z tohoto návodu, [Stáhněte si ukázku](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Další dokumentaci k knihovně pro správu Azure CDN pro .NET najdete v [odkazu na webu MSDN](/dotnet/api/overview/azure/cdn).

Spravujte své prostředky CDN pomocí [PowerShellu](cdn-manage-powershell.md).
