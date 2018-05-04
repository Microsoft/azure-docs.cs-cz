---
title: Upgrade z mobilní služby Azure App Service - Node.js
description: Zjistěte, jak snadno upgradovat aplikaci Mobile Services pro aplikaci služby mobilní aplikace
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
ms.openlocfilehash: 07ebf23270addc63793278d4e0510c187289b82c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Upgrade existující Mobile Service Node.js Azure App Service
Mobile App Service je nový způsob vytváření mobilních aplikací pomocí Microsoft Azure. Další informace najdete v tématu [co jsou Mobile Apps?].

Tento článek popisuje postup upgradu existující aplikaci back-end Node.js z Azure Mobile Services na nové App Service Mobile Apps. Při provádění tohoto upgradu, aplikace pro Mobile Services můžete nadále fungovat.  Pokud je třeba upgradovat aplikace back-end Node.js, podívejte se na [upgradu vaší .NET Mobile Services](app-service-mobile-net-upgrading-from-mobile-services.md).

Při upgradu mobilního back-endu do služby Azure App Service má přístup ke všem funkcím služby App Service a se účtují podle [služby App Service – ceny], není Mobile Services ceny.

## <a name="migrate-vs-upgrade"></a>Migrace a upgrade
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Je doporučeno, které [provést migraci](app-service-mobile-migrating-from-mobile-services.md) před zahájením upgradu. Tímto způsobem můžete umístit obě verze vaší aplikace stejném plánu služby App Service a způsobit bez dalších nákladů.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Vylepšení v serveru mobilní aplikace Node.js SDK
Upgrade na novou [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) obsahuje mnoho vylepšení, včetně:

