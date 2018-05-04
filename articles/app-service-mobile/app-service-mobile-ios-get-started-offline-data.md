---
title: Povolit offline synchronizace u mobilních aplikací pro iOS | Microsoft Docs
description: Naučte se používat Azure App Service mobilní aplikace do mezipaměti a synchronizaci dat offline v aplikacích pro iOS.
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 2f415f1886c654f3bdd880cdccaadc7aa3e69892
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Povolit offline synchronizace u mobilních aplikací pro iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz se zaměřuje na offline synchronizace se funkce Mobile Apps služby Azure App Service pro iOS. S offline synchronizaci koncoví uživatelé mohou komunikovat s mobilní aplikací lze zobrazit, přidat nebo upravit data, i když mají žádné síťové připojení. Změny se ukládají do místní databáze. Jakmile zařízení přejde do režimu online, budou změny synchronizovány s vzdálené back-end.

Pokud je vaše první zkušenosti s Mobile Apps, musí nejprve dokončit kurz [vytvořit aplikaci pro iOS]. Pokud nepoužijete stažené úvodní serverový projekt, je nutné přidat do projektu balíčky rozšíření přístup k datům. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizací dat v Mobile Apps].

## <a name="review-sync"></a>Zkontrolujte kód synchronizace klienta
Klientského projektu, který jste stáhli pro [vytvořit aplikaci pro iOS] kurzu již obsahuje kód, který podporuje offline synchronizace pomocí místní databáze založené na datech jádra. Tento oddíl shrnuje, co je již zahrnut v kurzu kódu. Koncepční přehled funkce, najdete v části [Offline synchronizací dat v Mobile Apps].

Pomocí funkce offline synchronizací dat z mobilní aplikace, mohou koncoví uživatelé komunikovat s místní databáze, i v případě, že síť je nedostupná. V aplikaci použít tyto funkce, inicializovat kontext synchronizace `MSClient` a odkazovat na místní úložiště. Pak referenční tabulku prostřednictvím **MSSyncTable** rozhraní.

V **QSTodoService.m** (Objective-C) nebo **ToDoTableViewController.swift** (Swift), Všimněte si, že typ člena **syncTable** je **MSSyncTable** . Offline synchronizace používá toto rozhraní tabulky synchronizace místo **MSTable**. Při synchronizaci tabulky se používá, všechny operace přejděte do místního úložiště a jsou synchronizovány pouze s vzdálené back-end s explicitní nabízení a vyžadování operace.

 Pokud chcete získat odkaz na tabulku synchronizace, použijte **syncTableWithName** metodu `MSClient`. Odebrat funkci offline synchronizace, použijte **tableWithName** místo.

Před provedením jakékoli operace s tabulkou, musí být inicializován místní úložiště. Tady je relevantní kód:

* **Jazyka Objective-C**. V **QSTodoService.init** metoda:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Kód SWIFT**. V **ToDoTableViewController.viewDidLoad** metoda:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Tato metoda vytvoří místní úložiště pomocí `MSCoreDataStore` rozhraní, která poskytuje sada SDK mobilní aplikace. Alternativně můžete zadat různé ukládají v místním úložišti implementací `MSSyncContextDataSource` protokolu. Navíc první parametr **MSSyncContext** slouží k určení obslužnou rutinu konflikt. Protože jsme uplynulo `nil`, se nám získat obslužná rutina konflikt výchozí selže ve všech konflikt.

Nyní můžeme provést operaci skutečné synchronizace a získat data z vzdálené back-end:

* **Jazyka Objective-C**. `syncData` nejprve nabízených oznámení nové změny a pak zavolá **pullData** k získání dat z vzdálené back-end. Pak **pullData** metoda získá nová data, která odpovídá dotazu:

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
* **Kód SWIFT**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
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

Ve verzi jazyka Objective-C v `syncData`, nejprve říkáme **pushWithCompletion** v kontextu synchronizace. Tato metoda je členem skupiny `MSSyncContext` (a ne samotné tabulky synchronizace) protože vynutí změny mezi všechny tabulky. Na server se odesílají pouze záznamy, které byly upraveny nějakým způsobem místně (prostřednictvím operace vytvoření). Potom pomocné rutiny **pullData** je volána, který volá **MSSyncTable.pullWithQuery** načíst vzdálená data a ukládá ho do místní databáze.

