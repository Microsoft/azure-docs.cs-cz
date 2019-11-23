---
title: Jak pracovat s back-end serverem sady Node. js pro Mobile Apps | Microsoft Docs
description: Naučte se pracovat s back-end serverem sady Node. js pro Azure App Service Mobile Apps.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a3883d233bd621607ec724e0c85734b508195340
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388661"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Jak používat Mobile Apps Node. js SDK

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
>  Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

Tento článek obsahuje podrobné informace a příklady, které ukazují, jak pracovat s back-end Node. js ve funkci Mobile Apps Azure App Service.

## <a name="Introduction"></a>Úvod

Mobile Apps poskytuje možnost přidat do webové aplikace webové rozhraní API pro přístup k datům optimalizované pro mobilní zařízení. Sada Mobile Apps SDK je k dispozici pro webové aplikace v ASP.NET a Node. js. Sada SDK poskytuje tyto operace:

* Operace s tabulkou (čtení, vložení, aktualizace, odstranění) pro přístup k datům
* Vlastní operace rozhraní API

Obě operace poskytují ověřování napříč všemi zprostředkovateli identity, kteří Azure App Service povolují. Mezi tyto poskytovatele patří poskytovatelé sociálních identit, jako je Facebook, Twitter, Google a Microsoft, a také Azure Active Directory pro podnikovou identitu.

Ukázky pro každý případ použití najdete v [Adresář ukázek na GitHubu].

## <a name="supported-platforms"></a>Podporované platformy

Sada Mobile Apps Node. js SDK podporuje aktuální verzi uzlu LTS a novější. V současné době je nejnovější verze LTS Node v 4.5.0. Jiné verze uzlu mohou fungovat, ale nejsou podporovány.

Sada Mobile Apps Node. js SDK podporuje dva ovladače databáze: 

* Ovladač Node-MSSQL podporuje Azure SQL Database a místní instance SQL Server.  
* Ovladač sqlite3 podporuje pouze databáze SQLite pouze v jediné instanci.

### <a name="howto-cmdline-basicapp"></a>Vytvoření základního back-endu Node. js pomocí příkazového řádku

Každý Mobile Apps back-end Node. js začíná jako aplikace ExpressJS. ExpressJS je nejoblíbenější rozhraní webové služby, které je k dispozici pro Node. js. Základní aplikaci [Express] můžete vytvořit následujícím způsobem:

1. V příkazu nebo okně PowerShellu vytvořte adresář pro váš projekt:

        mkdir basicapp

1. Spusťte `npm init` pro inicializaci struktury balíčku:

        cd basicapp
        npm init

   Příkaz `npm init` požádá o sadu otázek pro inicializaci projektu. Podívejte se na příklad výstupu:

   ![Výstup inicializace npm][0]

1. Nainstalujte knihovny `express` a `azure-mobile-apps` z úložiště npm:

        npm install --save express azure-mobile-apps

1. Vytvořte soubor App. js pro implementaci základního mobilního serveru:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Tato aplikace vytvoří mobilní optimalizované webové rozhraní API s jedním koncovým bodem (`/tables/TodoItem`), které poskytuje neověřený přístup k základnímu úložišti dat SQL pomocí dynamického schématu. Je vhodný pro následující rychlé starty klientské knihovny:

* [Rychlý Start pro klienta Android]
* [Rychlý Start klienta Apache Cordova]
* [rychlý Start klienta iOS]
* [Rychlý Start klienta Windows Storu]
* [Rychlé spuštění klienta Xamarin.iOS]
* [Rychlý Start pro klienta Xamarin. Android]
* [Rychlé spuštění klienta Xamarin.Forms]

Kód pro tuto základní aplikaci najdete v [Ukázka basicapp na GitHubu].

### <a name="howto-vs2015-basicapp"></a>Vytvoření back-endu Node. js pomocí sady Visual Studio 2015

Visual Studio 2015 vyžaduje rozšíření pro vývoj aplikací Node. js v rámci integrovaného vývojového prostředí (IDE). Začněte tím, že nainstalujete [Node. js Tools 1,1 pro Visual Studio]. Po dokončení instalace vytvořte aplikaci Express 4. x:

1. Otevřete dialogové okno **Nový projekt** (ze **souboru** > **Nový** > **projekt**).
1. Rozbalte **šablony** > **JavaScriptu** > **Node. js**.
1. Vyberte **základní aplikaci Azure Node. js Express 4**.
1. Zadejte název projektu. Vyberte **OK**.

   ![Visual Studio 2015 – nový projekt][1]
