---
title: Jak pracovat s back-end .NET server SDK pro Mobile Apps | Dokumentace Microsoftu
description: Zjistěte, jak pracovat s back-end .NET server SDK pro Azure App Service Mobile Apps.
keywords: služby App service, služby azure app service, mobilní aplikace, mobilní služby, škálování, škálovatelné, aplikace nasazení azure app nasazení
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
ms.openlocfilehash: 8216dafdd846f10ca1c8fc33b710a093aca20c7b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "31589624"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Práce se serverovou sadou .NET back-end SDK v prostředí Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Toto téma ukazuje, jak použít server back-end .NET SDK v klíčové scénáře v Azure App Service Mobile Apps. Sada Azure Mobile Apps SDK umožňuje pracovat s mobilní klienty z vaší aplikace ASP.NET.

> [!TIP]
> [.NET server SDK pro Azure Mobile Apps] [ 2] je open source na Githubu. Úložiště obsahuje všechny zdrojového kódu, včetně sadu testů jednotek sada SDK celý server a některé ukázkové projekty.
>
>

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentaci k sadě SDK serveru je umístěná tady: [referenční informace k Azure Mobile Apps .NET][1].

## <a name="create-app"></a>Postupy: vytvoření back-end mobilní aplikace .NET
Pokud začínáte nový projekt, můžete vytvořit buď pomocí aplikace služby App Service [Azure Portal] nebo Visual Studio. Můžete místní spuštění aplikace služby App Service nebo publikovat projekt do cloudové mobilní aplikace služby App Service.

