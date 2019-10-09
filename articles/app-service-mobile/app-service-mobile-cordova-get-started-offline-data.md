---
title: Povolit offline synchronizaci pro mobilní aplikace Azure (Cordova) | Microsoft Docs
description: Naučte se používat App Service mobilní aplikace pro ukládání a synchronizaci offline dat v aplikaci Cordova.
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: dc1183e1557d634ab1880376a1347f43f33b329f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027505"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikace Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj koncových a integrovaných služeb od centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) ještě dnes.

## <a name="overview"></a>Přehled
V tomto kurzu se seznámíte s funkcí offline synchronizace služby Azure Mobile Apps pro Cordova. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací @ no__t-0viewing, přidáváním nebo úpravou dat @ no__t-1even, když není k dispozici žádné síťové připojení. Změny jsou uloženy v místní databázi.  Jakmile je zařízení zase online, tyto změny se synchronizují se vzdálenou službou.

Tento kurz je založený na řešení Cordova rychlý Start pro Mobile Apps, které vytvoříte po dokončení kurzu [Rychlý Start Apache Cordova]. V tomto kurzu aktualizujete řešení pro rychlé zprovoznění a přidáte offline funkce Azure Mobile Apps.  V aplikaci také zvýrazníte kód specifický pro práci v režimu offline.