1. Klikněte pravým tlačítkem na uzel **npm** a vyberte **instalovat nové balíčky npm**.
1. Po vytvoření první aplikace Node. js možná budete muset katalog npm aktualizovat. V případě potřeby vyberte **aktualizovat** .
1. Do vyhledávacího pole zadejte **Azure-Mobile-Apps** . Vyberte balíček **Azure-Mobile-Apps 2.0.0** a pak vyberte **instalovat balíček**.

   ![Nainstalovat nové balíčky npm][2]
1. Vyberte **Zavřít**.
1. Otevřete soubor App. js a přidejte podporu pro sadu Mobile Apps SDK. Na řádku 6 v dolní části knihovny `require` příkazy přidejte následující kód:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Přibližně na řádku 27 za jinými příkazy `app.use` přidejte následující kód:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Uložte soubor.

1. Buď spusťte aplikaci místně (rozhraní API se obsluhuje `http://localhost:3000`), nebo ho publikujte do Azure.

### <a name="create-node-backend-portal"></a>Vytvořte back-end Node. js pomocí Azure Portal

Můžete vytvořit Mobile Apps back-end přímo v [Azure Portal]. Pomocí kurzu [Vytvoření mobilní aplikace](app-service-mobile-ios-get-started.md) můžete buď provést následující kroky, nebo vytvořit klienta a Server společně. Tento kurz obsahuje zjednodušenou verzi těchto pokynů a je nejvhodnější pro projekty nástroje pro kontrolu konceptu.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpátky **v podokně Začínáme v části** **Vytvoření rozhraní API pro vytváření tabulek**vyberte **Node. js** jako svůj back-end jazyk.
Zaškrtněte políčko pro potvrzuji **, že tato možnost přepíše veškerý obsah webu**, a pak vyberte **vytvořit tabulku TodoItem**.

### <a name="download-quickstart"></a>Stažení back-endu pro rychlé zprovoznění Node. js pomocí Gitu

Když vytvoříte Mobile Apps back-end Node. js pomocí podokna v **rychlém startu** portálu, vytvoří se pro vás projekt Node. js a nasadí se na váš web. Na portálu můžete přidat tabulky a rozhraní API a upravovat soubory kódu pro back-end Node. js. Můžete také použít různé nástroje pro nasazení ke stažení back-endového projektu, abyste mohli přidávat nebo upravovat tabulky a rozhraní API a pak projekt znovu publikovat. Další informace najdete v [Průvodce nasazením Azure App Service].

Následující postup používá úložiště Git ke stažení kódu projektu pro rychlý Start:

1. Pokud jste to ještě neudělali, nainstalujte Git. Postup potřebný k instalaci Gitu se liší mezi operačními systémy. Informace o distribucích specifických pro konkrétní operační systém a pokyny k instalaci najdete v tématu [instalace Gitu](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Další informace najdete v tématu [Příprava úložiště](../app-service/deploy-local-git.md#prepare-your-repository) pro povolení úložiště Git pro váš back-end Web. Poznamenejte si uživatelské jméno a heslo nasazení.
3. V podokně pro back-end Mobile Apps si poznamenejte nastavení **adresy URL pro klonování Gitu** .
4. Spusťte příkaz `git clone` pomocí adresy URL klonu Git. V případě potřeby zadejte své heslo, jako v následujícím příkladu:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Přejděte do místního adresáře (`/todolist` v předchozím příkladu) a Všimněte si, že byly staženy soubory projektu. Vyhledejte soubor TodoItem. JSON v adresáři `/tables`. Tento soubor definuje oprávnění pro tabulku. Vyhledejte také soubor TodoItem. js ve stejném adresáři. Definuje skripty operace CRUD pro tabulku.
6. Po provedení změn v souborech projektu spusťte následující příkazy, které přidejte, potvrďte a nahrajte změny webu:

        $ git commit -m "updated the table script"
        $ git push origin master

   Když do projektu přidáte nové soubory, musíte nejprve spustit příkaz `git add .`.

Lokalita se znovu publikuje při každém odeslání nové sady potvrzení do lokality.

### <a name="howto-publish-to-azure"></a>Publikování back-endu Node. js do Azure

Microsoft Azure poskytuje mnoho mechanismů pro publikování Mobile Apps back-endu Node. js do služby Azure. Mezi tyto mechanismy patří nástroje pro nasazení integrované do sady Visual Studio, nástroje příkazového řádku a možnosti průběžného nasazování založené na správě zdrojového kódu. Další informace najdete v [Průvodce nasazením Azure App Service].

Azure App Service má konkrétní Rady pro aplikace Node. js, které byste si měli projít před publikováním back-endu:

* [Zadejte verzi uzlu]
* Jak [používat moduly uzlů]

### <a name="howto-enable-homepage"></a>Povolit domovskou stránku vaší aplikace

Mnoho aplikací je kombinací webových a mobilních aplikací. K kombinování těchto dvou omezujících vlastností můžete použít ExpressJS Framework. V některých případech však budete chtít implementovat pouze mobilní rozhraní. Je vhodné zadat domovskou stránku, aby bylo zajištěno, že je služba App Service v provozu. Můžete buď zadat vlastní domovskou stránku, nebo povolit dočasnou domovskou stránku. Chcete-li povolit dočasnou domovskou stránku, použijte následující kód k vytvoření instance Mobile Apps:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Pokud chcete tuto možnost dostupnou pouze při vývoji místně, můžete toto nastavení přidat do souboru azureMobile. js.

## <a name="TableOperations"></a>Operace s tabulkami

Sada SDK serveru Azure-Mobile-Apps pro Node. js nabízí mechanismy pro vystavení datových tabulek uložených v Azure SQL Database jako webové rozhraní API. Poskytuje pět operací:

| Operace | Popis |
| --- | --- |
| ZÍSKAT/Tables/*TableName* |Načte všechny záznamy v tabulce. |
| ZÍSKAT/Tables/*TableName*/: ID |Načte konkrétní záznam v tabulce. |
| POST/Tables/*TableName* |Vytvoří v tabulce záznam. |
| PATCH/Tables/*TableName*/: ID |Aktualizuje záznam v tabulce. |
| Odstranit/Tables/*TableName*/: ID |Odstraní záznam v tabulce. |

Toto webové rozhraní API podporuje [OData] a rozšiřuje schéma tabulky, aby podporovalo [synchronizaci offline dat].

### <a name="howto-dynamicschema"></a>Definování tabulek pomocí dynamického schématu

Předtím, než budete moci použít tabulku, je nutné ji definovat. Můžete definovat tabulky pomocí statického schématu (kde můžete definovat sloupce ve schématu) nebo dynamicky (kde sada SDK řídí schéma na základě příchozích požadavků). Kromě toho můžete určit konkrétní aspekty webového rozhraní API přidáním kódu jazyka JavaScript do definice.

V rámci osvědčeného postupu byste měli definovat každou tabulku v souboru JavaScriptu v adresáři `tables` a potom pomocí metody `tables.import()` importovat tabulky. Při rozšiřování ukázky základní aplikace byste měli upravit soubor App. js:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Definujte tabulku v./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabulky ve výchozím nastavení používají dynamické schéma. Pokud chcete dynamické schéma globálně vypnout, nastavte v Azure Portal nastavení `MS_DynamicSchema` aplikace na false.

Úplný příklad najdete v [Ukázka TODO na GitHubu].

### <a name="howto-staticschema"></a>Definování tabulek pomocí statického schématu

Můžete explicitně definovat sloupce, které se zveřejňují přes webové rozhraní API. Sada Node. js SDK pro Azure-Mobile-Apps automaticky přidá do seznamu, který je k dispozici pro synchronizaci offline dat, všechny další sloupce, které jsou potřeba. Například klientské aplikace pro rychlý Start vyžadují tabulku se dvěma sloupci: `text` (řetězec) a `complete` (logická hodnota).  
Tabulka může být definována v souboru JavaScriptu definice tabulky (umístěný v adresáři `tables`) následujícím způsobem:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Definujete-li tabulky staticky, je také nutné zavolat metodu `tables.initialize()` pro vytvoření schématu databáze při spuštění. Metoda `tables.initialize()` vrátí [Slíbit] , aby webová služba nesloužila požadavkům před inicializací databáze.

### <a name="howto-sqlexpress-setup"></a>Použití SQL Server Express jako vývojového úložiště dat na místním počítači

Sada Mobile Apps Node. js SDK nabízí tři možnosti pro poskytování dat mimo pole:

* Použijte ovladač **paměti** k poskytnutí netrvalého úložiště příkladů.
* Pomocí ovladače **MSSQL** poskytněte SQL Server Express úložiště dat pro vývoj.
* Pomocí ovladače **MSSQL** poskytněte Azure SQL Database úložiště dat pro produkční prostředí.

Mobile Apps Node. js SDK používá [balíček Node. js pro MSSQL] k navázání a použití připojení ke SQL Server Express i SQL Database. Tento balíček vyžaduje, abyste povolili připojení TCP na instanci SQL Server Express.

> [!TIP]
> Ovladač paměti neposkytuje kompletní sadu funkcí pro testování. Pokud chcete místně testovat svůj back-end, doporučujeme použít SQL Server Express úložiště dat a ovladač MSSQL.

1. Stáhněte a nainstalujte [Microsoft SQL Server 2014 Express]. Ujistěte se, že nainstalujete SQL Server 2014 Express s edicemi Tools. Pokud explicitně nepožadujete podporu 64, verze 32 při spuštění spotřebuje méně paměti.
1. Spusťte SQL Server 2014 Configuration Manager:

   a. Rozbalte uzel **SQL Server konfigurace sítě** v nabídce stromu.

   b. Vyberte **protokoly pro SQLEXPRESS**.

   c. Klikněte pravým tlačítkem na **TCP/IP** a vyberte **Povolit**. V automaticky otevíraném okně vyberte **OK** .

   d. Klikněte pravým tlačítkem na **TCP/IP** a vyberte **vlastnosti**.

   e. Vyberte kartu **IP adresy** .

   f. Vyhledejte uzel **IPAll** . Do pole **port TCP** zadejte **1433**.

      ![Konfigurace SQL Server Express pro protokol TCP/IP][3]

   g. Vyberte **OK**. V automaticky otevíraném okně vyberte **OK** .

   h. V nabídce stromu vyberte **SQL Server služby** .

   i. Klikněte pravým tlačítkem na **SQL Server (SQLEXPRESS)** a vyberte **restartovat**.

   j. Zavřete Configuration Manager SQL Server 2014.

1. Spusťte Management Studio SQL Server 2014 a připojte se k místní instanci SQL Server Express:

   1. V Průzkumník objektů klikněte pravým tlačítkem na svou instanci a vyberte **vlastnosti**.
   1. Vyberte stránku **zabezpečení** .
   1. Ujistěte se, že je vybrána možnost **SQL Server a režim ověřování systému Windows** .
   1. Vyberte **OK**.

      ![Konfigurace ověřování SQL Server Express][4]
   1. V Průzkumník objektů rozbalte **přihlašovací údaje** > **zabezpečení** .
   1. Klikněte pravým tlačítkem na **přihlašovací údaje** a vyberte **nové přihlašovací údaje**.
   1. Zadejte přihlašovací jméno. Vyberte **Ověřování SQL Serveru**. Zadejte heslo a potom zadejte stejné heslo do pole **Potvrdit heslo**. Heslo musí splňovat požadavky Windows na složitost.
   1. Vyberte **OK**.

      ![Přidat nového uživatele do SQL Server Express][5]
   1. Klikněte pravým tlačítkem na nové přihlášení a vyberte **vlastnosti**.
   1. Vyberte stránku **role serveru** .
   1. Zaškrtněte políčko role serveru **dbcreator** .
   1. Vyberte **OK**.
   1. Zavřete Management Studio SQL Server 2015.

Ujistěte se, že jste si poznamenali uživatelské jméno a heslo, které jste vybrali. V závislosti na požadavcích vaší databáze možná budete muset přiřadit další role serveru nebo oprávnění.

Aplikace Node. js přečte proměnnou prostředí `SQLCONNSTR_MS_TableConnectionString` pro připojovací řetězec pro tuto databázi. Tuto proměnnou můžete nastavit ve svém prostředí. K nastavení této proměnné prostředí můžete použít například PowerShell:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Přístup k databázi prostřednictvím připojení TCP/IP. Zadejte uživatelské jméno a heslo pro připojení.

### <a name="howto-config-localdev"></a>Konfigurace projektu pro místní vývoj

Mobile Apps přečte soubor JavaScriptu s názvem *azureMobile. js* z místního systému souborů. Nepoužívejte tento soubor ke konfiguraci sady Mobile Apps SDK v produkčním prostředí. Místo toho použijte **nastavení aplikace** v [Azure Portal].

Soubor azureMobile. js by měl exportovat objekt konfigurace. Nejběžnější nastavení:

* Nastavení databáze
* Nastavení protokolování diagnostiky
* Alternativní nastavení CORS

Tento příklad souboru **azureMobile. js** implementuje předchozí nastavení databáze:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

Doporučujeme přidat **azureMobile. js** do souboru **. gitignore** (nebo jiného souboru pro ignorování zdrojového kódu), abyste zabránili ukládání hesel do cloudu. V **nastavení aplikace** vždy nakonfigurujte nastavení výroby v rámci [Azure Portal].

### <a name="howto-appsettings"></a>Konfigurace nastavení aplikace pro mobilní aplikaci

Většina nastavení v souboru azureMobile. js má ekvivalentní nastavení aplikace v [Azure Portal]. Pomocí následujícího seznamu nakonfigurujte aplikaci v **nastavení aplikace**:

| Nastavení aplikace | nastavení azureMobile. js | Popis | Platné hodnoty |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |jméno |Název aplikace |řetězec |
| **MS_MobileLoggingLevel** |Logging. Level |Minimální úroveň protokolu pro zprávy, které se mají protokolovat |Chyba, upozornění, informace, podrobný, ladění, Silly |
| **MS_DebugMode** |ladí |Povolí nebo zakáže režim ladění. |true, false |
| **MS_TableSchema** |data.schema |Výchozí název schématu pro tabulky SQL |String (výchozí: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Povolí nebo zakáže režim ladění. |true, false |
| **MS_DisableVersionHeader** |verze (nastavená na undefined) |Zakáže hlavičku X-ZUMO-server-Version. |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Zakáže kontrolu verze rozhraní API klienta. |true, false |

Nastavení aplikace nastavíte takto:

1. Přihlásit se na [Azure Portal].
1. Vyberte **všechny prostředky** nebo **App Services**a pak vyberte název mobilní aplikace.
1. Ve výchozím nastavení se otevře podokno **Nastavení** . Pokud tomu tak není, vyberte **Nastavení**.
1. V nabídce **Obecné** vyberte **nastavení aplikace**.
1. Přejděte do části **nastavení aplikace** .
1. Pokud už nastavení aplikace existuje, vyberte hodnotu nastavení aplikace a upravte hodnotu.
   Pokud nastavení aplikace neexistuje, zadejte nastavení aplikace v poli **klíč** a hodnotu v poli **hodnota** .
1. Vyberte **Uložit**.

Změna většiny nastavení aplikace vyžaduje restart služby.

### <a name="howto-use-sqlazure"></a>Použití SQL Database jako úložiště produkčních dat

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Použití Azure SQL Database jako úložiště dat je identické napříč všemi Azure App Service typy aplikací. Pokud jste to ještě neudělali, postupujte podle těchto kroků a vytvořte Mobile Apps back-end:

1. Přihlásit se na [Azure Portal].
1. V levém horním rohu okna vyberte tlačítko **+ nový** > **web a mobilní zařízení** > **mobilní aplikace**a zadejte název pro Mobile Apps back end.
1. Do pole **Skupina prostředků** zadejte stejný název jako vaše aplikace.
1. Je vybrán výchozí plán App Service. Pokud chcete změnit plán App Service:

   a. Vyberte **App Service plán** >  **+ vytvořit nové**.

   b. Zadejte název nového plánu App Service a vyberte příslušné umístění.

   c. Vyberte odpovídající cenovou úroveň pro službu. Výběrem **Zobrazit vše** zobrazíte další cenové možnosti, například **Free** a **Shared**.

   d. Klikněte na tlačítko **vyberte** tlačítko.

   e. Zpátky v podokně **App Service plán** vyberte **OK**.
1. Vyberte **Vytvořit**.

Zřízení Mobile Apps back-endu může trvat několik minut. Po zřízení back-endu Mobile Apps se portál otevře v podokně **nastavení** Mobile Apps back-endu.

Můžete se rozhodnout buď připojit existující databázi SQL k Mobile Apps back-endu, nebo vytvořit novou databázi SQL. V této části vytvoříme databázi SQL.

> [!NOTE]
> Pokud již máte databázi ve stejném umístění jako Mobile Apps back-end, můžete místo toho vybrat možnost **použít existující databázi** a pak vybrat tuto databázi. Nedoporučujeme používat databázi v jiném umístění kvůli vyšší latenci.

1. V novém Mobile Apps back-endu vyberte **nastavení** > **mobilní aplikace** > **data** >  **+ Přidat**.
1. V podokně **přidat datové připojení** vyberte **SQL Database-konfigurovat požadovaná nastavení** > **vytvořit novou databázi**. Do pole **název** zadejte název nové databáze.
1. Vyberte možnost **Server**. V podokně **Nový server** zadejte jedinečný název serveru do pole **název serveru** a zadejte vhodné přihlašovací jméno a heslo správce serveru. Ujistěte se, že je vybraná možnost **Povolení služeb Azure pro přístup k serveru** . Vyberte **OK**.

   ![Vytvoření databáze Azure SQL][6]
1. V podokně **Nová databáze** vyberte **OK**.
1. Zpátky v podokně **přidat datové připojení** vyberte **připojovací řetězec**a zadejte přihlašovací jméno a heslo, které jste zadali při vytváření databáze. Pokud používáte existující databázi, zadejte přihlašovací údaje pro tuto databázi. Vyberte **OK**.
1. Vraťte se znovu v podokně **přidat datové připojení** , vyberte **OK** a vytvořte databázi.

<!--- END OF ALTERNATE INCLUDE -->

Vytvoření databáze může trvat několik minut. Pomocí oblasti **oznámení** můžete monitorovat průběh nasazení. Neprovádějte postup, dokud se databáze úspěšně nasadí. Po nasazení databáze se vytvoří připojovací řetězec pro instanci SQL Database v nastavení back-endu Mobile Apps aplikace. Toto nastavení aplikace můžete zobrazit v **nastavení** > **nastavení aplikace** > **připojovacích řetězců**.

### <a name="howto-tables-auth"></a>Vyžadovat ověřování pro přístup k tabulkám

Pokud chcete App Service ověřování použít s koncovým bodem `tables`, musíte nejdřív nakonfigurovat App Service ověřování v [Azure Portal] . Další informace najdete v Průvodci konfigurací poskytovatele identity, kterého chcete použít:

* [Konfigurace ověřování Azure Active Directory]
* [Konfigurace ověřování na Facebooku]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Konfigurace ověřování na Twitteru]

Každá tabulka má vlastnost Access, kterou můžete použít k řízení přístupu k tabulce. Následující ukázka znázorňuje staticky definovanou tabulku s požadovaným ověřováním.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Vlastnost Access může mít jednu ze tří hodnot:

* *anonymní* označuje, že klientská aplikace může číst data bez ověřování.
* *ověřováno* znamená, že klientská aplikace musí v žádosti odeslat platný ověřovací token.
* *disabled* označuje, že tato tabulka je aktuálně zakázaná.

Pokud vlastnost Access není definovaná, je povolený neověřený přístup.

### <a name="howto-tables-getidentity"></a>Použití ověřovacích deklarací s tabulkami
Můžete nastavit různé deklarace identity, které jsou požadovány při nastavení ověřování. Tyto deklarace nejsou obvykle k dispozici prostřednictvím objektu `context.user`. Můžete je však načíst pomocí metody `context.user.getIdentity()`. Metoda `getIdentity()` vrací příslib, který se překládá na objekt. Objekt je označen metodou ověřování (`facebook`, `google`, `twitter`, `microsoftaccount`nebo `aad`).

Pokud jste například nastavili ověřování účet Microsoft a požádáte o deklaraci e-mailových adres, můžete k záznamu přidat e-mailovou adresu pomocí následujícího řadiče tabulky:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Pokud chcete zjistit, jaké deklarace identity jsou k dispozici, použijte webový prohlížeč k zobrazení `/.auth/me`ho koncového bodu vašeho webu.

### <a name="howto-tables-disabled"></a>Zakázat přístup ke konkrétním operacím tabulky

Kromě zobrazování v tabulce lze vlastnost Access použít k řízení jednotlivých operací. K dispozici jsou čtyři operace:

* `read` je operace GET RESTful v tabulce.
* `insert` je operace POST RESTful v tabulce.
* `update` je operace opravy RESTful v tabulce.
* `delete` je operace odstranění RESTful v tabulce.

Můžete například chtít poskytnout neověřenou tabulku, která je jen pro čtení:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Úprava dotazu, který se používá při operacích s tabulkou

Běžným požadavkem na operace s tabulkou je poskytnutí omezeného zobrazení dat. Můžete například zadat tabulku, která je označena ID ověřeného uživatele, aby bylo možné číst nebo aktualizovat pouze vlastní záznamy. Tato funkce poskytuje následující definici tabulky:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

Operace, které obvykle spouštějí dotaz, mají vlastnost dotazu, kterou lze upravit pomocí klauzule `where`. Vlastnost dotazu je objekt [QueryJS] , který slouží k převodu dotazu OData na něco, co může zpracování back-endu dat zpracovat. Pro jednoduché případy rovnosti (například předchozí) můžete použít mapu. Můžete také přidat konkrétní klauzule SQL:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurace obnovitelného odstranění tabulky

Obnovitelné odstranění ve skutečnosti neodstraní záznamy. Místo toho je označí jako odstraněné v rámci databáze tím, že nastaví odstraněný sloupec na hodnotu true. Sada Mobile Apps SDK automaticky odebere záznamy s odstraněnou z výsledků, pokud mobilní klientská sada SDK nepoužívá `IncludeDeleted()`. Chcete-li nakonfigurovat tabulku pro obnovitelné odstranění, nastavte vlastnost `softDelete` v souboru definice tabulky:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Měli byste vytvořit mechanismus pro odstraňování záznamů: klientská aplikace, Webová úloha, funkce Azure nebo vlastní rozhraní API.

### <a name="howto-tables-seeding"></a>Osazení databáze daty

Když vytváříte novou aplikaci, možná budete chtít naplnit tabulku daty. To můžete provést v souboru JavaScriptu definice tabulky následujícím způsobem:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Dosazení dat probíhá pouze v případě, že jste k vytvoření tabulky použili sadu Mobile Apps SDK. Pokud tabulka v databázi již existuje, do tabulky nebudou vložena žádná data. Pokud je dynamické schéma zapnuté, schéma se odvozuje od dosazených dat.

Doporučujeme, abyste explicitně volali metodu `tables.initialize()` pro vytvoření tabulky při spuštění služby.

### <a name="Swagger"></a>Povolit podporu Swagger
Mobile Apps obsahuje integrovanou podporu [Swagger] . Pokud chcete povolit podporu Swagger, nejdřív nainstalujte Swagger – uživatelské rozhraní jako závislost:

    npm install --save swagger-ui

Pak můžete povolit podporu Swagger v konstruktoru Mobile Apps:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

V edicích pro vývoj pravděpodobně budete chtít povolit pouze podporu Swagger. To můžete provést pomocí nastavení aplikace `NODE_ENV`:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Koncový bod `swagger` se nachází na adrese http://*yoursite*. azurewebsites.NET/Swagger. K uživatelskému rozhraní Swagger můžete přistupovat prostřednictvím koncového bodu `/swagger/ui`. Pokud se rozhodnete, že budete vyžadovat ověřování v celé aplikaci, Swagger vytvoří chybu. Nejlepších výsledků dosáhnete, pokud povolíte neověřené požadavky v Azure App Service ověřování/nastavení autorizace a pak pomocí vlastnosti `table.access` ovládací prvek ověřování.

Můžete také přidat možnost Swagger do souboru azureMobile. js, pokud chcete, aby byla podpora Swagger jenom pro vývoj místně.

## <a name="a-namepushpush-notifications"></a><a name="push"/>nabízených oznámení

Mobile Apps se integruje s Azure Notification Hubs, takže můžete odesílat cílené nabízená oznámení milionům zařízení na všech hlavních platformách. Pomocí Notification Hubs můžete odesílat nabízená oznámení do zařízení se systémy iOS, Android a Windows. Další informace o všech tom, co můžete s Notification Hubs provádět, najdete v tématu [Notification Hubs Overview](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Odesílání nabízených oznámení

Následující kód ukazuje, jak použít objekt `push` k odesílání nabízených oznámení všesměrového vysílání do registrovaných zařízení se systémem iOS:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Vytvořením nabízené registrace šablony z klienta můžete místo toho poslat šablonu nabízenou oznámení do zařízení na všech podporovaných platformách. Následující kód ukazuje, jak odeslat oznámení šablony:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>Odesílání nabízených oznámení ověřenému uživateli pomocí značek
Když ověřený uživatel zaregistruje nabízená oznámení, značka ID uživatele se automaticky přidá k registraci. Pomocí této značky můžete odesílat nabízená oznámení na všechna zařízení zaregistrovaná konkrétním uživatelem. Následující kód Získá identifikátor SID uživatele, který vytváří požadavek, a pošle jim nabízené oznámení pro každé registraci zařízení pro tohoto uživatele:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Při registraci nabízených oznámení z ověřeného klienta se ujistěte, že je ověřování dokončeno, než se pokusíte o registraci.

## <a name="CustomAPI"></a>Vlastní rozhraní API

### <a name="howto-customapi-basic"></a>Definování vlastního rozhraní API

Kromě rozhraní API pro přístup k datům prostřednictvím koncového bodu `/tables` může Mobile Apps poskytovat vlastní pokrytí rozhraní API. Vlastní rozhraní API jsou definována podobným způsobem jako definice tabulek a mají přístup ke všem stejným zařízením, včetně ověřování.

Pokud chcete použít App Service ověřování s vlastním rozhraním API, musíte nejdřív nakonfigurovat App Service ověřování v [Azure Portal] . Další informace najdete v Průvodci konfigurací poskytovatele identity, kterého chcete použít:

* [Konfigurace ověřování Azure Active Directory]
* [Konfigurace ověřování na Facebooku]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Konfigurace ověřování na Twitteru]

Vlastní rozhraní API jsou definována podobným způsobem jako rozhraní API tabulky:

1. Vytvořte adresář `api`.
1. Vytvořte soubor JavaScriptu definice rozhraní API v adresáři `api`.
1. Použijte metodu import pro import adresáře `api`.

Tady je definice rozhraní prototyp API na základě ukázky základní aplikace, kterou jsme použili dříve:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Pojďme použít ukázkové rozhraní API, které vrátí datum serveru pomocí metody `Date.now()`. Zde je soubor API/Date. js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Každý parametr je jedním ze standardních RESTful operací: GET, POST, PATCH nebo DELETE. Metoda je standardní funkce [Middleware ExpressJS] , která odesílá požadovaný výstup.

### <a name="howto-customapi-auth"></a>Vyžadovat ověřování pro přístup k vlastnímu rozhraní API

Sada Mobile Apps SDK implementuje ověřování stejným způsobem jak pro koncový bod `tables`, tak pro vlastní rozhraní API. Chcete-li přidat ověřování do rozhraní API vyvinutého v předchozí části, přidejte vlastnost `access`:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Můžete také zadat ověřování pro konkrétní operace:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Stejný token, který se používá pro `tables` koncový bod, musí být použit pro vlastní rozhraní API, která vyžadují ověřování.

### <a name="howto-customapi-auth"></a>Zpracovat rozsáhlá nahrávání souborů

Sada Mobile Apps SDK používá [middleware v rámci analyzátoru textu](https://github.com/expressjs/body-parser) k přijímání a dekódování obsahu těla v příspěvku. Je možné předem nakonfigurovat tělo – analyzátor pro příjem většího nahrávání souborů:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

Soubor má před přenosem kódování Base-64. Toto kódování zvyšuje velikost skutečného nahrávání (a velikost, pro kterou je nutné mít účet).

### <a name="howto-customapi-sql"></a>Spustit vlastní příkazy SQL

Sada Mobile Apps SDK umožňuje přístup k celému kontextu prostřednictvím objektu Request. Můžete snadno spustit parametrizované příkazy SQL pro definovaného poskytovatele dat:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>Tém

### <a name="howto-diagnostic-logs"></a>Ladění, diagnostika a řešení potíží s Mobile Apps

Azure App Service poskytuje několik technik ladění a řešení potíží pro aplikace Node. js.
Chcete-li začít s řešením potíží s Mobile Apps back-endu Node. js, přečtěte si následující články:

* [Azure App Service monitorování]
* [Povolit protokolování diagnostiky v Azure App Service]
* [Řešení potíží s Azure App Service v aplikaci Visual Studio]

Aplikace Node. js mají přístup k široké škále nástrojů diagnostického protokolu. Interně Mobile Apps Node. js SDK používá [Winston] k protokolování diagnostiky. Protokolování se automaticky povolí, když povolíte režim ladění nebo nastavíte nastavení aplikace `MS_DebugMode` na hodnotu true v [Azure Portal]. Vygenerované protokoly se zobrazí v diagnostických protokolech v [Azure Portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Rychlý Start pro klienta Android]: app-service-mobile-android-get-started.md
[Rychlý Start klienta Apache Cordova]: app-service-mobile-cordova-get-started.md
[rychlý Start klienta iOS]: app-service-mobile-ios-get-started.md
[Rychlé spuštění klienta Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Rychlý Start pro klienta Xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
[Rychlé spuštění klienta Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Rychlý Start klienta Windows Storu]: app-service-mobile-windows-store-dotnet-get-started.md
[synchronizaci offline dat]: app-service-mobile-offline-data-sync.md
[Konfigurace ověřování Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Konfigurace ověřování na Facebooku]: ../app-service/configure-authentication-provider-facebook.md
[Konfigurace ověřování Google]: ../app-service/configure-authentication-provider-google.md
[Konfigurace ověřování společnosti Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurace ověřování na Twitteru]: ../app-service/configure-authentication-provider-twitter.md
[Průvodce nasazením Azure App Service]: ../app-service/deploy-local-git.md
[Azure App Service monitorování]: ../app-service/web-sites-monitor.md
[Povolit protokolování diagnostiky v Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Řešení potíží s Azure App Service v aplikaci Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Zadejte verzi uzlu]: ../nodejs-specify-node-version-azure-apps.md
[používat moduly uzlů]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Slíbit]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[Ukázka basicapp na GitHubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[Ukázka TODO na GitHubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Adresář ukázek na GitHubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node. js Tools 1,1 pro Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[balíček Node. js pro MSSQL]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Middleware ExpressJS]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