Pokud přidáváte mobilní funkce do existujícího projektu, přečtěte si článek [stáhnout a inicializujte sadu SDK](#install-sdk) oddílu.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Vytvoření back-endu .NET, pomocí webu Azure portal
Pokud chcete vytvořit back-endu mobilní aplikace App Service, buď použijte [rychlý úvodní kurz] [ 3] nebo postupujte podle těchto kroků:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpět v *Začínáme* okně v části **vytvořit rozhraní API tabulky**, zvolte **jazyka C#** jako vaše **jazyk back-endu**. Klikněte na tlačítko **Stáhnout**, extrahujte komprimované soubory projektu do místního počítače a otevřete řešení v sadě Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Vytvoření back-endu .NET, pomocí sady Visual Studio 2017

Nainstalujte úlohy Azure prostřednictvím instalačního programu sady Visual Studio k publikování do projektu Azure Mobile Apps v sadě Visual Studio. Po instalaci sady SDK, vytvořte aplikaci ASP.NET pomocí následujících kroků:

1. Otevřít **nový projekt** dialogového okna (z **souboru** > **nový** > **projekt...** ).
2. Rozbalte **Visual C#** a vyberte **webové**.
3. Vyberte **webová aplikace ASP.NET (.NET Framework)**.
4. Zadejte název projektu. Pak klikněte na **OK**.
5. Vyberte **mobilní aplikace Azure** ze seznamu šablon.
6. Klikněte na tlačítko **OK** k vytvoření řešení.
7. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a zvolte **publikování...** , klikněte na tlačítko **služby App Service** jako cíl publikování.
8. Postupujte podle výzev k ověření a zvolte nové nebo existující služby Azure App Service k publikování.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Vytvoření back-endu .NET, pomocí sady Visual Studio 2015

Nainstalujte [Azure SDK for .NET][4] (verze 2.9.0 nebo novější) k vytvoření projektu Azure Mobile Apps v sadě Visual Studio. Po instalaci sady SDK, vytvořte aplikaci ASP.NET pomocí následujících kroků:

1. Otevřít **nový projekt** dialogového okna (z **souboru** > **nový** > **projekt...** ).
2. Rozbalte **šablony** > **Visual C#** a vyberte **webové**.
3. Vyberte **webové aplikace ASP.NET**.
4. Zadejte název projektu. Pak klikněte na **OK**.
5. V části *ASP.NET 4.5.2 šablony*vyberte **mobilní aplikace Azure**. Zkontrolujte **hostovat v cloudu** vytvoření mobilního back-endu v cloudu, do které můžete publikovat tento projekt.
6. Klikněte na **OK**.

## <a name="install-sdk"></a>Postupy: stažení a inicializujte sadu SDK
Sada SDK je k dispozici na [NuGet.org]. Tento balíček obsahuje základní funkce vyžaduje, abyste mohli začít používat sadu SDK. Inicializujte sadu SDK, budete muset provést akce na **HttpConfiguration** objektu.

### <a name="install-the-sdk"></a>Instalace sady SDK
Instalace sady SDK, klikněte pravým tlačítkem na projekt serveru v sadě Visual Studio, vyberte **spravovat balíčky NuGet**, vyhledejte [Microsoft.Azure.Mobile.Server] balíček a pak klikněte na **instalace** .

### <a name="server-project-setup"></a> Inicializuje serverový projekt
Projekt .NET back-end serveru je inicializován podobně jako jiné projekty ASP.NET, včetně třídy pro spuštění OWIN. Ujistěte se, že máte odkazuje balíček NuGet `Microsoft.Owin.Host.SystemWeb`. Postup přidání této třídy v sadě Visual Studio, klikněte pravým tlačítkem na svůj serverový projekt a vyberte **přidat** >
**nová položka**, pak **webové**  >  ** Obecné** > **třída OWIN Startup**.  Třídy generuje s použitím následující atribut:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

V `Configuration()` metoda OWIN při spuštění třídy, použijte **HttpConfiguration** objekt konfigurace prostředí Azure Mobile Apps.
Následující příklad inicializuje serverový projekt s žádné přidané funkce:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Pokud chcete povolit jednotlivé funkce, musí volat metody rozšíření na **MobileAppConfiguration** objekt před voláním **volat metodu**. Například následující kód přidá výchozí trasy do všech řadičů rozhraní API, které mají atribut `[MobileAppController]` během inicializace:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Rychlý start serveru z Azure portal volání **UseDefaultConfiguration()**. Tato ekvivalent následující nastavení:

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

Rozšiřující metody používané jsou:

* `AddMobileAppHomeController()` poskytuje výchozí domovskou stránku Azure Mobile Apps.
* `MapApiControllers()` poskytuje možnosti vlastního rozhraní API pro řadiče WebAPI upravené pomocí `[MobileAppController]` atribut.
* `AddTables()` poskytuje mapování `/tables` koncových bodů pro tabulku řadiče.
* `AddTablesWithEntityFramework()` je sdružený pro mapování `/tables` koncové body pomocí Entity Frameworku na základě řadiče.
* `AddPushNotifications()` poskytuje jednoduchý způsob registrace zařízení pro Notification Hubs.
* `MapLegacyCrossDomainController()` poskytuje standardní hlavičky CORS pro místní vývoj.

### <a name="sdk-extensions"></a>Rozšíření sady SDK
Následující balíčky rozšíření založená na Nugetu poskytují různé mobilní funkce, které můžete použít v aplikaci. Povolit rozšíření při inicializaci pomocí **MobileAppConfiguration** objektu.

* [Microsoft.Azure.Mobile.Server.Quickstart] podporuje základní nastavení mobilní aplikace. V konfiguraci přidaný voláním **UseDefaultConfiguration** rozšiřující metoda během inicializace. Toto rozšíření obsahuje následující rozšíření: oznámení, ověřování, Entity, tabulek, mezi doménami a domovské balíčky. Tento balíček je používán rychlý start Mobile Apps k dispozici na na webu Azure portal.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementuje výchozí *této mobilní aplikace je vytvořená a spuštěná stránky* kořenového webu. Přidejte do konfigurace voláním **AddMobileAppHomeController** – metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) obsahuje třídy pro práci s daty a sad up datového kanálu. Přidejte do konfigurace voláním **AddTables** – metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) umožňuje rozhraní Entity Framework pro přístup k datům ve službě SQL Database. Přidejte do konfigurace voláním **AddTablesWithEntityFramework** – metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Authentication] umožňuje ověřování a sad up middlewaru OWIN, který slouží k ověření tokenů. Přidejte do konfigurace voláním **AddAppServiceAuthentication** a **objekt IAppBuilder**. **UseAppServiceAuthentication** metody rozšíření.
* [Microsoft.Azure.Mobile.Server.Notifications] povoluje nabízená oznámení a definuje koncový bod registrace nabízených oznámení. Přidejte do konfigurace voláním **AddPushNotifications** – metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) vytvoří kontroler, který pracuje s daty z mobilní aplikace do starší verze webových prohlížečů. Přidejte do konfigurace voláním **MapLegacyCrossDomainController** – metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Login] poskytuje AppServiceLoginHandler.CreateToken() metodu, která je statická metoda, používají při ověřování vlastní scénáře.

