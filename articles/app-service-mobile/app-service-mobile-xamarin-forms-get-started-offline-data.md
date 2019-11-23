---
title: Povolení offline synchronizace pro mobilní aplikace Azure (Xamarin. Forms) | Microsoft Docs
description: Naučte se používat App Service mobilní aplikace pro ukládání a synchronizaci offline dat v aplikaci Xamarin. Forms.
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 65bd619a95b102c4ad4be57f1661a89fbae43e22
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388428"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikace Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled
Tento kurz zavádí funkci offline synchronizace pro Azure Mobile Apps pro Xamarin. Forms. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidávání nebo upravování dat – i když není dostupné žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení zase online, tyto změny se synchronizují se vzdálenou službou.

Tento kurz je založený na řešení rychlý Start pro Xamarin. Forms pro Mobile Apps, které vytvoříte po dokončení kurzu [vytvoření aplikace Xamarin iOS]. Řešení rychlé zprovoznění pro Xamarin. Forms obsahuje kód pro podporu offline synchronizace, který stačí povolit. V tomto kurzu aktualizujete řešení rychlého startu, abyste mohli zapnout offline funkce Azure Mobile Apps. V aplikaci také zvýrazníte kód specifický pro práci v režimu offline. Pokud nepoužíváte stažené řešení pro rychlé zprovoznění, musíte do svého projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps][1].

