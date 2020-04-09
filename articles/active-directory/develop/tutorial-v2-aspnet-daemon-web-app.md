---
title: Vytvoření víceklientského daemonu, který používá koncový bod platformy identit microsoftu
description: V tomto kurzu se dozvíte, jak volat ASP.NET webové rozhraní API chráněné službou Azure Active Directory z aplikace WPF (Desktop) systému Windows. WPF klient ověřuje uživatele, požaduje přístupový token a volá webové rozhraní API.
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
ms.openlocfilehash: a4d7030f7a58a6252c6e596fc2c248163694a1e8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880869"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Kurz: Vytvoření víceklientského daemonu, který používá koncový bod platformy identit y Microsoft

V tomto kurzu se dozvíte, jak používat platformu identit microsoftu pro přístup k datům firemních zákazníků Microsoftu v dlouhotrvajícím, neinteraktivním procesu. Ukázkový daemon používá [udělení pověření klienta OAuth2](v2-oauth2-client-creds-grant-flow.md) k získání přístupového tokenu. Daemon pak používá token k volání [Microsoft Grapha](https://graph.microsoft.io) a přístup u dat organizace.

> [!div class="checklist"]
> * Integrace aplikace pro daemon s platformou identit Microsoftu
> * Udělení oprávnění aplikace přímo aplikaci správcem
> * Získání přístupového tokenu pro volání rozhraní Microsoft Graph API
> * Zavolejte rozhraní Microsoft Graph API.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Aplikace je vytvořena jako ASP.NET aplikace MVC. Používá middleware OWIN OpenID Connect pro přihlášení uživatelů.  

Komponenta "daemon" v této ukázce je řadič rozhraní API . `SyncController.cs` Při volání řadiče se natáhne seznam uživatelů v klienta Azure Active Directory zákazníka (Azure AD) z Microsoft Graphu. `SyncController.cs`je spuštěna voláním AJAX ve webové aplikaci. Používá [knihovnu ověřování Společnosti Microsoft (MSAL) pro rozhraní .NET](msal-overview.md) k získání přístupového tokenu pro aplikaci Microsoft Graph.

>[!NOTE]
> Pokud s platformou identit microsoftu teče težkou, doporučujeme začít s [rychlým startem daemonu .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scénář

Vzhledem k tomu, že aplikace je víceklientská aplikace pro firemní zákazníky Microsoftu, musí zákazníkům poskytnout způsob, jak se k aplikaci "zaregistrovat" nebo připojit k firemním datům. Během toku připojení správce společnosti nejprve udělí *oprávnění aplikace* přímo do aplikace, aby měl přístup k firemním datům neinteraktivním způsobem bez přítomnosti přihlášeného uživatele. Většina logiky v této ukázce ukazuje, jak dosáhnout tohoto toku připojení pomocí koncového bodu souhlasu identity platformy [pro správu.](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Další informace o konceptech použitých v této ukázce naleznete v [dokumentaci protokolu pověření klienta pro koncový bod platformy identity](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit ukázku v tomto rychlém startu, budete potřebovat:

- [Visual Studio 2017 nebo 2019](https://visualstudio.microsoft.com/downloads/).
- Tenanta Azure AD. Další informace najdete v [tématu Jak získat klienta Azure AD](quickstart-create-new-tenant.md).
- Jeden nebo více uživatelských účtů ve vašem tenantovi Azure AD. Tato ukázka nebude fungovat s účtem Microsoft (dříve účet Windows Live). Pokud jste se k [portálu Azure](https://portal.azure.com) přihlásili pomocí účtu Microsoft a nikdy jste si v adresáři nevytvořili uživatelský účet, musíte to udělat hned.

## <a name="clone-or-download-this-repository"></a>Klonovat nebo stáhnout tento repozitář

Z prostředí nebo příkazového řádku zadejte tento příkaz:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Nebo [si stáhněte ukázku v souboru zip](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registrace vaší aplikace

Tato ukázka má jeden projekt. Pokud chcete zaregistrovat aplikaci u svého klienta Azure AD, můžete buď:

- Postupujte podle kroků v [části Registrace ukázky u tenanta Služby Azure Active Directory](#register-your-application) a [nakonfigurujte ukázku tak, aby používala klienta Azure AD](#choose-the-azure-ad-tenant).
- Použijte skripty Prostředí PowerShell, které:
  - *Automaticky* vytvořte aplikace Azure AD a související objekty (hesla, oprávnění, závislosti) za vás.
  - Upravte konfigurační soubory projektů sady Visual Studio.

Pokud chcete použít automatizaci:

1. Ve Windows spusťte prostředí PowerShell a přejděte do kořenového adresáře klonovaného adresáře.
1. Spusťte tento příkaz:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Spusťte skript a vytvořte aplikaci Azure AD a odpovídajícím způsobem nakonfigurujte kód ukázkové aplikace:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Další způsoby spouštění skriptů jsou popsány ve [skriptech pro vytváření aplikací](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Otevřete řešení Visual Studio a vyberte **Spustit** pro spuštění kódu.

Pokud nechcete používat automatizaci, použijte kroky v následujících částech.

### <a name="choose-the-azure-ad-tenant"></a>Výběr klienta Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet ve více než jednom tenantovi Azure AD, vyberte svůj profil v nabídce v horní části stránky a pak vyberte **Přepnout adresář**.
1. Změňte relaci portálu na požadovaný klient Azure AD.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrace klientské aplikace (dotnet-web-daemon-v2)

1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) na platformě identit Microsoftu pro vývojáře.
1. Vyberte **možnost Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   - V části **Název** zadejte smysluplný název aplikace, který se uživatelům aplikace zobrazí. Zadejte například **dotnet-web-daemon-v2**.
   - V části **Podporované typy účtů** vyberte účty v **libovolném organizačním adresáři**.
   - V části **Přesměrovat identifikátor URI (volitelné)** vyberte v poli se seznamem **možnost Web** a zadejte následující identifikátory URI přesměrování:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Pokud existují více než dvě identifikátory URI přesměrování, budete je muset přidat z karty **Ověřování** později, po úspěšném vytvoření aplikace.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace najděte hodnotu **ID aplikace (klienta)** a zaznamenejte ji na později. Budete ji potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt.
1. V seznamu stránek pro aplikaci vyberte **Ověřování**. Potom:
   - V části **Upřesnit nastavení** nastavte adresu **https://localhost:44316/Account/EndSession**URL **odhlášení** na adresu .
   - V části **Upřesnit nastavení** > **Implicitní udělení** vyberte **přístupové tokeny** a **tokeny ID**. Tato ukázka vyžaduje [implicitní tok grantu,](v2-oauth2-implicit-grant-flow.md) který má být povolen pro přihlášení uživatele a volání rozhraní API.
1. Vyberte **Uložit**.
1. Na stránce **Certifikáty & tajných kódů** vyberte v části **Tajné klíče klienta** **nový tajný klíč klienta**. Potom:

   1. Zadejte popis klíče (například **tajný klíč aplikace**),
   1. Vyberte dobu trvání klíče **buď v 1 rok**, V 2 **roky**, nebo **nikdy nevyprší**.
   1. Vyberte tlačítko **Přidat.** 
   1. Když se zobrazí hodnota klíče, zkopírujte ji a uložte na bezpečném místě. Tento klíč budete později potřebovat ke konfiguraci projektu v sadě Visual Studio. Nebude znovu zobrazena ani ji možné ji získat jiným způsobem.
1. V seznamu stránek aplikace vyberte **oprávnění rozhraní API**. Potom:
   1. Vyberte tlačítko **Přidat oprávnění**.
   1. Ujistěte se, že je vybrána karta **Microsoft API.**
   1. V části **Běžně používaná rozhraní MICROSOFT API** vyberte microsoft **graph**.
   1. V části **Oprávnění aplikace** zkontrolujte, zda jsou vybrána správná oprávnění: **User.Read.All**.
   1. Vyberte tlačítko **Přidat oprávnění.**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurace ukázky pro použití vašeho klienta Azure AD

V následujících krocích **ClientID** je stejný jako "ID aplikace" nebo **AppId**.

Otevřete řešení v sadě Visual Studio a nakonfigurujte projekty.

### <a name="configure-the-client-project"></a>Konfigurace klientského projektu

Pokud jste použili instalační skripty, budou pro vás použity následující změny.

1. Otevřete soubor **UserSync\Web.Config.**
1. Najít klíč aplikace **ida:ClientId**. Nahraďte existující hodnotu ID aplikace **dotnet-web-daemon-v2** aplikace zkopírované z portálu Azure.
1. Najít klíč aplikace **ida:ClientSecret**. Nahraďte existující hodnotu klíčem, který jste uložili při vytváření aplikace **dotnet-web-daemon-v2** na webu Azure Portal.

## <a name="run-the-sample"></a>Spuštění ukázky

Vyčistěte řešení, znovu vytvořte řešení, spusťte aplikaci UserSync a přihlaste se jako správce v tenantovi Azure AD. Pokud nemáte klienta Azure AD pro testování, můžete [postupovat podle těchto pokynů](quickstart-create-new-tenant.md) a získat jeden.

Když se přihlásíte, aplikace vás nejprve požádá o oprávnění k přihlášení a přečtení vašeho uživatelského profilu. Tento souhlas umožňuje aplikaci zajistit, že jste firemní uživatel.

![Souhlas uživatele](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Aplikace se pak pokusí synchronizovat seznam uživatelů z vašeho klienta Azure AD prostřednictvím Microsoft Graphu. Pokud nemůže, požádá vás (správce klienta) o připojení vašeho klienta k aplikaci.

Aplikace pak požádá o oprávnění ke čtení seznamu uživatelů ve vašem tenantovi.

![Souhlas správce](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Po udělení oprávnění jste z aplikace odhlášeni. Toto odhlášení zajišťuje, že všechny existující přístupové tokeny pro Microsoft Graph je odebrán z mezipaměti tokenů. Při znovu přihlášení, čerstvé token, který je získán bude mít potřebná oprávnění k volání do Microsoft Graphu.


Když udělíte oprávnění, aplikace pak může dotaz pro uživatele v libovolném bodě. Můžete to ověřit výběrem tlačítka **Synchronizovat uživatele** a aktualizací seznamu uživatelů. Zkuste přidat nebo odebrat uživatele a znovu synchronizovat seznam. (Mějte však na vědomí, že aplikace synchronizuje pouze první stránku uživatelů.)

## <a name="about-the-code"></a>O kódu

Příslušný kód pro tuto ukázku je v následujících souborech:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Počáteční přihlášení. Zejména akce na řadiči mají **Authorize** atribut, který nutí uživatele k přihlášení. Aplikace používá [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) k přihlášení uživatele.
- **Controllers\SyncController.cs**: Synchronizace seznamu uživatelů s místním úložištěm v paměti.
- **Controllers\UserController.cs**: Zobrazení seznamu uživatelů z místního úložiště v paměti.
- **Controllers\AccountController.cs**: Získání oprávnění od správce klienta pomocí koncového bodu souhlasu správce.

## <a name="re-create-the-sample-app"></a>Opětovné vytvoření ukázkové aplikace

1. V sadě Visual Studio vytvořte nový projekt **Visual C#** **ASP.NET webovou aplikaci (.NET Framework).** 
1. Na další obrazovce zvolte šablonu projektu **MVC.** Přidejte také odkazy na složky a základní odkazy pro **webové rozhraní API**, protože později přidáte řadič webového rozhraní API. Ponechte zvolený režim ověřování projektu jako výchozí: **Bez ověřování**.
1. Vyberte projekt v okně **Průzkumník řešení** a vyberte klávesu **F4.** 
1. Ve vlastnostech projektu nastavte **hodnotu SSL povoleno** na **hodnotu True**. Poznamenejte si informace v **adrese URL SSL**. Budete ji potřebovat při konfiguraci registrace této aplikace na webu Azure Portal.
1. Přidejte následující ASP.NET middleware OWIN NuGet balíčky: 
   - Adresář Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Rozšíření Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. Ve složce **App_Start:**
   1. Vytvořte třídu s názvem **Startup.Auth.cs**. 
   1. Odebrat **. App_Start** z názvu oboru názvů. 
   1. Nahraďte kód pro třídu **Startup** kódem ze stejného souboru ukázkové aplikace.       
   Ujistěte se, že vzít celou definici třídy. Definice se změní z **veřejné třídy Spuštění** na **veřejné částečné třídy Spuštění.**
1. V **Startup.Auth.cs**vyřešte chybějící odkazy přidáním **pomocí** příkazů navržených aplikací Visual Studio IntelliSense.
1. Klepněte pravým tlačítkem myši na projekt, vyberte **přidat**a pak vyberte **třídu**.
1. Do vyhledávacího pole zadejte **OWIN**. **Třída OWIN Startup** se zobrazí jako výběr. Vyberte ji a pojmenujte třídu **Startup.cs**.
1. V **Startup.cs**nahraďte kód pro třídu **Startup** kódem ze stejného souboru ukázkové aplikace. Opět si všimněte, že definice se změní z **veřejné třídy Spuštění** na **veřejné částečné třídy Spuštění**.
1. Ve složce **Modely** přidejte novou třídu nazvanou **MsGraphUser.cs**. Nahraďte implementaci obsahem souboru se stejným názvem ze vzorku.
1. Přidejte nový **řadič MVC 5 - Prázdná** instance s názvem **AccountController**. Nahraďte implementaci obsahem souboru se stejným názvem ze vzorku.
1. Přidejte nový **řadič MVC 5 - Prázdná** instance s názvem **UserController**. Nahraďte implementaci obsahem souboru se stejným názvem ze vzorku.
1. Přidání nového **webového rozhraní API 2 Controller – prázdná** instance s názvem **SyncController**. Nahraďte implementaci obsahem souboru se stejným názvem ze vzorku.
1. Pro uživatelské rozhraní přidejte do složky **Zobrazení\Účet** tři instance **Empty (bez modelu)** s názvem **GrantPermissions**, **Index**a **UserMismatch**. Přidat a jeden s názvem **Index** ve složce **Zobrazení\Uživatel.** Nahraďte implementaci obsahem souboru se stejným názvem ze vzorku.
1. Aktualizujte **sdílené\_rozložení.cshtml** a **Home\Index.cshtml,** abyste správně propojili různá zobrazení.

## <a name="deploy-the-sample-to-azure"></a>Nasazení ukázky do Azure

Tento projekt má webové aplikace a webové api projekty. Pokud je chcete nasadit na weby Azure, postupujte pro každý z nich takto:

1. Vytvořte web Azure.
1. Publikujte webová aplikace a webová api na web.
1. Aktualizujte klienty tak, aby místo služby IIS Express volali na web.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Vytvoření a publikování dotnet-web-daemon-v2 na webu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém horním rohu vyberte **Vytvořit prostředek**.
1. Vyberte **Web** > **Web App**a pojmenujte svůj web. Pojmenujte jej například **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Vyberte informace pro **předplatné**, **skupinu prostředků**a **plán a umístění služby App**. **Operační systém** je **Windows**a **Publikování** je **kód**.
1. Vyberte **Vytvořit** a počkejte, až se vytvoří služba aplikace.
1. Když dostanete oznámení **o úspěšném nasazení,** vyberte **Přejít na prostředek** a přejděte na nově vytvořenou službu aplikace.
1. Po vytvoření webu ho najděte na **řídicím panelu** a vyberte ho, abyste otevřeli obrazovku **Přehled** služby aplikace.
1. Na kartě **Přehled** služby aplikace stáhněte profil publikování tak, že vyberete odkaz **Získat profil publikování** a uložte ho. Můžete použít jiné mechanismy nasazení, jako je například nasazení ze správy zdrojového kódu.
1. Přepněte do sady Visual Studio a potom:
   1. Přejděte na projekt **dotnet-web-daemon-v2.** 
   1. Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a pak vyberte **Publikovat**.
   1. Na dolním panelu vyberte **Importovat profil** a importujte profil publikování, který jste stáhli dříve.
1. Vyberte **Konfigurovat**.
1. Na kartě **Připojení** aktualizujte cílovou adresu URL tak, aby používá "https". Použijte například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Vyberte **další**.
1. Na kartě **Nastavení** zkontrolujte, zda není zaškrtnuto **políčko Povolit organizační ověřování.**  
1. Vyberte **Uložit**. Na hlavní obrazovce vyberte **Publikovat.**

Visual Studio publikuje projekt a automaticky otevře prohlížeč na adresu URL projektu. Pokud se zobrazí výchozí webová stránka projektu, publikace byla úspěšná.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualizace registrace aplikace klienta Azure AD pro dotnet-web-daemon-v2

1. Vraťte se na portál [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte službu **Azure Active Directory** a pak vyberte Registrace **aplikací**.
1. Vyberte aplikaci **dotnet-web-daemon-v2.**
1. Na stránce **Ověřování** pro vaši aplikaci aktualizujte pole **adresy URL odhlášení** s adresou služby. Použijte například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. V nabídce **Branding** aktualizujte **adresu URL domovské stránky** na adresu služby. Použijte například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Konfiguraci uložte.
1. Přidejte stejnou adresu URL do seznamu hodnot nabídky**Uri přesměrování** **ověřování.** >  Pokud máte více adres URL přesměrování, ujistěte se, že existuje nová položka, která používá identifikátor URI služby aplikace pro každou adresu URL přesměrování.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Když už nepotřebujete, odstraňte objekt aplikace, který jste vytvořili v kroku [Registrace aplikace.](#register-your-application)  Chcete-li aplikaci odebrat, postupujte podle pokynů v části [Odebrání aplikace vytvořené vámi nebo vaší organizací](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Podpora

Pomocí [přetečení zásobníku](http://stackoverflow.com/questions/tagged/msal) získáte podporu od komunity.
Nejprve se zeptejte na přetečení zásobníku a projděte si existující problémy, abyste zjistili, zda vás někdo předtím nepoložil.
Ujistěte se, že vaše otázky nebo komentáře jsou označeny "adal", "msal" a "dotnet".

Pokud najdete chybu v ukázce, prosím, zvýšit problém na [GitHub problémy](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Pokud najdete chybu v MSAL.NET, prosím, zvýšit problém [na MSAL.NET GitHub problémy](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Chcete-li poskytnout doporučení, přejděte na [stránku Hlas uživatele](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Další kroky
Další informace o různých [tocích ověřování a scénářích aplikací,](authentication-flows-app-scenarios.md) které podporuje platforma identit Microsoftu.

Další informace naleznete v následující koncepční dokumentaci:

- [Nájem ve službě Azure Active Directory](single-and-multi-tenant-apps.md)
- [Prostředí vyjádření souhlasu s aplikací Azure AD](application-consent-experience.md)
- [Přihlášení libovolného uživatele služby Azure Active Directory pomocí vzoru víceklientské aplikace](howto-convert-app-to-be-multi-tenant.md)
- [Principy souhlasu uživatelů a správců](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Instanční objekty aplikací a služeb v Azure Active Directory](app-objects-and-service-principals.md)
- [Úvodní příručka: Registrace aplikace s platformou microsoftu pro identity](quickstart-register-app.md)
- [Rychlý start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
- [Získání tokenu pro aplikaci s toky pověření klienta](msal-client-applications.md)

Jednodušší víceklientské konzoly daemon aplikace naleznete v tématu [.NET Core daemon quickstart](quickstart-v2-netcore-daemon.md).