## <a name="publish-server-project"></a>Postupy: publikování projektu serveru
Tato část ukazuje, jak k publikování projektu back-end .NET v sadě Visual Studio. Můžete také nasadit pomocí back-endový projekt [Git](../app-service/app-service-deploy-local-git.md) nebo jiné metody k dispozici zde.

1. V sadě Visual Studio znovu sestavte projekt pro obnovování balíčků NuGet.
2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na tlačítko **publikovat**. Při prvním publikování, budete muset definovat profil publikování. Pokud již máte profil definována, můžete ho vyberte a klikněte na **publikovat**.
3. Pokud budete vyzváni k výběru cíl publikování, klikněte na tlačítko **Microsoft Azure App Service** > **Další**, pak (v případě potřeby) Přihlaste se pomocí přihlašovacích údajů Azure.
   Visual Studio stáhne a bezpečně uloží vaše nastavení publikování přímo z Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Zvolte vaši **předplatné**vyberte **typ prostředku** z **zobrazení**, rozbalte **mobilní aplikace**a klikněte na back-endu mobilní aplikace a pak klikněte na **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Zkontrolujte zadané informace o profilu publikování a klikněte na tlačítko **publikovat**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Back-endu mobilní aplikace úspěšně publikovala, uvidíte cílová stránka, která signalizuje úspěch.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Postupy: definování řadič tabulek
Definujte řadič tabulek ke zveřejnění tabulku SQL, aby mobilní klienty.  Konfigurace Kontroleru tabulky vyžaduje tři kroky:

1. Vytvořte objekt pro přenos dat (DTO) třídu.
2. Konfigurace odkazu na tabulku v třídy DbContext mobilní zařízení.
3. Vytvoříte řadič tabulek.

Objekt pro přenos dat (DTO) je prostého jazyka C# objekt, který dědí z `EntityData`.  Příklad:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

Objekt DTO se používá k definování tabulky v databázi SQL.  Chcete-li vytvořit položky databáze, přidejte `DbSet<>` vlastností pro objekt DbContext, který používáte.  Ve výchozím nastavení šablona projektu pro Azure Mobile Apps, se nazývá uvolněn objekt DbContext `Models\MobileServiceContext.cs`:

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

Pokud máte nainstalované sady SDK Azure nyní můžete vytvořit šablony řadič tabulek takto:

1. Klikněte pravým tlačítkem na složku řadiče a vyberte **přidat** > **Kontroleru...** .
2. Vyberte **Azure Mobile Apps tabulky řadič** možnost a potom klikněte na **přidat**.
3. V **přidat kontroler** dialogové okno:
   * V **třída modelu** rozevíracím seznamu vyberte novou DTO.
   * V **DbContext** rozevíracím seznamu vyberte třídy DbContext mobilní služby.
   * Název Kontroleru se vytvoří za vás.
4. Klikněte na tlačítko **Add** (Přidat).

Serverový projekt quickstart obsahuje příklad pro jednoduchý **TodoItemController**.

### <a name="adjust-pagesize"></a>Postupy: přizpůsobení velikosti stránkovacího tabulky
Ve výchozím nastavení Azure Mobile Apps vrátí 50 záznamy každý požadavek.  Stránkování zajistí, že klient neblokuje jejich vlákno uživatelského rozhraní ani server příliš dlouho, zajišťuje vhodné uživatelské prostředí. Chcete-li změnit velikost tabulky stránkování, zvýšit na straně serveru "povolenou velikost dotazu" a velikost stránky na straně klienta na straně serveru "povolenou velikost dotazu" je upravena pomocí `EnableQuery` atribut:

    [EnableQuery(PageSize = 500)]

Ujistěte se hodnota vlastnosti PageSize je stejná nebo větší než velikost požadavku klienta.  Odkazovat na konkrétní klienta postupy dokumentaci podrobnosti o změně velikosti stránky klienta.

## <a name="how-to-define-a-custom-api-controller"></a>Postupy: definování vlastní kontroler API
Vlastní kontroler API poskytuje naprosto základní funkce pro back-endu mobilní aplikace zveřejněním koncový bod. Můžete zaregistrovat řadič API specifické pro mobilní zařízení pomocí atributu [MobileAppController]. `MobileAppController` Atribut zaregistruje trasu, nastaví serializátor Mobile Apps JSON a zapne [kontrole verze klienta](app-service-mobile-client-and-server-versioning.md).

