---
title: Práce s sadou SDK back-endového serveru .NET
description: Zjistěte, jak pracovat s back-endovým serverem .NET SDK pro mobilní aplikace služby Azure App Service.
keywords: app service, azure app service, mobilní aplikace, mobilní služba, škálovatelné, škálovatelné, nasazení aplikací, nasazení aplikací Azure
author: conceptdev
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: cafb0a7e2bf0fbce82448236a2da98079144121e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250137"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Toto téma ukazuje, jak používat back-endový server .NET SDK v klíčových scénářích Azure App Service Mobile Apps. Sada Azure Mobile Apps SDK vám pomůže pracovat s mobilními klienty z vaší ASP.NET aplikace.

> [!TIP]
> [Sada SDK serveru .NET pro mobilní aplikace Azure][2] je open source na GitHubu. Úložiště obsahuje veškerý zdrojový kód včetně celé sady testů jednotek sady SDK serveru a některých ukázkových projektů.
>
>

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentace pro server SDK se nachází zde: [Azure Mobile Apps .NET Reference][1].

## <a name="how-to-create-a-net-mobile-app-backend"></a><a name="create-app"></a>Postup: Vytvoření back-endu mobilní aplikace .NET
Pokud spouštíte nový projekt, můžete vytvořit aplikaci služby App Service pomocí [portálu Azure] nebo Visual Studia. Aplikaci App Service můžete spustit místně nebo publikovat projekt do mobilní aplikace app service na základě cloudu.

