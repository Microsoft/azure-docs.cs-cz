---
title: Začínáme s knihovnou Azure CDN pro .NET | Dokumenty společnosti Microsoft
description: Zjistěte, jak psát aplikace .NET pro správu Azure CDN pomocí Sady Visual Studio.
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
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7e3ad3a5928b36c221bb83b1c4012c3c9e14f35d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594182"
---
# <a name="get-started-with-azure-cdn-development"></a>Začínáme s vývojem pro Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

[Knihovna Azure CDN pro rozhraní .NET](/dotnet/api/overview/azure/cdn) můžete automatizovat vytváření a správu profilů cdn a koncových bodů.  Tento kurz vás provede vytvořením jednoduché aplikace konzoly .NET, která ukazuje několik dostupných operací.  Tento kurz není určen k popisu všech aspektů knihovny Azure CDN pro rozhraní .NET podrobně.

K dokončení tohoto kurzu potřebujete Visual Studio 2015.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) je volně k dispozici ke stažení.

> [!TIP]
> [Dokončený projekt z tohoto kurzu](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) je k dispozici ke stažení na MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Vytvoření projektu a přidání balíčků Nuget
Teď, když jsme vytvořili skupinu prostředků pro naše profily CDN a udělili naše aplikace Azure AD oprávnění ke správě profilů CDN a koncových bodů v rámci této skupiny, můžeme začít vytvářet naši aplikaci.

V rámci Sady Visual Studio 2015 otevřete dialogové okno nového projektu kliknutím na **Soubor**, **Nový** **projekt.**  Rozbalte **visual c#** a vyberte **windows** v podokně vlevo.  V prostředním podokně klikněte na **Konzolová aplikace.**  Pojmenujte projekt a klepněte na tlačítko **OK**.  

![Nový projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Náš projekt bude používat některé knihovny Azure obsažené v balíčcích Nuget.  Přidáme je do projektu.

1. Klepněte na nabídku **Nástroje,** **Správce balíčků Aplikace Nuget**a potom na **konzolu Správce balíčků**.
   
    ![Správa balíčků Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. V konzole Správce balíčků proveďte následující příkaz pro instalaci **knihovny ADAL (Active Directory Authentication Library):**
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Chcete-li nainstalovat **knihovnu Azure CDN Management Library,** proveďte následující kroky :
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Směrnic, konstanty, hlavní metody a pomocné metody
Pojďme si základní strukturu našeho programu písemného.

1. Zpět na kartě Program.cs `using` nahraďte direktivy v horní části následujícími:
   
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
2. Musíme definovat některé konstanty, které naše metody budou používat.  Ve `Program` třídě, ale `Main` před metodou, přidejte následující.  Nezapomeňte nahradit zástupné ** &lt;symboly, včetně&gt;úhlových závorek**, vlastními hodnotami podle potřeby.
   
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
3. Také na úrovni třídy definujte tyto dvě proměnné.  Použijeme je později k určení, zda náš profil a koncový bod již existují.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Nahraďte metodu `Main` takto:
   
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
5. Některé z našich dalších metod se chystáte vyzvat uživatele s "Ano / Ne" otázky.  Přidejte následující metodu, aby to bylo trochu jednodušší:
   
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

Nyní, když je zapsána základní struktura našeho programu, `Main` měli bychom vytvořit metody volané metodou.

## <a name="authentication"></a>Ověřování
Než budeme moct použít Azure CDN Management Library, musíme ověřit náš instanční objekt a získat ověřovací token.  Tato metoda používá ADAL k načtení tokenu.

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

Pokud používáte individuální ověřování `GetAccessToken` uživatele, metoda bude vypadat mírně odlišně.

> [!IMPORTANT]
> Tuto ukázku kódu použijte pouze v případě, že se rozhodnete mít individuální ověřování uživatele namísto instančního objektu.
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

Nezapomeňte nahradit `<redirect URI>` identifikátor URI přesměrování, který jste zadali při registraci aplikace ve službě Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Seznam profilů CDN a koncových bodů
Nyní jsme připraveni provést operace CDN.  První věc, kterou naše metoda dělá, je seznam všech profilů a koncových bodů v naší skupině prostředků, a pokud najde shodu pro profil a názvy koncových bodů zadané v našich konstantách, na to si všimneme později, abychom se nepokoušeli vytvářet duplikáty.

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
Dále vytvoříme profil.

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
> Výše uvedený příklad přiřadí koncovému bodu počátek s `www.contoso.com`názvem *Contoso* s názvem hostitele .  Měli byste to změnit tak, aby ukazovalna na název hostitele vašeho vlastního původu.
> 
> 

## <a name="purge-an-endpoint"></a>Vymazání koncového bodu
Za předpokladu, že byl vytvořen koncový bod, jeden společný úkol, který můžeme chtít provést v našem programu je vymazání obsahu v našem koncovém bodu.

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
> Ve výše uvedeném `/*` příkladu řetězec označuje, že chci vymazat vše v kořenovém adresáři cesty koncového bodu.  To je ekvivalentní ke kontrole **vyčistit vše** v dialogovém okně "vyčistit" portálu Azure. V `CreateCdnProfile` metodě jsem vytvořil náš profil jako **Azure CDN z** profilu Verizon pomocí kódu `Sku = new Sku(SkuName.StandardVerizon)`, takže to bude úspěšné.  Azure **CDN z akamai** profily však nepodporují **vyčistit všechny**, takže pokud jsem používal profil Akamai pro tento kurz, budu muset zahrnout konkrétní cesty k vymazání.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranění profilů a koncových bodů CDN
Poslední metody odstraní náš koncový bod a profil.

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
Nyní můžeme zkompilovat a spustit program kliknutím na tlačítko **Start** v sadě Visual Studio.

![Spuštěný program](./media/cdn-app-dev-net/cdn-program-running-1.png)

Když program dosáhne výše uvedené výzvy, měli byste být schopni vrátit se do vaší skupiny prostředků na webu Azure portal a zjistit, že profil byl vytvořen.

![Výborně!](./media/cdn-app-dev-net/cdn-success.png)

Poté můžeme potvrdit výzvy ke spuštění zbývající části programu.

![Dokončení programu](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Další kroky
Chcete-li zobrazit dokončený projekt z tohoto návodu, [stáhněte si ukázku](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Další dokumentaci k další dokumentaci v knihovně správy Azure CDN pro rozhraní .NET zobrazíte [odkaz na msdn](/dotnet/api/overview/azure/cdn).

Spravujte prostředky CDN pomocí [prostředí PowerShell](cdn-manage-powershell.md).

