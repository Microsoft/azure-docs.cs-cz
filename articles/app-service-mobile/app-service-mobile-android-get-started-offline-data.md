---
title: Povolení offline synchronizace pro mobilní aplikace Azure (Android)
description: Zjistěte, jak používat App Service Mobile Apps do mezipaměti a synchronizaci offline dat v aplikaci pro Android
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
ms.openlocfilehash: a20c79acce8c9dc9051651a0473fd07b8e62f5de
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960341"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Povolení offline synchronizace u vaší mobilní aplikace pro Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz se zabývá funkci offline synchronizace služby Azure Mobile Apps pro Android. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není žádné síťové připojení. Změny jsou uloženy v místní databázi. Když je zařízení online, tyto změny se synchronizují s vzdálené back-endem.

Pokud je toto vaše první zkušenost s Azure Mobile Apps, byste nejprve dokončit kurz [vytvoření aplikace pro Android]. Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíčky rozšíření datového přístupu do vašeho projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizace aplikace pro podporu offline synchronizace
Offline synchronizace, číst a zapisovat z *synchronizovat* (pomocí *IMobileServiceSyncTable* rozhraní), který je součástí **SQLite** databázi na vašem zařízení.

K zadání a vyžádání změn mezi zařízením a Azure Mobile Services, můžete použít *kontext synchronizace* (*MobileServiceClient.SyncContext*), která bude inicializovat místní databázi k ukládání data místně.

1. V `TodoActivity.java`, Odkomentujte existující definici `mToDoTable` a zrušte komentář u verze tabulky synchronizace:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. V `onCreate` metoda, Odkomentujte existující inicializace `mToDoTable` a zrušte komentář u této definice:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. V `refreshItemsFromTable` Odkomentujte definici `results` a zrušte komentář u této definice:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Odkomentujte definici `refreshItemsFromMobileServiceTable`.
5. Zrušením komentáře u definice `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Zrušením komentáře u definice `sync`:
   
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
V této části testování chování pomocí Wi-Fi na a pak vypněte Wi-Fi, k vytvoření scénáři offline.

Při přidávání položek dat, jsou uloženy v místní úložiště SQLite, ale není synchronizované do mobilní služby, dokud nestisknete klávesu **aktualizovat** tlačítko. Ostatní aplikace můžou mít různé požadavky týkající se při data musí být synchronizovány, ale pro účely ukázky v tomto kurzu má uživatel explicitně si ji vyžádat.

Když stisknete tlačítko, spustí novou úlohu na pozadí. Nejdříve odešle všechny změny provedené na místní úložiště pomocí kontextu synchronizace a potom si změnit data z Azure do místní tabulky.

### <a name="offline-testing"></a>Testování offline
1. Umístit zařízení nebo simulátor v *režim v letadle*. Tím se vytvoří v režimu offline scénáři.
2. Přidat některé *ToDo* položky nebo označit některé položky jako dokončené. Ukončete zařízení nebo simulátor (nebo vynuceně zavřít aplikaci) a znovu spusťte. Ověřte, že vaše změny byly trvale zaznamenány v zařízení vzhledem k tomu, že jsou uloženy v místní úložiště SQLite.
3. Zobrazení obsahu Azure *TodoItem* buď pomocí nástroje SQL jako tabulka *SQL Server Management Studio*, nebo klienta REST, jako *Fiddler* nebo  *Postman*. Ověřte, že nové položky mají *není* byly synchronizované do serveru
   
       + Back-end Node.js najdete [webu Azure portal](https://portal.azure.com/), a v mobilní aplikaci klepněte na back-endu **jednoduché tabulky** > **TodoItem** zobrazíte obsah `TodoItem`tabulky.
       + Back-endu .NET, zobrazit obsah buď pomocí SQL, jako nástroj *SQL Server Management Studio*, nebo klienta REST, jako *Fiddler* nebo *Postman*.
4. Zapněte Wi-Fi v zařízení nebo simulátor. Stiskněte klávesu **aktualizovat** tlačítko.
5. Zobrazení dat TodoItem znovu na webu Azure Portal. Nové a změněné TodoItems by teď měl vypadat.

## <a name="additional-resources"></a>Další prostředky
* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [Cloud Cover: Offline synchronizace v Azure Mobile Services] \(Poznámka: je video v Mobile Services, ale podobným způsobem jako v Azure Mobile Apps pracuje offline synchronizace\)

<!-- URLs. -->

[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Vytvoření aplikace pro Android]: app-service-mobile-android-get-started.md

[Cloud Cover: Offline synchronizace v Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

