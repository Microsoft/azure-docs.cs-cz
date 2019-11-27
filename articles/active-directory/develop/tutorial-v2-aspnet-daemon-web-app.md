---
title: Volání webového rozhraní API ASP.NET chráněného Azure AD – Microsoft Identity Platform
description: V tomto rychlém startu se dozvíte, jak volat webové rozhraní API ASP.NET chráněné Azure Active Directory z aplikace Windows Desktop (WPF). Klient WPF ověří uživatele, požádá o přístupový token a zavolá webové rozhraní API.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328413"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Sestavení démona s více klienty pomocí koncového bodu Microsoft Identity Platform

V tomto kurzu se naučíte používat Microsoft Identity Platform pro přístup k datům obchodních zákazníků Microsoftu v dlouhodobém, neinteraktivním procesu. Ukázkový démon používá [pověření klienta OAuth2](v2-oauth2-client-creds-grant-flow.md) k získání přístupového tokenu, který potom používá k volání [Microsoft Graph](https://graph.microsoft.io) a přístup k datům organizace.

Aplikace je sestavená jako aplikace ASP.NET MVC a k přihlašování uživatelů používá middleware OWIN OpenID Connect.  Jeho komponentou "démon" v této ukázce je kontroler rozhraní API, který se při volání vyžádá v seznamu uživatelů v tenantovi Azure AD zákazníka z Microsoft Graph.  Tento `SyncController.cs` spouští volání AJAX ve webové aplikaci a používá [knihovnu Microsoft Authentication Library (MSAL) pro .NET](msal-overview.md) k získání přístupového tokenu pro Microsoft Graph.

Pro zjednodušenou aplikaci démona konzoly si Projděte příručku [.NET Core démon rychlý Start](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scénář

Vzhledem k tomu, že aplikace je víceklientské aplikace určená pro použití jakýmkoli zákazníkem společnosti Microsoft, musí zákazníkům poskytnout možnost "zaregistrovat" nebo "připojit" aplikaci k podnikovým datům.  Během procesu připojení správce společnosti nejprve udělí **oprávnění aplikace** přímo do aplikace, aby mohl přistupovat k firemním datům neinteraktivním způsobem bez přihlášeného uživatele.  Většina logiky v této ukázce ukazuje, jak dosáhnout tohoto toku připojení pomocí koncového bodu [souhlasu správce](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) platformy identity.

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Další informace o konceptech použitých v této ukázce si přečtěte v [dokumentaci k protokolu přihlašovacích údajů klienta koncového bodu platformy identity](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete ukázku spustit v tomto rychlém startu, budete potřebovat:

- [Visual Studio 2017 nebo 2019](https://visualstudio.microsoft.com/downloads/)
- Tenant Azure Active Directory (Azure AD). Další informace o tom, jak získat tenanta Azure AD, najdete v tématu [Jak získat tenanta Azure AD](quickstart-create-new-tenant.md) .
- Jeden nebo více uživatelských účtů v tenantovi Azure AD. Tato ukázka nebude fungovat s účet Microsoft (dříve účet Windows Live). Pokud jste se k [Azure Portal](https://portal.azure.com) přihlásili pomocí účet Microsoft a nikdy jste v adresáři ještě nevytvořili uživatelský účet, musíte to udělat hned teď.

## <a name="clone-or-download-this-repository"></a>Klonovat nebo stáhnout toto úložiště

Z prostředí nebo příkazového řádku:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Nebo [si ukázku Stáhněte v souboru ZIP](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>Registrace ukázkové aplikace v tenantovi Azure AD

V této ukázce je jeden projekt. Pokud ho chcete zaregistrovat, můžete:

- Použijte postup [registrace ukázky u vašeho tenanta Azure Active Directory](#register-the-sample-application-with-your-azure-ad-tenant) a [konfiguraci ukázky pro použití vašeho tenanta Azure AD](#choose-the-azure-ad-tenant-for-the-applications) .
- Nebo použijte skripty prostředí PowerShell, které:
  - **Automaticky** vytvoří aplikace a související objekty služby Azure AD (hesla, oprávnění, závislosti).
  - Upravte konfigurační soubory projektů sady Visual Studio.

Pokud chcete použít tuto automatizaci:

1. Ve Windows spusťte PowerShell a přejděte do kořenového adresáře klonovaného adresáře.
1. V prostředí PowerShell spusťte:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Spusťte skript pro vytvoření aplikace Azure AD a odpovídajícím způsobem nakonfigurujte kód ukázkové aplikace.
1. V prostředí PowerShell spusťte:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > Další způsoby, jak spouštět skripty, jsou popsány v tématu [skripty pro vytváření aplikací](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md) .

1. Otevřete řešení sady Visual Studio a kliknutím na Spustit spusťte kód.

Pokud nechcete tuto automatizaci používat, postupujte podle následujících pokynů.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>Výběr tenanta Azure AD pro aplikace

Jako první krok budete potřebovat:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet přítomen ve více než jednom tenantovi služby Azure AD, vyberte profil v pravém horním rohu v nabídce v horní části stránky a pak **Přepněte do adresáře**.
   Změňte relaci portálu na požadovaného tenanta Azure AD.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrace klientské aplikace (dotnet-web-démon-v2)

1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
1. Vyberte **Nová registrace**.
1. Když se zobrazí **stránka Registrace aplikace**, zadejte registrační informace vaší aplikace:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `dotnet-web-daemon-v2`.
   - V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři**.
   - V části identifikátor URI přesměrování (volitelné) vyberte v poli se seznamem možnost **Web** a zadejte následující identifikátory URI pro přesměrování:
       - `https://localhost:44316/`
       - Pokud je k dispozici více identifikátorů URI přesměrování, je nutné je po úspěšném vytvoření aplikace přidat z karty **ověřování** později. `https://localhost:44316/Account/GrantPermissions`
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt.
1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
   - V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na `https://localhost:44316/Account/EndSession`
   - V části **Upřesnit nastavení** | **implicitního udělení oprávnění** kontrolujte **přístupové tokeny** a **tokeny ID** , protože tato ukázka vyžaduje, aby byl [tok IMPLICITNÍHO udělení](v2-oauth2-implicit-grant-flow.md) povolen pro přihlášení uživatele a volání rozhraní API.
1. Vyberte **Uložit**.
1. Na stránce **certifikáty & tajné klíče** v části **tajné klíče klienta** vyberte možnost **nový tajný klíč klienta**:

   - Zadejte popis klíče (instance `app secret`),
   - Vyberte dobu trvání klíče buď **v 1 roce**, **2 roky**, nebo **nikdy nevyprší**.
   - Když stisknete tlačítko **Přidat** , hodnota klíče se zobrazí, zkopíruje a uloží se do bezpečného umístění.
   - Tento klíč budete potřebovat později ke konfiguraci projektu v aplikaci Visual Studio. Tato hodnota klíče se znovu nezobrazí ani není dostupná žádným jiným způsobem, takže ji nahrajte hned, jak je vidět z Azure Portal.
1. V seznamu stránek aplikace vyberte **oprávnění rozhraní API** .
   - Klikněte na tlačítko **Přidat oprávnění** a pak na
   - Ujistěte se, že je vybraná karta **rozhraní API Microsoftu** .
   - V části *běžně používaná rozhraní Microsoft API* klikněte na **Microsoft Graph**
   - V části **oprávnění aplikace** zkontrolujte, že jsou zaškrtnutá správná oprávnění: **User. Read. All.**
   - Vyberte tlačítko **Přidat oprávnění** .

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurace ukázky pro použití vašeho tenanta Azure AD

V následujících krocích je "ClientID" stejný jako "ID aplikace" nebo "AppId".

Otevřete řešení v aplikaci Visual Studio a nakonfigurujte projekty.

### <a name="configure-the-client-project"></a>Konfigurovat klientský projekt

Pokud jste použili instalační skripty, budou pro vás provedeny následující změny.

1. Otevřít soubor `UserSync\Web.Config`
1. Vyhledejte klíč aplikace `ida:ClientId` a nahraďte existující hodnotu ID aplikace (clientId) `dotnet-web-daemon-v2` aplikace zkopírované z Azure Portal.
1. Vyhledejte klíč aplikace `ida:ClientSecret` a nahraďte existující hodnotu klíčem, který jste uložili během vytváření `dotnet-web-daemon-v2` aplikace v Azure Portal.

## <a name="run-the-sample"></a>Spuštění ukázky

Vyčistěte řešení, znovu sestavte řešení a spusťte aplikaci UserSync a začněte přihlášením jako správce v tenantovi Azure AD.  Pokud pro testování nemáte tenanta Azure AD, můžete si ho stáhnout [podle těchto pokynů](quickstart-create-new-tenant.md) .

Když se přihlásíte, aplikace vás nejdřív vyzve k zadání oprávnění k přihlášení & čtení profilu uživatele.  Tento souhlas umožňuje aplikaci zajistit, že jste firemním uživatelem.

![Souhlas uživatele](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Aplikace se pak pokusí synchronizovat seznam uživatelů z vašeho tenanta Azure AD prostřednictvím Microsoft Graph.  Pokud to není možné, požádá vás (Správce klienta), aby připojil vašeho tenanta k aplikaci.

Aplikace pak bude požádána o oprávnění ke čtení seznamu uživatelů ve vašem tenantovi.

![Souhlas správce](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Po udělení oprávnění se z aplikace odhlásíte**. K tomu je potřeba zajistit, aby se všechny existující přístupové tokeny pro graf odebraly z mezipaměti tokenů. Po opětovném přihlášení bude mít nově získaný token potřebná oprávnění k volání MS graphu.
Když udělíte oprávnění, aplikace se pak bude moci dotazovat na uživatele v jakémkoli okamžiku.  Můžete to ověřit tak, že kliknete na tlačítko **synchronizovat uživatele** na stránce Uživatelé a aktualizujete seznam uživatelů.  Zkuste přidat nebo odebrat uživatele a seznam znovu synchronizovat (ale nezapomeňte, že synchronizuje jenom první stránku uživatelů!).

## <a name="about-the-code"></a>O kódu

Příslušný kód pro tuto ukázku je v následujících souborech:

- Počáteční přihlášení: `App_Start\Startup.Auth.cs``Controllers\AccountController.cs`.  Konkrétně akce na řadiči mají atribut autorizovat, který vynutí, aby se uživatel přihlásil. Aplikace používá [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) k přihlášení uživatele.
- Synchronizuje se seznam uživatelů s místním úložištěm v paměti: `Controllers\SyncController.cs`
- Zobrazuje se seznam uživatelů z místního úložiště v paměti: `Controllers\UserController.cs`
- Získávají se oprávnění od správce tenanta pomocí koncového bodu souhlasu správce: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>Znovu vytvořit tuto ukázkovou aplikaci

1. V aplikaci Visual Studio vytvořte nový projekt `Visual C#` `ASP.NET Web Application (.NET Framework)`. Na další obrazovce vyberte šablonu projektu `MVC`. Přidejte také složku a základní odkazy pro `Web API`, protože později přidáte kontroler webového rozhraní API.  Nechejte vybraný režim ověřování projektu jako výchozí, tj. `No Authentication`".
2. Vyberte projekt v okně **Průzkumník řešení** a stisknutím klávesy **F4** převeďte vlastnosti projektu. V okně Vlastnosti projektu nastavte možnost **SSL povoleno** , aby byla `True`. Poznamenejte si **adresu URL protokolu SSL**. Budete ho potřebovat při konfiguraci registrace této aplikace v Azure Portal.
3. Přidejte následující ASP.Net OWIN middleware balíčky NuGet: `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` a `Microsoft.Owin.Host.SystemWeb`, `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` a `Microsoft.Identity.Client`. 
4. Ve složce `App_Start` vytvořte `Startup.Auth.cs`třídy. Z názvu oboru názvů odeberte `.App_Start`.  Nahraďte kód pro třídu `Startup` kódem ze stejného souboru ukázkové aplikace.  Nezapomeňte vzít celou definici třídy.  Definice se změní z `public class Startup` na `public partial class Startup`
5. V `Startup.Auth.cs` vyřešit chybějící odkazy přidáním příkazů `using` navržených pomocí IntelliSense v aplikaci Visual Studio.
6. Klikněte pravým tlačítkem na projekt, vyberte Přidat, vyberte "třídu" a do vyhledávacího pole zadejte "OWIN".  "OWIN Startup Class" se zobrazí jako výběr; vyberte ji a pojmenujte `Startup.cs`třídy.
7. V `Startup.cs`nahraďte kód `Startup` třídy kódem ze stejného souboru ukázkové aplikace.  Znovu si poznamenejte změnu definice z `public class Startup` na `public partial class Startup`.
8. Ve složce přidejte novou třídu s názvem `MsGraphUser.cs`.  Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
9. Přidejte nový **kontroler MVC 5 – prázdný** s názvem `AccountController`. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
10. Přidejte nový **kontroler MVC 5 – prázdný** s názvem `UserController`. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
11. Přidejte nový **kontroler webového rozhraní API 2 – prázdný** s názvem `SyncController`. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
12. Pro uživatelské rozhraní ve složce `Views\Account` přidejte tři **prázdná zobrazení (bez modelu)** s názvem `GrantPermissions`, `Index` a `UserMismatch` a jednu s názvem `Index` ve složce `Views\User`. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
13. Aktualizujte `Shared\_Layout.cshtml` a `Home\Index.cshtml` pro správné propojení různých zobrazení.

## <a name="deploy-this-sample-to-azure"></a>Nasadit tuto ukázku do Azure

Tento projekt má projekty WebApp/Web API. Pokud je chcete nasadit na weby Azure, budete pro každý z nich potřebovat:

- Vytvoření webu Azure
- publikujte webovou aplikaci/webové rozhraní API na web a
- aktualizujte klienty, kteří budou volat web místo IIS Express.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>Vytvoření a publikování `dotnet-web-daemon-v2` na webu Azure

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. V levém horním rohu klikněte na `Create a resource`, vyberte **webová** --> **Webová aplikace**a zadejte název svého webu, například `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Pak vyberte `Subscription`, `Resource Group``App service plan and Location`. `OS` bude **Windows** a `Publish` bude **kód**.
1. Klikněte na `Create` a počkejte na vytvoření App Service.
1. Jakmile dostanete oznámení `Deployment succeeded`, klikněte na `Go to resource` a přejděte do nově vytvořené služby App Service.
1. Po vytvoření webu ho vyhledejte na **řídicím panelu** a kliknutím na něj otevřete App Services obrazovce s **přehledem** .
1. Na kartě **přehled** App Service stáhněte profil publikování kliknutím na odkaz **získat profil publikování** a uložte ho.  Lze také použít jiné mechanismy nasazení, jako je například ze správy zdrojového kódu.
1. Přepněte do sady Visual Studio a přejděte do projektu dotnet-web-démon-v2.  Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte **publikovat**.  Klikněte na tlačítko **Importovat profil** na dolním panelu a importujte profil publikování, který jste si stáhli dříve.
1. Klikněte na **Konfigurovat** a v `Connection tab`aktualizujte cílovou adresu URL tak, aby byla `https` na adrese URL domovské stránky, například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Klikněte na **Další**.
1. Na kartě nastavení se ujistěte, že není vybraná možnost `Enable Organizational Authentication`.  Klikněte na možnost **Uložit**. Na hlavní obrazovce klikněte na **publikovat** .
1. Visual Studio projekt publikuje a automaticky otevře prohlížeč na adrese URL projektu.  Pokud se zobrazí výchozí webová stránka projektu, publikace byla úspěšně dokončena.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualizujte registraci aplikace tenanta Azure AD pro `dotnet-web-daemon-v2`

1. Přejděte zpět na [Azure Portal](https://portal.azure.com).
V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací**.
1. Na obrazovce výsledná obrazovka vyberte aplikaci `dotnet-web-daemon-v2`.
1. V **ověřování** | pro vaši aplikaci aktualizujte pole Adresa URL pro odhlášení pomocí adresy vaší služby, například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. V nabídce *značky* aktualizujte **adresu URL domovské stránky**na adresu vaší služby, například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Konfiguraci uložte.
1. Přidejte stejnou adresu URL do seznamu hodnot v nabídce *ověřování-> identifikátory URI přesměrování* . Pokud máte více adres URL pro přesměrování, ujistěte se, že pro každou adresu URL pro přesměrování existuje nový záznam s použitím identifikátoru URI služby App Service.

## <a name="community-help-and-support"></a>Pomoc a podpora komunity

K získání podpory od komunity použijte [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) .
Nejdřív si položte otázky na Stack Overflow a Projděte si stávající problémy, abyste viděli, jestli se někdo na svůj dotaz dotazoval.
Ujistěte se, že jsou vaše dotazy nebo komentáře označené pomocí [`adal` `msal` `dotnet`].

Pokud v ukázce najdete a máte chybu, vyřešte problém na [problémech na GitHubu](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Pokud v MSAL.NET zjistíte chybu, vyřešte problém na [MSAL.NET GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Pokud chcete zadat doporučení, navštivte následující [stránku uživatelského hlasu](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o různých [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md) , které podporuje platforma Microsoft Identity Platform.

Další informace najdete v následující Koncepční dokumentaci:

- [Tenantů v Azure Active Directory](single-and-multi-tenant-apps.md)
- [Prostředí vyjádření souhlasu s aplikací Azure AD](application-consent-experience.md)
- [Postupy: přihlášení libovolného Azure Active Directory uživatele pomocí vzoru aplikace pro více tenantů](howto-convert-app-to-be-multi-tenant.md)
- [Vysvětlení souhlasu uživatele a správce](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Aplikační a instanční objekty v Azure Active Directory](app-objects-and-service-principals.md)
- [Rychlý Start: registrace aplikace s platformou Microsoft identity](quickstart-register-app.md)
- [Rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
- [Získání tokenu pro aplikaci pomocí toků přihlašovacích údajů klienta](msal-client-applications.md)

Pro jednodušší aplikaci démona konzole pro více tenantů si Projděte příručku [.NET Core démon rychlý Start](quickstart-v2-netcore-daemon.md).
