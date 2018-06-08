---
title: Jak pracovat s .NET back-end serveru SDK pro Mobile Apps | Microsoft Docs
description: Naučte se pracovat s .NET back-end serveru SDK pro Azure App Service Mobile Apps.
keywords: služby App service, služby azure app service, mobilní aplikace, mobilní služby, nasazení aplikací nasazení azure škálovatelné, aplikace stupnice
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
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "31589624"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Práce se serverovou sadou .NET back-end SDK v prostředí Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Toto téma ukazuje, jak používat rozhraní .NET back-end serveru SDK v klíčové scénáře Azure App Service Mobile Apps. Azure Mobile Apps SDK umožňuje pracovat s mobilních klientů z vaší aplikace ASP.NET.

> [!TIP]
> [Server .NET SDK pro Azure Mobile Apps] [ 2] je open source na Githubu. Úložiště obsahuje všechny zdrojový kód, včetně celý server SDK jednotky testovací sady a některé ukázkové projekty.
>
>

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentaci k nástroji pro server SDK se nachází zde: [Azure Mobile Apps .NET – referenční informace][1].

## <a name="create-app"></a>Postupy: vytvoření back-endu mobilní aplikace .NET
Pokud spouštíte nový projekt, můžete vytvořit buď pomocí aplikace služby App Service [Azure Portal] nebo Visual Studio. Můžete spustit aplikace služby App Service místně nebo publikování tohoto projektu cloudové mobilní aplikace služby App Service.