V Swift verze, protože není nezbytně nutné, pomocí operace push je bez volání **pushWithCompletion**. Pokud jsou všechny změny čekající na vyřízení ve kontext synchronizace pro tabulky, která provádí operace push, pull vždy problémy push nejdřív. Pokud máte více než jedné tabulky synchronizace, je však vhodné explicitně volání nabízené zajistit, že všechno, co je konzistentní napříč související tabulky.

V Objective-C i Swift verze, můžete použít **pullWithQuery** metoda k zadání dotazu pro filtrování záznamů, můžete obnovit. V tomto příkladu dotaz načte všechny záznamy v vzdálených `TodoItem` tabulky.

Druhý parametr **pullWithQuery** je ID dotazu, který se používá pro *přírůstkové synchronizace*. Přírůstkové synchronizace vyhledá všechny záznamy, která byla změněna od poslední synchronizace, pomocí záznamu `UpdatedAt` časové razítko (nazývá `updatedAt` v místním úložišti.) ID dotazu by měla být popisný řetězec, který je jedinečný pro každý logický dotaz ve vaší aplikaci. Pro vyjádření výslovného nesouhlasu přírůstkové synchronizace, předat `nil` jako ID dotazu. Tento přístup může být potenciálně neefektivní, protože ho načte všechny záznamy na každou vyžádanou operaci.

Aplikace jazyka Objective-C synchronizuje se při úpravě nebo přidání dat, když uživatel provede gesto aktualizace a při spuštění.

Aplikace Swift synchronizuje, když uživatel provádí gesto aktualizace a při spuštění.

Důvodu synchronizace aplikace vždy, když je dat (Objective-C) nebo při každém spuštění aplikace (Objective-C a Swift), aplikace se předpokládá, že uživatel je online. V další části bude aktualizovat aplikaci tak, aby uživatelé mohou upravovat, i když se nachází v režimu offline.

## <a name="review-core-data"></a>Přečtěte si základní datový model
Pokud používáte offline úložiště dat jádra, je nutné zadat konkrétní tabulky a pole v datovém modelu. Ukázková aplikace již obsahuje datový model se správném formátu. V této části jsme provede následující tabulky, chcete-li zobrazit, jak se používají.

Otevřete **QSDataModel.xcdatamodeld**. Čtyři tabulky jsou definovány--tři, které používají sadu SDK a jeden, který se používá pro úkol, položky sami:
  * MS_TableOperations: Sleduje položky, které je nutné synchronizovat se serverem.
  * MS_TableOperationErrors: Sleduje všechny chyby, které dojít během offline synchronizace.
  * MS_TableConfig: Sleduje poslední aktualizace čas poslední operace synchronizace pro všechny operace vyžádání obsahu.
  * TodoItem: Ukládá položky úkolů. Systémové sloupce **createdAt**, **updatedAt**, a **verze** jsou vlastnosti volitelné systému.

> [!NOTE]
> Mobile Apps SDK rezervuje názvy sloupců, které začínají "**``**". Nepoužívejte tuto předponu s jakoukoli jinou hodnotu než systémové sloupce. Názvy sloupců jsou, jinak hodnota upravovat, když používáte vzdálené back-end.
>
>

Pokud používáte funkci offline synchronizace, definujte tabulky dat a tři systémové tabulky.

### <a name="system-tables"></a>Systémové tabulky

**MS_TableOperations**  

![Atributy MS_TableOperations tabulky][defining-core-data-tableoperations-entity]

| Atribut | Typ |
| --- | --- |
| id | Celé číslo 64 |
| itemId | Řetězec |
| properties | Binární Data |
| tabulka | Řetězec |
| tableKind | Celé číslo 16 |


**MS_TableOperationErrors**

 ![Atributy MS_TableOperationErrors tabulky][defining-core-data-tableoperationerrors-entity]

| Atribut | Typ |
| --- | --- |
| id |Řetězec |
| operationId |Celé číslo 64 |
| properties |Binární Data |
| tableKind |Celé číslo 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atribut | Typ |
| --- | --- |
| id |Řetězec |
| key |Řetězec |
| Typ_klíče. |Celé číslo 64 |
| tabulka |Řetězec |
| hodnota |Řetězec |

### <a name="data-table"></a>Datová tabulka

**TodoItem**

