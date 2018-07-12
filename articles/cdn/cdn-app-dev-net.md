---
title: Začínáme s Azure CDN Library pro .NET | Dokumentace Microsoftu
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38454460"
---
# <a name="get-started-with-azure-cdn-development"></a>Začínáme s vývojem pro Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Můžete použít [ke knihovně CDN Azure pro .NET](https://msdn.microsoft.com/library/mt657769.aspx) automatizovat vytváření a správa koncových bodů a profilů CDN.  Tento kurz vás provede vytvořením jednoduchou konzolovou aplikaci .NET, který ukazuje několik dostupných operací.  V tomto kurzu není určená k podrobnému popisu, všechny aspekty knihovně CDN Azure pro .NET.

Visual Studio 2015 k dokončení tohoto kurzu potřebujete.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) je volně k dispozici ke stažení.

> [!TIP]
> [Dokončený projekt z tohoto kurzu](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) je k dispozici ke stažení na webu MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Vytvoření projektu a přidání balíčků Nuget
Teď, když jsme vytvořili skupinu prostředků pro naše profily CDN a naše oprávnění aplikace Azure AD ke správě profilů CDN a koncové body v rámci dané skupiny, můžeme začít vytváření naši aplikaci.

V sadě Visual Studio 2015, klikněte na **souboru**, **nový**, **projektu...**  otevřete dialogové okno nového projektu.  Rozbalte **Visual C#** a pak vyberte **Windows** v podokně na levé straně.  Klikněte na tlačítko **konzolovou aplikaci** v prostředním podokně.  Pojmenujte svůj projekt a potom klikněte na **OK**.  

![Nový projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Náš projekt bude používat některé knihovny Azure obsažené v balíčcích Nuget.  Přidejme do projektu.

1. Klikněte na tlačítko **nástroje** nabídce **Správce balíčků Nuget**, pak **Konzola správce balíčků**.
   
    ![Spravovat balíčky Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. V konzole Správce balíčků spustíte následující příkaz k instalaci **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Spuštěním následujících příkazů nainstalujte **knihovny správy Azure CDN**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktivy, konstanty, metoda main a metody helper
Pojďme si základní struktura naše programu.

1. Zpět na kartě souboru Program.cs nahraďte `using` direktivy v horní části stránky následujícím kódem:
   
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
2. Budeme muset definovat některé konstanty, které bude používat naše metody.  V `Program` třídy, ale předtím, než `Main` metodu, přidejte následující.  Nezapomeňte nahradit zástupné symboly, včetně  **&lt;ostrých závorek&gt;**, vlastními hodnotami podle potřeby.
   
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
3. Také definujte na úrovni třídy těchto dvou proměnných.  Použijeme tyto později určit, pokud náš profilu a koncového bodu již existují.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Nahradit `Main` metodu následujícím způsobem:
   
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
5. Některé z našich jiných metod budou uživatele s dotazy "Ano/Ne".  Přidejte následující metodu do ujistěte, že o něco jednodušší:
   
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

Teď, když je základní struktura náš program napsán, jsme měli vytvořit metody volané `Main` metody.

## <a name="authentication"></a>Authentication
Jsme mohli používat knihovny správy Azure CDN, potřebujeme k ověření naší instanční objekt služby a získat ověřovací token.  Tato metoda načtení tokenu pomocí ADAL.

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

Pokud používáte ověřování jednotlivých uživatelů, `GetAccessToken` metoda bude vypadat trochu jinak.

> [!IMPORTANT]
> Tento vzorový kód použijte, pouze pokud zvolíte ověřování jednotlivých uživatelů místo instančního objektu služby.
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

Nezapomeňte nahradit `<redirect URI>` se identifikátor URI, které jste zadali při registraci aplikace ve službě Azure AD pro přesměrování.

## <a name="list-cdn-profiles-and-endpoints"></a>Seznam profilů CDN a koncových bodů
Nyní jsme připraveni provádět operace CDN.  První věcí, kterou naše metodu je seznam všechny profily a koncové body do skupiny prostředků a pokud najde shodu pro profilu a koncového bodu názvy zadané v našich konstanty učiní poznamenejte si, který později tak, že jsme se nepokoušejte vytvořit duplicitní položky.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Vytvořte profily CDN a koncových bodů
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

Po vytvoření profilu, vytvoříme koncový bod.

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
> Výše uvedený příklad přiřadí koncový bod s názvem původ *Contoso* s názvem hostitele `www.contoso.com`.  Měli byste změnit to tak, aby odkazoval na vlastní původ název hostitele.
> 
> 

## <a name="purge-an-endpoint"></a>Vyprázdnění koncového bodu
Za předpokladu, že se vytvořil koncový bod, je jednou z běžných úloh, která chceme v naší aplikaci provést vymazání obsahu v našich koncový bod.

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
> V příkladu výše, řetězec `/*` označuje, že chcete vyprázdnit vše, co v kořenové složce cesta ke koncovému bodu.  Jedná se o ekvivalent kontrola **vymazat všechny** v dialogovém okně "Vymazat" na webu Azure portal. V `CreateCdnProfile` metody jsem vytvořil naše se profil **Azure CDN od Verizonu** profilována za použití kódu `Sku = new Sku(SkuName.StandardVerizon)`, takže to bude úspěšné.  Ale **Azure CDN od Akamai** profily nepodporují **vymazat všechny**, takže pokud jsem byl pomocí Akamai profilu pro účely tohoto kurzu, musím zahrnout konkrétní cesty k vyprázdnění.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranění koncových bodů a profilů CDN
Poslední metody odstraní naše koncového bodu a profilu.

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
Jsme teď můžete zkompilovat a spustit program kliknutím **Start** tlačítko v sadě Visual Studio.

![Program spuštěný](./media/cdn-app-dev-net/cdn-program-running-1.png)

Když program dosáhne řádku výše, je třeba vrátit do vaší skupiny prostředků na webu Azure Portal a zobrazit, zda byl vytvořen profil.

![Výborně!](./media/cdn-app-dev-net/cdn-success.png)

Pak můžeme potvrdit pokynů ke spuštění zbytku programu.

![Dokončení programu](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Další kroky
Dokončený projekt z tohoto návodu, zobrazíte [Stáhněte ukázku](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Chcete-li vyhledat další dokumentaci v Azure CDN Management Library pro .NET, podívejte se [odkaz na webové stránce MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Správa prostředků CDN pomocí [Powershellu](cdn-manage-powershell.md).

