---
title: Povolení offline synchronizace u mobilních aplikací pro iOS | Dokumentace Microsoftu
description: Zjistěte, jak používat mobilní aplikace Azure App Service do mezipaměti a synchronizaci offline dat v aplikacích pro iOS.
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
ms.openlocfilehash: bc0afcf1ac7d9e7a777d850e1b6df7b915837f3a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956870"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Povolení offline synchronizace u mobilních aplikací pro iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz se zabývá offline synchronizaci s funkcí Mobile Apps služby Azure App Service pro iOS. S offline synchronizaci koncoví uživatelé můžou pracovat s mobilní aplikaci a zobrazit, přidat ani upravovat data, i když nemají žádné připojení k síti. Změny jsou uloženy v místní databázi. Jakmile je zařízení online, změny synchronizují s vzdálené back-endu.

Pokud je toto vaše první zkušenost s funkcí Mobile Apps, byste nejprve dokončit kurz [Vytvoření aplikace pro iOS]. Pokud nepoužijete stažené úvodní serverový projekt, musíte přidat balíčky rozšíření přístup k datům do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v mobilních aplikacích].

## <a name="review-sync"></a>Revize kódu synchronizace klienta
Klientský projekt, který jste stáhli pro [Vytvoření aplikace pro iOS] kurzu již obsahuje kód, který podporuje offline synchronizaci s použitím místní databáze založené na datech Core. Tento oddíl shrnuje, co je již součástí kódu. Koncepční přehled funkce, najdete v části [Synchronizace offline dat v mobilních aplikacích].

Offline synchronizace dat funkci Mobile Apps, pomocí můžete koncovým uživatelům pracovat s místní databází i v případě, že síť není dostupný. Pokud chcete tyto funkce využít ve vaší aplikaci, inicializovat kontext synchronizace `MSClient` a odkazovat na místní úložiště. Pak můžete odkazovat na tabulky prostřednictvím **MSSyncTable** rozhraní.

V **QSTodoService.m** (Objective-C) nebo **ToDoTableViewController.swift** (Swift), Všimněte si, že typ člena **syncTable** je **MSSyncTable** . Offline synchronizace používá toto rozhraní tabulky synchronizace místo **MSTable**. Při použití synchronizace tabulky všechny operace přejděte do místního úložiště a jsou synchronizovány pouze s vzdálené back-endu s explicitní operacemi push a pull.

 Chcete-li získat odkaz na tabulku synchronizace, použijte **syncTableWithName** metodu na `MSClient`. Chcete-li odebrat funkci offline synchronizace, použijte **tableWithName** místo.

Před provedením jakékoli operace s tabulkou, musí být inicializován místního úložiště. Tady je příslušný kód:

* **Objective-C**. V **QSTodoService.init** metody:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Kód SWIFT**. V **ToDoTableViewController.viewDidLoad** metody:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Tato metoda vytvoří pomocí místního úložiště `MSCoreDataStore` rozhraní, které poskytuje sady SDK mobilních aplikací. Alternativně můžete zadat jiný místní úložiště pomocí implementace `MSSyncContextDataSource` protokolu. Také, první parametr **MSSyncContext** je možné určit obslužnou rutinu konflikt. Protože jsme prošly `nil`, získáme obslužná rutina konflikt výchozí v rozporu se nezdaří.

Teď můžeme provádět operace skutečné synchronizace a získejte data ze vzdáleného back-endu:

* **Objective-C**. `syncData` nejprve vložení nových změn a pak zavolá **pullData** k získání dat z vzdálené back-endu. Pak **pullData** metoda načte nová data, která odpovídá dotazu:

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

Ve verzi jazyka Objective-C v `syncData`, nejprve volat **pushWithCompletion** v kontextu synchronizace. Tato metoda je členem `MSSyncContext` (a ne samotné tabulky synchronizace) vzhledem k tomu, že ho nabízených oznámení změny ve všech tabulkách. Pouze záznamy, které byly změněny nějakým způsobem místně (prostřednictvím operací CUD) odesílají na server. Potom pomocné rutiny **pullData** je volána, který volá **MSSyncTable.pullWithQuery** načíst vzdálená data a uloží je do místní databáze.

