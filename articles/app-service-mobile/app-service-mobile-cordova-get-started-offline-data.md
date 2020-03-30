---
title: Povolit offline synchronizaci (Cordova)
description: Přečtěte si, jak používat mobilní aplikaci App Service k ukládání a synchronizaci offline dat ve vaší aplikaci Cordova do mezipaměti.
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5a2d5ec8da5c1a317039e656f6df884a10efe7c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459414"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz představuje funkci offline synchronizace mobilních aplikací Azure pro Cordovu. Offline synchronizace umožňuje koncovým uživatelům&mdash;pracovat se zobrazením,&mdash;přidáváním nebo úpravou dat mobilní aplikací, i když neexistuje žádné síťové připojení. Změny jsou uloženy v místní databázi.  Jakmile je zařízení opět online, tyto změny se synchronizují se vzdálenou službou.

Tento výukový program je založen na řešení rychlého startu Cordova pro mobilní aplikace, které vytvoříte po dokončení výukového programu [Apache Cordova quick start]. V tomto kurzu aktualizujete řešení rychlého startu a přidáte offline funkce mobilních aplikací Azure.  V aplikaci také zvýrazníme kód specifický pro offline.

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizace dat v Mobilních aplikacích Azure]. Podrobnosti o využití rozhraní API naleznete v [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Přidání offline synchronizace do řešení rychlého startu
Do aplikace musí být přidán kód synchronizace offline. Offline synchronizace vyžaduje plugin cordova-sqlite-storage, který se automaticky přidá do vaší aplikace, když je do projektu zahrnut plugin Azure Mobile Apps. Projekt Quickstart zahrnuje oba tyto pluginy.

1. V Průzkumníku řešení sady Visual Studio otevřete soubor index.js a nahraďte následující kód

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    s tímto kódem:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Dále nahraďte následující kód:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    s tímto kódem:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Předchozí přidání kódu inicializovat místní úložiště a definovat místní tabulku, která odpovídá hodnoty sloupců používané v back-endu Azure. (Do tohoto kódu není nutné zahrnout všechny hodnoty sloupců.)  Pole `version` je udržováno mobilním back-endem a používá se pro řešení konfliktů.

    Získáte odkaz na kontext synchronizace voláním **getSyncContext**. Kontext synchronizace pomáhá zachovat relace tabulek sledováním a odesíláním `.push()` změn ve všech tabulkách, které klientská aplikace změnila při volání.

3. Aktualizujte adresu URL aplikace na adresu URL aplikace mobilní aplikace.

4. Dále nahraďte tento kód:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    s tímto kódem:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Předchozí kód inicializuje kontext synchronizace a pak volá getSyncTable (místo getTable) získat odkaz na místní tabulku.

    Tento kód používá místní databázi pro všechny operace tabulky vytvoření, čtení, aktualizace a odstranění (CRUD).

    Tato ukázka provádí jednoduché zpracování chyb při konfliktech synchronizace. Skutečná aplikace by zpracování různých chyb, jako jsou podmínky sítě, konflikty serveru a další. Příklady kódu naleznete v [ukázce offline synchronizace].

5. Dále přidejte tuto funkci k provedení skutečné synchronizace.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Můžete se rozhodnout, kdy chcete push změny back-endu mobilní aplikace voláním **syncContext.push()**. Můžete například volat **syncBackend** v obslužné rutině události tlačítka vázané na tlačítko synchronizace.

## <a name="offline-sync-considerations"></a>Důležité informace o synchronizaci offline

V ukázce **push** metoda **syncContext** se nazývá pouze při spuštění aplikace ve funkci zpětného volání pro přihlášení.  V aplikaci reálného světa můžete také tuto funkci synchronizace spustit ručně nebo při změně stavu sítě.

Při vyžádat je spuštěna proti tabulce, která má čekající místní aktualizace sledovány kontextu, že operace vyžádat automaticky aktivuje push. Při aktualizaci, přidávání a vyplňování položek v této ukázce můžete vynechat explicitní **nabízené** volání, protože může být redundantní.

V poskytnutém kódu jsou dotazovány všechny záznamy v tabulce remote totoItem, ale je také možné filtrovat záznamy předáním id dotazu a dotazu k **nabízení**. Další informace najdete v části *Přírůstková synchronizace* v [offline synchronizaci dat v Mobilních aplikacích Azure].

## <a name="optional-disable-authentication"></a>(Nepovinné) Zakázat ověřování

Pokud nechcete nastavit ověřování před testováním offline synchronizace, zakomentujte funkci zpětného volání pro přihlášení, ale nechte kód uvnitř funkce zpětného volání bez komentáře.  Po odecommentování přihlašovacích řádků kód následuje:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Teď se aplikace synchronizuje s back-endem Azure při spuštění aplikace.

## <a name="run-the-client-app"></a>Spuštění klientské aplikace
S offline synchronizace nyní povolena, můžete spustit klientskou aplikaci alespoň jednou na každé platformě naplnit databázi místního úložiště. Později simulujte offline scénář a upravte data v místním obchodě, když je aplikace offline.

## <a name="optional-test-the-sync-behavior"></a>(Nepovinné) Testování chování synchronizace
V této části upravíte projekt klienta tak, aby simuloval offline scénář pomocí neplatné adresy URL aplikace pro back-end. Při přidání nebo změně datových položek jsou tyto změny uchovávány v místním úložišti, ale nejsou synchronizovány s úložištěm dat back-endu, dokud nebude připojení obnoveno.

1. V Průzkumníku řešení otevřete soubor projektu index.js a změňte adresu URL aplikace tak, aby ukazovala na neplatnou adresu URL, například na následující kód:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. V souboru index.html `<meta>` aktualizujte element CSP stejnou neplatnou adresou URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Vytvořte a spusťte klientskou aplikaci a všimněte si, že je zaznamenána výjimka v konzole, když se aplikace pokusí o synchronizaci s back-endem po přihlášení. Všechny nové položky, které přidáte, existují pouze v místním úložišti, dokud nejsou zatlačeny do mobilního back-endu. Klientská aplikace se chová, jako by byla připojena k back-endu.

4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvalé v místním úložišti.

5. (Nepovinné) Pomocí Visual Studia zobrazte tabulku Azure SQL Database a podívejte se, že se data v back-endové databázi nezměnila.

    V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte do databáze v **Azure**->**SQL Databases**. Klepněte pravým tlačítkem myši na databázi a vyberte **otevřít v průzkumníku objektů serveru SQL Server**. Nyní můžete procházet databázový obsah SQL a její obsah.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Nepovinné) Otestujte opětovné připojení k mobilnímu back-endu

