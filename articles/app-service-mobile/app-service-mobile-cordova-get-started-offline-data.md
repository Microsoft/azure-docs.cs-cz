---
title: Povolení offline synchronizace pro mobilní aplikace Azure (Cordova) | Dokumentace Microsoftu
description: Zjistěte, jak používat mobilní aplikace služby App Service do mezipaměti a synchronizaci offline dat v aplikaci Cordova
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
ms.openlocfilehash: 44c54b570a38eb1a3b9ca773893599d1d497dfa2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972146"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Tento kurz představuje funkci offline synchronizace služby Azure Mobile Apps pro Cordova. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není žádné síťové připojení. Změny jsou uloženy v místní databázi.  Když je zařízení online, tyto změny se synchronizují s vzdálené služby.

Tento kurz je založený na rychlý start řešení Cordova pro Mobile Apps, který vytvoříte, po dokončení tohoto kurzu [rychlý start pro Apache Cordova]. V tomto kurzu rychlý start řešení Chcete-li přidat offline funkce Azure Mobile Apps aktualizujete.  Můžeme také zvýraznit offline specifické pro kód v aplikaci.

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps]. Podrobnosti o použití rozhraní API najdete [dokumentace k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Přidání offline synchronizace do řešení rychlý start
Offline synchronizace kód musí být přidán do aplikace. Offline synchronizace vyžaduje modul plug-in cordova úložiště sqlite, který automaticky přidá do vaší aplikace při plugin Azure Mobile Apps je součástí projektu. Projekt rychlý Start obsahuje oba tyto moduly plug-in.

1. V Průzkumníku řešení sady Visual Studio otevřete index.js a nahraďte následující kód

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    s tímto kódem:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. V dalším kroku nahraďte následujícím kódem:

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

    Předchozím přidávání kódu inicializovat místní úložiště a definovat místní tabulku, která odpovídá sloupec hodnot použitých v Azure back-endu. (Není nutné zahrnout všechny hodnoty ve sloupcích v tomto kódu.)  `version` Pole se spravuje pomocí mobilního back-endu a slouží k odstranění konfliktů.

    Získání odkazu na kontext synchronizace voláním **getSyncContext**. Kontext synchronizace umožňuje zachovat relací mezi tabulkami sledováním a předávání změn operací push ve všech tabulkách klientská aplikace byla změněna při `.push()` je volána.

3. Aktualizujte adresu URL aplikace adresu URL vaší mobilní aplikace aplikace.

4. V dalším kroku nahraďte tento kód:

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

    Předchozí kód inicializuje kontext synchronizace a pak zavolá getSyncTable (namísto jít) k získání odkazu na místní tabulku.

    Tento kód používá místní databázi pro všechny vytváření, čtení, aktualizace a odstranění (CRUD) operací tabulky.

    Tato ukázka provádí jednoduché na konflikty pro zpracování chyb. Aplikace skutečný by zpracovávat různé chyby, jako je síťové podmínky, je v konfliktu serveru a dalších. Příklady kódu naleznete v tématu [Ukázka offline synchronizace].

5. Dále přidejte tuto funkci provádět skutečný synchronizace.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Můžete se rozhodnout, kdy odešlete změny do back-endu mobilní aplikace po zavolání **syncContext.push()**. Například lze zavolat **syncBackend** v obslužné rutině události tlačítko vázané na tlačítko synchronizovat.

## <a name="offline-sync-considerations"></a>Důležité informace o offline synchronizace

V ukázce **nabízených** metodu **syncContext** volat pouze při spuštění aplikace ve funkci zpětného volání pro přihlášení.  V reálné aplikaci může provést tuto funkci synchronizaci spustit ručně nebo při změně stavu sítě.

Pokud o přijetí změn se provede na tabulku, která se má čekající místní aktuality sledována podle kontextu, který vyžádanou operaci automaticky aktivační události oznámení. Při aktualizaci, přidání a dokončení položky v této ukázce můžete vynechat explicitní **nabízených** volat, protože může být redundantní.

Zadaný kód se generuje dotaz na všechny záznamy v tabulce vzdálené todoItem, ale je také možné filtrovat záznamy předáním id dotazu a dotaz pro **nabízených**. Další informace najdete v části *Přírůstková synchronizace* v [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Volitelné) Zakázat ověřování

Pokud nechcete nastavit ověřování před testování offline synchronizaci, okomentujte funkci zpětného volání pro přihlášení, ale ponechte kód uvnitř funkce zpětného volání komentář.  Po okomentování řádky přihlášení, následující kód:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Teď aplikace se synchronizuje s Azure back-endu při spuštění aplikace.

## <a name="run-the-client-app"></a>Spuštění klientské aplikace
Offline synchronizace teď můžete spustit klientskou aplikaci alespoň jednou na jednotlivých platformách k naplnění databáze místní úložiště. Později simulovat scénáři offline a upravovat data v místním úložišti, když je aplikace v režimu offline.

## <a name="optional-test-the-sync-behavior"></a>(Volitelné) Otestování chování synchronizace
V této části upravíte klientský projekt k simulaci scénáři offline pomocí Neplatná aplikace URL vaší back-endu. Při přidání nebo změně položek dat, tyto změny jsou uloženy v místním úložišti, ale nejsou synchronizované do backendového úložiště dat, dokud se připojení znovu navázáno.

1. V Průzkumníku řešení otevřete soubor index.js projektu a změňte adresu URL aplikace tak, aby odkazoval na neplatnou adresu URL, jako v následujícím kódu:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. V index.html, aktualizujte CSP `<meta>` element se stejným neplatná adresa URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Sestavení a spuštění klientské aplikace a Všimněte si, že výjimka přihlášen v konzole, když se aplikace pokusí synchronizovat s back-endu po přihlášení. Všechny nové položky, které přidáte existovat pouze v místním úložišti, dokud se nasdílejí do mobilního back-endu. Klientská aplikace chová se jako připojení k back-endu.

4. Aplikaci zavřete a restartujte ji k ověření, že jsou zachované místní úložiště nové položky, kterou jste vytvořili.

5. (Volitelné) Pomocí sady Visual Studio zobrazíte tabulky Azure SQL Database zobrazíte, že nedošlo ke změně dat v back-end databáze.

    V sadě Visual Studio, otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet do tabulky SQL database a její obsah.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Volitelné) Testování opětovné připojení k mobilním back-endu

