---
title: Upgrade z mobilní služby Azure App Service
description: Zjistěte, jak snadno upgradovat aplikaci Mobile Services pro aplikaci služby mobilní aplikace
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 05041637aa5cbb044e6731208825f75edec83352
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Upgrade existující Mobile Service Azure .NET do služby App Service
Mobile App Service je nový způsob vytváření mobilních aplikací pomocí Microsoft Azure. Další informace najdete v tématu [co jsou Mobile Apps?].

Toto téma popisuje postup upgradu existující aplikaci .NET back-end Azure Mobile Services nové App Service Mobile Apps. Při provádění tohoto upgradu, aplikace pro Mobile Services můžete nadále fungovat.   Pokud je třeba upgradovat aplikace back-end Node.js, podívejte se na [upgradu vaší Node.js Mobile Services](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Při upgradu mobilního back-endu do služby Azure App Service má přístup ke všem funkcím služby App Service a se účtují podle [služby App Service – ceny], není Mobile Services ceny.

## <a name="migrate-vs-upgrade"></a>Migrace a upgrade
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Je doporučeno, které [provést migraci](app-service-mobile-migrating-from-mobile-services.md) před zahájením upgradu. Tímto způsobem můžete umístit obě verze vaší aplikace stejném plánu služby App Service a způsobit bez dalších nákladů.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Vylepšení v nástroji server mobilní aplikace .NET SDK
Upgrade na novou [Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) poskytuje následující výhody:

* Větší flexibilitu v NuGet závislosti. Hostitelské prostředí už poskytuje vlastní verzích balíčky NuGet, takže můžete použít alternativní verze kompatibilní. Pokud bugfixes nové důležité aktualizace nebo aktualizace zabezpečení Mobile serveru SDK nebo závislosti, je však nutné ručně aktualizovat služby.
* Větší míra flexibility v mobilních SDK. Funkce, které můžete řídit explicitně a trasy jsou nastaveny, jako je například ověřování, tabulka rozhraní API a koncový bod registrace nabízených. Další informace najdete v tématu [jak používat .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Podpora pro jiné typy projektů ASP.NET a trasy. Nyní můžete hostovat řadiče MVC a webového rozhraní API ve stejném projektu jako projektu mobilního back-endu.
* Podpora pro nové funkce služby App Service ověřování, které umožňují použít obvyklé konfigurace ověřování ve vaší webové a mobilní aplikace.

## <a name="overview"></a>Základní přehled upgradu
V mnoha případech upgradu bude stejně jednoduché jako přepnutí na nový server Mobile Apps SDK a opětovné publikování kódu do nové instance mobilní aplikace. Existují, ale některé scénáře, které se vyžadují určitou další konfiguraci, jako je například pokročilého ověřování scénáře a pracovat s naplánované úlohy. Každá z těchto je popsané v dalších oddílech.

> [!TIP]
> Doporučujeme přečíst si a pochopit zbývající část tohoto tématu úplně před zahájením upgradu. Poznamenejte si všechny funkce, pomocí kterého jsou vyznačeny níže.
>
>

Klientem Mobile Services SDK jsou **není** kompatibilní s novým serverem Mobile Apps SDK. Chcete-li zajistit kontinuitu služeb pro vaši aplikaci, by neměl publikovat změny v lokalitě aktuálně obsluhující publikované klientů. Místo toho by měla vytvoříte novou mobilní aplikaci, která slouží jako duplicitní. Tuto aplikaci můžete umístit na stejný plán služby App Service účtovány dodatečné finanční náklady.

Pak bude mít dvě verze této aplikace: takovou, která zůstává stejný a slouží v zástupné a druhé, které potom můžete upgradovat a cíl s novou verzi klienta publikované aplikace. Můžete přesunout a otestujte svůj kód vaší tempem, ale ujistěte se, že všechny opravy chyb, které použije na obě. Jakmile si myslíte, že požadovaný počet klientských aplikací v zástupné byly aktualizovány na nejnovější verzi, můžete odstranit původní migrovanou aplikaci vyžadujete-li.

Úplné obrys pro proces upgradu je následující:

1. Vytvoření nové mobilní aplikace
2. Aktualizace projekt, který používá nové sady SDK serveru
3. Vydání nové verze klientské aplikace
4. (Volitelné) Odstranit původní migrované instanci

## <a name="mobile-app-version"></a>Vytváření druhé instance aplikace
Prvním krokem při upgradu je vytvoření prostředku mobilní aplikace, který bude hostitelem nové verze aplikace. Pokud jste již migrovali stávající mobilní službu, můžete vytvořit tato verze na stejný plán, který hostování. Otevřete [portál Azure] a přejděte na migrované aplikace. Poznamenejte si plán služby App Service je spuštěn na.

V dalším kroku následujícím vytvořit druhou instanci aplikace [pokyny pro vytvoření rozhraní .NET back-end](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Po zobrazení výzvy zvolte svůj plán služby App Service nebo "hostování plán" Vyberte plán, který migrované aplikace.

Budete pravděpodobně chtít použít stejnou databázi a Centrum oznámení, jako jste to udělali v Mobile Services. Tyto hodnoty můžete zkopírovat otevřením [portál Azure] a přejdete na původní aplikace, klikněte na tlačítko **nastavení** > **nastavení aplikace**. V části **připojovací řetězce**, kopie `MS_NotificationHubConnectionString` a `MS_TableConnectionString`. Přejděte na nový web upgradu a vložit do přepsal všechny existující hodnoty. Tento postup opakujte pro všechny ostatní nastavení aplikace potřebám vaší aplikace. Pokud nepoužíváte migrovanou službu, si můžete přečíst připojovací řetězce a nastavení aplikace z **konfigurace** kartě části Mobile Services [portál Azure classic].

Vytvořit kopii projekt ASP.NET pro aplikaci a publikovat do nové lokality. Pomocí kopie klientskou aplikaci aktualizovat pomocí nové adrese URL, ověřte, že vše funguje podle očekávání.

## <a name="updating-the-server-project"></a>Aktualizace serveru projektu
Mobile Apps poskytuje novou [mobilní aplikace Server SDK] který poskytuje mnohem stejné funkce jako runtime Mobile Services. Nejdřív byste měli odebrat všechny odkazy na balíčky Mobile Services. Vyhledejte v Správce balíčků NuGet `WindowsAzure.MobileServices.Backend`. Většina aplikací se zobrazí několik balíčků tady, včetně `WindowsAzure.MobileServices.Backend.Tables` a `WindowsAzure.MobileServices.Backend.Entity`. V takovém případě začínat nejnižší balíček ve stromové struktuře závislosti, jako například `Entity`a jeho odebrání. Po zobrazení výzvy, neodstraňujte všechny závislé balíčky. Tento postup opakujte, dokud neodeberete `WindowsAzure.MobileServices.Backend` sám sebe.

V tomto okamžiku budete mít projekt, který již neodkazuje na Mobile Services SDK.

Dále přidáte odkazy Mobile Apps SDK. Pro tento upgrade, Většina vývojářů chtít stáhnout a nainstalovat `Microsoft.Azure.Mobile.Server.Quickstart` balíček, jak to bude stáhnout požadované celou sadu.

Bude mnoha chyby kompilátoru vyplývající z rozdíly mezi sady SDK, ale jsou snadno adresu a jsou popsané ve zbývající části této části.

### <a name="base-configuration"></a>Základní konfigurace
Potom v WebApiConfig.cs, můžete nahradit:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

with

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Pokud chcete další informace o novém serveru .NET SDK a jak přidat nebo odebrat funkce z vaší aplikace, naleznete v tématu [jak používat .NET server SDK] tématu.
>
>

Pokud vaše aplikace umožňuje používat funkce ověřování, budete také muset zaregistrovat OWIN middleware. V takovém případě byste měli přesunout výše uvedený kód konfigurace do nové třídy OWIN při spuštění.

1. Přidejte balíček NuGet `Microsoft.Owin.Host.SystemWeb` Pokud již není zahrnutý v projektu.
2. V sadě Visual Studio, klikněte pravým tlačítkem na projekt a vyberte **přidat** -> **novou položku**. Vyberte **webové** -> **Obecné** -> **třídy pro spuštění OWIN**.
3. Přesunutí ve výše uvedeném kódu pro MobileAppConfiguration z `WebApiConfig.Register()` k `Configuration()` metoda třídy nové spuštění.

Zajistěte, aby `Configuration()` metoda končí:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Existují další změny související s ověřováním, které jsou popsané v následující části úplného ověření.

### <a name="working-with-data"></a>Práce s daty
V Mobile Services zpracovávají název mobilní aplikace jako výchozí název schématu v instalačním programu rozhraní Entity Framework.

K zajištění, že máte stejné schéma se na ně odkazovat jako dříve, použijte následující nastavení schématu v DbContext pro aplikaci:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Zkontrolujte prosím, že máte MS_MobileServiceName nastavit, pokud uděláte výše. Také můžete zadat jiný název schématu pokud dříve vaší aplikace to přizpůsobit.

### <a name="system-properties"></a>Vlastnosti systému
#### <a name="naming"></a>Pojmenování
Na serveru Azure Mobile Services SDK vlastnosti systému vždycky obsahovat dvojité podtržítko (`__`) předponu pro vlastnosti:

* __createdAt
* __updatedAt
* __deleted
* __version

Klientem Mobile Services SDK mít speciální logiku pro analýzu vlastnosti systému v tomto formátu.

V Azure Mobile Apps vlastnosti systému už mít speciální formát a mají tyto názvy:

* CreatedAt
* updatedAt
* odstraněna
* verze

Klient Mobile Apps sady SDK použít nové názvy vlastnosti systému, je potřeba kód klienta žádné změny. Ale pokud přímo provádíte volání REST k službě pak měli byste změnit své dotazy odpovídajícím způsobem.

#### <a name="local-store"></a>Místní úložiště
Změny názvy vlastností systému znamená, že místní databázi offline synchronizace pro Mobile Services není kompatibilní s Mobile Apps. Pokud je to možné Vyhněte se upgrade klienta, které aplikace z mobilní služby Mobile Apps až po čekající změny byly odeslány na server. Potom upgradovaný aplikace by měl používat nový název souboru databáze.

Pokud klientská aplikace je upgrade z mobilní služby na mobilní aplikace při existují čekající offline změny ve frontě operaci, musí používat nové názvy sloupců aktualizovat systémové databáze. V systému iOS toho lze dosáhnout pomocí lightweight migrace změňte názvy sloupců. Pro Android a spravovaného klienta rozhraní .NET byste měli zapsat vlastní SQL přejmenování sloupce pro vaše data objektu tabulky.

V systému iOS měli byste změnit schéma základní Data pro vaše data entity tak, aby odpovídala následující. Všimněte si, že vlastnosti `createdAt`, `updatedAt` a `version` již nemáte `ms_` předpony:

| Atribut | Typ | Poznámka |
| --- | --- | --- |
| id |Řetězec, označen jako požadovaný |primární klíč v vzdáleného úložiště |
| CreatedAt |Datum |(volitelné) mapuje vlastnosti createdAt systému |
| updatedAt |Datum |(volitelné) mapuje vlastnosti updatedAt systému |
| verze |Řetězec |(volitelné) používán pro zjišťování konfliktů, map k verzi |

#### <a name="querying-system-properties"></a>Dotaz na vlastnosti systému
V Azure Mobile Services nejsou odesílány vlastnosti systému ve výchozím nastavení, ale jenom v případě, že jsou vyžádané pomocí řetězce dotazu `__systemProperties`. Naopak v systému Azure Mobile Apps jsou vlastnosti **vždycky vybraná** vzhledem k tomu, že jsou součástí objektový model serveru SDK.

Tato změna ovlivňuje především vlastních implementací správce domény, jako je například rozšíření `MappedEntityDomainManager`. V Mobile Services, pokud klient požaduje nikdy všechny vlastnosti systému, je možné použít `MappedEntityDomainManager` který nemapují ve skutečnosti všechny vlastnosti. V Azure Mobile Apps, ale tyto nenamapovaný vlastnosti dojde k chybě v dotazech GET.

Nejjednodušší způsob, jak vyřešit tento problém je upravit vaše DTOs tak, aby se dědí `ITableData` místo `EntityData`. Poté, přidejte `[NotMapped]` atribut pole, která by měla být vynechán.

Například následující definuje `TodoItem` bez vlastností systému:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Poznámka: Pokud dojde k chybám `NotMapped`, přidejte odkaz na sestavení `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobilní služby zahrnuty některé podpora CORS nástrojem pro zabalení řešení ASP.NET CORS. Odebrali jsme tuto vrstvu zabalení předat vývojář další kontrolu, tak můžete přímo využít [podporu pro ASP.NET CORS](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Jsou hlavní problémové oblasti pokud pomocí CORS, která `eTag` a `Location` hlavičky, musí být povoleno v pořadí pro klienta sady SDK pracovat správně.

### <a name="push-notifications"></a>Nabízená oznámení
Nabízená instalace je hlavní položky, které možná chybí ze sady SDK serveru PushRegistrationHandler třída. Registrace zpracovávané trochu jinak v Mobile Apps a tagless registrace se ve výchozím nastavení povolené. Správa značky mohou možné dosáhnout použitím vlastního rozhraní API. Podrobnosti najdete [registrace pro značky](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) pokyny pro další informace.

### <a name="scheduled-jobs"></a>Naplánované úlohy
Naplánované úlohy nejsou součástí Mobile Apps, všechny existující úlohy, které máte ve vašem .NET back-end bude nutné aktualizovat jednotlivě. Jednou z možností je vytvořit naplánované [webovou úlohu] na webu kód mobilní aplikace. Můžete také nastavit kontroler, který obsahuje kód úlohy a nakonfigurovat [Azure Scheduler] narazí tohoto koncového bodu podle plánu, očekávané.

### <a name="miscellaneous-changes"></a>Ostatní změny
Nyní musí mít všechny ApiControllers, které budou využívat mobilního klienta `[MobileAppController]` atribut. To již zahrnuté ve výchozím nastavení, aby další ApiControllers přejdete mobilní formátování nemá vliv.

`ApiServices` Objekt je už součástí sady SDK. Pro přístup k nastavení mobilní aplikace, můžete použít následující:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Podobně protokolování se nyní provádí pomocí standardní zápis trasování ASP.NET:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Důležité informace o ověřování
Ověření součástí Mobile Services nyní byly přesunuty do funkce ověřování/autorizace služby App Service. Informace o povolení to pro váš web načtením najdete [přidání ověřování do mobilní aplikace](app-service-mobile-ios-get-started-users.md) tématu.

Pro někteří poskytovatelé, například AAD a Facebook, Google musí být schopné využít existující registraci z vaší kopie aplikace. Stačí přidat novou adresu URL přesměrování na registraci a přejděte na portál zprostředkovatele identity. Nakonfigurujte ověřování/autorizace služby App Service se ID klienta a tajný klíč.

### <a name="controller-action-authorization"></a>Autorizace akce kontroleru
Všechny instance `[AuthorizeLevel(AuthorizationLevel.User)]` atribut je třeba změnit teď používat standardní ASP.NET `[Authorize]` atribut. Kromě toho řadiče jsou nyní anonymní ve výchozím nastavení, jako ostatní aplikace ASP.NET.
Pokud používáte jednu z jiné AuthorizeLevel možnosti, například správce nebo aplikaci, Upozorňujeme, že jsou pryč. Místo toho můžete nastavit AuthorizationFilters pro sdílené tajné klíče nebo nakonfigurovat objektu služby AAD umožňující volání služba služba bezpečně.

### <a name="getting-additional-user-information"></a>Získání informací o další uživatele
Můžete získat další uživatelské informace, včetně přístupových tokenů prostřednictvím `GetAppServiceIdentityAsync()` metoda:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Kromě toho pokud vaše aplikace používá závislosti na uživatelské ID, jako je například ukládání je v databázi, je důležité si uvědomit, že ID uživatelů mezi Mobile Services a App Service Mobile Apps se liší. ID uživatele Mobile Services, můžete pořád dostat, když. Všechny dílčí ProviderCredentials mít vlastnost ID uživatele. Z příkladu před pokračováním proto:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Pokud vaše aplikace využít všechny závislosti na ID uživatele, je důležité, pokud je to možné využívat stejnou registraci pomocí zprostředkovatele identity. ID uživatelů jsou obvykle omezená na registrace aplikace, která byla použita, takže Představujeme nové registrace může způsobit problémy s odpovídajícím uživatelé na svá data.

### <a name="custom-authentication"></a>Vlastního ověřování
Pokud vaše aplikace používá vlastní řešení ověřování, můžete zajistit, že upgradovaná lokality má přístup k systému. Postupujte podle pokynů nové pro vlastní ověřování v [Přehled sady SDK serveru .NET] integrovat řešení. Upozorňujeme, že komponenty vlastního ověřování jsou stále ve verzi preview.

## <a name="updating-clients"></a>Aktualizace klientů
Až budete mít provozní back-end mobilní aplikace, můžete pracovat na novou verzi klientskou aplikaci, která se využívá. Mobilní aplikace také zahrnuje novou verzi klienta sady SDK a podobně jako u upgrade serveru výše, budete muset odebrat všechny odkazy na sady Mobile Services SDK před instalací verze mobilní aplikace.

Jednou z hlavních změn mezi verzemi je, že konstruktorů už nebudou potřebovat klíčem aplikace. Nyní jednoduše předáte v adrese URL mobilní aplikace. Například na klientů .NET `MobileServiceClient` konstruktor je nyní:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Další informace o instalaci nové sady SDK a použití nového strukturu přes odkazy níže:

* [iOS verze 3.0.0 nebo novější](app-service-mobile-ios-how-to-use-client-library.md)
* [Rozhraní .NET (Windows nebo Xamarin) verze 2.0.0 nebo novější](app-service-mobile-dotnet-how-to-use-client-library.md)

Pokud vaše aplikace provede pomocí nabízených oznámení, poznamenejte si konkrétní registrace pokyny pro každou platformu, protože byly některé změny existuje také.

Pokud máte novou verzi klienta, která je připraveno, vyzkoušejte ji proti projektu upgradovaný server. Po ověření, že bude fungovat, můžete vydat novou verzi vaší aplikace pro zákazníky. Nakonec Jakmile vaši zákazníci jste využili příležitost a získat tyto aktualizace, můžete odstranit Mobile Services verzi vaší aplikace. V tomto okamžiku úplně upgradu na aplikaci služby mobilní aplikace pomocí nejnovější Mobile Apps serveru SDK.

<!-- URLs. -->

[portál Azure]: https://portal.azure.com/
[portál Azure classic]: https://manage.windowsazure.com/
[co jsou Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[mobilní aplikace Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[webovou úlohu]: https://github.com/Azure/azure-webjobs-sdk/wiki
[jak používat .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[služby App Service – ceny]: https://azure.microsoft.com/pricing/details/app-service/
[Přehled sady SDK serveru .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