Další informace o funkci offline synchronizace najdete v tématu [synchronizace offline dat v Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Povolení funkce offline synchronizace v řešení pro rychlé zprovoznění
Kód offline synchronizace je součástí projektu pomocí C# direktiv preprocesoru. Pokud je definován symbol **\_synchronizace\_offline** , jsou tyto cesty kódu zahrnuty do sestavení. Pro aplikace pro Windows je nutné nainstalovat také platformu SQLite.

1. V aplikaci Visual Studio klikněte pravým tlačítkem na řešení > **Spravovat balíčky NuGet pro řešení...** , vyhledejte a nainstalujte balíček NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** pro všechny projekty v řešení.
2. V Průzkumník řešení otevřete soubor TodoItemManager.cs z projektu s **přenosným** názvem, který je Přenosná knihovna tříd projektu, a pak zrušte komentář následující direktivy preprocesoru:

        #define OFFLINE_SYNC_ENABLED
3. Volitelné Pro podporu zařízení s Windows nainstalujte jeden z následujících balíčků za běhu programu SQLite:

   * **Windows 8.1 runtime:** Nainstalujte [SQLite pro Windows 8.1][3].
   * **Windows Phone 8,1:** Nainstalujte [SQLite pro Windows Phone 8,1][4].
   * **Univerzální platforma Windows** Nainstalujte [SQLite pro univerzální univerzální systém Windows][5].

     I když rychlý start neobsahuje univerzální projekt pro Windows, podpora Univerzální platformy Windows je podporovaná ve formulářích Xamarin.
4. Volitelné V každém projektu aplikace pro Windows klikněte pravým tlačítkem na **odkazy** > **Přidat odkaz...** , rozbalte **rozšíření**složky **Windows** >.
    Povolte příslušné **SQLite pro sadu Windows** SDK spolu s **modulem C++ runtime Visual 2013 pro sadu Windows** SDK.
    Názvy sady SDK SQLite se mírně liší u jednotlivých platforem Windows.

## <a name="review-the-client-sync-code"></a>Kontrola kódu synchronizace klienta
Tady je stručný přehled toho, co je již součástí kódu kurzu v rámci direktiv `#if OFFLINE_SYNC_ENABLED`. Funkce offline synchronizace je v souboru projektu TodoItemManager.cs v přenositelném projektu knihovny tříd. Koncepční přehled této funkce najdete v tématu [synchronizace offline dat v Azure Mobile Apps][2].

* Před provedením jakékoli operace tabulky je nutné inicializovat místní úložiště. Místní databáze úložiště je inicializována v konstruktoru třídy **TodoItemManager** pomocí následujícího kódu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Tento kód vytvoří novou místní databázi SQLite pomocí třídy **MobileServiceSQLiteStore** .

    Metoda **define** vytvoří tabulku v místním úložišti, která odpovídá polím v poskytnutém typu.  Typ nemusí zahrnovat všechny sloupce, které jsou ve vzdálené databázi. Je možné uložit podmnožinu sloupců.
* Pole **TODO** v **TodoItemManager** je typ **IMobileServiceSyncTable** namísto **IMobileServiceTable**. Tato třída používá místní databázi pro všechny operace vytvoření, čtení, aktualizace a odstranění (CRUD) tabulky. Určíte, kdy se tyto změny vloží do back-endu mobilní aplikace, voláním **PushAsync** na **IMobileServiceSyncContext**. Kontext synchronizace pomáhá zachovat vztahy mezi tabulkami sledováním a vkládáním změn ve všech tabulkách. klientská aplikace byla při volání **PushAsync** změněna.

    Pro synchronizaci s back-endu mobilní aplikace se volá následující metoda **SyncAsync** :

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Tato ukázka používá jednoduché zpracování chyb s výchozí obslužnou rutinou synchronizace. Skutečná aplikace by zpracovávala různé chyby, jako jsou třeba síťové podmínky a konflikty serveru, pomocí vlastní implementace **IMobileServiceSyncHandler** .

## <a name="offline-sync-considerations"></a>Pokyny k offline synchronizaci
V ukázce je metoda **SyncAsync** volána pouze při spuštění a při žádosti o synchronizaci.  Pokud chcete zahájit synchronizaci v aplikaci pro Android nebo iOS, přejděte na seznam položek. pro Windows použijte tlačítko **synchronizovat** . V reálné aplikaci můžete také při změně stavu sítě vytvořit aktivační událost synchronizace.

Při spuštění operace vyžádání na tabulce, která má nedokončené místní aktualizace sledované kontextem, tato operace vyžádané replikace automaticky aktivuje předchozí nabízený kontext. Při aktualizaci, přidávání a dokončování položek v této ukázce můžete vynechat explicitní volání **PushAsync** .

V poskytnutém kódu se dotazují všechny záznamy ve vzdálené tabulce TodoItem, ale je také možné filtrovat záznamy předáním ID dotazu a dotazu do **PushAsync**. Další informace najdete v části o *přírůstkové synchronizaci* při [synchronizaci offline dat v Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Spuštění klientské aplikace
Když je offline synchronizace zapnutá, spusťte na každé platformě aspoň jednou klientskou aplikaci, aby se naplnila místní databáze úložiště. Později Simulujte scénář offline a upravíte data v místním úložišti, zatímco je aplikace offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizace chování synchronizace klientské aplikace
V této části upravíte projekt klienta pro simulaci offline scénáře pomocí neplatné adresy URL aplikace pro váš back-end. Případně můžete vypnout síťová připojení přesunutím zařízení do režimu v letadle.  Když přidáte nebo změníte datové položky, tyto změny se uchovávají v místním úložišti, ale nesynchronizují se do úložiště dat back-endu, dokud se připojení znovu nevytvoří.

1. V Průzkumník řešení otevřete soubor projektu Constants.cs z **přenosného** projektu a změňte hodnotu `ApplicationURL` tak, aby odkazovala na neplatnou adresu URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otevřete soubor TodoItemManager.cs z **přenosného** projektu a pak přidejte **catch** pro základní třídu **výjimky** do **Try... blok catch** v **SyncAsync**. Tento blok **catch** zapíše zprávu výjimky do konzoly, a to následujícím způsobem:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Sestavte a spusťte klientskou aplikaci.  Přidejte nějaké nové položky. Všimněte si, že v konzole je zaznamenána výjimka pro každý pokus o synchronizaci s back-end. Tyto nové položky existují jenom v místním úložišti, dokud je nebudete moct vložit do mobilního back-endu. Klientská aplikace se chová, jako by byla připojena k back-endu, a podporuje všechny operace vytvoření, čtení, aktualizace a odstranění (CRUD).
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvale uložené v místním úložišti.
5. Volitelné Pomocí sady Visual Studio si můžete zobrazit tabulku Azure SQL Database, abyste viděli, že se data v back-end databázi nezměnila.

    V aplikaci Visual Studio otevřete **Průzkumník serveru**. Přejděte do databáze ve **službě Azure**->**databáze SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumník objektů systému SQL Server**. Teď můžete přejít k tabulce SQL Database a jejímu obsahu.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizace klientské aplikace pro opětovné připojení k vašemu mobilnímu back-endu
V této části znovu připojíte aplikaci k mobilnímu back-endu, která simuluje aplikaci, která se vrátí zpátky do online stavu. Při provádění gesta aktualizace se data synchronizují do mobilního back-endu.

1. Znovu otevřete Constants.cs. Opravte `applicationURL`, aby odkazovalo na správnou adresu URL.
2. Znovu sestavte a spusťte klientskou aplikaci. Aplikace se po spuštění pokusí o synchronizaci s back-endu mobilní aplikace. Ověřte, že v konzole ladění nejsou protokolovány žádné výjimky.
3. Volitelné Zobrazte aktualizovaná data pomocí Průzkumník objektů systému SQL Server nebo nástroje REST, jako je Fiddler nebo [post][6]. Všimněte si, že data byla synchronizovaná mezi databází back-end a místním úložištěm.

    Všimněte si, že data byla synchronizovaná mezi databází a místním úložištěm a obsahuje položky, které jste přidali v době, kdy byla aplikace odpojena.

## <a name="additional-resources"></a>Další prostředky
* [Synchronizace offline dat v prostředí Azure Mobile Apps][2]
* [POSTUPY pro sadu Azure Mobile Apps .NET SDK][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