Pokud přidáváte mobilní funkce do existujícího projektu, přečtěte si část [Stažení a inicializace sady SDK.](#install-sdk)

### <a name="create-a-net-backend-using-the-azure-portal"></a>Vytvoření back-endu .NET pomocí portálu Azure
Chcete-li vytvořit mobilní back-end služby App Service, postupujte podle [kurzu rychlého startu][3] nebo postupujte takto:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpět v okně *Začínáme* v části **Vytvořit rozhraní API tabulky**zvolte **C#** jako **jazyk back-endu**. Klepněte na tlačítko **Stáhnout**, extrahujte komprimované soubory projektu do místního počítače a otevřete řešení v sadě Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Vytvoření back-endu .NET pomocí Visual Studia 2017

Nainstalujte úlohu Azure prostřednictvím Instalační služby Visual Studia a publikujte se do projektu Azure Mobile Apps z Visual Studia. Po instalaci sady SDK vytvořte ASP.NET aplikaci pomocí následujících kroků:

1. Otevřete dialogové okno **Nový projekt** **New** > (z **nového projektu** > **souboru...**).
2. Rozbalte **visual c#** a vyberte **web**.
3. Vyberte **ASP.NET webovou aplikaci (.NET Framework).**
4. Vyplňte název projektu. Pak klikněte na **OK**.
5. Ze seznamu šablon vyberte **Mobilní aplikace Azure.**
6. Chcete-li vytvořit řešení, klepněte na tlačítko **OK.**
7. Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a zvolte **Publikovat...**, pak zvolte **App Service** jako cíl publikování.
8. Podle pokynů k ověření a zvolte novou nebo existující službu Azure App Service, kterou chcete publikovat.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Vytvoření back-endu .NET pomocí Visual Studia 2015

Nainstalujte [azure sdk pro .NET][4] (verze 2.9.0 nebo novější) k vytvoření projektu Azure Mobile Apps ve Visual Studiu. Po instalaci sady SDK vytvořte ASP.NET aplikaci pomocí následujících kroků:

1. Otevřete dialogové okno **Nový projekt** **New** > (z **nového projektu** > **souboru...**).
2. Rozbalte **položku** > **Visual C#** a vyberte možnost **Web**.
3. Vyberte **Webová aplikace ASP.NET**.
4. Vyplňte název projektu. Pak klikněte na **OK**.
5. V *části ASP.NET šablon 4.5.2*vyberte Možnost Mobilní aplikace **Azure**. Zkontrolujte **hostitele v cloudu** a vytvořte mobilní back-end v cloudu, do kterého můžete publikovat tento projekt.
6. Klikněte na tlačítko **OK**.

## <a name="how-to-download-and-initialize-the-sdk"></a><a name="install-sdk"></a>Postup: Stažení a inicializaci sady SDK
Sada SDK je k dispozici na [NuGet.org]. Tento balíček obsahuje základní funkce potřebné k zahájení používání sady SDK. Chcete-li inicializovat sadu SDK, je třeba provést akce s objektem **HttpConfiguration.**

### <a name="install-the-sdk"></a>Instalace sady SDK
Chcete-li nainstalovat sadu SDK, klepněte pravým tlačítkem myši na serverový projekt ve visual tat, vyberte **spravovat balíčky NuGet**, vyhledejte balíček [Microsoft.Azure.Mobile.Server] a klikněte na **Nainstalovat**.

### <a name="initialize-the-server-project"></a><a name="server-project-setup"></a>Inicializovat projekt serveru
Projekt back-endového serveru .NET je inicializován podobně jako ostatní ASP.NET projekty zahrnutím spouštěcí třídy OWIN. Ujistěte se, že jste `Microsoft.Owin.Host.SystemWeb`odkazovali na balíček NuGet . Chcete-li přidat tuto třídu v sadě Visual Studio, klepněte pravým tlačítkem myši na serverový projekt a **vyberte** >
přidat**novou položku**, potom na **položku Web** > **General** > **OWIN Startup Class**.  Třída je generována s následujícím atributem:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Při `Configuration()` metodě spouštěcí třídy OWIN nakonfigurujte prostředí Azure Mobile Apps pomocí objektu **HttpConfiguration.**
Následující příklad inicializuje projekt serveru bez přidaných funkcí:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Chcete-li povolit jednotlivé funkce, musíte volat metody rozšíření na **objektMobileAppConfiguration** před **voláním ApplyTo**. Například následující kód přidá výchozí trasy do všech řadičů `[MobileAppController]` rozhraní API, které mají atribut během inicializace:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Rychlý start serveru z portálu Azure volá **UseDefaultConfiguration()**. To odpovídá následujícímu nastavení:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Použité metody rozšíření jsou:

* `AddMobileAppHomeController()`poskytuje výchozí domovskou stránku mobilních aplikací Azure.
* `MapApiControllers()`poskytuje vlastní funkce rozhraní API pro `[MobileAppController]` řadiče WebAPI dekorované atributem.
* `AddTables()`poskytuje mapování koncových `/tables` bodů na řadiče tabulky.
* `AddTablesWithEntityFramework()`je zkratka pro mapování `/tables` koncových bodů pomocí řadičů založených na rozhraní entity Framework.
* `AddPushNotifications()`poskytuje jednoduchou metodu registrace zařízení pro centra oznámení.
* `MapLegacyCrossDomainController()`poskytuje standardní hlavičky CORS pro místní vývoj.

### <a name="sdk-extensions"></a>Rozšíření sady SDK
Následující balíčky rozšíření založené na NuGet poskytují různé mobilní funkce, které lze použít v aplikaci. Rozšíření během inicializace povolíte pomocí objektu **MobileAppConfiguration.**

* [Microsoft.Azure.Mobile.Server.Quickstart] Podporuje základní nastavení mobilních aplikací. Přidáno do konfigurace voláním metody rozšíření **UseDefaultConfiguration** během inicializace. Toto rozšíření zahrnuje následující rozšíření: Oznámení, Ověřování, Entita, Tabulky, Mezi doménami a Balíčky domů. Tento balíček používá rychlý start mobilních aplikací dostupný na webu Azure Portal.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementuje výchozí *tuto mobilní aplikaci je v provozu pro* kořenový web. Přidejte do konfigurace voláním metody rozšíření **AddMobileAppHomeController.**
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) obsahuje třídy pro práci s daty a nastaví datový kanál. Přidejte do konfigurace voláním metody rozšíření **AddTables.**
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) umožňuje rozhraní Entity Framework pro přístup k datům v databázi SQL. Přidejte do konfigurace voláním metody rozšíření **AddTablesWithEntityFramework.**
* [Microsoft.Azure.Mobile.Server.Authentication] Umožňuje ověřování a nastavuje middleware OWIN používané k ověřování tokenů. Přidejte do konfigurace voláním **AddAppServiceAuthentication** a **IAppBuilder**. Metody rozšíření **UseAppServiceAuthentication.**
* [Microsoft.Azure.Mobile.Server.Notifications] Povolí nabízená oznámení a definuje koncový bod pro registraci nabízených oznámení. Přidejte do konfigurace voláním metody rozšíření **AddPushNotifications.**
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Vytvoří řadič, který slouží data starším webovým prohlížečům z mobilní aplikace. Přidejte do konfigurace voláním metody rozšíření **MapLegacyCrossDomainController.**
* [Microsoft.Azure.Mobile.Server.Login] Poskytuje metodu AppServiceLoginHandler.CreateToken(), což je statická metoda používaná při scénářích vlastního ověřování.

