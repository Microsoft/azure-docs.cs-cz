---
title: Začínáme s knihovnou Azure CDN pro rozhraní .NET | Microsoft Docs
description: Zjistěte, jak psát aplikace .NET pro správu Azure CDN pomocí sady Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5379586355ece98af6295236d6cbd09cb31c742b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23843046"
---
# <a name="get-started-with-azure-cdn-development"></a>Začínáme s vývojem pro Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Můžete použít [knihovny CDN Azure pro .NET](https://msdn.microsoft.com/library/mt657769.aspx) k automatizaci vytváření a Správa profilů CDN a koncové body.  Tento kurz vás provede vytvoření jednoduché konzolové aplikace .NET, která demonstruje řadu dostupné operace.  V tomto kurzu není určeno k podrobně popisují všechny aspekty Azure CDN Library pro .NET.

Budete potřebovat k dokončení tohoto kurzu sady Visual Studio 2015.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) je dostupná volně ke stažení.

> [!TIP]
> [Dokončený projekt z tohoto kurzu](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) je k dispozici ke stažení na webu MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Vytvoření projektu a přidání balíčků Nuget
Teď, když jsme vytvořili skupinu prostředků pro naše profily CDN a získá naše Azure AD aplikace oprávnění ke správě profilů CDN a koncové body v rámci dané skupiny, můžeme začít vytváření aplikace.

Z v rámci sady Visual Studio 2015, klikněte na **soubor**, **nový**, **projektu...**  otevřete dialogové okno Nový projekt.  Rozbalte položku **Visual C#**, pak vyberte **Windows** v podokně na levé straně.  Klikněte na tlačítko **konzolové aplikace** v prostředním podokně.  Název projektu a pak klikněte na **OK**.  

![Nový projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Naše projektu bude používat některé Azure knihovny, které jsou součástí balíčků Nuget.  Umožňuje přidat do projektu.

1. Klikněte **nástroje** nabídce **Správce balíčků Nuget**, pak **Konzola správce balíčků**.
   
    ![Správa balíčků Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. V konzole Správce balíčků spustíte následující příkaz k instalaci **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Spuštěním následujících příkazů nainstalujte **Knihovna správy Azure CDN**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktivy, konstanty, hlavní metoda a pomocné metody
Pojďme základní strukturu naše programu zapsána.

1. Zpět na kartě Program.cs nahradit `using` direktivy v horní části následujícím kódem:
   
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
2. Je potřeba definovat některé konstanty, který bude používat naše metody.  V `Program` třídy, ale předtím, než `Main` metoda, přidejte následující.  Ujistěte se, zda jste nahradili zástupné symboly, včetně  **&lt;lomené závorky&gt;**, s vlastní hodnoty, podle potřeby.
   
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
3. Také na úrovni třídy definujte tyto dvě proměnné.  Použijeme tyto později k určení, zda naše profilu a koncového bodu už existují.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Nahraďte `Main` metoda následujícím způsobem:
   
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
5. Některé z našich dalších metod se chystáte Dotázat se uživatele pomocí otázek "Ano".  Přidejte následující metodu pro snazší, trochu:
   
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

Teď, když je zapsán základní struktura naše program, by měl vytvoříme volá metody `Main` metoda.

## <a name="authentication"></a>Authentication
Knihovna správy Azure CDN jsme mohli používat, je potřeba ověřit naše instanční objekt a získat ověřovací token.  Tato metoda používá ADAL pro načtení tokenu.

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

Pokud používáte ověřování jednotlivých uživatelů, `GetAccessToken` metoda bude vypadat mírně lišit.

> [!IMPORTANT]
> Tato ukázka kódu použijte, pouze pokud zvolíte ověřování jednotlivého uživatele místo objekt služby.
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

Nezapomeňte nahradit `<redirect URI>` s přesměrováním URI, které jste zadali při registraci aplikace ve službě Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Koncové body a seznam profilů CDN
Nyní je připraven k provádění operací CDN.  První krok, který nemá naše metoda je seznam všech profilů a koncových bodů v naší skupiny prostředků a pokud najde shoda pro zadané v našem konstanty názvy profilu a koncového bodu usnadňuje Poznámka této později, pokusíme nemáte k vytvoření duplicitní položky.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Vytvoření profilů CDN a koncové body
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
> V předchozím příkladu přiřadí koncový bod počátek s názvem *Contoso* s hostitele `www.contoso.com`.  Měli byste změnit to tak, aby odkazoval na název vlastního počátečního hostitele.
> 
> 

## <a name="purge-an-endpoint"></a>Vyprázdnění koncového bodu
Za předpokladu, že byl vytvořen koncový bod, jeden běžný úkol, který jsme může být třeba provést v našem programu je mazání obsahu v našem koncový bod.

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
> V příkladu výše, řetězec `/*` označuje, že chcete vymazat vše v kořenovém cestu ke koncovému bodu.  Jde o ekvivalent kontrola **vyprázdnit všechny** v dialogovém okně "mazání" portálu Azure. V `CreateCdnProfile` metoda, vytvořené naše profilu jako **Azure CDN společnosti Verizon** profilu pomocí kódu `Sku = new Sku(SkuName.StandardVerizon)`, takže to bude úspěšné.  Ale **Azure CDN společnosti Akamai** profily nepodporují **vyprázdnit všechny**, takže pokud použití profilem Akamai pro účely tohoto kurzu, by je nutné zahrnout konkrétní cesty k vyprázdnění.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranit profily CDN a koncové body
Poslední metody odstraní naše koncový bod a profil.

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
Jsme teď můžete zkompilovat a program spusťte kliknutím **spustit** tlačítko v sadě Visual Studio.

![Spuštění programu](./media/cdn-app-dev-net/cdn-program-running-1.png)

Když program dosáhne řádku výše, byste měli mít se vrátíte do vaší skupiny prostředků na portálu Azure najdete v části vytvořený profil.

![Výborně!](./media/cdn-app-dev-net/cdn-success.png)

Potom může potvrdit pokynů ke spuštění zbytek programu.

![Dokončení programu](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Další kroky
Zobrazíte dokončený projekt z tohoto návodu [stáhnout vzorek](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Chcete-li vyhledat další dokumentaci v knihovně správy CDN Azure pro .NET, podívejte se [odkaz na webu MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Spravovat prostředky CDN s [prostředí PowerShell](cdn-manage-powershell.md).

