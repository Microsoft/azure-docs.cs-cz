---
title: Povolení offline synchronizace (iOS)
description: Přečtěte si, jak používat mobilní aplikace Azure App Service k ukládání do mezipaměti a synchronizaci offline dat v aplikacích iOS.
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: d943213814b999f101a541abb0195a9fdd5a7423
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459170"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Povolení offline synchronizace s mobilními aplikacemi pro iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz se zabývá offline synchronizací s funkcí Mobilní aplikace služby Azure App Service pro iOS. Díky offline synchronizaci mohou koncoví uživatelé pracovat s mobilní aplikací a zobrazovat, přidávat nebo upravovat data, i když nemají žádné síťové připojení. Změny jsou uloženy v místní databázi. Po přepychu zařízení se změny synchronizují se vzdáleným back-endem.

Pokud je toto vaše první zkušenost s mobilními aplikacemi, měli byste nejprve dokončit výukový program [Vytvořit aplikaci pro iOS]. Pokud nepoužíváte stažený projekt serveru rychlého spuštění, je nutné do projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete [v tématu Offline synchronizace dat v mobilních aplikacích].

## <a name="review-the-client-sync-code"></a><a name="review-sync"></a>Kontrola kódu synchronizace klienta
Projekt klienta, který jste stáhli pro kurz [Vytvoření aplikace pro iOS,] již obsahuje kód, který podporuje offline synchronizaci pomocí místní databáze založené na základních datech. Tato část shrnuje, co je již zahrnuto v kódu kurzu. Koncepční přehled této funkce najdete v tématu [Offline synchronizace dat v mobilních aplikacích].

Pomocí funkce offline synchronizace dat mobilních aplikací mohou koncoví uživatelé pracovat s místní databází, i když je síť nepřístupná. Chcete-li tyto funkce používat v aplikaci, `MSClient` inicializujete kontext synchronizace a odkazujete na místní úložiště. Potom odkazujete na tabulku prostřednictvím rozhraní **MSSyncTable.**

V **QSTodoService.m** (Objective-C) nebo **ToDoTableViewController.swift** (Swift) všimněte si, že typ člena **syncTable** je **MSSyncTable**. Offline synchronizace používá toto rozhraní synchronizační tabulky místo **MSTable**. Při použití synchronizační tabulky všechny operace přejít do místního úložiště a jsou synchronizovány pouze se vzdáleným back-endem s explicitními operacemi nabízení a vytahování.

 Chcete-li získat odkaz na synchronizační tabulku, použijte `MSClient`metodu **syncTableWithName** na . Chcete-li odebrat funkce offline synchronizace, použijte místo toho **tableWithName.**

Před provedením všech operací tabulky musí být místní úložiště inicializováno. Zde je příslušný kód:

* **Cíl C**. V metodě **QSTodoService.init:**

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Rychlý**. V metodě **ToDoTableViewController.viewDidLoad:**

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Tato metoda vytvoří místní úložiště `MSCoreDataStore` pomocí rozhraní, které poskytuje sada SDK mobilní aplikace. Alternativně můžete poskytnout jiné místní úložiště implementací `MSSyncContextDataSource` protokolu. První parametr **MSSyncContext** se také používá k určení obslužné rutiny konfliktu. Protože jsme `nil`prošli , získáme výchozí konflikt obslužnou rutinu, která selže na jakýkoli konflikt.

Nyní provedeme skutečnou operaci synchronizace a získáme data ze vzdáleného back-endu:

* **Cíl C**. `syncData`nejprve odešle nové změny a pak volání **pullData** získat data ze vzdáleného back-endu. Metoda **pullData** zase získá nová data, která odpovídají dotazu:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Rychlý**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Ve verzi Objective-C `syncData`v aplikaci , nejprve voláme **pushWithCompletion** v kontextu synchronizace. Tato metoda je `MSSyncContext` členem (a nikoli samotné synchronizační tabulky), protože odesílá změny ve všech tabulkách. Na server jsou odesílány pouze záznamy, které byly určitým způsobem změněny (prostřednictvím operací CUD). Potom pomocník **pullData** se nazývá, který volá **MSSyncTable.pullWithQuery** načíst vzdálená data a uložit ji v místní databázi.