## <a name="how-to-publish-the-server-project"></a><a name="publish-server-project"></a>Postup: Publikování projektu serveru
Tato část ukazuje, jak publikovat back-endový projekt .NET z Visual Studia. Můžete také nasadit back-endový projekt pomocí [Gitu](../app-service/deploy-local-git.md) nebo některé z dalších metod, které jsou k dispozici.

1. V sadě Visual Studio znovu sestavit projekt obnovit balíčky NuGet.
2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a klikněte na **Publikovat**. Při prvním publikování je třeba definovat profil publikování. Pokud již máte definovaný profil, můžete ho vybrat a klepnout na **publikovat**.
3. Pokud budete vyzváni k výběru cíle publikování, klikněte na **Microsoft Azure App Service** > **Next**a pak (v případě potřeby) přihlaste se pomocí přihlašovacích údajů azure.
   Visual Studio ke stažení a bezpečně ukládá nastavení publikování přímo z Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Zvolte **předplatné**, vyberte **Typ prostředku** ze **zobrazení**, rozbalte **mobilní aplikaci**a klikněte na back-end mobilní aplikace a klikněte na **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Ověřte informace o profilu publikování a klepněte na tlačítko **Publikovat**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Po úspěšném publikování back-endu mobilní aplikace se zobrazí vstupní stránka označující úspěch.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="how-to-define-a-table-controller"></a><a name="define-table-controller"></a>Postup: Definování řadiče tabulky
Definujte řadič tabulky, který zpřístupní tabulku SQL mobilním klientům.  Konfigurace řadiče tabulky vyžaduje tři kroky:

1. Vytvořte třídu objektu přenosu dat (DTO).
2. Nakonfigurujte odkaz na tabulku ve třídě Mobile DbContext.
3. Vytvořte řadič tabulky.

A Data Transfer Object (DTO) je prostý objekt `EntityData`Jazyka C#, který dědí z .  Například:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO se používá k definování tabulky v databázi SQL.  Chcete-li vytvořit položku `DbSet<>` databáze, přidejte vlastnost dbcontext, kterou používáte.  Ve výchozí šabloně projektu pro Azure Mobile `Models\MobileServiceContext.cs`Apps se nazývá DbContext :

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Pokud máte nainstalovanou sadku Azure SDK, můžete teď vytvořit řadič tabulky šablon takto:

1. Klikněte pravým tlačítkem myši na složku Řadiče a vyberte **přidat** > **řadič...**.
2. Vyberte možnost **Řadič tabulky mobilních aplikací Azure** a klikněte na **Přidat**.
3. V dialogovém okně **Přidat řadič:**
   * V rozevíracím seznamu **Třídy model** vyberte nové DTO.
   * V rozevíracím souboru **DbContext** vyberte třídu DbContext mobilní služby.
   * Název řadiče je vytvořen pro vás.