Pokud přidáváte mobilní funkce do existujícího projektu, najdete v článku [stáhnout a inicializujte sadu SDK](#install-sdk) části.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Vytvořit rozhraní .NET back-end pomocí portálu Azure
K vytvoření App Service mobilního back-endu, buď použijte [rychlý úvodní kurz] [ 3] nebo postupujte podle těchto kroků:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpět v *Začínáme* okno, v části **vytvořit tabulku rozhraní API**, zvolte **C#** jako vaše **back-end jazyk**. Klikněte na tlačítko **Stáhnout**, extrahujte komprimované soubory projektu do místního počítače a otevřete řešení v sadě Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Vytvořit rozhraní .NET back-end pomocí Visual Studio 2017

Nainstalujte Azure zatížení prostřednictvím Instalační program Visual Studio pro publikování do projektu Azure Mobile Apps ze sady Visual Studio. Po dokončení instalace sady SDK, vytvořte aplikaci ASP.NET pomocí následujících kroků:

1. Otevřete **nový projekt** dialogové okno (z **soubor** > **nový** > **projekt...** ).
2. Rozbalte položku **Visual C#** a vyberte **webové**.
3. Vyberte **webové aplikace ASP.NET (rozhraní .NET Framework)**.
4. Zadejte název projektu. Pak klikněte na **OK**.
5. Vyberte **mobilní aplikace Azure** ze seznamu šablon.
6. Klikněte na tlačítko **OK** k vytvoření řešení.
7. Klikněte pravým tlačítkem na projekt v **Průzkumníku řešení** a zvolte **publikování...** , zvolte **služby App Service** jako cíl publikování.
8. Postupujte podle pokynů k ověření a vybrat nový nebo existující službu aplikaci Azure k publikování.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Vytvořit rozhraní .NET back-end pomocí Visual Studio 2015

Nainstalujte [Azure SDK for .NET][4] (verze 2.9.0 nebo novější) k vytvoření projektu Azure Mobile Apps v sadě Visual Studio. Po dokončení instalace sady SDK, vytvořte aplikaci ASP.NET pomocí následujících kroků:

1. Otevřete **nový projekt** dialogové okno (z **soubor** > **nový** > **projekt...** ).
2. Rozbalte položku **šablony** > **Visual C#** a vyberte **webové**.
3. Vyberte **webové aplikace ASP.NET**.
4. Zadejte název projektu. Pak klikněte na **OK**.
5. V části *ASP.NET 4.5.2 šablony*, vyberte **mobilní aplikace Azure**. Zkontrolujte **hostitel v cloudu** vytvoření mobilního back-end v cloudu, do které můžete publikovat tento projekt.
6. Klikněte na **OK**.

## <a name="install-sdk"></a>Postupy: stažení a inicializujte sadu SDK
Sada SDK je k dispozici na [NuGet.org]. Tento balíček obsahuje základní funkce vyžaduje, abyste mohli začít používat sadu SDK. K inicializaci sady SDK, je nutné k provedení akce na **HttpConfiguration** objektu.

### <a name="install-the-sdk"></a>Instalace sady SDK
Chcete-li nainstalovat sadu SDK, klikněte pravým tlačítkem na serverový projekt v sadě Visual Studio, vyberte **spravovat balíčky NuGet**, vyhledejte [Microsoft.Azure.Mobile.Server] balíček a potom klikněte na **instalace** .

### <a name="server-project-setup"></a> Inicializace serverový projekt
Projekt .NET back-end server je inicializován podobně jako ostatní projekty ASP.NET, včetně třídy pro spuštění OWIN. Ujistěte se, že máte odkazuje balíček NuGet `Microsoft.Owin.Host.SystemWeb`. Postup přidání této třídy v sadě Visual Studio, klikněte pravým tlačítkem na serverový projekt a vyberte **přidat** >
**nová položka**, pak **webové**  >  ** Obecné** > **třídy pro spuštění OWIN**.  Třída je generována následující atribut:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

V `Configuration()` metoda vaší třídy pro spuštění OWIN, použijte **HttpConfiguration** objekt konfigurace prostředí Azure Mobile Apps.
Následující příklad inicializuje serverový projekt s žádné nové funkce:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Povolit jednotlivé funkce, musí volat metody rozšíření pro **MobileAppConfiguration** objekt před voláním **metodu**. Například následující kód přidá výchozí trasy na všechny řadiče rozhraní API, které mají atribut `[MobileAppController]` během inicializace:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Rychlé spuštění serveru z portálu Azure volání **UseDefaultConfiguration()**. Tento ekvivalent následující nastavení:

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

* `AddMobileAppHomeController()` poskytuje výchozí Azure Mobile Apps domovskou stránku.
* `MapApiControllers()` poskytuje možnosti vlastního rozhraní API pro WebAPI řadiče označených pomocí `[MobileAppController]` atribut.
* `AddTables()` poskytuje mapování `/tables` koncové body k tabulce řadiče.
* `AddTablesWithEntityFramework()` je sdružený pro mapování `/tables` používající rozhraní Entity Framework koncových bodů na základě řadiče.
* `AddPushNotifications()` poskytuje jednoduchý způsob registrace zařízení pro centra oznámení.
* `MapLegacyCrossDomainController()` poskytuje standardní hlavičky CORS pro místní vývoj.

### <a name="sdk-extensions"></a>Rozšíření sady SDK
Následující balíčky NuGet prostřednictvím rozšíření poskytují různé mobilní funkce, které mohou být využívána vaší aplikace. Povolit rozšíření během inicializace s použitím **MobileAppConfiguration** objektu.

* [Microsoft.Azure.Mobile.Server.Quickstart] podporuje základní nastavení mobilní aplikace. V konfiguraci přidaný voláním **UseDefaultConfiguration** metoda rozšíření během inicializace. Toto rozšíření zahrnuje následující rozšíření: oznámení, ověřování, entita, tabulky, mezi doménami a domovské balíčky. Tento balíček je používán k dispozici na portálu Azure mobilní aplikace pro rychlý start.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementuje výchozí *tuto aplikaci je spuštěný a funkční stránky* pro kořenový adresář webu. Přidejte ke konfiguraci voláním **AddMobileAppHomeController** metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) obsahuje třídy pro práci s daty a nastaví up datovém kanálu. Přidejte ke konfiguraci voláním **AddTables** metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) umožňuje Entity Framework pro přístup k datům v databázi SQL. Přidejte ke konfiguraci voláním **AddTablesWithEntityFramework** metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Authentication] umožňuje ověřování a nastaví up middlewaru OWIN, který slouží k ověření tokenů. Přidejte ke konfiguraci voláním **AddAppServiceAuthentication** a **IAppBuilder**. **UseAppServiceAuthentication** rozšiřující metody.
* [Microsoft.Azure.Mobile.Server.Notifications] povoluje nabízená oznámení a definuje koncový bod nabízené registrace. Přidejte ke konfiguraci voláním **AddPushNotifications** metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) vytvoří kontroler, který slouží data do starší verze webových prohlížečů z mobilní aplikace. Přidejte ke konfiguraci voláním **MapLegacyCrossDomainController** metoda rozšíření.
* [Microsoft.Azure.Mobile.Server.Login] poskytuje AppServiceLoginHandler.CreateToken() metodu, která se používají při ověřování vlastní scénáře statickou metodu.

