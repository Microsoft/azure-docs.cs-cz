---
title: 'Kurz: sestavení démona s více klienty, který přistupuje k Microsoft Graph obchodním datům | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu se dozvíte, jak volat webové rozhraní API ASP.NET chráněné Azure Active Directory z aplikace Windows Desktop (WPF). Klient WPF ověří uživatele, požádá o přístupový token a zavolá webové rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 5816bc542ff550accedd33ba9a7fd9d98583b3d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653758"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>Kurz: sestavení démona s více klienty, který používá platformu Microsoft identity

V tomto kurzu stáhnete a spustíte webovou aplikaci démona ASP.NET, která demonstruje použití přihlašovacích údajů klienta OAuth 2,0, aby získala přístupový token pro volání rozhraní API Microsoft Graph.

V tomto kurzu:

> [!div class="checklist"]
> * Integrace aplikace démona s platformou Microsoft identity
> * Udělení oprávnění k aplikaci přímo aplikaci správcem
> * Získání přístupového tokenu pro volání rozhraní Microsoft Graph API
> * Zavolejte Microsoft Graph rozhraní API.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

- [Visual Studio 2017 nebo 2019](https://visualstudio.microsoft.com/downloads/).
- Tenanta Azure AD. Další informace najdete v tématu [Jak získat tenanta Azure AD](quickstart-create-new-tenant.md).
- Jeden nebo více uživatelských účtů v tenantovi Azure AD. Tato ukázka nebude fungovat s účet Microsoft. Pokud jste se k [Azure Portal](https://portal.azure.com) přihlásili pomocí účet Microsoft a nikdy jste ve svém adresáři nevytvořili uživatelský účet, udělejte to teď.

## <a name="scenario"></a>Scenario

Aplikace je sestavená jako aplikace ASP.NET MVC. K přihlašování uživatelů používá middleware OWIN OpenID Connect.

Komponentou "démon" v této ukázce je kontroler rozhraní API, `SyncController.cs` . Když se kontroler zavolá, vyžádá si seznam uživatelů v tenantovi Azure Active Directory zákazníka (Azure AD) z Microsoft Graph. `SyncController.cs` je aktivován voláním AJAX ve webové aplikaci. K získání přístupového tokenu pro Microsoft Graph používá [Microsoft Authentication Library (MSAL) pro .NET](msal-overview.md) .

Vzhledem k tomu, že aplikace je víceklientské aplikace pro obchodní zákazníky Microsoftu, musí zákazníkům poskytnout způsob, jak se zaregistrovat nebo připojit k datům společnosti. Během toku připojení globální správce nejprve udělí *oprávnění aplikace* přímo do aplikace, aby mohl přistupovat k firemním datům neinteraktivním způsobem bez přihlášeného uživatele. Většina logiky v této ukázce ukazuje, jak dosáhnout tohoto toku připojení pomocí koncového bodu [souhlasu správce](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) platformy identity.

![Diagram zobrazuje aplikaci v UserSync se třemi místními položkami připojujícími se k Azure. ověřování pomocí spouštěcích teček znovu získává token, který umožňuje interaktivní připojení k Azure a D, AccountController získání souhlasu správce pro připojení k Azure a D a SyncController čtení uživatele pro připojení k Microsoft Graph.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Další informace o konceptech použitých v této ukázce najdete v dokumentaci k [protokolu přihlašovacích údajů klienta pro platformu identity](v2-oauth2-client-creds-grant-flow.md).

## <a name="clone-or-download-this-repository"></a>Klonovat nebo stáhnout toto úložiště

V prostředí nebo příkazovém řádku zadejte tento příkaz:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Nebo [si ukázku Stáhněte v souboru ZIP](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registrace aplikace

Tato ukázka má jeden projekt. Pokud chcete aplikaci zaregistrovat u svého tenanta Azure AD, můžete:

- Postupujte podle kroků v části [registrace ukázky u svého tenanta Azure Active Directory](#register-the-client-app-dotnet-web-daemon-v2) a [nakonfigurujete ukázku, aby používala vašeho tenanta Azure AD](#choose-the-azure-ad-tenant).
- Použijte skripty prostředí PowerShell, které:
  - *Automaticky* vytvořte aplikace Azure AD a související objekty (hesla, oprávnění, závislosti) za vás.
  - Upravte konfigurační soubory projektů sady Visual Studio.

Pokud chcete použít automatizaci:

1. V systému Windows spusťte PowerShell a přejdete do kořenového adresáře klonovaného adresáře.
1. Spusťte tento příkaz:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Spusťte skript pro vytvoření aplikace Azure AD a příslušným způsobem nakonfigurujte kód ukázkové aplikace:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Další způsoby spouštění skriptů jsou popsány v tématu [skripty pro vytváření aplikací](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Otevřete řešení sady Visual Studio a výběrem **Spustit** spusťte kód.

Pokud nechcete používat automatizaci, postupujte podle kroků v následujících částech.

### <a name="choose-the-azure-ad-tenant"></a>Výběr tenanta Azure AD

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrace klientské aplikace (dotnet-web-démon-v2)

1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace, například `dotnet-web-daemon-v2` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři**.
1. V části **identifikátor URI přesměrování (volitelné)** vyberte **Web** v poli se seznamem a zadejte `https://localhost:44316/` a `https://localhost:44316/Account/GrantPermissions` jako identifikátory URI přesměrování.

    Pokud existuje více než dva identifikátory URI přesměrování, budete je muset přidat z karty **ověřování** později po úspěšném vytvoření aplikace.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt.
1. V části **Spravovat** vyberte **ověřování**.
1. Nastavte **adresu URL pro odhlášení front-Channel** na `https://localhost:44316/Account/EndSession` .
1. V části **implicitní udělení a hybridní toky** vyberte **přístupové tokeny** a **tokeny ID**. Tato ukázka vyžaduje, aby byl [tok implicitního udělení](v2-oauth2-implicit-grant-flow.md) povolen pro přihlášení uživatele a volání rozhraní API.
1. Vyberte **Uložit**.
1. V části **Správa** vyberte **Certifikáty a tajné kódy**.
1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta**. 
1. Zadejte popis klíče (například **tajný klíč aplikace**).
1. Vyberte dobu trvání klíče buď **v 1 roce**, **2 roky**, nebo **nikdy nevyprší**.
1. Vyberte **Přidat**. Poznamenejte si hodnotu klíče v bezpečném umístění. Tento klíč budete potřebovat později ke konfiguraci projektu v aplikaci Visual Studio.
1. V části **Spravovat** vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**.
1. V části **běžně používaná rozhraní Microsoft API** vyberte **Microsoft Graph**.
1. V části **oprávnění aplikace** se ujistěte, že jsou vybrána správná oprávnění: **User. Read. All**.
1. Vyberte **Přidat oprávnění**.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurace ukázky pro použití vašeho tenanta Azure AD

V následujících krocích je **ClientID** stejný jako "ID aplikace" nebo **AppID**.

Otevřete řešení v aplikaci Visual Studio a nakonfigurujte projekty.

### <a name="configure-the-client-project"></a>Konfigurovat klientský projekt

Pokud jste použili instalační skripty, budou pro vás provedeny následující změny.

1. Otevřete soubor **UserSync\Web.Config** .
1. Vyhledejte klíč aplikace **IDA: ClientID**. Nahraďte existující hodnotu ID aplikace aplikace **dotnet-web-démon-v2** zkopírovanou z Azure Portal.
1. Vyhledejte klíč aplikace v aplikaci **IDA: ClientSecret**. Existující hodnotu nahraďte klíčem, který jste uložili během vytváření aplikace **dotnet-web-démon-v2** v Azure Portal.

## <a name="run-the-sample"></a>Spuštění ukázky

Vyčistěte řešení, znovu sestavte řešení, spusťte aplikaci UserSync a pak se přihlaste jako správce tenanta služby Azure AD. Pokud pro testování nemáte tenanta Azure AD, můžete si ho stáhnout [podle těchto pokynů](quickstart-create-new-tenant.md) .

Když se přihlásíte, aplikace si nejdřív vyžádá oprávnění k přihlášení a čtení vašeho profilu uživatele. Díky tomuto souhlasu aplikace zajistíte, že jste firemním uživatelem.

![Souhlas uživatele](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Aplikace se pak pokusí synchronizovat seznam uživatelů z vašeho tenanta Azure AD prostřednictvím Microsoft Graph. Pokud to nepomůže, požádá vás (Správce klienta), aby připojil vašeho tenanta k aplikaci.

Aplikace pak vyzve k zadání oprávnění ke čtení seznamu uživatelů ve vašem tenantovi.

![Souhlas správce](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Po udělení oprávnění jste se z aplikace odhlásili. Toto přihlášení zajišťuje, že všechny existující přístupové tokeny pro Microsoft Graph budou odebrány z mezipaměti tokenů. Po opětovném přihlášení bude mít nově získaný token potřebná oprávnění k volání Microsoft Graph.


Když udělíte oprávnění, aplikace se pak může v libovolném bodě dotazovat na uživatele. Můžete to ověřit tak, že vyberete tlačítko **synchronizovat uživatele** a aktualizujete seznam uživatelů. Zkuste přidat nebo odebrat uživatele a znovu synchronizovat seznam. (Ale Všimněte si, že aplikace synchronizuje jenom první stránku uživatelů.)

## <a name="about-the-code"></a>O kódu

Příslušný kód pro tuto ukázku je v následujících souborech:

- **App_start \Startup.auth.cs**, **Controllers\AccountController.cs**: počáteční přihlášení. Konkrétně akce na řadiči mají atribut **autorizovat** , který vynutí, aby se uživatel přihlásil. Aplikace používá [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) k přihlášení uživatele.
- **Controllers\SyncController.cs**: synchronizuje seznam uživatelů s místním úložištěm v paměti.
- **Controllers\UserController.cs**: zobrazení seznamu uživatelů z místního úložiště v paměti.
- **Controllers\AccountController.cs**: nabývá oprávnění od správce tenanta pomocí koncového bodu souhlasu správce.

## <a name="re-create-the-sample-app"></a>Opětovné vytvoření ukázkové aplikace

1. V aplikaci Visual Studio vytvořte nový projekt webové aplikace **Visual C#** **ASP.NET (.NET Framework)** .
1. Na další obrazovce vyberte šablonu projektu **MVC** . Přidejte také složku a základní odkazy pro **webové rozhraní API**, protože později přidáte kontroler webového rozhraní API. Nechejte vybraný režim ověřování projektu jako výchozí: **bez ověřování**.
1. V okně **Průzkumník řešení** vyberte projekt a vyberte klávesu **F4** .
1. V okně Vlastnosti projektu nastavte možnost **SSL povoleno** na **hodnotu true**. Poznamenejte si informace v **adrese URL protokolu SSL**. Budete je potřebovat při konfiguraci registrace této aplikace v Azure Portal.
1. Přidejte následující balíčky NuGet pro middleware OWIN ASP.NET:
   - Microsoft. Owin. Security. dataactive
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft. IdentityModel. Protocol. Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft. identity. Client
1. Ve složce **app_start** :
   1. Vytvořte třídu s názvem **Startup. auth. cs**.
   1. Odebrat **. App_Start** z názvu oboru názvů.
   1. Nahraďte kód pro **spouštěcí** třídu kódem ze stejného souboru ukázkové aplikace.
   Nezapomeňte vzít v úvahu definici celé třídy. Definice se změní z **spuštění veřejné třídy** na **veřejnou částečnou třídu.**
1. Při **spuštění. auth. cs** vyřešte chybějící odkazy přidáním příkazů, které jsou navrženy **pomocí** technologie IntelliSense sady Visual Studio.
1. Klikněte pravým tlačítkem na projekt, vyberte **Přidat** a pak vyberte **Třída**.
1. Do vyhledávacího pole zadejte **Owin**. **Spouštěcí třída Owin** se zobrazuje jako výběr. Vyberte ji a pojmenujte třídu **Startup. cs**.
1. V souboru **Startup. cs** nahraďte kód pro **spouštěcí** třídu kódem ze stejného souboru ukázkové aplikace. Znovu si všimněte, že se změní definice z **spuštění veřejné třídy** na **veřejnou částečnou třídu**.
1. Ve složce **modely** přidejte novou třídu s názvem **MsGraphUser. cs**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Přidejte nový **kontroler MVC 5 – prázdná instance s** názvem **AccountController**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Přidejte nový **kontroler MVC 5 – prázdná instance s** názvem **UserController**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Přidejte nový **kontroler webového rozhraní API 2 – prázdná instance s** názvem **SyncController**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Pro uživatelské rozhraní ve složce **Views\Account** přidejte tři **prázdné (bez modelu) zobrazení** instancí s názvem **GrantPermissions**, **index** a **UserMismatch**. Přidejte a jeden pojmenovaný **index** do složky **Views\User** . Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Aktualizujte **Shared \_ layout. cshtml** a **Home\Index.cshtml** , aby správně propojuje různá zobrazení.

## <a name="deploy-the-sample-to-azure"></a>Nasazení ukázky do Azure

Tento projekt má webové aplikace a projekty webového rozhraní API. Pokud je chcete nasadit na Azure websites, proveďte následující kroky pro každé z nich:

1. Vytvořte web Azure.
1. Publikujte webovou aplikaci a webová rozhraní API na web.
1. Aktualizujte klienty pro volání webu místo IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Vytvoření a publikování dotnet-web-démon-v2 na webu Azure

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. V levém horním rohu vyberte **Vytvořit prostředek**.
1. Vyberte **Webová**  >  **Webová aplikace** a potom zadejte název svého webu. Například pojmenujte ho **dotnet-web-daemon-v2-contoso.azurewebsites.NET**.
1. Vyberte informace pro **předplatné**, **skupinu prostředků** a **plán a umístění služby App Service**. **Operační** systém je **Windows** a **publikování** je **kód**.
1. Vyberte **vytvořit** a počkejte, než se služba App Service vytvoří.
1. Až se vám zobrazí oznámení o **úspěšném nasazení** , vyberte **Přejít k prostředku** a přejít na nově vytvořenou službu App Service.
1. Po vytvoření webu ho Najděte na **řídicím panelu** a vyberte ho a otevřete tak obrazovku s **přehledem** služby App Service.
1. Na kartě **Přehled** služby App Service stáhněte profil publikování, a to tak, že vyberete odkaz **získat profil publikování** a uložíte ho. Můžete použít další mechanismy nasazení, jako je například nasazení ze správy zdrojového kódu.
1. Přepněte do sady Visual Studio a potom:
   1. Přejít do projektu **dotnet-web-démon-v2** .
   1. Klikněte pravým tlačítkem na projekt v Průzkumník řešení a pak vyberte **publikovat**.
   1. Na dolním panelu vyberte **Importovat profil** a importujte profil publikování, který jste si stáhli dříve.
1. Vyberte **Konfigurovat**.
1. Na kartě **připojení** aktualizujte cílovou adresu URL tak, aby používala https. Například použijte `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` . Vyberte **Další**.
1. Na kartě **Nastavení** se ujistěte, že je zaškrtnuté políčko **Povolit ověřování organizace** .
1. Vyberte **Uložit**. Na hlavní obrazovce vyberte **publikovat** .

Visual Studio projekt zveřejní a automaticky otevře prohlížeč na adrese URL projektu. Pokud se zobrazí výchozí webová stránka projektu, publikace byla úspěšná.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualizace registrace aplikace tenanta Azure AD pro dotnet-web-démon-v2

1. Vraťte se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. V levém podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací**.
1. Vyberte aplikaci **dotnet-web-démon-v2** .
1. Na stránce **ověřování** pro vaši aplikaci aktualizujte pole **Adresa URL pro odhlášení front-Channel** adresou vaší služby. Například použijte `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession` .
1. V nabídce **značky** aktualizujte adresu **URL domovské stránky** na adresu vaší služby. Například použijte `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` .
1. Konfiguraci uložte.
1. V seznamu hodnot v   >  nabídce **identifikátory URI přesměrování** ověřování přidejte stejnou adresu URL. Pokud máte více adres URL pro přesměrování, ujistěte se, že je k dispozici nová položka, která pro každou adresu URL přesměrování používá identifikátor URI služby App Service.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, odstraňte objekt aplikace, který jste vytvořili v kroku [Registrace aplikace](#register-your-application) .  Pokud chcete aplikaci odebrat, postupujte podle pokynů v části [odebrání aplikace vytvořené vámi nebo vaší organizací](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Získání pomoci

K získání podpory od komunity použijte [Microsoft Q&a](/answers/products/) .
Položte své dotazy na [Microsoft Q&](/answers/products/) a Projděte si stávající problémy, abyste viděli, jestli se někdo na svůj dotaz předá dotaz.
Ujistěte se, že vaše dotazy nebo komentáře jsou označené jako "Azure-AD-ADAL-vyřazení", "Azure-AD-msal a" dotnet-Standard ".

Pokud v ukázce zjistíte chybu, vyřešte prosím problém na [problémech na GitHubu](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Pokud v MSAL.NET zjistíte chybu, vyřešte problém na [MSAL.NET GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Pokud chcete zadat doporučení, navštivte [stránku hlas uživatele](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Další kroky

Další informace o vytváření aplikací démona, které k přístupu k chráněným webovým rozhraním API používají platformu Microsoft identity:

> [!div class="nextstepaction"]
> [Scénář: aplikace démona, která volá webová rozhraní API](scenario-daemon-overview.md)