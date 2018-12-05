---
title: Upgrade z mobilní služby do Azure App Service
description: Zjistěte, jak snadno upgradovat aplikaci Mobile Services do mobilní aplikace služby App Service
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
ms.openlocfilehash: 2d346739cd2e80546aee921317e278c1cff32b34
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873134"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Upgrade existující mobilní služby Azure .NET do služby App Service
Mobile App Service je nový způsob vytváření mobilních aplikací pomocí Microsoft Azure. Další informace najdete v tématu [co jsou Mobile Apps?].

Toto téma popisuje, jak upgradovat stávající aplikace back-endu .NET z Azure Mobile Services na nové aplikace App Service Mobile Apps. Při tomto upgradu existující aplikace Mobile Services můžete nadále fungovat.   Pokud je třeba upgradovat aplikace back-end Node.js, podívejte se na [upgrade vašeho Mobile Services Node.js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Při upgradu back-end mobilní službě Azure App Service, má přístup ke všem funkcím služby App Service a účtují podle [ceny služeb App Service], ne Mobile Services, ceny.

## <a name="migrate-vs-upgrade"></a>Migrace a upgrade
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Je doporučeno, kterou jste [provést migraci](app-service-mobile-migrating-from-mobile-services.md) před probíhá jeho upgrade. Tímto způsobem můžete umístit obě verze vaší aplikace na stejném plánu služby App Service a žádné další poplatky se vám účtovat.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Vylepšení v sadě SDK serveru .NET v Mobile Apps
Upgrade na novou [sady Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) poskytuje následující výhody:

* Větší flexibilitu v závislosti na NuGet. Hostitelské prostředí už poskytuje vlastní verze balíčků NuGet, takže můžete použít alternativní verze kompatibilní. Nicméně pokud nové důležité opravy chyb nebo aktualizace zabezpečení do Mobile SDK serveru nebo závislosti, musíte aktualizovat vaši službu ručně.
* Větší flexibilitu v mobilní sady SDK. Můžete explicitně určit funkcí, které a tras se nastavení, jako je například ověřování, table API a koncový bod registrace nabízených oznámení. Další informace najdete v tématu [použití .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Podpora pro ostatní typy projektů ASP.NET a trasy. Můžete hostovat řadiče MVC a webového rozhraní API ve stejném projektu jako projekt mobilního back-endu.
* Podpora pro nové funkce služby App Service ověřování, které umožňují použít běžné konfigurace ověřování ve webových a mobilních aplikací.

## <a name="overview"></a>Základní přehled upgradu
V mnoha případech upgrade bude snadné – stačí přepnout na nový server Mobile Apps sady SDK a opětovné publikování kódu do nové instance mobilní aplikace. Existují však některé scénáře, které bude vyžadovat další konfiguraci, jako je například scénáře pokročilé ověřování a práci se naplánované úlohy. Každý z nich najdete v dalších částech.

> [!TIP]
> Doporučujeme, abyste si a pochopit zbývající části tohoto tématu zcela před zahájením upgradu. Poznamenejte si všechny funkce používáte, které jsou uvedeny níže.
>
>

Sady SDK klienta Mobile Services jsou **není** kompatibilní s novým serverem Mobile Apps SDK. Aby bylo možné zajistit kontinuitu služeb pro vaši aplikaci, by neměla publikovat změny do lokality nyní obsluhuje publikované klientů. Místo toho by měl vytvořit novou mobilní aplikaci, která slouží jako duplicitní. Tuto aplikaci můžete umístit na stejný plán služby App Service vyhnout další finančních nákladů.

Pak budete mít dvě verze aplikace: jednu, která zůstane stejný a slouží v reálném světě a druhý, který potom můžete upgradovat a cíl s novou verzi klienta publikované aplikace. Můžete přesunout a testují vytvořený kód vlastním tempem, ale měli byste si ověřit, že se všechny opravy chyb, které provedete použije k oběma. Jakmile máte pocit, že požadovaný počet klientských aplikací v reálném světě aktualizovali na nejnovější verzi, můžete odstranit původní migrované aplikace vyžadujete-li.

Úplné osnovy pro proces upgradu vypadá takto:

1. Vytvoření nové mobilní aplikace
2. Aktualizovat projekt, který používá nové sady SDK serveru
3. Vydat novou verzi vaší klientské aplikace
4. (Volitelné) Odstraňte původní migrované instance

## <a name="mobile-app-version"></a>Vytváření druhé instance aplikace
Prvním krokem v rámci upgradu je vytvoření mobilní aplikace prostředku, který bude hostitelem nové verze aplikace. Pokud jste již migrovali stávající mobilní službu, můžete k vytvoření této verzi ve stejném plánu hostingu. Otevřít [Azure Portal] a přejděte na migrované aplikace. Poznamenejte si plán služby App Service je spuštěn na.

V dalším kroku následujícím vytvořit druhou instanci aplikace [pokyny k vytvoření back-end .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Po zobrazení výzvy vyberte plán služby App Service nebo "plán hostování" zvolte plán migrované aplikace.

Pravděpodobně budete chtít použijte stejnou databázi a centra oznámení, jako jste to udělali v Mobile Services. Tyto hodnoty můžete zkopírovat tak, že otevřete [Azure Portal] a přejdete na původní aplikace, pak klikněte na tlačítko **nastavení** > **nastavení aplikace**. V části **připojovací řetězce**, kopie `MS_NotificationHubConnectionString` a `MS_TableConnectionString`. Přejděte na svůj nový web upgradu a vložte je do přepíše všechny existující hodnoty. Tento postup opakujte pro další nastavení aplikace požadavkům vašich aplikací. Pokud nepoužíváte migrovanou službu, si můžete přečíst připojovací řetězce a nastavení aplikace z **konfigurovat** kartu části Mobile Services [Portál Azure Classic].

Provedete kopii projektu ASP.NET pro aplikaci a publikujete ji na svůj nový web. Pomocí zkopírovat klientské aplikace aktualizovat novou adresu URL, ověřte, že vše funguje podle očekávání.

## <a name="updating-the-server-project"></a>Aktualizuje se projekt serveru
Mobile Apps poskytuje novou [Mobilní aplikace Server SDK] poskytující podstatě stejné funkce jako modul runtime Mobile Services. Nejdřív byste měli odebrat všechny odkazy na balíčky Mobile Services. Ve správci balíčků NuGet, vyhledejte `WindowsAzure.MobileServices.Backend`. Většina aplikací se zobrazí několik balíčků, včetně `WindowsAzure.MobileServices.Backend.Tables` a `WindowsAzure.MobileServices.Backend.Entity`. V takovém případě začněte s nejnižší balíček ve stromu závislosti, jako `Entity`a jeho odebrání. Po zobrazení výzvy, neodebírejte všechny závislé balíčky. Tento postup opakujte, dokud neodeberete `WindowsAzure.MobileServices.Backend` samotný.

V tomto okamžiku máte projekt, který již neodkazuje na Mobile Services SDK.

Dále přidáte odkazy na sady Mobile Apps SDK. Pro tento upgrade, Většina vývojářů chtít stáhnout a nainstalovat `Microsoft.Azure.Mobile.Server.Quickstart` zabalit, protože se tím přetáhne v celé vyžadována sada.

Bude existovat mnoho chyb kompilátoru vyplývající z rozdíly mezi sad SDK, ale jsou můžeme snadno vyřešit a jsou zahrnuté ve zbytku této části.

### <a name="base-configuration"></a>Základní konfigurace
Pak v WebApiConfig.cs, můžete nahradit:

```csharp
// Use this class to set configuration options for your mobile service
ConfigOptions options = new ConfigOptions();

// Use this class to set WebAPI configuration options
HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));
```

with

```csharp
HttpConfiguration config = new HttpConfiguration();
new MobileAppConfiguration()
    .UseDefaultConfiguration()
.ApplyTo(config);

```

> [!NOTE]
> Pokud chcete další informace o nové sadě SDK serveru .NET a postup přidání nebo odebrání funkcí z vaší aplikace, naleznete [jak používat sadu SDK serveru .NET] tématu.
>
>

Pokud vaše aplikace provádí pomocí funkce ověřování, budete také muset zaregistrovat OWIN middleware. Konfigurace kódu uvedeného výše v tomto případě měla přesunout do nové třída OWIN Startup.

1. Přidání balíčku NuGet `Microsoft.Owin.Host.SystemWeb` Pokud už není zahrnutý v projektu.
2. V sadě Visual Studio, klikněte pravým tlačítkem na projekt a vyberte **přidat** -> **nová položka**. Vyberte **webové** -> **Obecné** -> **třída OWIN Startup**.
3. Přesunout výše uvedený kód pro MobileAppConfiguration z `WebApiConfig.Register()` k `Configuration()` metoda třídy nové spuštění.

Ujistěte se, že `Configuration()` končí metody:

```csharp
app.UseWebApi(config)
app.UseAppServiceAuthentication(config);
```

Existují další změny související s ověřováním, které jsou popsané v následující části úplného ověření.

### <a name="working-with-data"></a>Práce s daty
V Mobile Services mobilní aplikace název pracoval jako výchozí název schématu v nastavení rozhraní Entity Framework.

Abyste měli jistotu, že máte stejné schéma, na kterou se odkazuje jako dříve, použijte následující nastavení schématu v DbContext pro vaši aplikaci:

```csharp
string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
```

Ujistěte se prosím, že máte MS_MobileServiceName nastavte, pokud provedete výše. Pokud dříve vaší aplikace tím přizpůsobené můžete také zadat jiný název schématu.

### <a name="system-properties"></a>Vlastnosti systému
#### <a name="naming"></a>Pojmenování
Na serveru Azure Mobile Services SDK vlastnosti systému vždy obsahovat dvojitým podtržítkem (`__`) předpona pro tyto vlastnosti:

* __createdAt
* __updatedAt
* __deleted
* __version

Mobile Services klientské sady SDK mají zvláštní logiku k analýze vlastnosti systému v tomto formátu.

Ve službě Azure Mobile Apps systémové vlastnosti už mají zvláštní formát a mají následující názvy:

* createdAt
* updatedAt
* odstraněna
* version

Mobile Apps klientské sady SDK pomocí nové názvy vlastností systému, takže nejsou potřeba pro klientský kód žádné změny. Ale pokud provádíte přímo volání REST pro vaši službu pak by měl změnit své dotazy odpovídajícím způsobem.

#### <a name="local-store"></a>Místní úložiště
Změny názvů vlastností systému znamená, že místní databázi offline synchronizace pro Mobile Services není kompatibilní s funkcí Mobile Apps. Pokud je to možné měli byste se vyhnout upgrade klienta, které aplikace ze služeb Mobile Services na Mobile Apps až po čekajících změn byly odeslány na server. Potom upgradovaný aplikace by měla používat nový název souboru databáze.

Pokud klientská aplikace se upgraduje z Mobile Services na Mobile Apps i když existují čekající změny provedené offline ve frontě operaci, musí používat nové názvy sloupců aktualizovat systémové databáze. V systémech iOS toho lze dosáhnout pomocí jednoduché přenesení změnit názvy sloupců. V Androidu a spravovaného klienta .NET měli byste psát vlastní SQL pro přejmenování sloupců pro tabulky dat objektu.

V systémech iOS měli byste změnit schéma základních dat pro entity dat tak, aby odpovídala následující. Všimněte si, že vlastnosti `createdAt`, `updatedAt` a `version` už nebude mít `ms_` předpona:

| Atribut | Typ | Poznámka |
| --- | --- | --- |
| id |Řetězec, označen jako požadovaný |primární klíč do vzdáleného úložiště |
| createdAt |Datum |(volitelné) mapuje na vlastnost createdAt systému |
| updatedAt |Datum |(volitelné) mapuje na vlastnost updatedAt systému |
| version |Řetězec |(volitelné) použít k detekci konfliktů, mapuje se na verzi |

#### <a name="querying-system-properties"></a>Dotazování na vlastnosti systému
Ve službě Azure Mobile Services se neodesílají vlastnosti systému, ve výchozím nastavení, ale pouze v případě, že pochází pomocí řetězce dotazu `__systemProperties`. Naproti tomu v systému Azure Mobile Apps jsou vlastnosti **vždycky vybraná** vzhledem k tomu, že jsou součástí objektového modelu serveru SDK.

Tato změna ovlivňuje hlavně vlastní implementace správce domény, jako je například rozšíření `MappedEntityDomainManager`. V Mobile Services, pokud klient požaduje nikdy všechny vlastnosti systému, je možné použít `MappedEntityDomainManager` , které nejsou namapované ve skutečnosti všechny vlastnosti. Ve službě Azure Mobile Apps, ale tyto nemapovaných vlastnosti způsobí chybu v dotazech GET.

Nejjednodušší způsob, jak tento problém je upravit vaše DTO tak, aby se dědí z `ITableData` místo `EntityData`. Pak přidejte `[NotMapped]` atributu na pole, která by měla být vynechán.

Například následující definuje `TodoItem` bez vlastností systému:

```csharp
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
```

Poznámka: Pokud dojde k chybám `NotMapped`, přidejte odkaz na sestavení `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobile Services zahrnuty některé podporu CORS obtékání řešení ASP.NET CORS. Tato vrstva zabalení se odebrala poskytnout větší kontrolu, vývojář tak může využít přímo [podporu ASP.NET CORS](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Hlavní oblasti zájmu pomocí CORS, která jsou `eTag` a `Location` záhlaví musí být povoleno v pořadí pro klientské sady SDK pracovat správně.

### <a name="push-notifications"></a>Nabízená oznámení
Hlavní položky, které mohou najít chybějící ze sady SDK serveru pro nabízené oznámení, je třída PushRegistrationHandler. Registrace zpracovávané trochu jinak v Mobile Apps a tagless registrace se ve výchozím nastavení povolená. Správa značky mohou dosáhnout pomocí vlastních rozhraní API. Podrobnosti najdete [registrace pro značky](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) pokyny pro další informace.

### <a name="scheduled-jobs"></a>Naplánované úlohy
Naplánované úlohy nejsou součástí Mobile Apps, takže všechny existující úlohy, které máte v back-endu .NET muset upgradovat jednotlivě. Jednou z možností je vytvořit plánovanou [webovou úlohu] na webu kód mobilní aplikace. Můžete také nastavení adaptéru, který obsahuje váš kód projektu a konfigurovat [Azure Scheduler] k volání tohoto koncového bodu podle očekávané plánu.

### <a name="miscellaneous-changes"></a>Různé změny
Nyní musí mít všechny ApiControllers, které budou využívat mobilní klient `[MobileAppController]` atribut. To již zahrnuté ve výchozím nastavení, aby další ApiControllers přejít mobilní formátování nemá vliv.

`ApiServices` Objekt již není součástí sady SDK. Pro přístup k nastavení mobilní aplikace, můžete použít následující:

```csharp
MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
```

Obdobně protokolování se teď provádí pomocí standardní zápis trasování ASP.NET:

```csharp
ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
traceWriter.Info("Hello, World");  
```

## <a name="authentication"></a>Důležité informace o ověřování
Ověření součástí Mobile Services nyní byly přesunuty do funkce ověřování/autorizace služby App Service. Informace o povolení této pro svůj web najdete [přidání ověřování do vaší mobilní aplikace](app-service-mobile-ios-get-started-users.md) tématu.

Pro někteří poskytovatelé, například AAD, Facebook nebo Google byste měli využít existující registraci aplikace kopírování. Stačí přejít na portál svého zprostředkovatele identity a přidejte novou adresu URL pro přesměrování na registraci. Nakonfigurujte ověřování/autorizace služby App Service s ID klienta a tajný klíč.

### <a name="controller-action-authorization"></a>Povolení akce kontroleru
Jakékoli instance `[AuthorizeLevel(AuthorizationLevel.User)]` atribut nutné změnit teď používat standardní technologie ASP.NET `[Authorize]` atribut. Kromě toho řadiče se teď anonymní ve výchozím nastavení, stejně jako v dalších aplikací ASP.NET.
Pokud používáte některou jinou AuthorizeLevel možnosti, jako je například správce nebo aplikace, mějte prosím na paměti, že jsou pryč. Místo toho můžete nastavit AuthorizationFilters pro sdílené tajné klíče nebo nakonfigurovat instančního objektu služby AAD povolit volání služba služba bezpečně.

### <a name="getting-additional-user-information"></a>Získání dalších informací o uživatelích
Můžete získat další uživatelské informace, včetně přístupových tokenů prostřednictvím `GetAppServiceIdentityAsync()` metody:

```csharp
FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
```

Navíc pokud vaše aplikace má závislosti uživatelské ID, jako je například ukládání do databáze, je důležité si uvědomit, že ID uživatelů mezi službami Mobile Services a App Service Mobile Apps se liší. Stále získáte ID uživatele Mobile Services, ale. Všechny podtřídy ProviderCredentials mít vlastnost ID uživatele. Proto budete pokračovat v příkladu před:

```csharp
string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
```

Pokud vaše aplikace provést všechny závislosti na ID uživatele, je důležité, pokud je to možné využívat stejné registrace pomocí zprostředkovatele identity. ID uživatelů mají obvykle rozsah registrace aplikace, která byla použita, takže Představujeme nové registrace může způsobit problémy s odpovídající uživatelé na svá data.

### <a name="custom-authentication"></a>Vlastní ověřování
Pokud vaše aplikace používá vlastní řešení pro ověřování, můžete zajistit, že upgradovaném serveru má přístup k systému. Postupujte podle pokynů pro vlastní ověřování v nové [Přehled sady .NET server SDK] integraci vašeho řešení. Všimněte si, že vlastní ověřování komponenty jsou stále ve verzi preview.

## <a name="updating-clients"></a>Aktualizace klientů
Jakmile budete mít provozní back-endu mobilní aplikace, můžete pracovat na novou verzi vaší aplikace klienta, který ji využívá. Mobile Apps zahrnuje taky novou verzi klientské sady SDK a podobně jako při upgradu serveru výše, musíte odebrat všechny odkazy na Mobile Services SDK před instalací verze mobilní aplikace.

Jedním z hlavních změn mezi verzemi je, že konstruktory už nevyžadují klíč aplikace. Nyní jednoduše předáváte v adrese URL vaší mobilní aplikace. Například u klientů .NET `MobileServiceClient` je konstruktor:

```csharp
public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://contoso.azurewebsites.net", // URL of the Mobile App
);
```

Další informace o instalaci nové sady SDK a použití novou strukturu prostřednictvím následujících odkazů:

* [iOS verze 3.0.0 nebo novější](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) verze 2.0.0 nebo novější](app-service-mobile-dotnet-how-to-use-client-library.md)

Pokud vaše aplikace provádí pomocí nabízených oznámení, poznamenejte si konkrétní registrace – pokyny pro jednotlivé platformy, jako byly některé změny tam také.

Až budete mít novou verzi klienta, která je připravená, vyzkoušejte si to na upgradovaný server projektu. Po ověření, že to funguje, můžete vydat novou verzi vaší aplikace pro zákazníky. Nakonec po vaši zákazníci jste využili příležitost dobře a získat tyto aktualizace, můžete odstranit Mobile Services verze vaší aplikace. V tomto okamžiku můžete zcela upgradovaly na App Service Mobile App pomocí nejnovější sadě SDK serveru Mobile Apps.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Portál Azure Classic]: https://manage.windowsazure.com/
[Co jsou Mobile Apps?]: app-service-mobile-value-prop.md
[Mobilní aplikace Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /azure/scheduler/
[Webovou úlohu]: https://github.com/Azure/azure-webjobs-sdk/wiki
[Jak používat sadu SDK serveru .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Ceny služeb App Service]: https://azure.microsoft.com/pricing/details/app-service/
[Přehled sady .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
