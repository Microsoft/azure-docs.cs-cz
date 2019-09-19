---
title: Jak pracovat se sadou SDK back-end serveru .NET pro Mobile Apps | Microsoft Docs
description: Naučte se pracovat se sadou SDK back-end serveru .NET pro Azure App Service Mobile Apps.
keywords: App Service, Azure App Service, Mobile App, Mobile Service, škálování, škálovatelné, nasazování aplikací, nasazení aplikací Azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: d277786fd08e1448b3d5ccf4fd45055fe069e4c0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097766"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Práce se serverovou sadou .NET back-end SDK v prostředí Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

V tomto tématu se dozvíte, jak používat sadu SDK back-end serveru .NET v klíčových Azure App Servicech Mobile Appsch scénářích. Sada Azure Mobile Apps SDK vám pomůže pracovat s mobilními klienty z aplikace ASP.NET.

> [!TIP]
> [Sada SDK serveru .NET pro Azure Mobile Apps][2] je open source na GitHubu. Úložiště obsahuje všechen zdrojový kód včetně celé sady serverových testů jednotek sady SDK a některých ukázkových projektů.
>
>

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentace pro sadu SDK serveru je umístěna zde: [Reference k Azure Mobile Apps .NET][1].

## <a name="create-app"></a>Jak: Vytvoření back-endu mobilní aplikace .NET
Pokud spouštíte nový projekt, můžete vytvořit aplikaci App Service pomocí [Azure Portal] nebo sady Visual Studio. Můžete spustit aplikaci App Service lokálně nebo projekt publikovat do cloudové App Service mobilní aplikace.

