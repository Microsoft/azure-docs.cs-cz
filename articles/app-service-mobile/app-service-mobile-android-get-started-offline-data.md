---
title: Zapnutí offline synchronizace pro mobilní aplikace Azure (Android)
description: Naučte se používat App Service Mobile Apps do mezipaměti a synchronizaci dat offline vaší aplikace Android
documentationcenter: android
author: conceptdev
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 152702bed0ea061c3cb86e2ff6f88bf204f9d243
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594185"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Zapnutí offline synchronizace pro mobilní aplikaci s Androidem
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz se zaměřuje offline synchronizace funkci Azure Mobile Apps pro Android. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací&mdash;zobrazení, přidávat a upravovat data&mdash;i v případě, že není žádné síťové připojení. Změny se ukládají do místní databáze. Jakmile zařízení do režimu online, tyto změny se synchronizují s vzdálené back-end.

Pokud je vaše první zkušenosti s Azure Mobile Apps, musí nejprve dokončit kurz [vytvoření aplikace pro Android]. Pokud použijete serverový projekt stažené rychlý start, je nutné přidat data přístup rozšiřující balíčky do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace, naleznete v tématu [Offline synchronizací dat v Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizace aplikace pro podporu offline synchronizace
S offline synchronizací pro čtení a zápisu z *synchronizace tabulky* (pomocí *IMobileServiceSyncTable* rozhraní), které je součástí **SQLite** databáze na vašem zařízení.

Pokud chcete push a změny mezi zařízením a Azure Mobile Services pro vyžádání obsahu, použijte *kontext synchronizace* (*MobileServiceClient.SyncContext*), který inicializace s místní databázi k ukládání data v místním počítači.

1. V `TodoActivity.java`, komentář existující definice `mToDoTable` a zrušte komentář u tabulky verze synchronizace:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. V `onCreate` metoda komentář existující inicializace `mToDoTable` a zrušte komentář u této definici:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. V `refreshItemsFromTable` komentář definice `results` a zrušte komentář u této definici:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Komentář definice `refreshItemsFromMobileServiceTable`.
5. Zrušením komentáře u definici `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Zrušením komentáře u definici `sync`:
   
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

## <a name="test-the-app"></a>Testování aplikace
V této části na test chování s Wi-Fi a pak vypněte Wi-Fi k vytvoření offline scénář.

Když přidáte datových položek, jsou uchovávat v místní úložiště SQLite, ale není synchronizovat do mobilní služby, dokud stisknete **aktualizovat** tlačítko. Ostatní aplikace může mít jiné požadavky týkající se při data musí být synchronizovány, ale pro účely ukázky v tomto kurzu má uživatel explicitní žádost o.

Stisknutím tohoto tlačítka spustí novou úlohu pozadí. Vynutí nejprve všechny změny provedené v místním úložišti pomocí kontextu synchronizace a potom si změnit dat z Azure do místní tabulky.

### <a name="offline-testing"></a>Offline testování
1. Umístit zařízení nebo v simulátoru *režim v letadle*. Tím se vytvoří offline scénář.
2. Přidejte nějaké *ToDo* položky nebo označit některé položky jako dokončené. Ukončení zařízení ani simulátor (nebo vynuceně zavřít aplikaci) a znovu spusťte. Ověřte, že změny jsou držena formou na zařízení vzhledem k tomu, že jsou uložené v místní úložiště SQLite.
3. Zobrazení obsahu Azure *TodoItem* tabulky buď pomocí nástroje SQL, jako *SQL Server Management Studio*, nebo REST klienta *Fiddler* nebo  *Postman*. Ověřte, že nové položky *není* byly synchronizované do serveru
   
       + Back-end Node.js, najdete [portál Azure](https://portal.azure.com/), a v mobilní aplikace klikněte na back-end **snadno tabulky** > **TodoItem** Chcete-li zobrazit obsah `TodoItem`tabulky.
       + Pro rozhraní .NET back-end, zobrazit obsah tabulky buď pomocí SQL nástroje, jako *SQL Server Management Studio*, nebo REST klienta *Fiddler* nebo *Postman*.
4. Zapněte Wi-Fi v zařízení ani simulátor. Stiskněte klávesu **aktualizovat** tlačítko.
5. Zobrazte TodoItem data znovu na portálu Azure. Nové a změněné TodoItems by se měla zobrazit.

## <a name="additional-resources"></a>Další prostředky
* [Offline synchronizací dat v Azure Mobile Apps]
* [Obálka cloudu: Offline synchronizace v Azure Mobile Services] \(Poznámka: je v Mobile Services, ale offline synchronizace funguje podobným způsobem jako v Azure Mobile Apps\)

<!-- URLs. -->

[Offline synchronizací dat v Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[vytvoření aplikace pro Android]: app-service-mobile-android-get-started.md

[Obálka cloudu: Offline synchronizace v Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