Na Swift verze, protože nebylo nezbytně nutné, pomocí operace push není žádná volání **pushWithCompletion**. Pokud nejsou žádné čekající změny v rámci synchronizace pro tabulky, který provádí operace push, pull vždy vydá push nejprve. Pokud máte více než jedné tabulky synchronizace, je však nejvhodnější explicitně volat metodou push do Ujistěte se, že všechno, co je konzistentní napříč související tabulky.

V konzoli Objective-C a Swift verze, můžete použít **pullWithQuery** metoda zadat dotaz pro filtrování záznamů, které chcete načíst. V tomto příkladu dotaz načte všechny záznamy ve vzdáleném úložišti `TodoItem` tabulky.

Druhý parametr **pullWithQuery** je ID dotazu, který se používá pro *Přírůstková synchronizace*. Přírůstková synchronizace načte pouze záznamy, které byly změněny od poslední synchronizace pomocí záznamu `UpdatedAt` časové razítko (volá `updatedAt` v místním úložišti.) ID dotazu by měl být popisný řetězec, který je jedinečný pro každý logický dotaz ve vaší aplikaci. Pokud chcete vyjádřit výslovný nesouhlas Přírůstková synchronizace, předejte `nil` jako ID dotazu. Tento přístup může být potenciálně neefektivní, protože načte všechny záznamy v každé operace přijetí změn.

Aplikace Objective-C synchronizuje při úpravě nebo přidání dat, když uživatel provede aktualizaci gesta a při jeho spuštění.

Aplikace Swift synchronizuje, když uživatel provádí aktualizace gesta a při jeho spuštění.

Protože aplikace se synchronizuje pokaždé, když se data změněna (Objective-C), nebo při každém spuštění aplikace (Objective-C a Swift), aplikace předpokládá, že uživatel je online. V další části bude aplikaci aktualizovat tak, aby uživatelé mohou upravovat, i když budou offline.

## <a name="review-core-data"></a>Přečtěte si základní datový model
Při použití úložiště offline základních dat, je nutné definovat konkrétní tabulky a pole v datovém modelu. Ukázková aplikace už obsahuje datového modelu o správném formátu. V této části provedeme tyto tabulky ukazují, jak se používají.

Otevřít **QSDataModel.xcdatamodeld**. Čtyři tabulky jsou definovány--tři, které používají sadu SDK a ten, který se používá pro úkol položky sami:
  * MS_TableOperations: Sleduje v položkách, které mají být synchronizovány se serverem.
  * MS_TableOperationErrors: Sleduje všechny chyby, ke kterým dochází při offline synchronizaci.
  * MS_TableConfig: Sleduje poslední aktualizace času poslední operace synchronizace pro všechny operace přijetí změn.
  * TodoItem: Ukládá položky úkolů. Systémovými sloupci **createdAt**, **updatedAt**, a **verze** jsou volitelné systémové vlastnosti.

> [!NOTE]
> Sady Mobile Apps SDK rezervuje názvy sloupců, které začínají řetězcem "**``**". Nepoužívejte tuto předponu s nic jiného než systémovými sloupci. V opačném případě jsou názvy sloupců změnit při použití vzdáleného back-endu.
>
>

Když použijete funkci offline synchronizace, definujte tři systémové tabulky a data tabulky.

### <a name="system-tables"></a>Systémové tabulky

**MS_TableOperations**  

![Atributy MS_TableOperations tabulky][defining-core-data-tableoperations-entity]

| Atribut | Typ |
| --- | --- |
| id | Celé číslo 64 |
| ID položky | Řetězec |
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
| Typ klíče |Celé číslo 64 |
| tabulka |Řetězec |
| hodnota |Řetězec |

### <a name="data-table"></a>Tabulka dat

**TodoItem**

| Atribut | Typ | Poznámka |
| --- | --- | --- |
| id | Řetězec, označen jako požadovaný |primární klíč do vzdáleného úložiště |
| Dokončení | Logická hodnota | Pole položky seznamu úkolů |
| text |Řetězec |Pole položky seznamu úkolů |
| createdAt | Datum | (volitelné) Mapuje **createdAt** vlastnost systému |
| updatedAt | Datum | (volitelné) Mapuje **updatedAt** vlastnost systému |
| version | Řetězec | (volitelné) Slouží ke zjištění konfliktu, mapuje se na verzi |