## <a name="publish-server-project"></a>Postupy: publikování projektu serveru
V této části se dozvíte, jak k publikování projektu back-end .NET ze sady Visual Studio. Můžete taky nasadit pomocí back-end projektu [Git](../app-service/app-service-deploy-local-git.md) nebo jiné metody k dispozici zde.

1. V sadě Visual Studio znovu sestavte projekt pro obnovování balíčků NuGet.
2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na tlačítko **publikovat**. Při prvním publikování, musíte zadat profil publikování. Pokud již máte profil definované, můžete jej vybrat a klikněte na tlačítko **publikovat**.
3. Pokud se zobrazí dotaz, vyberte cíl publikování, klikněte na tlačítko **Microsoft Azure App Service** > **Další**, pak (v případě potřeby) přihlásit se pomocí svých přihlašovacích údajů Azure.
   Visual Studio stáhne a bezpečně uloží vaše nastavení publikování přímo z Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Zvolte vaše **předplatné**, vyberte **typ prostředku** z **zobrazení**, rozbalte položku **mobilní aplikace**a klikněte na váš back-end mobilní aplikace a pak klikněte na **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Zkontrolujte zadané informace o profilu publikování a klikněte na tlačítko **publikovat**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Pokud váš back-end mobilní aplikace byla úspěšně publikována, zobrazí se cílová stránka udávající úspěch.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Postupy: definování řadič tabulky
Zadejte řadič tabulky vystavit tabulku SQL, aby mobilních klientů.  Konfigurace řadič tabulky vyžaduje tři kroky:

1. Vytvořte třídu objektu pro přenos dat (DTO).
2. Nakonfigurujte odkaz na tabulku v třídy Mobile DbContext.
3. Vytvořte řadič tabulky.

Objekt pro přenos dat (DTO) je prostý C# objekt, který dědí z `EntityData`.  Příklad:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO se používá k definování tabulky v databázi SQL.  Chcete-li vytvořit položku databáze, přidejte `DbSet<>` vlastnost, která má DbContext, kterou používáte.  Ve výchozím nastavení šablona projektu pro Azure Mobile Apps, se nazývá DbContext `Models\MobileServiceContext.cs`:

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

Pokud máte sadu Azure SDK nainstalovat, nyní můžete vytvořit řadič tabulky šablony následujícím způsobem:

1. Klikněte pravým tlačítkem na složku řadiče a vyberte **přidat** > **řadiče...** .
2. Vyberte **Azure Mobile Apps tabulky řadič** možnost a potom klikněte na **přidat**.
3. V **přidat kontroler** dialogové okno:
   * V **třída modelu** rozevíracího seznamu, vyberte vaše nové DTO.
   * V **DbContext** rozevíracího seznamu, vyberte třídy DbContext služby Mobile.
   * Název Kontroleru je vytvořen.