V této části znovu připojíte aplikaci k mobilnímu back-endu, který simuluje, že se aplikace vrátí do stavu online. Když se přihlásíte, data se synchronizují s mobilním back-endem.

1. Znovu otevřete soubor index.js a obnovte adresu URL aplikace.
2. Znovu otevřete soubor index index.html a `<meta>` opravte adresu URL aplikace v elementu CSP.
3. Znovu sestavit a spustit klientskou aplikaci. Aplikace se pokusí synchronizovat s back-endem mobilní aplikace po přihlášení. Ověřte, zda nejsou do konzoly ladění zaznamenány žádné výjimky.
4. (Nepovinné) Zobrazení aktualizovaných dat pomocí průzkumníka objektů serveru SQL Server nebo nástroje REST, jako je Šumař. Všimněte si, že data byla synchronizována mezi back-endovou databází a místním úložištěm.

    Všimněte si, že data byla synchronizována mezi databází a místním úložištěm a obsahuje položky, které jste přidali, když byla aplikace odpojena.

## <a name="additional-resources"></a>Další zdroje
* [Offline synchronizace dat pro Azure Mobile Apps]
* [Nástroje Visual Studia pro Apache Cordova]

## <a name="next-steps"></a>Další kroky
* Zkontrolujte pokročilejší funkce offline synchronizace, jako je řešení konfliktů v [ukázce offline synchronizace]
* Projděte si odkaz na rozhraní API pro synchronizaci offline v [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova rychlý start]: app-service-mobile-cordova-get-started.md
[ukázka synchronizace offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Offline synchronizace dat pro Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Nástroje Visual Studia pro Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
