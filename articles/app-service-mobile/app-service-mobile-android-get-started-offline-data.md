---
title: Povolit offline synchronizaci (Android)
description: Přečtěte si, jak používat mobilní aplikace App Service k ukládání dat do mezipaměti a synchronizaci offline dat v aplikaci pro Android.
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c215105af5fe1ef8056b0d816cf2c2a6b96f2038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461619"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci pro Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz se zabývá funkcí offline synchronizace mobilních aplikací Azure pro Android. Offline synchronizace umožňuje koncovým uživatelům&mdash;pracovat se zobrazením,&mdash;přidáváním nebo úpravou dat mobilní aplikací, i když neexistuje žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení opět online, tyto změny se synchronizují se vzdáleným back-endem.

Pokud je to vaše první zkušenost s Mobilní aplikace Azure, měli byste nejprve dokončit kurz [Vytvořit aplikaci pro Android]. Pokud nepoužíváte stažený projekt serveru rychlého startu, je nutné do projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizace dat v Mobilních aplikacích Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizace aplikace pro podporu offline synchronizace
S offline synchronizací čtete a píšete ze *synchronizační tabulky* (pomocí rozhraní *IMobileServiceSyncTable),* která je součástí databáze **SQLite** na vašem zařízení.

Chcete-li push a vyžádat změny mezi zařízením a Azure Mobile Services, použijte *kontext synchronizace* *(MobileServiceClient.SyncContext*), který inicializovat s místní databází pro místní ukládání dat místně.

1. V `TodoActivity.java`aplikaci comment out `mToDoTable` the existing definition of and uncomment the sync table version: In , comment out the existing definition of and uncomment the sync table version: In , comment out the existing definition of and uncomment the sync table version: In
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. V `onCreate` metodě zakomentujte existující `mToDoTable` inicializaci a odkomentujte tuto definici:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. V `refreshItemsFromTable` komentáři se `results` definice a odkomentujte tuto definici:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Komentář k definici `refreshItemsFromMobileServiceTable`.
5. Odkomentujte `refreshItemsFromMobileServiceTableSyncTable`definici :
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Odkomentujte `sync`definici :
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Otestování aplikace
V této části otestujete chování se zapnutou Wi-Fi a pak vypnete WiFi a vytvoříte offline scénář.

Když přidáte datové položky, jsou uchovávány v místním úložišti SQLite, ale nejsou synchronizovány s mobilní službou, dokud nestisknete tlačítko **Aktualizovat.** Jiné aplikace mohou mít různé požadavky týkající se potřeby synchronizace dat, ale pro ukázkové účely tento kurz má uživatel explicitně požádat.

Po stisknutí tohoto tlačítka se spustí nová úloha na pozadí. Nejprve odešle všechny změny provedené v místním úložišti pomocí kontextu synchronizace a pak vytáhne všechna změněná data z Azure do místní tabulky.

### <a name="offline-testing"></a>Offline testování
1. Umístěte zařízení nebo simulátor do *režimu letadlo*. Tím se vytvoří offline scénář.
2. Přidejte některé položky *todo* nebo označte některé položky jako dokončené. Ukončete zařízení nebo simulátor (nebo aplikaci násilně zavřete) a restartujte počítač. Ověřte, zda byly změny v zařízení trvalé, protože jsou uloženy v místním úložišti SQLite.
3. Zobrazení obsahu tabulky Azure *TodoItem* buď pomocí nástroje SQL, jako je *SQL Server Management Studio*, nebo klienta REST, jako je *Fiddler* nebo *Postman*. Ověřte, zda *not* nebyly nové položky synchronizovány se serverem
   
       + Pro back-end Node.js přejděte na [portál Azure](https://portal.azure.com/)a v back-endu mobilní aplikace klikněte na **Položku snadné tabulky** > **TodoItem** a zobrazte obsah `TodoItem` tabulky.
       + Pro back-end .NET zobrazte obsah tabulky pomocí nástroje SQL, jako je *například SQL Server Management Studio*, nebo klienta REST, například *Fiddler* nebo *Postman*.
4. Zapněte Wi-Fi v zařízení nebo simulátoru. Dále stiskněte tlačítko **Aktualizovat.**
5. Zobrazte znovu data TodoItem na webu Azure Portal. Nyní by se měly zobrazit nové a změněné položky TodoItems.

## <a name="additional-resources"></a>Další zdroje
* [Offline synchronizace dat pro Azure Mobile Apps]
* [Cloud Cover: Offline synchronizace v mobilních službách] \(Azure poznámka: video je na mobilní služby, ale offline synchronizace funguje podobným způsobem v Azure Mobile Apps\)

<!-- URLs. -->

[Offline synchronizace dat pro Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Vytvoření aplikace pro Android]: app-service-mobile-android-get-started.md

[Cloud Cover: Offline synchronizace v mobilních službách Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