Ve verzi Swift, protože operace push nebyla nezbytně nutná, neexistuje žádná výzva **pushWithCompletion**. Pokud existují nějaké změny čekající na vyřízení v kontextu synchronizace pro tabulku, která provádí operaci push, pull vždy vydá push první. Pokud však máte více než jednu tabulku synchronizace, je nejlepší explicitně volat push, abyste zajistili, že vše je konzistentní napříč souvisejícími tabulkami.

Ve verzích Objective-C a Swift můžete použít metodu **pullWithQuery** k určení dotazu pro filtrování záznamů, které chcete načíst. V tomto příkladu dotaz načte všechny `TodoItem` záznamy ve vzdálené tabulce.

Druhý parametr **pullWithQuery** je ID dotazu, který se používá pro *přírůstkovou synchronizaci*. Přírůstková synchronizace načte pouze záznamy, které byly změněny od poslední synchronizace, pomocí `UpdatedAt` časového razítka záznamu (volaného `updatedAt` v místním úložišti.) ID dotazu by měl být popisný řetězec, který je jedinečný pro každý logický dotaz ve vaší aplikaci. Chcete-li se odhlásit `nil` z přírůstkové synchronizace, předejte jako ID dotazu. Tento přístup může být potenciálně neefektivní, protože načte všechny záznamy na každou operaci vyžádat.

Aplikace Objective-C se synchronizuje při úpravě nebo přidání dat, když uživatel provede gesto aktualizace a při spuštění.

Aplikace Swift se synchronizuje, když uživatel provede gesto aktualizace a při spuštění.

Vzhledem k tomu, že se aplikace synchronizuje při každé změně dat (Objective-C) nebo při každém spuštění aplikace (Objective-C a Swift), aplikace předpokládá, že uživatel je online. V pozdější části aktualizujete aplikaci tak, aby uživatelé mohli upravovat, i když jsou offline.

## <a name="review-the-core-data-model"></a><a name="review-core-data"></a>Kontrola modelu základních dat
Při použití úložiště core data offline, musíte definovat konkrétní tabulky a pole v datovém modelu. Ukázková aplikace již obsahuje datový model se správným formátem. V této části procházíme tyto tabulky, abychom ukázali, jak se používají.

Otevřete **soubor QSDataModel.xcmodeld**. Čtyři tabulky jsou definovány -- tři, které používá sada SDK a jedna, která se používá pro samotné položky s cílem:
  * MS_TableOperations: Sleduje položky, které je třeba synchronizovat se serverem.
  * MS_TableOperationErrors: Sleduje všechny chyby, ke kterým došlo během offline synchronizace.
  * MS_TableConfig: Sleduje poslední aktualizovaný čas poslední operace synchronizace pro všechny operace vyžádat.
  * TodoItem: Ukládá položky, které chcete provést. Systémové sloupce **createdAt**, **updatedAt**a **verze** jsou volitelné vlastnosti systému.

> [!NOTE]
> Sada SDK pro mobilní aplikace si**``** vyhrazuje názvy sloupců začínající na " ". Nepoužívejte tuto předponu s ničím jiným než se sloupci systému. V opačném případě budou názvy sloupců změněny při použití vzdáleného back-endu.
>
>

Při použití funkce offline synchronizace definujte tři systémové tabulky a tabulku dat.

### <a name="system-tables"></a>Systémové tabulky

**MS_TableOperations**  

![MS_TableOperations atributy tabulky][defining-core-data-tableoperations-entity]

| Atribut | Typ |
| --- | --- |
| id | Celé číslo 64 |
| Itemid | Řetězec |
| properties | Binární data |
| tabulka | Řetězec |
| tableKind | Celé číslo 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors atributy tabulky][defining-core-data-tableoperationerrors-entity]

| Atribut | Typ |
| --- | --- |
| id |Řetězec |
| operationId |Celé číslo 64 |
| properties |Binární data |
| tableKind |Celé číslo 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atribut | Typ |
| --- | --- |
| id |Řetězec |
| key |Řetězec |
| Keytype |Celé číslo 64 |
| tabulka |Řetězec |
| value |Řetězec |

### <a name="data-table"></a>Tabulka dat

**TodoPoložka**