Další informace o funkci offline synchronizace najdete v tématu [synchronizace offline dat v Azure Mobile Apps]. Podrobnosti o využití rozhraní API najdete v [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Přidání offline synchronizace do řešení pro rychlé zprovoznění
Do aplikace se musí přidat kód offline synchronizace. Offline synchronizace vyžaduje modul plug-in Cordova-SQLite-Storage, který se automaticky přidá do aplikace, když je v projektu zahrnutý modul plug-in Azure Mobile Apps. Projekt rychlý Start zahrnuje oba tyto moduly plug-in.

1. V Průzkumník řešení aplikace Visual Studio otevřete index. js a nahraďte následující kód

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

    Předchozí přidání kódu inicializuje místní úložiště a definuje místní tabulku, která odpovídá hodnotám sloupců použitým v back-endu Azure. (Do tohoto kódu nemusíte vkládat všechny hodnoty sloupců.)  Pole `version` se uchovává v mobilním back-endu a používá se pro řešení konfliktů.

    Získáte odkaz na kontext synchronizace voláním **getSyncContext**. Kontext synchronizace pomáhá zachovat vztahy mezi tabulkami sledováním a vkládáním změn ve všech tabulkách. klientská aplikace se změnila při volání `.push()`.

3. Aktualizujte adresu URL aplikace na adresu URL aplikace Mobilní aplikace.

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

    Předchozí kód inicializuje kontext synchronizace a poté volá metodu getsync (místo metody GetTable), aby získal odkaz na místní tabulku.

    Tento kód používá místní databázi pro všechny operace vytvoření, čtení, aktualizace a odstranění (CRUD) tabulky.

    Tato ukázka provádí jednoduché zpracování chyb při konfliktu synchronizace. Skutečná aplikace by vedla k různým chybám, jako jsou stavy sítě, konflikty serverů a další. Příklady kódu naleznete v tématu [Ukázka offline synchronizace].

5. Pak přidejte tuto funkci, která provede skutečnou synchronizaci.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Rozhodnete, kdy doručovat změny do back-endu mobilní aplikace voláním **syncContext. push ()** . Například můžete volat **syncBackend** v obslužné rutině události tlačítka vázané na tlačítko synchronizovat.

## <a name="offline-sync-considerations"></a>Pokyny k offline synchronizaci

V ukázce je metoda **push** **syncContext** volána pouze při spuštění aplikace ve funkci zpětného volání pro přihlášení.  V reálné aplikaci můžete také aktivovat tuto funkci synchronizace ručně nebo při změně stavu sítě.

Při spuštění operace vyžádání na tabulce, která má nedokončené místní aktualizace sledované kontextem, bude tato operace vyžádané replikace automaticky spouštět push. Při aktualizaci, přidávání a dokončování položek v této ukázce můžete vynechat explicitní volání **push** , protože může být redundantní.

V poskytnutém kódu jsou dotazovány všechny záznamy ve vzdálené tabulce todoItem, ale je také možné filtrovat záznamy předáním ID dotazu a dotazu do příkazu **push**. Další informace najdete v části o *přírůstkové synchronizaci* při [Synchronizace offline dat v Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>Volitelné Zakázat ověřování

Pokud nechcete nastavit ověřování před testováním offline synchronizace, odkomentujte funkci zpětného volání pro přihlášení, ale nechte kód uvnitř funkce zpětného volání.  Po přidání komentáře k přihlašovacím řádkům kódu následuje následující kód:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nyní se aplikace při spuštění aplikace synchronizuje s back-endu Azure.

## <a name="run-the-client-app"></a>Spuštění klientské aplikace
Když je offline synchronizace povolená, můžete na každé platformě spustit alespoň jednou klientskou aplikaci, aby se naplnila místní databáze úložiště. Později Simulujte scénář offline a upravíte data v místním úložišti, zatímco je aplikace offline.

## <a name="optional-test-the-sync-behavior"></a>Volitelné Testování chování synchronizace
V této části upravíte projekt klienta pro simulaci offline scénáře pomocí neplatné adresy URL aplikace pro váš back-end. Když přidáte nebo změníte datové položky, budou tyto změny uloženy v místním úložišti, ale nejsou synchronizovány do úložiště dat back-endu, dokud nebude připojení znovu navázáno.

1. V Průzkumník řešení otevřete soubor projektu index. js a změňte adresu URL aplikace tak, aby odkazovala na neplatnou adresu URL, podobně jako v následujícím kódu:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. V souboru index. html aktualizujte prvek CSP `<meta>` se stejnou neplatnou adresou URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Sestavte a spusťte klientskou aplikaci a Všimněte si, že v konzole se zaprotokoluje výjimka, když se aplikace po přihlášení pokusí o synchronizaci s back-endu. Jakékoli nové přidané položky existují pouze v místním úložišti, dokud nebudou vloženy do mobilního back-endu. Klientská aplikace se chová, jako by byla připojená k back-endu.

4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvale uložené v místním úložišti.

5. Volitelné Pomocí sady Visual Studio si můžete zobrazit tabulku Azure SQL Database, abyste viděli, že se data v back-end databázi nezměnila.

    V aplikaci Visual Studio otevřete **Průzkumník serveru**. Přejděte do databáze ve **službě Azure**->**databáze SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumník objektů systému SQL Server**. Teď můžete přejít k tabulce SQL Database a jejímu obsahu.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Volitelné Otestování opětovného připojení k vašemu mobilnímu back-endu

V této části znovu připojíte aplikaci k mobilnímu back-endu, která simuluje aplikaci, která se vrátí zpátky do online stavu. Když se přihlásíte, data se synchronizují do mobilního back-endu.

1. Znovu otevřete index. js a obnovte adresu URL aplikace.
2. Znovu otevřete index. html a opravte adresu URL aplikace v elementu CSP `<meta>`.
3. Znovu sestavte a spusťte klientskou aplikaci. Aplikace se po přihlášení pokusí o synchronizaci s back-endu mobilní aplikace. Ověřte, že v konzole ladění nejsou protokolovány žádné výjimky.
4. Volitelné Zobrazte aktualizovaná data pomocí Průzkumník objektů systému SQL Server nebo nástroje REST, jako je Fiddler. Všimněte si, že data byla synchronizovaná mezi databází back-end a místním úložištěm.

    Všimněte si, že data byla synchronizovaná mezi databází a místním úložištěm a obsahuje položky, které jste přidali v době, kdy byla aplikace odpojena.

## <a name="additional-resources"></a>Další zdroje
* [Synchronizace offline dat v Azure Mobile Apps]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Další kroky
* Projděte si pokročilejší funkce offline synchronizace, jako je řešení konfliktů v [Ukázka offline synchronizace] .
* Přečtěte si referenční informace k rozhraní API offline synchronizace v [dokumentaci k rozhraní API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Rychlý Start Apache Cordova]: app-service-mobile-cordova-get-started.md
[Ukázka offline synchronizace]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Synchronizace offline dat v Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