* Na základě [Express framework](http://expressjs.com/en/index.html), novou sadu SDK uzlu je šedé – a navržené tak, aby nové verze uzlu jako se. Můžete přizpůsobit chování aplikace s Express middleware.
* Významné zlepšení výkonu ve srovnání s Mobile Services SDK.
* Web můžete hostovat spolu s vaší mobilní back-end; Podobně je snadno přidat sadu SDK Azure Mobile pro všechny existující express.v4 aplikace.
* Vytvořené pro vývoj pro různé platformy a místní, Mobile Apps SDK může být vyvinuté a spustit místně na platformách Windows, Linux a OSX. Nyní je snadno použitelný běžné technik vývoje uzlu, jako je spuštění [téměř jako](https://mochajs.org/) testy před jejich nasazením.

## <a name="overview"></a>Základní přehled upgradu
Chcete-li pomoci při upgradování back-end Node.js, Azure App Service poskytl balíček kompatibility.  Po upgradu budete mít novou lokalitu, která se dá nasadit na nový Web App Service.

Klientem Mobile Services SDK jsou **není** kompatibilní s novým serverem Mobile Apps SDK. Chcete-li zajistit kontinuitu služeb pro vaši aplikaci, by neměl publikovat změny v lokalitě aktuálně obsluhující publikované klientů. Místo toho by měla vytvoříte novou mobilní aplikaci, která slouží jako duplicitní. Tuto aplikaci můžete umístit na stejný plán služby App Service účtovány dodatečné finanční náklady.

Pak bude mít dvě verze této aplikace: ten, který zůstává stejný a slouží v zástupné a druhé, které potom můžete upgradovat a cíl s novou verzi klienta publikované aplikace. Můžete přesunout a otestujte svůj kód vaší tempem, ale ujistěte se, že všechny opravy chyb, které použije na obě. Jakmile si myslíte, že požadovaný počet klientských aplikací v zástupné byly aktualizovány na nejnovější verzi, můžete odstranit původní migrovanou aplikaci vyžadujete-li. Ho nebude vynakládá žádné další peněžní, pokud hostované ve stejném plán služby App Service jako mobilní aplikace.

Úplné obrys pro proces upgradu je následující:

1. Stáhněte si existující Mobile Service (migrované) Azure.
2. Převeďte projekt na mobilní aplikace Azure pomocí balíčku pro kompatibilitu.
3. Opravte případné rozdíly (například nastavení ověřování).
4. Nasazení projektu převedený mobilní aplikace Azure do nové služby App Service.
5. Vydání nové verze klienta aplikace, který používá nové mobilní aplikace.
6. (Volitelné) Odstraňte původní aplikace migrované mobilní služby.

Odstranění může dojít, když nevidíte přenosy dat na původní migrované mobilní službě.

## <a name="install-npm-package"></a> Nainstalujte požadavky
[Uzel] musíte nainstalovat na místním počítači.  Musíte také nainstalovat balíček kompatibility.  Po instalaci uzlu spuštěním následujícího příkazu z nové cmd nebo příkazovém řádku prostředí PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Získat skripty Azure Mobile Services
* Přihlaste se k [portálu Azure].
* Pomocí **všechny prostředky** nebo **App Services**, najít váš web Mobile Services.
* V rámci lokality, klikněte na **nástroje** -> **Kudu** -> **přejděte** otevřete Kudu lokality.
* Klikněte na **ladění konzoly** -> **prostředí PowerShell** otevřete konzolu pro ladění.
* Přejděte na `site/wwwroot/App_Data/config` pak kliknutím na každý adresář
* Klikněte na ikonu stahování vedle `scripts` adresáře.

To bude stahovat skripty ve formátu ZIP.  Vytvořte nový adresář na místním počítači a rozbalte `scripts.ZIP` soubor v adresáři.  Tím se vytvoří `scripts` adresáře.

## <a name="scaffold-app"></a> Vygenerovat nový back-end Azure Mobile Apps
Spusťte následující příkaz z adresáře, která obsahuje adresář skriptů:

```scaffold-mobile-app scripts out```

Tím se vytvoří vygenerované Azure Mobile Apps back-end v `out` adresáře.  Ačkoli to není vyžadováno, je vhodné zkontrolovat `out` adresář do úložiště zdrojového kódu podle svého výběru.

## <a name="deploy-ama-app"></a> Nasazení váš back-end Azure Mobile Apps
Během nasazení budete muset provést následující:

1. Vytvoření nové mobilní aplikace v [portálu Azure].
2. Spustit `createViews.sql` skript v připojené databázi.
3. Připojit databázi, který bude propojen služby Mobile do služby App Service.
4. Žádné další prostředky (například Notification Hubs) propojte nové služby App Service.
5. Nasaďte generovaný kód do nové lokality.

### <a name="create-a-new-mobile-app"></a>Vytvoření nové mobilní aplikace
1. Přihlaste se na [portálu Azure].
2. Klikněte na **+NOVÝ** > **Web + mobilní zařízení** > **Mobilní aplikace** a potom zadejte název back-endu mobilní aplikace .
3. V případě **skupiny prostředků** vyberte existující skupinu prostředků nebo vytvořte novou (použijte stejný název, jaký má aplikace).

    Můžete buď vybrat jiný plán služby App Service nebo vytvořit nový. Další informace o App Services plány a postup vytvoření nového plánu v různých cenových vrstvy a v požadovaném umístění najdete v části [podrobný přehled plánů služby Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. V případě **plánu služby App Service** je vybraný výchozí plán (na [úrovni Standard](https://azure.microsoft.com/pricing/details/app-service/)). Můžete také vybrat jiný plán nebo [vytvořte novou](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Určit nastavení plánu služby App Service [umístění, funkce, náklady a výpočetní prostředky](https://azure.microsoft.com/pricing/details/app-service/) přidružené k vaší aplikaci.

    Až se rozhodnete pro konkrétní plán, klikněte na **Vytvořit**. Tím vytvoříte back-end mobilní aplikace.

### <a name="run-createviewssql"></a>Spustit CreateViews.SQL
Vygenerované aplikaci, která obsahuje soubor s názvem `createViews.sql`.  Tento skript je třeba spustit v cílové databázi.  Nelze získat připojovací řetězec pro cílovou databázi z migrovaných mobilní služby z **nastavení** v části **připojovací řetězce**.  Je název `MS_TableConnectionString`.

Můžete spustit tento skript v SQL Server Management Studio nebo Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Propojení databáze do vaší služby App Service
Připojit existující databázi do vaší služby App Service:

* V [portálu Azure], otevřete App Service.
* Vyberte **všechna nastavení** -> **datová připojení**.
* Klikněte na **+ přidat**.
* V rozevíracím seznamu vyberte **databáze SQL**
* V části **SQL Database**, vyberte existující databázi a pak klikněte na **vyberte**.
* V části **připojovací řetězec**, zadejte uživatelské jméno a heslo pro databázi a potom klikněte na **OK**.
* V **přidat datová připojení** klikněte na **OK**.

Uživatelské jméno a heslo lze nalézt zobrazením připojovací řetězec pro cílovou databázi na migrované služby Mobile.

### <a name="set-up-authentication"></a>Nastavení ověřování
Azure Mobile Apps můžete ke konfiguraci Azure Active Directory, Facebook, Google, Microsoft a ověřování v rámci služby Twitter.  Vlastní ověřování bude muset být vyvinutá samostatně.  Odkazovat [konceptů ověřování] dokumentaci a [rychlý start ověřování] Další informace naleznete v dokumentaci.  

## <a name="updating-clients"></a>Aktualizace mobilních klientů
Až budete mít provozní back-end mobilní aplikace, můžete pracovat na novou verzi klientskou aplikaci, která se využívá. Mobilní aplikace také zahrnuje novou verzi klienta sady SDK a podobně jako u upgrade serveru výše, budete muset odebrat všechny odkazy na sady Mobile Services SDK před instalací verze mobilní aplikace.

Jednou z hlavních změn mezi verzemi je, že konstruktorů už nebudou potřebovat klíčem aplikace.
Nyní jednoduše předáte v adrese URL mobilní aplikace. Například na klientů .NET `MobileServiceClient` konstruktor je nyní:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Další informace o instalaci nové sady SDK a použití nového strukturu přes odkazy níže:

* [Verzi systému Android 2.2 nebo vyšší](app-service-mobile-android-how-to-use-client-library.md)
* [iOS verze 3.0.0 nebo novější](app-service-mobile-ios-how-to-use-client-library.md)
* [Rozhraní .NET (Windows nebo Xamarin) verze 2.0.0 nebo novější](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova verze 2.0 nebo novější](app-service-mobile-cordova-how-to-use-client-library.md)

Pokud vaše aplikace provede pomocí nabízených oznámení, poznamenejte si konkrétní registrace pokyny pro každou platformu, protože byly některé změny existuje také.

Pokud máte novou verzi klienta, která je připraveno, vyzkoušejte ji proti projektu upgradovaný server. Po ověření, že bude fungovat, můžete vydat novou verzi vaší aplikace pro zákazníky. Nakonec Jakmile vaši zákazníci jste využili příležitost a získat tyto aktualizace, můžete odstranit Mobile Services verzi vaší aplikace. V tomto okamžiku úplně upgradu na aplikaci služby mobilní aplikace pomocí nejnovější Mobile Apps serveru SDK.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[co jsou Mobile Apps?]: app-service-mobile-value-prop.md
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
[služby App Service – ceny]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[konceptů ověřování]: ../app-service/app-service-authentication-overview.md
[rychlý start ověřování]: app-service-mobile-auth.md

[portálu Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
