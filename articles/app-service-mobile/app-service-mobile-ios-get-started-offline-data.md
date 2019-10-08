---
title: Povolení offline synchronizace s mobilními aplikacemi pro iOS | Microsoft Docs
description: Naučte se používat Azure App Service Mobile Apps k ukládání a synchronizaci offline dat v aplikacích pro iOS.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 0c96442de5b8eea2ec969c48e6a815b6ae78b5c4
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027280"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Povolení offline synchronizace s mobilními aplikacemi pro iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj koncových a integrovaných služeb od centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) ještě dnes.

## <a name="overview"></a>Přehled
Tento kurz pokrývá offline synchronizaci s funkcí Mobile Apps Azure App Service pro iOS. Při offline synchronizaci koncoví uživatelé můžou komunikovat s mobilní aplikací a zobrazovat, přidávat nebo upravovat data, i když nemají žádné síťové připojení. Změny jsou uloženy v místní databázi. Až bude zařízení zase online, změny se synchronizují se vzdáleným back-endu.

Pokud je to vaše první prostředí s Mobile Apps, měli byste nejdřív dokončit kurz [Vytvoření aplikace pro iOS]. Pokud nepoužíváte stažený serverový projekt pro rychlý Start, musíte do svého projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete [Synchronizace offline dat v Mobile Apps].

## <a name="review-sync"></a>Kontrola kódu synchronizace klienta
Klientský projekt, který jste stáhli v kurzu [Vytvoření aplikace pro iOS] , už obsahuje kód, který podporuje offline synchronizaci s použitím místní databáze založené na datech. Tato část shrnuje, co už je součástí kódu kurzu. Koncepční přehled této funkce najdete [Synchronizace offline dat v Mobile Apps].

Pomocí funkce synchronizace offline dat Mobile Apps můžou koncoví uživatelé pracovat s místní databází i v případě, že síť není dostupná. Chcete-li použít tyto funkce v aplikaci, inicializujte kontext synchronizace `MSClient` a odkázat na místní úložiště. Pak odkazujete na tabulku prostřednictvím rozhraní **MSSyncTable** .

V **QSTodoService. m** (cíl-C) nebo **ToDoTableViewController. SWIFT** (SWIFT) si všimněte, že typ **synchronizace** členů je **MSSyncTable**. Offline synchronizace používá toto rozhraní tabulky synchronizace místo **MSTable**. Při použití synchronizační tabulky budou všechny operace přejít do místního úložiště a synchronizovat se pouze se vzdáleným back-end s explicitními operacemi push a Pull.

 Chcete-li získat odkaz na tabulku synchronizace, použijte metodu **syncTableWithName** na `MSClient`. Pokud chcete odebrat funkci offline synchronizace, místo toho použijte **tableWithName** .

Před provedením jakékoli operace tabulky je nutné inicializovat místní úložiště. Zde je příslušný kód:

* **Cíl – C**. V metodě **QSTodoService. init** :

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **SWIFT**. V metodě **ToDoTableViewController. viewDidLoad** :

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Tato metoda vytvoří místní úložiště pomocí rozhraní @no__t 0, které poskytuje sada SDK pro Mobile Apps. Alternativně můžete poskytnout jiné místní úložiště implementací protokolu `MSSyncContextDataSource`. Také první parametr **MSSyncContext** slouží k určení obslužné rutiny konfliktu. Vzhledem k tomu, že jsme prošli `nil`, získáme výchozí obslužnou rutinu konfliktu, která při jakémkoli konfliktu neprojde.

Teď provedeme skutečnou operaci synchronizace a získáme data ze vzdáleného back-endu:

* **Cíl – C**. @no__t – 0 první vloží nové změny a potom zavolá **pullData** , aby získala data ze vzdáleného back-endu. Pak metoda **pullData** získá nová data, která odpovídají dotazu:

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
* **SWIFT**:
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

V rámci verze cíl-C v `syncData` nejdříve zavolejte **pushWithCompletion** v kontextu synchronizace. Tato metoda je členem `MSSyncContext` (a nikoli tabulkou Sync), protože přenáší změny napříč všemi tabulkami. Na server jsou odesílány pouze záznamy, které byly místně upravovány (prostřednictvím operací CUD). Pak se zavolá pomocný **pullData** , který volá **MSSyncTable. pullWithQuery** , aby načetl Vzdálená data a uložil je do místní databáze.

