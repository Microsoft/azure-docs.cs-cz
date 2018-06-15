---
title: Zapnutí offline synchronizace pro mobilní aplikace Azure (Cordova) | Microsoft Docs
description: Další informace o použití aplikace služby mobilní aplikace do mezipaměti a synchronizaci dat offline v aplikaci Cordova
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: c12328a441a8cc438fa3e974863cc8adf8651b50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593710"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Zapnutí offline synchronizace pro mobilní aplikaci Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Tento kurz představuje offline synchronizace funkci Azure Mobile Apps pro Cordova. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací&mdash;zobrazení, přidávat a upravovat data&mdash;i v případě, že není žádné síťové připojení. Změny se ukládají do místní databáze.  Když je zařízení do režimu online, tyto změny se synchronizují s vzdálené služby.

V tomto kurzu vychází z řešení rychlý start Cordova pro mobilní aplikace, kterou vytvoříte po dokončení tohoto kurzu [Apache Cordova úvodní]. V tomto kurzu aktualizujete řešení rychlý start pro přidání offline funkcí Azure Mobile Apps.  Také jsme zvýrazněte offline konkrétní kódu v aplikaci.

Další informace o funkci offline synchronizace, naleznete v tématu [Offline synchronizací dat v Azure Mobile Apps]. Podrobnosti využití rozhraní API najdete v tématu [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Přidejte offline synchronizace k řešení rychlý start
Kód offline synchronizace je nutné přidat do aplikace. Offline synchronizace vyžaduje úložiště sqlite cordova modul plug-in, který automaticky získá přidán do vaší aplikace při modul plug-in Azure Mobile Apps je zahrnutý v projektu. Projektu pro rychlý start zahrnuje obě tyto moduly plug-in.

1. V Průzkumníku řešení v sadě Visual Studio otevřete index.js a nahraďte následujícím kódem

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    s tímto kódem:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Potom nahraďte následujícím kódem:

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

    Předchozí kód přidání inicializovat místní úložiště a definovat místní tabulku, která odpovídá sloupci hodnoty používané ve vaší službě Azure back-end. (Není potřeba mají být zahrnuty všechny hodnoty ve sloupcích tento kód.)  `version` Pole se spravuje pomocí mobilního back-endu a používá se pro řešení konfliktů.

    Získat odkaz na kontext synchronizace voláním **getSyncContext**. Kontext synchronizace umožňuje zachovat relace mezi tabulkami sledováním a vkládání změny ve všech tabulkách klientské aplikace změnil při `.push()` je volána.

3. Aktualizujte adresu URL aplikace na adresu URL aplikace mobilní aplikace.

4. Potom nahraďte tento kód:

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

    Předchozí kód inicializuje kontext synchronizace a potom volá getSyncTable (namísto jít) k získání odkazu na místní tabulku.

    Tento kód používá pro všechny místní databázi vytvořit, číst, aktualizovat a odstranit tabulku operace.

    Tato ukázka provádí zpracování na konflikty synchronizace jednoduché chyb. Reálné aplikaci byste zpracovávat různé chyby jako síťové podmínky, server je v konfliktu a dalších. Příklady kódu najdete v tématu [offline synchronizace ukázka].

5. Dál přidejte této funkci můžete provést skutečné synchronizaci.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Rozhodnete, kdy k replikaci změn na back-end mobilní aplikace při volání **syncContext.push()**. Například může volat **syncBackend** v obslužné rutiny události tlačítka vázaný na tlačítko synchronizovat.

## <a name="offline-sync-considerations"></a>Důležité informace o offline synchronizace

V ukázce **nabízené** metodu **syncContext** je volána pouze při spuštění aplikace ve funkci zpětného volání pro přihlášení.  V reálné aplikaci může také mít tato funkce synchronizaci spustit ručně nebo při změně stavu sítě.

Když je spustit vyžádání pro tabulku, která má čekající místní aktualizace sleduje kontext, který pull operace automaticky aktivuje push. Při aktualizaci, přidávání a dokončení položky v této ukázce, můžete vynechat explicitní **nabízené** volat, protože je redundantní.

V zadané kódu jsou předmětem dotazování všechny záznamy v tabulce vzdálené todoItem, ale je také možné filtrování záznamů předáním id dotazu a dotazy s cílem **nabízené**. Další informace najdete v části *přírůstkové synchronizace* v [Offline synchronizací dat v Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Volitelné) Zakázat ověřování

Pokud nechcete nastavit ověřování před testování offline synchronizace, komentář funkce zpětného volání pro přihlášení, ale nechte kód uvnitř uncommented funkce zpětného volání.  Po komentářů řádkům přihlášení, následuje kód:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nyní v aplikaci synchronizace s Azure back-end při spuštění aplikace.

## <a name="run-the-client-app"></a>Spustit klientskou aplikaci
S offline synchronizací teď povolená můžete spustit klientskou aplikaci alespoň jednou na každou platformu k naplnění databáze místního úložiště. Později simulovat offline scénář a upravit data v místním úložišti, zatímco aplikace v režimu offline.

## <a name="optional-test-the-sync-behavior"></a>(Volitelné) Testování chování synchronizace
V této části upravíte projektu klienta k simulaci offline scénář pomocí adresy URL Neplatná aplikace pro váš back-end. Při přidání nebo změna datových položek, tyto změny jsou uložené v místním úložišti, ale nejsou synchronizovány do úložiště dat back-end, dokud nebude znovu navázané připojení.

1. V Průzkumníku řešení otevřete soubor projektu index.js a změňte adresu URL aplikace tak, aby odkazovalo na neplatnou adresu URL, například následující kód:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. V index.html, aktualizace CSP `<meta>` element se stejným neplatná adresa URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Sestavit a spustit klientskou aplikaci a Všimněte si, že výjimku přihlášen v konzole, když se aplikace pokusí synchronizovat s back-end po přihlášení. Všechny nové položky, které přidáte existovat pouze v místním úložišti, dokud se instaluje do mobilního back-endu. Klientská aplikace se chová jako kdyby je připojené k back-end.

4. Zavřete aplikaci a restartujte ji k ověření, že nové položky, které jste vytvořili, jsou nastavené jako trvalé do místního úložiště.

5. (Volitelné) Pomocí sady Visual Studio zobrazíte tabulka Azure SQL Database v tématu, že se nezměnil na data v databázi back-end.

    V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet tabulku databáze SQL a její obsah.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Volitelné) Testování obnovení připojení na vaše mobilní back-end