1. V sadě Visual Studio, klikněte pravým tlačítkem na složku řadiče a potom klikněte na **přidat** > **řadič**vyberte **kontroler rozhraní Web API 2&mdash;prázdný** a Klikněte na tlačítko **přidat**.
2. Zadat **názvu Kontroleru**, jako například `CustomController`a klikněte na tlačítko **přidat**.
3. V novém souboru třídy kontroleru, přidejte následující příkaz using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Použít **[MobileAppController]** atributu na definici třídy kontroleru rozhraní API, jako v následujícím příkladu:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. V souboru App_Start/Startup.MobileApp.cs, přidejte volání **MapApiControllers** metodu rozšíření, jako v následujícím příkladu:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Můžete také použít `UseDefaultConfiguration()` místo rozšiřující metoda `MapApiControllers()`. Každý kontroler, který nemá **MobileAppControllerAttribute** použít i nadále přístupný klienty, ale nemusí být zpracován správně klienty z libovolného klienta Mobile App SDK.

## <a name="how-to-work-with-authentication"></a>Postupy: práce s ověřováním
Azure Mobile Apps využívá ověřování pomocí služby App Service / autorizace zabezpečit mobilní back-end.  Tato část ukazuje, jak provádět následující úlohy související s ověřováním v projektu .NET back-end serveru:

* [Postupy: přidání ověřování do aplikace project server](#add-auth)
* [Postupy: použití vlastní ověřování pro vaši aplikaci](#custom-auth)
* [Postupy: načtení ověřit informace o uživateli](#user-info)
* [Postupy: omezení přístupu k datům pro oprávněné uživatele](#authorize)

### <a name="add-auth"></a>Postupy: přidání ověřování do aplikace project server
Ověřování můžete přidat do projektu serveru tím, že rozšíří **MobileAppConfiguration** objektů a konfiguraci middlewaru OWIN. Při instalaci [Microsoft.Azure.Mobile.Server.Quickstart] balíčku a volání **UseDefaultConfiguration** metodu rozšíření, můžete přeskočit ke kroku 3.

1. V sadě Visual Studio, nainstalujte [Microsoft.Azure.Mobile.Server.Authentication] balíčku.
2. V souboru Startup.cs projektu přidejte následující řádek kódu na začátku **konfigurace** metody:

        app.UseAppServiceAuthentication(config);

    Tato komponenta middlewaru OWIN ověří tokeny vystavené službou přidružená brána služby App Service.
3. Přidat `[Authorize]` atribut kontroler nebo metodu, která vyžaduje ověření.

Další informace o tom, jak ověřovat klienty back-end Mobile Apps, najdete v článku [přidání ověřování do aplikace](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Postupy: použití vlastní ověřování pro vaši aplikaci
> [!IMPORTANT]
> Pokud chcete povolit vlastní ověřování, musíte nejprve povolit ověřování pomocí služby App Service bez výběru zprostředkovatele pro službu App Service na webu Azure Portal. To vám umožní proměnnou prostředí WEBSITE_AUTH_SIGNING_KEY, když jsou hostované.
> 
> 
Pokud nechcete používat jednoho z těchto poskytovatelů ověřování/autorizace služby App Service, můžete implementovat vlastní přihlašovací jméno systému. Nainstalujte [Microsoft.Azure.Mobile.Server.Login] balíčku pro účely pomoci s generování tokenů ověřování.  Zadejte vlastní kód pro ověření přihlašovacích údajů. Můžete například zkontrolovat proti solené a hodnoty hash hesla v databázi. V následujícím příkladu `isValidAssertion()` – metoda (definovaný jinde) zodpovídá za tyto kontroly.

Vlastní ověřování je zveřejněný prostřednictvím vytváření objektu ApiController a jestli vystavuje `register` a `login` akce. Klient musí použít vlastního uživatelského rozhraní pro shromažďování informací od uživatele.  Informace se pak odešle do rozhraní API pomocí standardní volání HTTP POST. Jakmile server ověřuje kontrolního výrazu, je token vystavované pomocí `AppServiceLoginHandler.CreateToken()` metody.  Objektu ApiController **by neměla** použít `[MobileAppController]` atribut.

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

V předchozím příkladu LoginResult a LoginResultUser jsou serializovatelné objekty zveřejňující požadované vlastnosti. Klient očekává, že přihlášení odpovědi má být vrácena jako objekty JSON ve formátu:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

`AppServiceLoginHandler.CreateToken()` Obsahuje metodu *cílovou skupinu* a *vystavitele* parametru. Oba tyto parametry jsou nastaveny na adresu URL kořenového adresáře aplikace používat schéma HTTPS. Podobně byste měli nastavit *secretKey* bude hodnota aplikaci prvku podpisový klíč. Jak je možné Mátová klíče a zosobnit uživatele distribuci podpisový klíč v klientovi. Můžete získat podpisového klíče při odkazování na hostované ve službě App Service *webu\_AUTH\_podepisování\_klíč* proměnné prostředí. V případě potřeby v rámci místní ladění, postupujte podle pokynů [místní ladění s ověřováním](#local-debug) části načtení klíče a uloží je jako nastavení aplikace.

Vydaný token mohou zahrnovat také další deklarace identity a datum vypršení platnosti.  Minimálně vydaný token musí obsahovat předmět (**sub**) deklarace identity.

Může podporovat standard klienta `loginAsync()` metoda přetěžováním ověřování trasy.  Pokud klient volá `client.loginAsync('custom');` k přihlášení, musí být trasu `/.auth/login/custom`.  Můžete nastavit trasy pro kontroler vlastní ověřování pomocí `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Použití `loginAsync()` přístup zajišťuje, že ověřovací token je připojen k každé následné volání služby.
>
>

### <a name="user-info"></a>Postupy: načtení ověřit informace o uživateli
Při ověření uživatele službou App Service, lze použít přiřazené ID uživatele a další informace ve vašem kódu .NET back-endu. Informace o uživateli je možné pro při autorizačním rozhodování v back-endu. Následující kód získá ID uživatele přidružené k požadavku:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Identifikátor SID je odvozen od ID uživatele specifickým pro zprostředkovatele a statická pro daného uživatele a zprostředkovatele přihlášení.  Identifikátor SID je neplatný ověřovací tokeny hodnotu null.

App Service také umožňuje požádat o deklarace identity specifické pro zprostředkovatele přihlášení. Každý poskytovatel identity můžete nabídnout podrobnější informace pomocí zprostředkovatele identity sada SDK.  Například můžete použít rozhraní Graph API sítě Facebook přátel informace.  V okně zprostředkovatele na webu Azure Portal můžete zadat deklarace identity, které jsou požadovány. Některé deklarace identity vyžadovat dodatečnou konfiguraci pomocí zprostředkovatele identity.

Následující kód volá **GetAppServiceIdentityAsync** metodu rozšíření k získání přihlašovacích údajů, které zahrnují přístup k tokenu potřebné k tomu žádosti na rozhraní Graph API Facebooku:

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

Přidat sadu pomocí příkazu pro `System.Security.Principal` poskytnout **GetAppServiceIdentityAsync** – metoda rozšíření.

### <a name="authorize"></a>Postupy: omezení přístupu k datům pro oprávněné uživatele
V předchozí části jsme vám ukázali jak načíst ID uživatele ověřeného uživatele. Můžete omezit přístup k datům a jiným prostředkům na základě této hodnoty. Například přidáním userId sloupce do tabulky a filtrování výsledků dotazu podle ID uživatele je jednoduchý způsob, jak omezit vrácená data jenom na autorizované uživatele. Následující kód vrátí řádky dat jenom v případě identifikátor SID se shoduje s hodnotou ve sloupci ID uživatele na tabulky TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

`Query()` Vrátí metoda `IQueryable` , který lze ovládat pomocí LINQ to zpracování filtrování.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Postupy: Přidání nabízených oznámení do aplikace project server
Přidání nabízených oznámení do projektu serveru tím, že rozšíří **MobileAppConfiguration** objektu a vytvoření klienta Notification Hubs.

1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt serveru a klikněte na tlačítko **spravovat balíčky NuGet**, vyhledejte `Microsoft.Azure.Mobile.Server.Notifications`, pak klikněte na tlačítko **nainstalovat**.
2. Opakováním tohoto kroku můžete nainstalovat `Microsoft.Azure.NotificationHubs` balíček, který obsahuje klientskou knihovnu služby Notification Hubs.
3. V App_Start/Startup.MobileApp.cs a přidejte volání **AddPushNotifications()** rozšiřující metoda během inicializace:

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

Nyní můžete klienta Notification Hubs k odesílání nabízených oznámení pro registrovaná zařízení. Další informace najdete v tématu [přidání nabízených oznámení do vaší aplikace](app-service-mobile-ios-get-started-push.md). Další informace o Notification Hubs najdete v tématu [Přehled služby Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Postupy: povolení cílené nabízených oznámení pomocí značek
Notification Hubs umožňuje posílat cílená oznámení na konkrétní registrace s použitím značek. Automaticky vytvoří několik značek:

* ID instalace identifikuje konkrétní zařízení.
* Id uživatele, založené na identifikátor SID ověřeného identifikuje konkrétního uživatele.

ID je přístupný z instalace **installationId** vlastnost **MobileServiceClient**.  Následující příklad ukazuje, jak přidat značku na konkrétní verzi v Notification Hubs pomocí ID instalace:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Žádné značky dodaný klientem během registrace nabízených oznámení jsou ignorovány back-endu, při vytváření instalace. Povolit klienta k přidání značek na instalaci, musíte vytvořit vlastní rozhraní API, které přidá značky použitím předchozího vzoru.

Zobrazit [klienta přidat nabízená oznámení značky] [ 5] v ukázce App Service Mobile Apps quickstart dokončené příklad.

## <a name="push-user"></a>Postupy: odesílání nabízených oznámení do ověřeného uživatele
Když ověřený uživatel zaregistruje pro nabízená oznámení, se automaticky přidá značku ID uživatele k registraci. Pomocí této značce je posílat nabízená oznámení pro všechna zařízení registrovaných tuto osobu. Následující kód načte identifikátor SID uživatele, který zadal žádost a odešle nabízené oznámení šablony k registraci všech zařízení pro tohoto uživatele:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Při registraci nabízených oznámení z ověřený klient, ujistěte se, že ověřování je dokončeno před pokusem o registraci. Další informace najdete v tématu [nabízet uživatelům] [ 6] v ukázce dokončené rychlý start App Service Mobile Apps pro .NET back-endu.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Postupy: ladění a řešení potíží s .NET Server SDK
Azure App Service nabízí několik ladění a techniky řešení potíží pro aplikace ASP.NET:

* [Monitorování služby Azure App Service](../app-service/web-sites-monitor.md)
* [Povolit diagnostické protokolování ve službě Azure App Service](../app-service/web-sites-enable-diagnostic-log.md)
* [Řešení potíží s Azure App Service v sadě Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Protokolování
Pro diagnostické protokoly služby App Service můžete napsat s využitím standardní zápis trasování ASP.NET. Předtím, než může zapisovat do protokolů, je nutné povolit diagnostiku v back-endu mobilní aplikace.

Povolení diagnostiky a zapisovat do protokolů:

1. Postupujte podle kroků v [postup povolení diagnostiky](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Přidejte následující příkaz using do souboru s kódem:

        using System.Web.Http.Tracing;
3. Vytvořte zapisovač trasování pro zápis z back-endu .NET pro diagnostické protokoly, následujícím způsobem:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Znovu publikovat svůj serverový projekt a přístup k back-endu mobilní aplikace provádět protokolování do cesty kódu.
5. Stáhněte si a vyhodnotit protokoly, jak je popsáno v [postupy: stažení protokolů](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Místní ladění s ověřováním
Můžete spustit aplikaci místně a otestujte změny před publikováním do cloudu. Většina Azure Mobile Apps back-EndY, stiskněte klávesu *F5* během činnosti v sadě Visual Studio. Existují však některé další aspekty při používání ověřování.

Musí mít mobilní aplikaci pomocí ověřování/autorizace služby App Service nakonfigurovaný založené na cloudu a váš klient musí mít koncový bod cloudu, který je zadán jako hostitele alternativních přihlašovacích údajů. Najdete v dokumentaci pro vaše klientská platforma pro určité kroky.

Zajistěte, aby mobilní back-end [Microsoft.Azure.Mobile.Server.Authentication] nainstalované. Poté v vaší aplikace OWIN při spuštění třídy, přidejte následující příkaz, po `MobileAppConfiguration` použil se pro vaše `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

V předchozím příkladu, měli byste nakonfigurovat *authAudience* a *authIssuer* nastavení aplikace v souboru Web.config souborů pro každou být adresa URL kořenového adresáře aplikace používat schéma HTTPS. Podobně byste měli nastavit *authSigningKey* bude hodnota aplikaci prvku podpisový klíč.
Postup získání podpisový klíč:

1. Přejděte do aplikace v rámci [Azure Portal]
2. Klikněte na tlačítko **nástroje**, **Kudu**, **Přejít**.
3. Na webu Kudu správy klikněte na tlačítko **prostředí**.
4. Najít hodnotu pro *webu\_AUTH\_podepisování\_klíč*.

Podpisový klíč pro použití *authSigningKey* parametr v konfiguraci vaší místní aplikace.  Mobilní back-end je teď umožňuje ověřovat tokeny při spuštění místně, které klient obdrží token z koncového bodu cloudu.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