V verzi SWIFT, protože nabízená operace nebyla striktně nutná, není volání **pushWithCompletion**. Pokud v kontextu synchronizace nečekají nějaké změny pro tabulku, která provádí operaci Push, vyžádat si před ní vždycky nabízenou oznámení. Pokud však máte více než jednu synchronizační tabulku, je vhodné explicitně volat metodu push, aby bylo zajištěno, že vše je konzistentní napříč souvisejícími tabulkami.

V rámci verze cíle C i SWIFT můžete pomocí metody **pullWithQuery** určit dotaz pro filtrování záznamů, které chcete načíst. V tomto příkladu dotaz načte všechny záznamy ve vzdálené tabulce `TodoItem`.

Druhým parametrem **pullWithQuery** je ID dotazu, které se používá pro *přírůstkovou synchronizaci*. Přírůstková synchronizace načte jenom záznamy, které se od poslední synchronizace změnily, pomocí časového razítka `UpdatedAt` záznamu (označovaného `updatedAt` v místním úložišti.) ID dotazu by měl být popisný řetězec, který je jedinečný pro každý logický dotaz ve vaší aplikaci. Pokud chcete odhlásit přírůstkovou synchronizaci, předejte `nil` jako ID dotazu. Tento přístup může být potenciálně neefektivní, protože načte všechny záznamy na každé operaci vyžádání obsahu.

Aplikace cíl-C se synchronizuje při změně nebo přidávání dat, když uživatel provede gesto aktualizace a při spuštění.

Aplikace SWIFT se synchronizuje, když uživatel provede gesto aktualizace a při spuštění.

Vzhledem k tomu, že se aplikace synchronizuje pokaždé, když se upraví data (cíl-C) nebo když se aplikace spustí (objektivní-C a SWIFT), aplikace předpokládá, že je uživatel online. V pozdější části budete aplikaci aktualizovat tak, aby uživatelé mohli upravovat i v případě, že jsou offline.

## <a name="review-core-data"></a>Kontrola základního datového modelu
Když použijete základní úložiště dat v režimu offline, musíte v datovém modelu definovat konkrétní tabulky a pole. Ukázková aplikace už obsahuje datový model se správným formátem. V této části projdeme tyto tabulky a ukážeme, jak se používají.

Otevřete **QSDataModel. xcdatamodeld**. Čtyři tabulky jsou definovány – tři, které používá sada SDK a ta, která se používá pro vlastní položky úkolů:
  * MS_TableOperations: sleduje položky, které je třeba synchronizovat se serverem.
  * MS_TableOperationErrors: sleduje všechny chyby, ke kterým dochází při offline synchronizaci.
  * MS_TableConfig: sleduje čas poslední aktualizace poslední operace synchronizace pro všechny operace Pull.
  * TodoItem: ukládá položky úkolů. Systémové sloupce **createdAt**, **updatedAt**a **Version** jsou volitelné systémové vlastnosti.

> [!NOTE]
> Sada SDK pro Mobile Apps rezervuje názvy sloupců, které začínají**na "``** ". Nepoužívejte tuto předponu s jinými než systémovými sloupci. Jinak se názvy sloupců při použití vzdáleného back-endu upraví.
>
>

Pokud používáte funkci offline synchronizace, definujte tři systémové tabulky a tabulku dat.

### <a name="system-tables"></a>Systémové tabulky

**MS_TableOperations**  

![Atributy tabulky MS_TableOperations][defining-core-data-tableoperations-entity]

| Atribut | Typ |
| --- | --- |
| id | Celé číslo 64 |
| itemId | Řetězec |
| properties | Binární data |
| Stolní | Řetězec |
| tableKind | Celé číslo 16 |