V této části můžete aplikaci znovu připojte mobilní back-endu, který simuluje aplikace vracející se zpět do stavu online. Při přihlášení, data se synchronizuje s mobilním back-endu.

1. Znovu otevřít index.js a obnovte adresu URL aplikace.
2. Znovu otevřít index.html a opravte adresy URL aplikace ve zprostředkovateli CSP `<meta>` elementu.
3. Znovu sestavit a spustit klientskou aplikaci. Aplikace se pokouší synchronizovat s back-endu mobilní aplikace po přihlášení. Ověřte, že žádné výjimky jsou přihlášeni konzolou pro ladění.
4. (Volitelné) Zobrazte aktualizovaná data pomocí Průzkumníku objektů systému SQL Server nebo REST nástroje, jako je Fiddleru. Všimněte si, že se synchronizovala data mezi back-end databáze a místní úložiště.

    Všimněte si, že data se synchronizovala mezi databází a místní úložiště a obsahuje položky, které jste přidali, zatímco aplikace byl odpojen.

## <a name="additional-resources"></a>Další zdroje informací:
* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Další postup
* Zkontrolujte další pokročilé funkce offline synchronizaci, jako je řešení konfliktů v [ukázka offline synchronizace]
* Projděte si referenční offline synchronizace rozhraní API v [dokumentace k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Rychlý start pro Apache Cordova]: app-service-mobile-cordova-get-started.md
[Ukázka offline synchronizace]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