## <a name="setup-sync"></a>Změny chování aplikace při synchronizaci
V této části upravíte aplikaci tak, aby na začátku aplikace nebo při vkládání a aktualizace položek nesynchronizuje. Synchronizuje se pouze v případě, že se provádí gesta tlačítko Aktualizovat.

**Objective-C**:

1. V **QSTodoListViewController.m**, změnit **viewDidLoad** metoda odebrání volání `[self refresh]` na konec metody. Data se teď synchronizovat se serverem při spuštění aplikace. Místo toho se synchronizuje s obsahem z místního úložiště.
2. V **QSTodoService.m**, upravte definici `addItem` tak, aby nebude synchronizovat po vložení položky. Odeberte `self syncData` blokovat a nahraďte následujícím kódem:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Upravit definici `completeItem` jak už bylo zmíněno dříve. Odeberte blok pro `self syncData` a nahraďte následujícím kódem:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Kód SWIFT**:

V `viewDidLoad`v **ToDoTableViewController.swift**, Odkomentujte následující dva řádky je znázorněno zde, zastavit, synchronizuje se při spuštění aplikace. V době psaní tohoto návodu aplikace Swift úkolů neaktualizuje službu, když někdo přidá nebo dokončení položky. Aktualizuje službu pouze při spuštění aplikace.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testování aplikace
V této části se připojíte k neplatnou adresu URL pro simulaci scénáři offline. Při přidávání položek dat, se nachází v místní ukládání základních dat, ale nejsou synchronizované s back-endu mobilních aplikací.

1. Změňte adresu URL mobilní aplikace v **QSTodoService.m** na neplatnou adresu URL a znovu spusťte aplikaci:

   **Objective-C**. V QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Kód SWIFT**. V ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Přidejte několik položek úkolů. Simulátor ukončete (nebo vynuceně zavřít aplikaci) a pak ji znovu spusťte. Ověřte, že změny uchovat.

3. Zobrazení obsahu vzdáleného **TodoItem** tabulky:
   * Pro Node.js back-end, přejděte [webu Azure portal](https://portal.azure.com/) a ve vaší mobilní aplikace back-endu, klikněte na tlačítko **jednoduché tabulky** > **TodoItem**.  
   * Pro .NET zpět ukončit, použijte nástroj SQL, jako je například SQL Server Management Studio nebo klienta REST, jako je například Fiddler nebo Postman.  

4. Ověřte, že nové položky mají *není* se synchronizovat se serverem.

5. Změňte adresu URL zpět na tu správnou v **QSTodoService.m**a znovu spusťte aplikaci.

6. Proveďte gesto aktualizace to potažením dolů seznam položek.  
Zobrazí se rotující indikátor průběhu.

7. Zobrazení **TodoItem** data znovu. Položky úkolů nové a změněné by teď mělo být zobrazené.

## <a name="summary"></a>Souhrn
K podpoře funkce offline synchronizaci, jsme použili `MSSyncTable` rozhraní a inicializován `MSClient.syncContext` s místním úložištěm. V tomto případě místní úložiště byla databáze založené na základní Data.

Při použití místního úložiště základních dat, je nutné definovat několik tabulek s [opravte vlastnosti systému](#review-core-data).

Normální vytvoření, čtení, aktualizace a odstranění (CRUD) operací pro mobilní aplikace pracují, jako kdyby je stále připojená aplikace se ale vyskytnout všechny operace na místní úložiště.

Když jsme místní úložiště synchronizované se serverem, jsme použili **MSSyncTable.pullWithQuery** metody.

## <a name="additional-resources"></a>Další zdroje informací:
* [Synchronizace offline dat v mobilních aplikacích]
* [Cloud Cover: Offline synchronizace v Azure Mobile Services] \(je videa o Mobile Services, ale mobilní aplikace offline synchronizaci funguje podobným způsobem.\)

<!-- URLs. -->


[Vytvoření aplikace pro iOS]: app-service-mobile-ios-get-started.md
[Synchronizace offline dat v mobilních aplikacích]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline synchronizace v Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