**MS_TableOperationErrors**

 ![Atributy tabulky MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

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
| keyType |Celé číslo 64 |
| Stolní |Řetězec |
| hodnota |Řetězec |

### <a name="data-table"></a>Tabulka dat

**TodoItem**

| Atribut | Typ | Poznámka |
| --- | --- | --- |
| id | Řetězec, označeno jako povinné |Primární klíč na vzdáleném úložišti |
| Plňte | Logická hodnota | Pole pro položku |
| text |Řetězec |Pole pro položku |
| CreatedAt | Datum | volitelné Mapuje na **createdAt** systémovou vlastnost |
| updatedAt | Datum | volitelné Mapuje na **updatedAt** systémovou vlastnost |
| version | Řetězec | volitelné Používá se k detekci konfliktů, mapování na verzi. |

## <a name="setup-sync"></a>Změna chování aplikace v synchronizaci
V této části aplikaci upravíte tak, aby se nesynchronizoval při spuštění aplikace, nebo když vložíte a aktualizujete položky. Synchronizuje se pouze v případě, že je provedeno tlačítko pro obnovení gesta.

**Cíl-C**:

1. V **QSTodoListViewController. m**změňte metodu **viewDidLoad** pro odebrání volání `[self refresh]` na konci metody. Data nejsou teď synchronizovaná se serverem při spuštění aplikace. Místo toho se synchronizuje s obsahem místního úložiště.
2. V **QSTodoService. m**upravte definici `addItem` tak, aby se po vložení položky nesynchronizoval. Odeberte blok `self syncData` a nahraďte ho následujícím:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Upravte definici `completeItem` jak je uvedeno výše. Odeberte blok pro `self syncData` a nahraďte ho následujícím:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**SWIFT**:

V `viewDidLoad` v **ToDoTableViewController. SWIFT**přidejte komentář ke všem zobrazeným řádkům a zastavte synchronizaci při spuštění aplikace. V době psaní tohoto textu aplikace SWIFT TODO neaktualizuje službu, když někdo přidá nebo dokončí položku. Aktualizuje službu jenom při spuštění aplikace.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testování aplikace
V této části se připojíte k neplatné adrese URL pro simulaci offline scénáře. Když přidáte datové položky, jsou uchovávány v místním úložišti dat, ale nejsou synchronizovány pomocí back-endu mobilní aplikace.

1. Změňte adresu URL mobilní aplikace v **QSTodoService. m** na neplatnou adresu URL a spusťte aplikaci znovu:

   **Cíl – C**. V QSTodoService. m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **SWIFT**. V ToDoTableViewController. SWIFT:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Přidejte některé položky úkolů. Ukončete simulátor (nebo aplikaci vynuceně ukončete) a pak ji znovu spusťte. Ověřte, zda vaše změny přetrvávají.

3. Zobrazení obsahu vzdálené tabulky **TodoItem** :
   * V případě back-endu Node. js přejděte na [Azure Portal](https://portal.azure.com/) a v back-endu mobilní aplikace klikněte na **snadné tabulky** > **TodoItem**.  
   * Pro back-end .NET použijte buď nástroj SQL, například SQL Server Management Studio, nebo klienta REST, jako je například Fiddler nebo post.  

4. Ověřte, že nové *položky nebyly* synchronizovány se serverem.

5. Změňte adresu URL na správnou v **QSTodoService. m**a znovu spusťte aplikaci.

6. Spusťte gesto obnovení tak, že seznam položek odvedete.  
Zobrazí se číselník průběhu.

7. Znovu zobrazte data **TodoItem** . Nyní by se měly zobrazit nové a změněné položky úkolů.

## <a name="summary"></a>Souhrn
Pro podporu funkce offline synchronizace používáme rozhraní @no__t 0 a inicializované `MSClient.syncContext` s místním úložištěm. V tomto případě je místní úložiště základní databází založenou na datech.

Pokud používáte místní úložiště dat základního data, je nutné definovat několik tabulek se [správnými vlastnostmi systému](#review-core-data).

Operace normálního vytváření, čtení, aktualizace a odstranění (CRUD) pro mobilní aplikace fungují jako v případě, že je aplikace stále připojená, ale všechny operace nastávají v místním úložišti.

Při synchronizaci místního úložiště se serverem jsme použili metodu **MSSyncTable. pullWithQuery** .

## <a name="additional-resources"></a>Další zdroje informací:
* [Synchronizace offline dat v Mobile Apps]
* [Cloudové pokrytí: offline synchronizace v Azure Mobile Services] \(The video se o Mobile Services, ale Mobile Apps offline synchronizace funguje podobným způsobem. \)

<!-- URLs. -->


[Vytvoření aplikace pro iOS]: app-service-mobile-ios-get-started.md
[Synchronizace offline dat v Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloudové pokrytí: offline synchronizace v Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
