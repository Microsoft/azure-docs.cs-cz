---
title: Upgrade z mobilní služby na Azure App Service – Node.js
description: Zjistěte, jak snadno upgradovat aplikaci Mobile Services do mobilní aplikace služby App Service
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 71be338aeb6d0234d22d412d6838e36a26797b20
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002112"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Upgrade existující mobilní služby Azure Node.js do služby App Service
Mobile App Service je nový způsob vytváření mobilních aplikací pomocí Microsoft Azure. Další informace najdete v tématu [co jsou Mobile Apps?].

Tento článek popisuje, jak upgradovat existující aplikace back-end Node.js z Azure Mobile Services na nové aplikace App Service Mobile Apps. Při tomto upgradu existující aplikace Mobile Services můžete nadále fungovat.  Pokud je třeba upgradovat aplikace back-end Node.js, podívejte se na [upgrade vašeho .NET Mobile Services](app-service-mobile-net-upgrading-from-mobile-services.md).

Při upgradu back-end mobilní službě Azure App Service, má přístup ke všem funkcím služby App Service a účtují podle [ceny služeb App Service], ne Mobile Services, ceny.

## <a name="migrate-vs-upgrade"></a>Migrace a upgrade
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Je doporučeno, kterou jste [provést migraci](app-service-mobile-migrating-from-mobile-services.md) před probíhá jeho upgrade. Tímto způsobem můžete umístit obě verze vaší aplikace na stejném plánu služby App Service a žádné další poplatky se vám účtovat.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Vylepšení v Mobile Apps Node.js server SDK
Upgrade na novou [sady Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) poskytuje mnoho vylepšení, včetně:

* Na základě [Express framework](https://expressjs.com/en/index.html), sadu SDK pro Node je odlehčené a navržená tak, aby držet krok s novými verzemi uzel jako pocházejí navýšení kapacity. Můžete přizpůsobit chování aplikace s Express middleware.
* Výrazné zlepšení výkonu ve srovnání s Mobile Services SDK.
* Nyní můžete hostovat web spolu s mobilním back-end Podobně je snadno přidat k jakékoli existující aplikaci express.v4 Azure Mobile SDK.
* Určená pro různé platformy i místním vývoji, sady Mobile Apps SDK může být vyvinuté a spustit místně na platformách Windows, Linuxu a os x. Je teď snadno použitelné běžných postupů vývoje uzlu, jako jsou spuštění [Mocha](https://mochajs.org/) testování před jejich nasazením.

## <a name="overview"></a>Základní přehled upgradu
Chcete-li pomoci při upgradu back-end Node.js, Azure App Service poskytuje kompatibilitu balíčku.  Po upgradu budete mít nový web, který je možné nasadit na nový Web App Service.

Sady SDK klienta Mobile Services jsou **není** kompatibilní s novým serverem Mobile Apps SDK. Aby bylo možné zajistit kontinuitu služeb pro vaši aplikaci, by neměla publikovat změny do lokality nyní obsluhuje publikované klientů. Místo toho by měl vytvořit novou mobilní aplikaci, která slouží jako duplicitní. Tuto aplikaci můžete umístit na stejný plán služby App Service vyhnout další finančních nákladů.

Pak budete mít dvě verze aplikace: ten, který zůstane stejný a slouží v reálném světě a druhý, který potom můžete upgradovat a cíl s novou verzi klienta publikované aplikace. Můžete přesunout a testují vytvořený kód vlastním tempem, ale měli byste si ověřit, že se všechny opravy chyb, které provedete použije k oběma. Jakmile máte pocit, že požadovaný počet klientských aplikací v reálném světě aktualizovali na nejnovější verzi, můžete odstranit původní migrované aplikace vyžadujete-li. Ho nebude mít za následek všechny další peněžní náklady, pokud hostované v rámci stejného plánu služby App Service jako mobilní aplikace.

Úplné osnovy pro proces upgradu vypadá takto:

1. Stáhněte existující Mobile Service (migrované) Azure.
2. Převeďte projekt na služby Azure Mobile Apps pomocí balíčku pro kompatibilitu.
3. Opravte případné rozdíly (například nastavení ověřování).
4. Nasazení projektu převedený mobilní aplikace Azure na novou službu App Service.
5. Vydat novou verzi vaší klientské aplikace, který používá novou mobilní aplikaci.
6. (Volitelné) Odstraňte původní migrované služby mobilní aplikace.

Odstranění může dojít, když se veškerý provoz na původní migrované mobilní službě.

## <a name="install-npm-package"></a> Instalace požadovaných součástí
[Uzel] byste měli nainstalovat na místním počítači.  Měli byste nainstalovat také balíček kompatibility.  Po dokončení instalace uzlu spustíte následující příkaz z nového příkazového řádku nebo příkazového řádku Powershellu:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Získání skriptů Azure Mobile Services
* Přihlaste se k [Azure Portal].
* Pomocí **všechny prostředky** nebo **App Services**, najít webu služby Mobile Services.
* V rámci stránky, klikněte na **nástroje** -> **Kudu** -> **Přejít** otevřete webu kudu.
* Klikněte na **ladění konzoly** -> **Powershellu** otevřete konzoly ladění.
* Přejděte na `site/wwwroot/App_Data/config` zase kliknutím na každý adresář
* Klikněte na ikonu stažení vedle `scripts` adresáře.

Tento příkaz stáhne skripty ve formátu ZIP.  Vytvoření nového adresáře na místním počítači a rozbalte `scripts.ZIP` soubor v adresáři.  Tím se vytvoří `scripts` adresáře.

## <a name="scaffold-app"></a> Vygenerované uživatelské rozhraní nového back-endu Azure Mobile Apps
Spusťte následující příkaz z adresáře, který obsahuje skripty adresáře:

```scaffold-mobile-app scripts out```

Tím se vytvoří automaticky generovaný Azure Mobile Apps back-end v `out` adresáře.  Ačkoli není vyžadována, je vhodné zkontrolovat `out` adresáře do úložiště zdrojového kódu podle vašeho výběru.

## <a name="deploy-ama-app"></a> Nasazení back-endu Azure Mobile Apps
Během nasazení budete potřebovat provést následující kroky:

1. Vytvoření nové mobilní aplikace v [Azure Portal].
2. Spustit `createViews.sql` skript v připojené databázi.
3. Propojí databázi, který je spojen do svojí mobilní služby na novou službu App Service.
4. Všechny další prostředky (například Notification Hubs) odkaz na novou službu App Service.
5. Nasazení generovaný kód do nové lokality.

### <a name="create-a-new-mobile-app"></a>Vytvoření nové mobilní aplikace
1. Přihlaste se na [Azure Portal].
2. Klikněte na **+NOVÝ** > **Web + mobilní zařízení** > **Mobilní aplikace** a potom zadejte název back-endu mobilní aplikace .
3. V případě **skupiny prostředků** vyberte existující skupinu prostředků nebo vytvořte novou (použijte stejný název, jaký má aplikace).

    Můžete buď vybrat jiný plán služby App Service nebo vytvořit nový. Další informace o plánech služby App Service a vytvoření nového plánu v různých cenových úrovních a požadovaném umístění najdete v článku [Podrobný přehled plánů služby Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. V případě **plánu služby App Service** je vybraný výchozí plán (na [úrovni Standard](https://azure.microsoft.com/pricing/details/app-service/)). Můžete také vybrat jiný plán nebo [vytvořit nový](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Určit nastavení plánu služby App Service [umístění, funkce, náklady a výpočetní prostředky](https://azure.microsoft.com/pricing/details/app-service/) spojené s vaší aplikací.

    Až se rozhodnete pro konkrétní plán, klikněte na **Vytvořit**. Tím vytvoříte back-end mobilní aplikace.

### <a name="run-createviewssql"></a>Spustit CreateViews.SQL
Vygenerovaná aplikace obsahuje soubor s názvem `createViews.sql`.  Tento skript je třeba spustit na cílovou databázi.  Připojovací řetězec pro cílovou databázi můžete získat z migrovaných mobilní služby **nastavení** stránky **připojovací řetězce**.  Je název `MS_TableConnectionString`.

Tento skript z v rámci SQL Server Management Studio nebo Visual Studio můžete spustit.

### <a name="link-the-database-to-your-app-service"></a>Odkaz databázi do služby App Service
Propojte existující databáze do služby App Service:

* V [Azure Portal], otevřete službu App Service.
* Vyberte **všechna nastavení** -> **datová připojení**.
* Klikněte na **+ přidat**.
* V rozevíracím seznamu vyberte **SQL Database**
* V části **SQL Database**, vyberte existující databázi a potom klikněte na **vyberte**.
* V části **připojovací řetězec**, zadejte uživatelské jméno a heslo pro databázi a potom klikněte na **OK**.
* V **přidat datová připojení** stránky, klikněte na **OK**.

Uživatelské jméno a heslo můžete najít zobrazením připojovací řetězec pro cílovou databázi v migrované mobilních službách.

### <a name="set-up-authentication"></a>Nastavení ověřování
Azure Mobile Apps umožňuje konfigurovat Azure Active Directory, Facebook, Google, Microsoft a Twitter ověřování v rámci služby.  Vlastní ověřování muset být vyvíjeny odděleně.  Odkazovat [Koncepty ověřování] dokumentaci a [rychlý start ověřování] Další informace naleznete v dokumentaci.  

## <a name="updating-clients"></a>Aktualizace mobilní klienty
Jakmile budete mít provozní back-endu mobilní aplikace, můžete pracovat na novou verzi vaší aplikace klienta, který ji využívá. Mobile Apps zahrnuje taky novou verzi klientské sady SDK a podobně jako při upgradu serveru výše, musíte odebrat všechny odkazy na Mobile Services SDK před instalací verze mobilní aplikace.

Jedním z hlavních změn mezi verzemi je, že konstruktory už nevyžadují klíč aplikace.
Nyní jednoduše předáváte v adrese URL vaší mobilní aplikace. Například u klientů .NET `MobileServiceClient` je konstruktor:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Další informace o instalaci nové sady SDK a použití novou strukturu prostřednictvím následujících odkazů:

* [Verze systému Android 2.2 nebo vyšší](app-service-mobile-android-how-to-use-client-library.md)
* [iOS verze 3.0.0 nebo novější](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) verze 2.0.0 nebo novější](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova verze 2.0 nebo novější](app-service-mobile-cordova-how-to-use-client-library.md)

Pokud vaše aplikace provádí pomocí nabízených oznámení, poznamenejte si konkrétní registrace – pokyny pro jednotlivé platformy, jako byly některé změny tam také.

Až budete mít novou verzi klienta, která je připravená, vyzkoušejte si to na upgradovaný server projektu. Po ověření, že to funguje, můžete vydat novou verzi vaší aplikace pro zákazníky. Nakonec po vaši zákazníci jste využili příležitost dobře a získat tyto aktualizace, můžete odstranit Mobile Services verze vaší aplikace. V tomto okamžiku můžete zcela upgradovaly na App Service Mobile App pomocí nejnovější sadě SDK serveru Mobile Apps.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Co jsou Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Ceny služeb App Service]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Koncepty ověřování]: ../app-service/app-service-authentication-overview.md
[Rychlý start ověřování]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