V této části je znovu připojit aplikaci mobilního back-end, který simuluje aplikace vracející se zpět do stavu online. Při přihlášení, data se synchronizované s vaší mobilní back-end.

1. Index.js znovu otevřete a obnovte adresy URL aplikace.
2. Otevřete index.html a opravte adresu URL aplikace v CSP `<meta>` elementu.
3. Znovu sestavit a spustit klientskou aplikaci. Aplikace se pokusí synchronizovat s back-end mobilní aplikace po přihlášení. Ověřte, že žádné výjimky jsou zaznamenány v konzole pro ladění.
4. (Volitelné) Zobrazte aktualizovaná data pomocí Průzkumníka objektů systému SQL Server nebo REST nástroje, například aplikaci Fiddler. Všimněte si, že data umístění byl synchronizován mezi back-endovou databázi a místní úložiště.

    Všimněte si data umístění byl synchronizován mezi databází a místní úložiště a obsahuje položky, které jste přidali v době, kdy byl odpojen vaší aplikace.

## <a name="additional-resources"></a>Další zdroje informací:
* [Offline synchronizací dat v Azure Mobile Apps]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Další postup
* Kontrola více rozšířené funkce offline synchronizace, jako je řešení konfliktů v [offline synchronizace ukázka]
* Zkontrolujte odkaz na offline synchronizace rozhraní API v [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova úvodní]: app-service-mobile-cordova-get-started.md
[offline synchronizace ukázka]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Offline synchronizací dat v Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