| Atribut | Typ | Poznámka |
| --- | --- | --- |
| id | Řetězec, označený jako povinný |Primární klíč ve vzdáleném úložišti |
| Kompletní | Logická hodnota | Pole položky v rozepsání |
| text |Řetězec |Pole položky v rozepsání |
| createdAt | Datum | (nepovinné) Mapy na **createdAt** vlastnost systému |
| aktualizovánoAt | Datum | (nepovinné) Mapy na **vlastnost aktualizovatna** vlastnostsystému |
| version | Řetězec | (nepovinné) Používá se ke zjišťování konfliktů, mapuje na verzi |

## <a name="change-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>Změna chování synchronizace aplikace
V této části upravíte aplikaci tak, aby se nesynchronizovala při spuštění aplikace nebo při vkládání a aktualizaci položek. Synchronizuje se pouze při provedení tlačítka refresh gesture.

**Cíl C**:

1. V **Souboru QSTodoListViewController.m**změňte metodu **viewDidLoad** a odeberte volání na `[self refresh]` konci metody. Nyní nejsou data synchronizována se serverem při spuštění aplikace. Místo toho se synchronizuje s obsahem místního úložiště.
2. V **QSTodoService.m**upravte `addItem` definici tak, aby se po vložení položky nesynchronizovala. Odstraňte `self syncData` blok a nahraďte jej následujícím:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Upravte definici, `completeItem` jak bylo uvedeno výše. Odstraňte blok `self syncData` a nahraďte jej následujícím:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Rychlý**:

V `viewDidLoad`aplikaci **ToDoTableViewController.swift**zakomentujte dva řádky, které jsou zde zobrazeny, a zastavte synchronizaci při spuštění aplikace. V době psaní tohoto článku aplikace Swift Todo neaktualizuje službu, když někdo přidá nebo dokončí položku. Aktualizuje službu pouze při spuštění aplikace.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-the-app"></a><a name="test-app"></a>Testování aplikace
V této části se připojíte k neplatné adrese URL a simulujete offline scénář. Když přidáte datové položky, jsou uloženy v místním úložišti Core Data, ale nejsou synchronizovány s back-endem mobilní aplikace.

1. Změňte adresu URL mobilní aplikace v **souboru QSTodoService.m** na neplatnou adresu URL a aplikaci spusťte znovu:

   **Cíl C**. V QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Rychlý**. V ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Přidejte některé položky, které chcete provést. Ukončete simulátor (nebo aplikaci násilně zavřete) a restartujte ji. Ověřte, zda změny přetrvávají.

3. Zobrazení obsahu vzdálené tabulky **TodoItem:**
   * Back-end Node.js najdete na [portálu Azure](https://portal.azure.com/) a v back-endu mobilní aplikace klikněte na **Položku Snadné tabulky** > **TodoItem**.  
   * Pro back-end .NET použijte buď nástroj SQL, například SQL Server Management Studio, nebo klientREST, například Fiddler nebo Postman.  

4. Ověřte, zda nové položky *nebyly* synchronizovány se serverem.

5. Změňte adresu URL zpět na správnou v **QSTodoService.m**a znovu spusťte aplikaci.

6. Proveďte gesto aktualizace stažením seznamu položek.  
Zobrazí se indikátor průběhu.

7. Znovu zobrazit data **TodoItem.** Nyní by měly být zobrazeny nové a změněné položky v části to-do.

## <a name="summary"></a>Souhrn
Pro podporu funkce offline synchronizace `MSSyncTable` jsme použili rozhraní a inicializovali `MSClient.syncContext` s místním úložištěm. V tomto případě místní úložiště byla databáze založená na základních datech.

Při použití místního úložiště jádrových dat je nutné definovat několik tabulek se [správnými vlastnostmi systému](#review-core-data).

Normální operace vytváření, čtení, aktualizace a odstraňování (CRUD) pro mobilní aplikace fungují, jako by aplikace byla stále připojená, ale všechny operace probíhají proti místnímu úložišti.

Když jsme synchronizovali místní úložiště se serverem, použili jsme metodu **MSSyncTable.pullWithQuery.**

## <a name="additional-resources"></a>Další zdroje
* [Synchronizace offline dat v mobilních aplikacích]
* [Cloud Cover: Offline synchronizace v mobilních službách] \(Azure Video se týká mobilních služeb, ale offline synchronizace mobilních aplikací funguje podobným způsobem.\)

<!-- URLs. -->


[Vytvoření aplikace pro iOS]: app-service-mobile-ios-get-started.md
[Synchronizace offline dat v mobilních aplikacích]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline synchronizace v mobilních službách Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
