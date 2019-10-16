---
title: Povolení offline synchronizace pro mobilní aplikace Azure (Android)
description: Naučte se používat App Service Mobile Apps k ukládání a synchronizaci offline dat v aplikaci pro Android.
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9c845c09c6b05436017f98323dfa78185cd58aa6
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388987"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci pro Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dnes.

## <a name="overview"></a>Přehled
Tento kurz se zabývá funkcí offline synchronizace pro Azure Mobile Apps pro Android. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací @ no__t-0viewing, přidáváním nebo úpravou dat @ no__t-1even, když není k dispozici žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení zase online, tyto změny se synchronizují se vzdáleným back-endu.

Pokud je to vaše první prostředí s Azure Mobile Apps, měli byste nejdřív dokončit kurz [Vytvoření aplikace pro Android]. Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, musíte do svého projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizace aplikace na podporu offline synchronizace
Offline synchronizace vám umožní číst a zapisovat z *synchronizační tabulky* (pomocí rozhraní *IMobileServiceSyncTable* ), které je součástí databáze **SQLite** na vašem zařízení.

Pokud chcete vložit a stáhnout změny mezi zařízením a Mobile Services Azure, použijte *kontext synchronizace* (*MobileServiceClient. SyncContext*), který se inicializuje s místní databází pro místní ukládání dat.

1. V `TodoActivity.java` Odkomentujte stávající definici `mToDoTable` a odkomentujte verzi synchronizační tabulky:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. V metodě `onCreate` Odkomentujte existující inicializaci `mToDoTable` a odkomentujte tuto definici:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. V `refreshItemsFromTable` Odkomentujte definici `results` a odkomentujte tuto definici:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Odkomentujte definici `refreshItemsFromMobileServiceTable`.
5. Odkomentujte definici `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Odkomentujte definici `sync`:
   
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
V této části otestujete chování pomocí Wi-Fi na a pak vypněte Wi-Fi a vytvořte offline scénář.

Když přidáte datové položky, jsou uchovávány v místním úložišti SQLite, ale nejsou synchronizovány do mobilní služby, dokud nestisknete tlačítko **aktualizovat** . Jiné aplikace mohou mít různé požadavky týkající se synchronizace dat, ale pro demonstrační účely tento kurz uživateli výslovně vyžádá.

Po stisknutí tohoto tlačítka se spustí nová úloha na pozadí. Nejprve pošle všechny změny provedené v místním úložišti pomocí kontextu synchronizace a potom všechna změněná data z Azure vyžádá do místní tabulky.

### <a name="offline-testing"></a>Testování offline
1. Zařízení nebo simulátor umístěte do *režimu v letadle*. Tím se vytvoří scénář offline.
2. Přidejte některé položky *TODO* nebo označte některé položky jako dokončené. Ukončete zařízení nebo simulátor (nebo aplikaci vynuceně ukončete) a restartujte ji. Ověřte, že vaše změny byly na zařízení trvalé, protože jsou uložené v místním úložišti SQLite.
3. Zobrazte obsah tabulky Azure *TodoItem* buď pomocí nástroje SQL, jako je například *SQL Server Management Studio*, nebo klienta REST, jako je například *Fiddler* nebo *Poster*. Ověřte, *že se nové* položky nesynchronizoval na server.
   
       + Pro back-end Node. js přejděte na [Azure Portal](https://portal.azure.com/)a v back-endu mobilní aplikace klikněte na **snadné tabulky** > **TodoItem** a zobrazte obsah tabulky `TodoItem`.
       + V případě back-endu .NET můžete zobrazit obsah tabulky buď pomocí nástroje SQL, jako je *SQL Server Management Studio*, nebo klienta REST, jako je *Fiddler* nebo *post*.
4. Zapnout Wi-Fi v zařízení nebo simulátoru. Potom stiskněte tlačítko **aktualizovat** .
5. Zobrazte data TodoItem znovu v Azure Portal. Nyní by se měla zobrazit nová a změněná TodoItems.

## <a name="additional-resources"></a>Další materiály
* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [Cloudové pokrytí: offline synchronizace v Azure Mobile Services] \(note: video je na Mobile Services, ale offline synchronizace funguje podobným způsobem jako v Azure Mobile Apps @ no__t-2.

<!-- URLs. -->

[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Vytvoření aplikace pro Android]: app-service-mobile-android-get-started.md

[Cloudové pokrytí: offline synchronizace v Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