Pokud přidáváte mobilní možnosti do existujícího projektu, přečtěte si část [Stažení a inicializace sady SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Vytvoření back-endu .NET pomocí Azure Portal
Pokud chcete vytvořit App Service mobilní back-end, postupujte podle [kurzu rychlý Start][3] nebo proveďte tyto kroky:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpátky v okně Začínáme v části **Vytvoření rozhraní API tabulky**vyberte **C#** jako **jazyk back-end**. Klikněte na **Stáhnout**, rozbalte komprimované soubory projektu do místního počítače a otevřete řešení v aplikaci Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Vytvoření back-endu .NET pomocí sady Visual Studio 2017

Nainstalujte úlohu Azure pomocí Instalační program pro Visual Studio pro publikování do Azure Mobile Apps projektu ze sady Visual Studio. Po instalaci sady SDK vytvořte aplikaci ASP.NET pomocí následujících kroků:

1. Otevřete dialogové okno **Nový projekt** (ze **souboru** > **Nový** > **projekt...** ).
2. Rozbalte **vizuál C#**  a vyberte **Web**.
3. Vyberte **Webová aplikace v ASP.NET (.NET Framework)** .
4. Zadejte název projektu. Pak klikněte na **OK**.
5. V seznamu šablon vyberte **mobilní aplikace Azure** .
6. Kliknutím na tlačítko **OK** vytvořte řešení.
7. Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a zvolte **publikovat...** a pak jako cíl publikování zvolte **App Service** .
8. Postupujte podle výzev k ověření a vyberte novou nebo existující Azure App Service pro publikování.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Vytvoření back-endu .NET pomocí sady Visual Studio 2015

Pokud chcete vytvořit projekt Azure Mobile Apps v sadě Visual Studio, nainstalujte [sadu Azure SDK pro .NET][4] (verze 2.9.0 nebo novější). Po instalaci sady SDK vytvořte aplikaci ASP.NET pomocí následujících kroků:

1. Otevřete dialogové okno **Nový projekt** (ze **souboru** > **Nový** > **projekt...** ).
2. Rozbalte položku **šablony** > **vizuál C#** a vyberte možnost **Web**.
3. Vyberte **Webová aplikace ASP.NET**.
4. Zadejte název projektu. Pak klikněte na **OK**.
5. V části *šablony ASP.NET 4.5.2*vyberte **mobilní aplikace Azure**. Pokud chcete vytvořit mobilní back-end v cloudu, do kterého můžete tento projekt publikovat, podívejte se do **části hostitel v cloudu** .
6. Klikněte na **OK**.

## <a name="install-sdk"></a>Jak: Stáhnout a inicializovat sadu SDK
Sada SDK je dostupná na [NuGet.org]. Tento balíček obsahuje základní funkce potřebné k tomu, abyste mohli začít používat sadu SDK. Chcete-li inicializovat sadu SDK, je třeba provést akce s objektem **HttpConfiguration** .

### <a name="install-the-sdk"></a>Instalace sady SDK
Pokud chcete nainstalovat sadu SDK, klikněte pravým tlačítkem na projekt serveru v sadě Visual Studio, vyberte **Spravovat balíčky NuGet**, vyhledejte balíček [Microsoft.Azure.Mobile.Server] a pak klikněte na **nainstalovat**.

### <a name="server-project-setup"></a>Inicializovat serverový projekt
Projekt back-end serveru .NET je inicializován podobně jako jiné projekty ASP.NET, včetně spouštěcí třídy OWIN. Ujistěte se, že máte odkaz na balíček `Microsoft.Owin.Host.SystemWeb`NuGet. Postup přidání této třídy v sadě Visual Studio, klikněte pravým tlačítkem na svůj serverový projekt a vyberte **přidat** >
**nová položka**, pak **webové** > **Obecné** > **třída OWIN Startup**.  Třída je vygenerována s následujícím atributem:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

V metodě třídy Owin Startup použijte objekt HttpConfiguration ke konfiguraci prostředí Azure Mobile Apps. `Configuration()`
Následující příklad inicializuje serverový projekt bez přidaných funkcí:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Chcete-li povolit jednotlivé funkce, je nutné volat metody rozšíření objektu **MobileAppConfiguration** před voláním metody **ApplyTo**. Například následující kód přidá výchozí trasy do všech řadičů rozhraní API, které mají atribut `[MobileAppController]` během inicializace:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Rychlý Start serveru z Azure Portal volá **UseDefaultConfiguration ()** . To odpovídá následujícímu nastavení:

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

Používané metody rozšíření:

* `AddMobileAppHomeController()`poskytuje výchozí domovskou stránku Azure Mobile Apps.
* `MapApiControllers()`poskytuje funkce vlastního rozhraní API pro řadiče WebApi dekorované `[MobileAppController]` s atributem.
* `AddTables()`poskytuje mapování `/tables` koncových bodů na řadiče tabulky.
* `AddTablesWithEntityFramework()`je krátká ruka pro mapování `/tables` koncových bodů pomocí entity Frameworkch řadičů na bázi.
* `AddPushNotifications()`poskytuje jednoduchou metodu registrace zařízení pro Notification Hubs.
* `MapLegacyCrossDomainController()`poskytuje standardní hlavičky CORS pro místní vývoj.

### <a name="sdk-extensions"></a>Rozšíření sady SDK
Následující balíčky rozšíření založené na NuGet poskytují různé mobilní funkce, které může používat vaše aplikace. Rozšíření můžete povolit během inicializace pomocí objektu **MobileAppConfiguration** .

* [Microsoft.Azure.Mobile.Server.Quickstart] podporuje základní instalaci Mobile Apps. Přidáno do konfigurace voláním metody rozšíření **UseDefaultConfiguration** během inicializace. Toto rozšíření zahrnuje následující rozšíření: Oznámení, ověřování, entita, tabulky, mezidoménové a domovské balíčky. Tento balíček používá rychlý Start Mobile Apps dostupný na Azure Portal.
* [Microsoft. Azure. Mobile. Server. Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementuje výchozí *tuto mobilní aplikaci* pro kořenovou stránku webu. Přidejte do konfigurace voláním metody rozšíření **AddMobileAppHomeController** .
* [Microsoft. Azure. Mobile. Server. Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) obsahuje třídy pro práci s daty a nastavení datového kanálu. Přidejte do konfigurace voláním metody rozšíření **AddTables** .
* [Microsoft. Azure. Mobile. Server. entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) umožňuje Entity Framework získat přístup k datům v SQL Database. Přidejte do konfigurace voláním metody rozšíření **AddTablesWithEntityFramework** .
* [Microsoft.Azure.Mobile.Server.Authentication] umožňuje ověřování a nastavuje Owin middleware, který slouží k ověření tokenů. Přidejte do konfigurace voláním **AddAppServiceAuthentication** a **IAppBuilder**. Metody rozšíření **UseAppServiceAuthentication** .
* [Microsoft.Azure.Mobile.Server.Notifications] umožňuje nabízená oznámení a definuje koncový bod registrace nabízených oznámení. Přidejte do konfigurace voláním metody rozšíření **AddPushNotifications** .
* [Microsoft. Azure. Mobile. Server. CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) vytvoří kontroler, který obsluhuje data pro starší webové prohlížeče z vaší mobilní aplikace. Přidejte do konfigurace voláním metody rozšíření **MapLegacyCrossDomainController** .
* [Microsoft.Azure.Mobile.Server.Login] poskytuje metodu AppServiceLoginHandler. okta (), která je statickou metodou použitou během vlastních scénářů ověřování.

## <a name="publish-server-project"></a>Jak: Publikování projektu serveru
V této části se dozvíte, jak publikovat projekt back-endu .NET ze sady Visual Studio. Back-end projekt můžete také nasadit pomocí [Gitu](../app-service/deploy-local-git.md) nebo kterékoli z dalších metod, které jsou zde k dispozici.

1. V aplikaci Visual Studio znovu sestavte projekt, aby se obnovily balíčky NuGet.
2. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt, klikněte na **publikovat**. Při prvním publikování musíte definovat profil publikování. Když už máte profil definovaný, můžete ho vybrat a kliknout na **publikovat**.
3. Pokud se zobrazí výzva k výběru cíle publikování, klikněte na **Microsoft Azure App Service** > **Další**a pak (Pokud je potřeba) Přihlaste se pomocí přihlašovacích údajů Azure.
   Visual Studio stahuje a bezpečně ukládá vaše nastavení publikování přímo z Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Zvolte své **předplatné**, vyberte **typ prostředku** z **zobrazení**, rozbalte **mobilní aplikaci**a klikněte na back-end mobilní aplikace a pak klikněte na **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Ověřte informace o profilu publikování a klikněte na **publikovat**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Po úspěšném publikování back-endu mobilní aplikace se zobrazí úvodní stránka s oznámením o úspěchu.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Jak: Definovat řadič tabulky
Definujte řadič tabulky k vystavení tabulky SQL pro mobilní klienty.  Konfigurace řadiče tabulky vyžaduje tři kroky:

1. Vytvořte třídu Přenos dat objektů (DTO).
2. Nakonfigurujte odkaz na tabulku v mobilní třídě DbContext.
3. Vytvořte řadič tabulky.

Objekt Přenos dat (DTO) je jednoduchý C# objekt, který dědí z. `EntityData`  Příklad:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO se používá k definování tabulky v rámci databáze SQL.  Chcete-li vytvořit položku databáze, přidejte `DbSet<>` vlastnost do DbContext, které používáte.  V výchozí šabloně projektu pro Azure Mobile Apps se volá `Models\MobileServiceContext.cs`DbContext:

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

Pokud máte nainstalovanou sadu Azure SDK, můžete teď vytvořit řadič tabulky šablon následujícím způsobem:

1. Klikněte pravým tlačítkem na složku řadiče a vyberte **Přidat** > **kontroler...** .
2. Vyberte možnost **řadič tabulky Azure Mobile Apps** a pak klikněte na **Přidat**.
3. V dialogovém okně **Přidat řadič** :
   * V rozevíracím seznamu **třída modelu** vyberte nové DTO.
   * V rozevíracím seznamu **DbContext** vyberte DbContext třídu mobilní služby.
   * Název kontroleru se vytvoří za vás.
4. Klikněte na **Přidat**.

Projekt serveru pro rychlý Start obsahuje příklad jednoduchého **TodoItemController**.

### <a name="adjust-pagesize"></a>Jak: Úprava velikosti stránkování tabulky
Ve výchozím nastavení Azure Mobile Apps vrátí 50 záznamů na požadavek.  Stránkování zajišťuje, že klient neodkazuje své vlákno uživatelského rozhraní ani server příliš dlouho a zajišťuje tak dobré prostředí pro uživatele. Chcete-li změnit velikost stránkování tabulky, zvyšte hodnotu povolené velikosti dotazu na straně serveru a velikost stránky na straně klienta s povolenou velikostí dotazu na straně serveru, která je upravena pomocí `EnableQuery` atributu:

    [EnableQuery(PageSize = 500)]

Zajistěte, aby byla hodnota PageSize stejná nebo větší než velikost požadovaná klientem.  Podrobnosti o změně velikosti stránky klienta najdete v dokumentaci konkrétního klienta.

## <a name="how-to-define-a-custom-api-controller"></a>Postup: Definování vlastního kontroleru rozhraní API
Vlastní kontroler rozhraní API poskytuje základní funkce back-endu mobilní aplikace tím, že vystavuje koncový bod. Pomocí atributu [MobileAppController] můžete zaregistrovat řadič API pro konkrétní mobilní zařízení. Atribut registruje trasu, nastaví Mobile Apps serializátor JSON a zapne [kontrolu verze klienta.](app-service-mobile-client-and-server-versioning.md) `MobileAppController`

1. V aplikaci Visual Studio klikněte pravým tlačítkem myši na složku řadiče a pak klikněte na **Přidat** > kontroler. Vyberte možnost **kontroler&mdash;webového rozhraní API 2** a klikněte na **Přidat**.
2. Zadejte **název kontroleru**, například `CustomController`a klikněte na **Přidat**.
3. V novém souboru třídy kontroleru přidejte následující příkaz using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Použijte atribut **[MobileAppController]** na definici třídy řadiče rozhraní API, jak je uvedeno v následujícím příkladu:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. V souboru App_Start/Startup. MobileApp. cs přidejte volání metody rozšíření **MapApiControllers** , jako v následujícím příkladu:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Můžete také použít `UseDefaultConfiguration()` metodu rozšíření `MapApiControllers()`místo. K jakémukoli řadiči, který nemá použité **MobileAppControllerAttribute** , se budou klienti pořád přihlašovat, ale nemusí je správně spotřebovat klienti pomocí žádné klientské sady SDK pro mobilní aplikace.

## <a name="how-to-work-with-authentication"></a>Postup: Práce s ověřováním
Azure Mobile Apps používá k zabezpečení mobilního back-endu App Service ověřování/autorizaci.  V této části se dozvíte, jak provádět následující úlohy související s ověřováním v projektu back-end serveru .NET:

* [Postupy: Přidání ověřování do projektu serveru](#add-auth)
* [Postupy: Použití vlastního ověřování pro vaši aplikaci](#custom-auth)
* [Postup: Načíst informace o ověřeném uživateli](#user-info)
* [Postup: Omezení přístupu k datům autorizovaných uživatelů](#authorize)

### <a name="add-auth"></a>Jak: Přidání ověřování do projektu serveru
Můžete přidat ověřování do projektu serveru rozšířením objektu **MobileAppConfiguration** a konfigurací middleware Owin. Když nainstalujete balíček [Microsoft.Azure.Mobile.Server.Quickstart] a zavoláte metodu rozšíření **UseDefaultConfiguration** , můžete přeskočit na krok 3.

1. V aplikaci Visual Studio nainstalujte balíček [Microsoft.Azure.Mobile.Server.Authentication] .
2. V souboru projektu Startup.cs přidejte na začátek metody **Konfigurace** následující řádek kódu:

        app.UseAppServiceAuthentication(config);

    Tato součást middleware OWIN ověřuje tokeny vydané přidruženou bránou App Service.
3. `[Authorize]` Přidejte atribut na libovolný kontroler nebo metodu, která vyžaduje ověření.

Další informace o ověřování klientů do back-endu Mobile Apps najdete v tématu [Přidání ověřování do aplikace](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Jak: Použití vlastního ověřování pro vaši aplikaci
> [!IMPORTANT]
> Aby bylo možné povolit vlastní ověřování, musíte nejprve povolit ověřování App Service bez výběru poskytovatele pro App Service v Azure Portal. Tato akce povolí proměnnou prostředí WEBSITE_AUTH_SIGNING_KEY při hostování.
> 
> 
> Pokud nechcete použít některý z App Service ověřovacích nebo autorizačních zprostředkovatelů, můžete implementovat vlastní přihlašovací systém. Nainstalujte balíček [Microsoft.Azure.Mobile.Server.Login] , který vám pomůže s generováním ověřovacího tokenu.  Zadejte vlastní kód pro ověření přihlašovacích údajů uživatele. Můžete například kontrolovat hesla na základě hodnoty salt a hesla v databázi. V následujícím `isValidAssertion()` příkladu je metoda (definovaná jinde) zodpovědná za tyto kontroly.

Vlastní ověřování je vystaveno vytvořením ApiController a vystavení `register` a `login` zpřístupnění akcí. Klient by měl k shromažďování informací od uživatele používat vlastní uživatelské rozhraní.  Informace se pak odešlou do rozhraní API se standardním voláním HTTP POST. Po ověření kontrolního výrazu serverem je token vydán pomocí `AppServiceLoginHandler.CreateToken()` metody.  ApiController **by neměl** používat `[MobileAppController]` atribut.

Ukázková `login` akce:

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

V předchozím příkladu jsou LoginResult a LoginResultUser serializovatelné objekty, které zveřejňují požadované vlastnosti. Klient očekává vrácení přihlašovacích odpovědí jako objektů JSON ve formátu:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Metoda zahrnuje *cílovou skupinu* a parametr vystavitele. `AppServiceLoginHandler.CreateToken()` Oba tyto parametry jsou nastaveny na adresu URL kořenového adresáře vaší aplikace pomocí schématu HTTPS. Podobně byste měli nastavit *secretKey* na hodnotu pro podpisový klíč vaší aplikace. Nedistribuujte podpisový klíč v klientovi, protože ho můžete použít k mentolováí klíčů a zosobnění uživatelů. Podpisový klíč můžete získat při hostování v App Service odkazem na proměnnou prostředí *pro\_podpisový\_klíč ověření\_webu* . V případě potřeby v místním kontextu ladění postupujte podle pokynů v části [místní ladění pomocí ověřování](#local-debug) a načtěte klíč a uložte ho jako nastavení aplikace.

Vydaný token může zahrnovat i další deklarace identity a datum vypršení platnosti.  Vydaný token musí mít minimálně deklaraci identity subjektu (**Sub**).

Metodu standardního klienta `loginAsync()` můžete podporovat přetížením ověřovací trasy.  Pokud klient volá `client.loginAsync('custom');` přihlášení, musí být `/.auth/login/custom`vaše trasa.  Trasu pro vlastní ověřovací řadič můžete nastavit pomocí `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> `loginAsync()` Použití přístupu zajistí, že se ověřovací token připojí ke každé následnému volání služby.
>
>

### <a name="user-info"></a>Jak: Načíst informace o ověřeném uživateli
Když je uživatel ověřený pomocí App Service, získáte přístup k přiřazenému ID uživatele a dalším informacím v kódu back-endu .NET. Informace o uživateli se dají použít k rozhodování o autorizaci v back-endu. Následující kód získá ID uživatele přidruženého k žádosti:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Identifikátor SID je odvozen od ID uživatele konkrétního poskytovatele a je pro daného uživatele a poskytovatele přihlašovacích údajů statický.  Identifikátor SID má pro neplatné ověřovací tokeny hodnotu null.

App Service taky umožňuje žádat o konkrétní deklarace identity od poskytovatele přihlášení. Každý zprostředkovatel identity může poskytovat další informace pomocí sady identity provider SDK.  Pro informace o přátelích můžete například použít Graph API Facebook.  Můžete zadat deklarace identity, které jsou požadovány v okně Provider v Azure Portal. Některé deklarace vyžadují další konfiguraci se zprostředkovatelem identity.

Následující kód volá metodu rozšíření **GetAppServiceIdentityAsync** pro získání přihlašovacích údajů, které zahrnují přístupový token potřebný k vytvoření požadavků na Facebook Graph API:

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

Přidejte příkaz using pro `System.Security.Principal` k poskytnutí metody rozšíření **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Jak: Omezení přístupu k datům autorizovaných uživatelů
V předchozí části jsme ukázali, jak načíst ID uživatele ověřeného uživatele. Na základě této hodnoty můžete omezit přístup k datům a dalším prostředkům. Například přidání sloupce userId do tabulky a filtrování výsledků dotazu pomocí ID uživatele je jednoduchý způsob, jak omezit vracená data pouze na autorizované uživatele. Následující kód vrátí řádky dat pouze v případě, že identifikátor SID odpovídá hodnotě ve sloupci UserId v tabulce TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

`Query()` Metoda vrátímetodu,kterámůžebýtzpracovánatechnologií`IQueryable` LINQ pro zpracování filtrování.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Postup: Přidání nabízených oznámení do projektu serveru
Přidejte nabízená oznámení do projektu serveru rozšířením objektu **MobileAppConfiguration** a vytvořením klienta Notification Hubs.

1. V aplikaci Visual Studio klikněte pravým tlačítkem myši na serverový projekt a pak klikněte na **Spravovat balíčky NuGet**, vyhledejte `Microsoft.Azure.Mobile.Server.Notifications`a pak klikněte na **nainstalovat**.
2. Zopakováním tohoto kroku nainstalujete `Microsoft.Azure.NotificationHubs` balíček, který obsahuje klientskou knihovnu Notification Hubs.
3. V App_Start/Startup. MobileApp. cs a při inicializaci přidejte volání metody rozšíření **AddPushNotifications ()** :

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Přidejte následující kód, který vytvoří klienta Notification Hubs:

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

Nyní můžete používat klienta Notification Hubs k odesílání nabízených oznámení do registrovaných zařízení. Další informace najdete v tématu [Přidání nabízených oznámení do vaší aplikace](app-service-mobile-ios-get-started-push.md). Další informace o Notification Hubs najdete v tématu [Notification Hubs Overview](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Jak: Povolit cílené nabízení pomocí značek
Notification Hubs umožňuje odesílat cílené oznámení na konkrétní registrace pomocí značek. Automaticky se vytvoří několik značek:

* ID instalace identifikuje konkrétní zařízení.
* ID uživatele založené na ověřeném identifikátoru SID identifikuje konkrétního uživatele.

K ID instalace je možné přistupovat z vlastnosti **installationId** v **MobileServiceClient**.  Následující příklad ukazuje, jak použít ID instalace k přidání značky ke konkrétní instalaci v Notification Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Všechny značky, které klient doručí během registrace nabízených oznámení, se při vytváření instalace ignorují v back-endu. Chcete-li povolit klientovi Přidání značek k instalaci, je nutné vytvořit vlastní rozhraní API, které přidá značky pomocí předchozího vzoru.

Příklad najdete v tématu úvodní [značky nabízených oznámení na straně klienta][5] v ukázce App Service Mobile Apps dokončený Start.

## <a name="push-user"></a>Jak: Odesílání nabízených oznámení ověřenému uživateli
Když ověřený uživatel zaregistruje nabízená oznámení, značka ID uživatele se automaticky přidá k registraci. Pomocí této značky můžete odesílat nabízená oznámení na všechna zařízení zaregistrovaná touto osobou. Následující kód Získá identifikátor SID uživatele, který požadavek odeslal, a pošle do každé registrace zařízení pro tuto osobu šablonu s nabízenými oznámeními:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Při registraci nabízených oznámení z ověřeného klienta se ujistěte, že je ověřování dokončeno, než se pokusíte o registraci. Další informace najdete v tématu úvodní ukázka pro [uživatele][6] v App Service Mobile Apps dokončená úvodní ukázka pro back-end .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Postup: Ladění a řešení potíží s .NET Server SDK
Azure App Service poskytuje několik technik ladění a řešení potíží pro aplikace ASP.NET:

* [Monitorování Azure App Service](../app-service/web-sites-monitor.md)
* [Povolit protokolování diagnostiky v Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Řešení potíží s Azure App Service v aplikaci Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Protokolování
Do protokolů App Service Diagnostics můžete zapisovat pomocí standardního zápisu trasování ASP.NET. Předtím, než budete moci zapisovat do protokolů, je nutné povolit diagnostiku v back-endu mobilní aplikace.

Postup povolení diagnostiky a zápis do protokolů:

1. Postupujte podle kroků v části [Povolení protokolování aplikace (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Do souboru kódu přidejte následující příkaz using:

        using System.Web.Http.Tracing;
3. Vytvořte zapisovač trasování pro zápis z back-endu .NET do diagnostických protokolů následujícím způsobem:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Znovu publikujte projekt serveru a přihlaste se k back-endu mobilní aplikace a spusťte v něm cestu kódu.
5. Stáhněte a vyhodnoťte protokoly, jak je popsáno v části [soubory protokolu Access](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debug"></a>Místní ladění s ověřováním
Svou aplikaci můžete spustit místně, abyste před jejich publikováním do cloudu otestovali změny. Pro většinu back-endu Azure Mobile Apps stiskněte klávesu *F5* při práci v aplikaci Visual Studio. Při použití ověřování však existují další předpoklady.

Musíte mít cloudovou mobilní aplikaci s nakonfigurovaným App Service ověřování/autorizací a klient musí mít koncový bod cloudu zadaný jako alternativní hostitel přihlášení. Konkrétní požadované kroky najdete v dokumentaci ke klientské platformě.

Ujistěte se, že váš mobilní back-end má nainstalované [Microsoft.Azure.Mobile.Server.Authentication] . Potom do třídy Owin Startup vaší aplikace přidejte následující, poté, co `MobileAppConfiguration` byl použit na vaše: `HttpConfiguration`

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

V předchozím příkladu byste měli nakonfigurovat nastavení aplikace *authAudience* a *authIssuer* v rámci souboru Web. config na každou adresu URL kořenového adresáře vaší aplikace pomocí schématu https. Podobně byste měli nastavit *authSigningKey* na hodnotu pro podpisový klíč vaší aplikace.
Získání podpisového klíče:

1. Přejděte do aplikace v [Azure Portal]
2. Klikněte na **nástroje**, **Kudu**, **Přejít**.
3. Na webu pro správu Kudu klikněte na **prostředí**.
4. Najděte hodnotu pro *podpisový\_\_klíč\_ověření webu*.

V konfiguraci místní aplikace použijte podpisový klíč pro parametr *authSigningKey* .  Váš mobilní back-end je nyní vybaven pro ověřování tokenů při spuštění místně, který klient obdrží token z cloudového koncového bodu.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