4. Klikněte na **Přidat**.

Projekt serveru rychlého startu obsahuje příklad jednoduchého **ovladače TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a><a name="adjust-pagesize"></a>Postup: Nastavení velikosti stránkování tabulky
Ve výchozím nastavení azure mobile apps vrátí 50 záznamů na požadavek.  Stránkování zajišťuje, že klient není svázat jejich vlákno uživatelského rozhraní ani server příliš dlouho, zajištění dobré uživatelské zkušenosti. Chcete-li změnit velikost stránkování tabulky, zvětšete na straně serveru "povolenou velikost dotazu" a `EnableQuery` velikost stránky na straně klienta Velikost stránky na straně serveru "Povolená velikost dotazu" se upraví pomocí atributu:

    [EnableQuery(PageSize = 500)]

Ujistěte se, že PageSize je stejná nebo větší než velikost požadovaná klientem.  Podrobnosti o změně velikosti stránky klienta naleznete v dokumentaci k konkrétnímu klientovi HOWTO.

## <a name="how-to-define-a-custom-api-controller"></a>Postup: Definování vlastního řadiče rozhraní API
Vlastní řadič rozhraní API poskytuje nejzákladnější funkce back-endu mobilní aplikace tím, že vystaví koncový bod. Pomocí atributu [MobileAppController] můžete zaregistrovat řadič rozhraní API specifický pro mobilní zařízení. Atribut `MobileAppController` zaregistruje trasu, nastaví serializátor Mobile Apps JSON a zapne [kontrolu verzí klienta](app-service-mobile-client-and-server-versioning.md).

1. V sadě Visual Studio klepněte pravým tlačítkem myši na složku Řadiče, potom klepněte na příkaz **Přidat** > **řadič**, vyberte možnost Webové rozhraní API 2 **&mdash;Řadič prázdný** a klepněte na příkaz **Přidat**.
2. Zadejte **název řadiče**, například `CustomController`, a klepněte na tlačítko **Přidat**.
3. Do nového souboru třídy řadiče přidejte následující příkaz using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Použijte atribut **[MobileAppController]** pro definici třídy řadiče rozhraní API, jako v následujícím příkladu:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. V souboru App_Start/Startup.MobileApp.cs přidejte volání do metody rozšíření **MapApiControllers,** jako v následujícím příkladu:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Můžete také použít `UseDefaultConfiguration()` metodu `MapApiControllers()`rozšíření namísto . Ke všem řadičům, který nemá použít **atribut MobileAppControllerAttribute,** mohou klienti stále přistupovat, ale klienti jej nemusí správně využívat pomocí sady SDK klienta mobilní aplikace.

## <a name="how-to-work-with-authentication"></a>Postup: Práce s ověřováním
Azure Mobile Apps používá ověřování / autorizaci služby App Service k zabezpečení vašeho mobilního back-endu.  Tato část ukazuje, jak provádět následující úlohy související s ověřováním v projektu back-endového serveru .NET:

* [Postup: Přidání ověřování do projektu serveru](#add-auth)
* [Postup: Použití vlastního ověřování pro vaši aplikaci](#custom-auth)
* [Postup: Načtení ověřených informací o uživateli](#user-info)
* [Postup: Omezení přístupu k datům pro oprávněné uživatele](#authorize)

### <a name="how-to-add-authentication-to-a-server-project"></a><a name="add-auth"></a>Postup: Přidání ověřování do projektu serveru
Ověřování můžete přidat do projektu serveru rozšířením objektu **MobileAppConfiguration** a konfigurací middlewaru OWIN. Při instalaci balíčku [Microsoft.Azure.Mobile.Server.Quickstart] a volání metody rozšíření **UseDefaultConfiguration** můžete přeskočit ke kroku 3.

1. Ve Visual Studiu nainstalujte balíček [Microsoft.Azure.Mobile.Server.Authentication.]
2. Do souboru projektu Startup.cs přidejte na začátek metody **Configuration** následující řádek kódu:

        app.UseAppServiceAuthentication(config);

    Tato komponenta middleware OWIN ověřuje tokeny vydané přidruženou bránou služby App Service.
3. Přidejte `[Authorize]` atribut do libovolného řadiče nebo metody, která vyžaduje ověření.

Informace o tom, jak ověřovat klienty v back-endu mobilních aplikací, najdete v [tématu Přidání ověřování do aplikace](app-service-mobile-ios-get-started-users.md).

### <a name="how-to-use-custom-authentication-for-your-application"></a><a name="custom-auth"></a>Postup: Použití vlastního ověřování pro vaši aplikaci
> [!IMPORTANT]
> Chcete-li povolit vlastní ověřování, musíte nejprve povolit ověřování pomocí služby App Service bez výběru zprostředkovatele pro vaši službu App Service na webu Azure Portal. To umožní proměnné prostředí WEBSITE_AUTH_SIGNING_KEY při hostování.
> 
> 
> Pokud nechcete používat jednoho z poskytovatelů ověřování/autorizace služby App Service, můžete implementovat vlastní přihlašovací systém. Nainstalujte balíček [Microsoft.Azure.Mobile.Server.Login,] který vám pomůže s generováním ověřovacích tokenů.  Zadejte vlastní kód pro ověřování pověření uživatele. Můžete například zkontrolovat proti solené a hash hesla v databázi. V níže uvedeném `isValidAssertion()` příkladu je metoda (definovaná jinde) zodpovědná za tyto kontroly.

Vlastní ověřování je vystavena vytvořením ApiController a vystavení `register` a `login` akce. Klient by měl použít vlastní uživatelské rozhraní ke shromažďování informací od uživatele.  Informace jsou pak odeslány do rozhraní API se standardním voláním HTTP POST. Jakmile server ověří kontrolní výraz, token je `AppServiceLoginHandler.CreateToken()` vydán pomocí metody.  ApiController **by neměl** `[MobileAppController]` používat atribut.

Příklad `login` akce:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

V předchozím příkladu LoginResult a LoginResultUser jsou serializovatelné objekty, které vystavují požadované vlastnosti. Klient očekává, že odpovědi na přihlášení budou vráceny jako objekty JSON formuláře:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Metoda `AppServiceLoginHandler.CreateToken()` zahrnuje *cílovou skupinu* a parametr *vystavittele.* Oba tyto parametry jsou nastaveny na adresu URL kořenového adresáře aplikace pomocí schématu HTTPS. Podobně byste měli nastavit *secretKey* hodnotu podpisového klíče aplikace. Nedistribuujte podpisový klíč v klientovi, protože jej lze použít k mátovým klíčům a zosobnění uživatelů. Podpisový klíč můžete získat, když je hostován ve službě App Service odkazem na proměnnou prostředí prostředí *PODPISU\_\_\_WEBU AUTH.* V případě potřeby v kontextu místního ladění postupujte podle pokynů v části [Místní ladění s ověřováním](#local-debug) k načtení klíče a uložení jako nastavení aplikace.

Vydaný token může obsahovat i další nároky a datum vypršení platnosti.  Minimálně vydaný token musí obsahovat předmět (**sub**) deklarace.

Standardní metodu klienta `loginAsync()` můžete podporovat přetížením ověřovací trasy.  Pokud klient `client.loginAsync('custom');` volá, aby se přihlásil, musí být `/.auth/login/custom`vaše trasa .  Trasu vlastního ověřovacího řadiče `MapHttpRoute()`můžete nastavit pomocí :

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Pomocí `loginAsync()` přístupu zajišťuje, že ověřovací token je připojen ke každému následnému volání služby.
>
>

### <a name="how-to-retrieve-authenticated-user-information"></a><a name="user-info"></a>Postup: Načtení ověřených informací o uživateli
Když je uživatel ověřen službou App Service, můžete získat přístup k přiřazenému ID uživatele a dalším informacím v back-endovém kódu .NET. Informace o uživateli lze použít k rozhodování o autorizaci v back-endu. Následující kód získá ID uživatele přidružené k požadavku:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID je odvozen z ID uživatele specifické pro zprostředkovatele a je statické pro daného uživatele a poskytovatele přihlášení.  SID je null pro neplatné ověřovací tokeny.

Služba App Service také umožňuje požadovat konkrétní nároky od vašeho poskytovatele přihlášení. Každý zprostředkovatel identity může poskytnout další informace pomocí sady SDK zprostředkovatele identity.  Například můžete použít Facebook Graph API pro přátele informace.  Můžete zadat deklarace identity, které jsou požadovány v okně zprostředkovatele na webu Azure Portal. Některé deklarace vyžadují další konfiguraci s poskytovatelem identity.

Následující kód volá metodu rozšíření **GetAppServiceIdentityAsync,** aby získal přihlašovací údaje, které zahrnují přístupový token potřebný k vyžádání rozhraní API pro Facebook Graph:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Přidejte příkaz `System.Security.Principal` using pro poskytnutí metody rozšíření **GetAppServiceIdentityAsync.**

### <a name="how-to-restrict-data-access-for-authorized-users"></a><a name="authorize"></a>Postup: Omezení přístupu k datům pro oprávněné uživatele
V předchozí části jsme ukázali, jak načíst ID uživatele ověřeného uživatele. Na základě této hodnoty můžete omezit přístup k datům a dalším prostředkům. Například přidání sloupce userId do tabulek a filtrování výsledků dotazu podle ID uživatele je jednoduchý způsob, jak omezit vrácená data pouze oprávněným uživatelům. Následující kód vrátí řádky dat pouze v případě, že sid odpovídá hodnotě ve sloupci UserId v tabulce TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Metoda `Query()` `IQueryable` vrátí, které mohou být manipulovány LINQ pro zpracování filtrování.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Postup: Přidání nabízených oznámení do projektu serveru
Přidejte nabízená oznámení do projektu serveru rozšířením objektu **MobileAppConfiguration** a vytvořením klienta Centra oznámení.

1. V sadě Visual Studio klepněte pravým tlačítkem myši na `Microsoft.Azure.Mobile.Server.Notifications`projekt serveru a klepněte na příkaz Spravovat **balíčky NuGet**, vyhledejte položku a potom klepněte na příkaz **Nainstalovat**.
2. Tento krok opakujte `Microsoft.Azure.NotificationHubs` a nainstalujte balíček, který obsahuje klientskou knihovnu Centra oznámení.
3. V App_Start/Startup.MobileApp.cs a přidejte volání do metody rozšíření **AddPushNotifications()** během inicializace:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Přidejte následující kód, který vytvoří klienta Centra oznámení:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Nyní můžete použít klienta Centra oznámení k odesílání nabízených oznámení do registrovaných zařízení. Další informace najdete v tématu [Přidání nabízených oznámení do vaší aplikace](app-service-mobile-ios-get-started-push.md). Další informace o centrech oznámení najdete v tématu [Přehled center oznámení](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="how-to-enable-targeted-push-using-tags"></a><a name="tags"></a>Postup: Povolení cíleného nabízení pomocí značek
Centra oznámení umožňují odesílat cílená oznámení konkrétním registracím pomocí značek. Několik značek se vytváří automaticky:

* ID instalace identifikuje konkrétní zařízení.
* ID uživatele na základě ověřeného sid identifikuje konkrétního uživatele.

ID instalace lze přistupovat z **vlastnosti installationId** na **MobileServiceClient**.  Následující příklad ukazuje, jak pomocí ID instalace přidat značku do konkrétní instalace v centru oznámení:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Všechny značky dodané klientem během registrace nabízených oznámení jsou při vytváření instalace back-endem ignorovány. Chcete-li klientovi povolit přidání značek do instalace, musíte vytvořit vlastní rozhraní API, které přidá značky pomocí předchozího vzoru.

Viz [Klient-přidané nabízené oznámení značky][5] v App Service Mobilní aplikace dokončil úvodní ukázku pro příklad.

## <a name="how-to-send-push-notifications-to-an-authenticated-user"></a><a name="push-user"></a>Postup: Odeslání nabízených oznámení ověřenému uživateli
Když se ověřený uživatel zaregistruje pro nabízená oznámení, značka ID uživatele se automaticky přidá do registrace. Pomocí této značky můžete odesílat nabízená oznámení do všech zařízení registrovaných touto osobou. Následující kód získá SID uživatele, který žádost a odešle šablony push oznámení pro každou registraci zařízení pro tuto osobu:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Při registraci nabízených oznámení z ověřeného klienta se ujistěte, že ověřování je dokončeno před pokusem o registraci. Další informace najdete v [tématu Nabízení uživatelům][6] v mobilních aplikacích služby App Service dokončila ukázku rychlého startu pro back-end .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Postup: Ladění a řešení potíží se sadou SDK serveru .NET Server
Služba Azure App Service poskytuje několik technik ladění a řešení potíží pro ASP.NET aplikace:

* [Monitorování služby aplikace Azure](../app-service/web-sites-monitor.md)
* [Povolení diagnostického protokolování ve službě Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Poradce při potížích s aplikační službou Azure ve Visual Studiu](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>protokolování
Můžete zapisovat do protokolů diagnostiky služby App Service pomocí standardníASP.NET zápis trasování. Před zápisem do protokolů je nutné povolit diagnostiku v back-endu mobilní aplikace.

Povolení diagnostiky a zápis do protokolů:

1. Postupujte podle pokynů v [části Povolit protokolování aplikací (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Do souboru kódu přidejte následující příkaz using:

        using System.Web.Http.Tracing;
3. Vytvořte zapisovač trasování pro zápis z back-endu .NET do diagnostických protokolů následujícím způsobem:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Znovu publikujte serverový projekt a získejte přístup k back-endu mobilní aplikace, abyste s protokolováním provedli cestu kódu.
5. Stáhněte a vyhodnoťte protokoly, jak je popsáno v [souborech protokolu aplikace Access](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debugging-with-authentication"></a><a name="local-debug"></a>Místní ladění s ověřováním
Můžete spustit aplikaci místně otestovat změny před jejich publikováním do cloudu. U většiny backendů mobilních aplikací Azure stiskněte *klávesu F5* ve Visual Studiu. Existují však některé další důležité informace při použití ověřování.

Musíte mít cloudovou mobilní aplikaci s nakonfigurovanou ověřováním a autorizací služby App Service a váš klient musí mít koncový bod cloudu určený jako alternativní hostitel přihlášení. Konkrétní požadované kroky naleznete v dokumentaci k klientské platformě.

Ujistěte se, že váš mobilní back-end má nainstalovanou [microsoft.azure.mobile.server.authentication.] Potom ve vaší aplikaci o win spuštění třídy, přidejte následující, po `MobileAppConfiguration` byla použita na vaše `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

V předchozím příkladu byste měli nakonfigurovat nastavení aplikace *authAudience* a *authIssuer* v rámci souboru Web.config tak, aby každá byla adresou URL kořenového adresáře aplikace pomocí schématu HTTPS. Podobně byste měli nastavit *authSigningKey* jako hodnotu podpisového klíče vaší aplikace.
Získání podpisového klíče:

1. Přechod na aplikaci v rámci [portálu Azure]
2. Klepněte na **položky Nástroje**, **Kudu**, **Přejít**.
3. V lokalitě Správa Kudu klepněte na **položku Životní prostředí**.
4. Najít hodnotu pro *webové\_\_stránky\_AUTH podpisový klíč*.

Použijte podpisový klíč pro parametr *authSigningKey* v konfiguraci místní aplikace.  Váš mobilní back-end je nyní vybaven k ověření tokenů při spuštění místně, které klient získá token z cloudového koncového bodu.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portál Azure]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Rychlý start]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Oznámení Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