4. Klikněte na tlačítko **Add** (Přidat).

Serverový projekt quickstart obsahuje příklad pro jednoduchý **TodoItemController**.

### <a name="adjust-pagesize"></a>Postupy: přizpůsobení velikosti stránkování tabulky
Ve výchozím nastavení vrátí Azure Mobile Apps 50 záznamů na základě požadavku.  Stránkování zajistí, že klient neblokuje jejich vlákna uživatelského rozhraní ani serveru příliš dlouho, zajišťuje vhodné uživatelské prostředí. Chcete-li změnit velikost tabulky stránkování, zvýšit na straně serveru "povolená velikost dotazu" a velikost stránky na straně klienta na straně serveru "povolená velikost dotazu" se upraví pomocí `EnableQuery` atribut:

    [EnableQuery(PageSize = 500)]

Ujistěte se, hodnota vlastnosti PageSize je stejná nebo větší než velikost, kterou klient požaduje.  Odkazovat na konkrétním klientovi postupy dokumentaci informace o změně velikosti stránky klienta.

## <a name="how-to-define-a-custom-api-controller"></a>Postupy: definování vlastní kontroler API
Vlastní kontroler API nabízí základní funkce na váš back-end mobilní aplikace při zavedení koncový bod. Můžete zaregistrovat řadič specifické mobilní rozhraní API pomocí atributu [MobileAppController]. `MobileAppController` Atribut zaregistruje trasu, nastaví serializátor JSON mobilní aplikace a zapne [kontrolu verzí klienta](app-service-mobile-client-and-server-versioning.md).

1. V sadě Visual Studio, klikněte pravým tlačítkem na složku řadiče a pak klikněte na **přidat** > **řadič**, vyberte **kontroler Web API 2&mdash;prázdný** a Klikněte na tlačítko **přidat**.
2. Zadejte **názvu Kontroleru**, jako například `CustomController`a klikněte na tlačítko **přidat**.
3. V soubor nové třídy kontroleru, přidejte následující příkaz using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Použít **[MobileAppController]** atribut definici třídy kontroleru rozhraní API, jako v následujícím příkladu:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. V souboru App_Start/Startup.MobileApp.cs, že přidáte volání **MapApiControllers** – metoda rozšíření, jako v následujícím příkladu:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Můžete také `UseDefaultConfiguration()` metoda rozšíření místo `MapApiControllers()`. Každý kontroler, který nemá **MobileAppControllerAttribute** použít můžete i nadále přistupovat klienti, ale nemusí být zpracován správně klienty pomocí libovolného mobilní aplikace klienta SDK.

## <a name="how-to-work-with-authentication"></a>Postupy: práce s ověřováním
Azure Mobile Apps používá aplikace služby ověřování / autorizace k zabezpečení mobilního back-endu.  V této části se dozvíte, jak provádět následující úlohy souvisejících s ověřováním v projektu .NET back-end serveru:

* [Postupy: přidání ověřování do projektu serveru](#add-auth)
* [Postupy: použití vlastního ověřování pro aplikaci](#custom-auth)
* [Postupy: načtení ověřit informace o uživateli](#user-info)
* [Postupy: omezení přístupu k datům Autorizovaní uživatelé](#authorize)

### <a name="add-auth"></a>Postupy: přidání ověřování do projektu serveru
Ověřování můžete přidat do projektu serveru tím, že rozšíří **MobileAppConfiguration** objekt a konfiguraci middlewaru OWIN. Při instalaci [Microsoft.Azure.Mobile.Server.Quickstart] balíčku a volání **UseDefaultConfiguration** metoda rozšíření, můžete přeskočit ke kroku 3.

1. V sadě Visual Studio, nainstalujte [Microsoft.Azure.Mobile.Server.Authentication] balíčku.
2. V souboru Startup.cs projektu, přidejte následující řádek kódu na začátku **konfigurace** metoda:

        app.UseAppServiceAuthentication(config);

    Tato komponenta middlewaru OWIN ověřuje tokeny vydané bránou přidružené služby App Service.
3. Přidat `[Authorize]` atribut žádné řadiče nebo metod, které vyžaduje ověření.

Další informace o ověřování klientů na váš back-end Mobile Apps naleznete v tématu [přidání ověřování do aplikace](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Postupy: použití vlastního ověřování pro aplikaci
> [!IMPORTANT]
> Pokud chcete povolit vlastní ověřování, musíte nejdřív povolit ověřování služby aplikace bez výběru poskytovatele pro aplikační služby na portálu Azure. Tato akce povolí proměnnou prostředí WEBSITE_AUTH_SIGNING_KEY při hostování.
> 
> 
Pokud nechcete používat jednoho z poskytovatelů ověřování/autorizace služby App Service, můžete implementovat systému přihlášení. Nainstalujte [Microsoft.Azure.Mobile.Server.Login] balíček vám pomůže při generování tokenů ověřování.  Zadejte vlastní kód pro ověření pověření uživatele. Můžete například zkontrolovat proti solené a hash hesla v databázi. V následujícím příkladu `isValidAssertion()` – metoda (definovanou jinde) zodpovídá za tyto kontroly.

Vlastní ověřování je zveřejněný prostřednictvím objektu ApiController vytvoření a vystavení `register` a `login` akce. Klient musí použít vlastní uživatelské rozhraní shromažďovat informace od uživatele.  Informace je poté odeslán do rozhraní API pomocí standardní volání HTTP POST. Jakmile server ověřuje kontrolní výraz, pomocí tokenu je vydat `AppServiceLoginHandler.CreateToken()` metoda.  Objektu ApiController **neměli** použít `[MobileAppController]` atribut.

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

V předchozím příkladu jsou LoginResult a LoginResultUser serializovatelné objekty vystavení požadované vlastnosti. Klient očekává přihlášení odpovědi má být vrácen jako objekty JSON ve tvaru:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

`AppServiceLoginHandler.CreateToken()` Metoda zahrnuje *cílovou skupinu* a *vystavitele* parametr. Oba tyto parametry jsou nastaveny na adresu URL kořenového adresáře aplikace pomocí schéma HTTPS. Podobně byste měli nastavit *secretKey* jako hodnota vaší aplikace je podpisový klíč. Nedistribuovat podpisový klíč v klientovi, jako je lze použít k Mátová klíče a zosobnit uživatele. Můžete získat podpisový klíč při odkazování na hostované ve službě App Service *webu\_AUTH\_PODPISOVÁNÍ\_klíč* proměnné prostředí. V případě potřeby v kontextu místního ladění, postupujte podle pokynů [místní ladění pomocí ověřování](#local-debug) oddílu načíst klíč a uložte ho jako nastavení aplikace.

Vystavený token může zahrnovat také další deklarace identity a datum vypršení platnosti.  Minimálně vydaný token musí obsahovat předmět (**sub**) deklarace identity.

Může podporovat standard klienta `loginAsync()` metoda podle přetížení ověřování trasy.  Pokud klient volá `client.loginAsync('custom');` k přihlášení, musí být vaše trasy `/.auth/login/custom`.  Můžete nastavit trasy pro vlastní ověřování pomocí řadiče `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Pomocí `loginAsync()` přístup zajišťuje, že ověřovací token je připojen k všechny následné volání služby.
>
>

### <a name="user-info"></a>Postupy: načtení ověřit informace o uživateli
Když je uživatel ověřen službou App Service, můžete přistupovat k přiřazené ID uživatele a další informace v rozhraní .NET back-end kódu. Informace o uživateli můžete použít pro při autorizačním rozhodování v back-end. Následující kód získá ID uživatele, který je přidružený k požadavku:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Identifikátor SID je odvozen od ID uživatele specifický pro zprostředkovatele a je statický pro daného uživatele a zprostředkovatele přihlášení.  Identifikátor SID je pro tokeny neplatným ověřovacím hodnotu null.

Služby App Service umožňuje taky deklarace identity specifické požadavky na poskytovatele přihlášení. Každý poskytovatel identity může poskytovat další informace pomocí zprostředkovatele identity SDK.  Například můžete použít rozhraní Graph API sítě Facebook informace přátel.  Zadávat lze deklarace identity, které jsou požadovány v okně zprostředkovatele na portálu Azure. Některé deklarace identity vyžadovat dodatečnou konfiguraci pomocí zprostředkovatele identity.

Následující kód volání **GetAppServiceIdentityAsync** metodu rozšíření k získání tokenu přihlašovací údaje, které zahrnují přístup potřeba provádět požadavky na rozhraní Graph API sítě Facebook:

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

Přidat pomocí příkazu pro `System.Security.Principal` zajistit **GetAppServiceIdentityAsync** metoda rozšíření.

### <a name="authorize"></a>Postupy: omezení přístupu k datům Autorizovaní uživatelé
V předchozí části jsme vám ukázal, jak načíst ID uživatele ověřeného uživatele. Můžete omezit přístup k datům a jiným prostředkům na základě této hodnoty. Například přidávání do tabulek sloupec ID uživatele a filtrování výsledků dotazu pomocí ID uživatele je jednoduchý způsob, jak omezit vrácená data jenom na oprávněné uživatele. Následující kód vrátí řádky dat pouze v případě hodnoty ve sloupci ID uživatele na tabulku TodoItem odpovídá SID:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

`Query()` Metoda vrátí `IQueryable` který smí uživatel manipulovat LINQ pro zpracování filtrování.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Postupy: Přidání nabízených oznámení do projektu serveru
Přidání nabízených oznámení do projektu serveru tím, že rozšíří **MobileAppConfiguration** objekt a vytvoření centra oznámení klienta.

1. V sadě Visual Studio, klikněte pravým tlačítkem na serverový projekt a klikněte na tlačítko **spravovat balíčky NuGet**, vyhledejte `Microsoft.Azure.Mobile.Server.Notifications`, pak klikněte na tlačítko **nainstalovat**.
2. Opakováním tohoto kroku můžete nainstalovat `Microsoft.Azure.NotificationHubs` balíčku, která zahrnuje klientské knihovny centra oznámení.
3. V App_Start/Startup.MobileApp.cs a přidejte volání **AddPushNotifications()** metoda rozšíření během inicializace:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Přidejte následující kód, který vytvoří klienta centra oznámení:

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

Teď můžete klienta služby Notification Hubs k odesílání nabízených oznámení na zaregistrovaných zařízení. Další informace najdete v tématu [přidat nabízená oznámení do vaší aplikace](app-service-mobile-ios-get-started-push.md). Další informace o centrech oznámení naleznete v tématu [přehledu této služby](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Postupy: povolení cílové nabízené pomocí značek
Centra oznámení umožňuje odesílat cílená oznámení na konkrétní registrací pomocí značek. Automaticky vytvoří několik značky:

* ID instalace identifikuje konkrétní zařízení.
* Id uživatele podle ověřené SID identifikuje konkrétního uživatele.

ID je přístupná z instalace **installationId** vlastnost na **MobileServiceClient**.  Následující příklad ukazuje, jak přidat značky do konkrétní instalace v Notification Hubs pomocí ID instalace:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Back-end jsou ignorovány všechny značky dodaný klientem během registrace nabízených oznámení, při vytváření instalace. Chcete-li klienta tak, aby se přidat značky do instalace, musíte vytvořit vlastní rozhraní API, které přidá značky pomocí předchozího vzoru.

V tématu [klienta přidat nabízená oznámení značky] [ 5] v ukázce dokončené rychlý start App Service Mobile Apps příklad.

## <a name="push-user"></a>Postupy: odesílání nabízených oznámení do ověřeného uživatele
Pokud ověřený uživatel zaregistruje pro nabízená oznámení, se automaticky přidá značku ID uživatele pro registraci. Pomocí této značky možné posílat nabízená oznámení pro všechna zařízení registrovaných osoba. Následující kód získá identifikátor SID uživatele, které zadal žádost a odešle šablony nabízených oznámení do každé registrace zařízení pro tuto osobu:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Při registraci pro nabízená oznámení z ověřený klient, ujistěte se, že před pokusem o registraci dokončením ověřování. Další informace najdete v tématu [Push uživatelům] [ 6] v ukázce App Service Mobile Apps dokončené rychlý start pro .NET back-end.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Postupy: ladění a řešení potíží s .NET SDK služby Server
Azure App Service poskytuje několik ladění a řešení potíží s techniky pro aplikace ASP.NET:

* [Monitorování služby Azure App Service](../app-service/web-sites-monitor.md)
* [Povolit protokolování diagnostiky v Azure App Service](../app-service/web-sites-enable-diagnostic-log.md)
* [Řešení potíží s Azure App Service v sadě Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Protokolování
Pomocí standardní zápis trasování ASP.NET, může se zapsat do služby App Service diagnostické protokoly. Před zápisem do protokolů, je nutné povolit diagnostiky v váš back-end mobilní aplikace.

Povolte diagnostiku a zapisovat do protokolů:

1. Postupujte podle kroků v [povolení diagnostiky](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Přidejte následující pomocí příkazu v souboru kódu:

        using System.Web.Http.Tracing;
3. Vytvořte zapisovač trasování pro zápis z back-end .NET do diagnostickým protokolům následujícím způsobem:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Znovu publikovat serverový projekt a přístup k back-end mobilní aplikace provést cestu protokolování do kódu.
5. Stáhněte si a vyhodnocovat u nich protokoly, jak je popsáno v [postupy: stažení protokolů](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Místní ladění pomocí ověřování
Můžete spustit aplikaci místně pro testování změny před publikováním do cloudu. Většina Azure Mobile Apps back-EndY, stiskněte klávesu *F5* při v sadě Visual Studio. Existují však některé další aspekty při použití ověřování.

Musíte mít mobilní aplikaci s ověřování/autorizace služby App Service nakonfigurované založené na cloudu a váš klient musí mít zadaného jako hostitel alternativního přihlašovacího koncového bodu cloudu. Najdete v dokumentaci pro vaše klientská platforma pro určité kroky.

Zajistěte, aby mobilního back-endu [Microsoft.Azure.Mobile.Server.Authentication] nainstalována. Potom v třídy pro spuštění OWIN vaší aplikace, přidejte následující, po `MobileAppConfiguration` byl použit vaší `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

V předchozím příkladu, měli byste nakonfigurovat *authAudience* a *authIssuer* nastavení aplikace v souboru Web.config souboru pro každý být adresa URL kořenového adresáře aplikace pomocí schéma HTTPS. Podobně byste měli nastavit *authSigningKey* jako hodnota vaší aplikace je podpisový klíč.
Postup získání podpisový klíč:

1. Přejděte do vaší aplikace v rámci [Azure Portal]
2. Klikněte na tlačítko **nástroje**, **Kudu**, **přejděte**.
3. V modulu Kudu správy lokality, klikněte na **prostředí**.
4. Najít hodnotu pro *webu\_AUTH\_PODPISOVÁNÍ\_klíč*.

Podpisový klíč pro použití *authSigningKey* parametr v konfiguraci vaší místní aplikace.  Mobilního back-endu je nyní vybavené, aby ověřoval tokeny při spuštění místně, které klient obdrží tokenu z koncového bodu cloudu.

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