| Atribut | Typ | Poznámka |
| --- | --- | --- |
| id | Řetězec, označen jako požadovaný |primární klíč v vzdáleného úložiště |
| Dokončení | Logická hodnota | Pole položky seznamu úkolů |
| Text |Řetězec |Pole položky seznamu úkolů |
| CreatedAt | Datum | (volitelné) Se mapuje na **createdAt** vlastnost systému |
| updatedAt | Datum | (volitelné) Se mapuje na **updatedAt** vlastnost systému |
| verze | Řetězec | (volitelné) Používá ke zjišťování konfliktů, map k verzi |

## <a name="setup-sync"></a>Změna chování aplikace při synchronizaci
V této části upravíte aplikace tak, aby ho na spuštění aplikace nebo když vložení a aktualizace položky není synchronizovaná. Synchronizuje se jenom v případě, že se provádí gesto tlačítko Aktualizovat.

**Jazyka Objective-C**:

1. V **QSTodoListViewController.m**, změnit **viewDidLoad** metoda odebrat volání `[self refresh]` na konci metody. Data se teď není synchronizovaný se serverem při spuštění aplikace. Místo toho je synchronizovaný s obsahem místní úložiště.
2. V **QSTodoService.m**, upravte definici `addItem` tak, aby se nebude synchronizovat po vložení položky. Odeberte `self syncData` blokovat a nahraďte ji následujícím kódem:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Upravit definici `completeItem` jak je uvedeno nahoře. Odeberte blok pro `self syncData` a nahraďte ji následujícím kódem:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Kód SWIFT**:

V `viewDidLoad`v **ToDoTableViewController.swift**, komentář dva řádky, které jsou zde uvedeny zastavit synchronizuje se při spuštění aplikace. V době psaní tohoto textu aplikace Swift Todo nelze aktualizovat službu, když někdo přidá nebo dokončení položku. Aktualizuje službu pouze při spuštění aplikace.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testování aplikace
V této části se můžete připojit k neplatná adresa URL k simulaci offline scénář. Když přidáte datových položek, jste uchovávat v místní základní datové úložiště, ale není synchronizovaná s back-end mobilní aplikace.

1. Změňte adresu URL mobilní aplikace v **QSTodoService.m** na neplatná adresa URL a znovu spusťte aplikaci:

   **Jazyka Objective-C**. V QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Kód SWIFT**. V ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Přidejte některé položky seznamu úkolů. Ukončení simulátoru (nebo vynuceně zavřít aplikaci) a pak ji znovu spusťte. Ověřte, že vaše změny se zachovají.

3. Zobrazit obsah vzdáleného **TodoItem** tabulky:
   * Pro Node.js back-end, přejděte na [portál Azure](https://portal.azure.com/) a ve vaší mobilní aplikace back-end, klikněte na tlačítko **snadno tabulky** > **TodoItem**.  
   * Pro .NET zpět ukončení, použijte nástroj SQL, jako je například SQL Server Management Studio, nebo klienta REST, například aplikaci Fiddler nebo Postman.  

4. Ověřte, že nové položky *není* byla synchronizovat se serverem.

5. Změňte adresu URL zpět na tu správnou v **QSTodoService.m**a znovu spusťte aplikaci.

6. Proveďte aktualizaci gesto přidáváním dolů seznam položek.  
Zobrazí se průběh číselník.

7. Zobrazení **TodoItem** data znovu. Úkolů nové a změněné položky by teď mělo být zobrazené.

## <a name="summary"></a>Souhrn
K podpoře funkce offline synchronizace, jsme použili `MSSyncTable` rozhraní a inicializovat `MSClient.syncContext` s místním úložištěm. V takovém případě se místní úložiště databáze založené na datech jádra.

Pokud používáte místní úložiště dat jádra, je nutné zadat několik tabulek s [opravte vlastnosti systému](#review-core-data).

Normální vytvořit, číst, aktualizovat a odstranit operace pro mobilní aplikace pracují, jako kdyby aplikace stále připojený, ale všechny operace nastat proti místní úložiště.

Když jsme místní úložiště synchronizovány se serverem, jsme použili **MSSyncTable.pullWithQuery** metoda.

## <a name="additional-resources"></a>Další zdroje informací:
* [Offline synchronizací dat v Mobile Apps]
* [Obálka cloudu: Offline synchronizace v Azure Mobile Services] \(je o Mobile Services, ale Mobile Apps offline synchronizace funguje podobným způsobem.\)

<!-- URLs. -->


[vytvořit aplikaci pro iOS]: app-service-mobile-ios-get-started.md
[Offline synchronizací dat v Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Obálka cloudu: Offline synchronizace v Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
