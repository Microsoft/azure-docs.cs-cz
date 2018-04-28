---
title: Vytvoření triggeru HTTP s využitím vstupní vazby Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak pomocí služby Azure Functions a triggerů HTTP dotazovat službu Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 85a9e66491513b016380913617d8e78cf5d82f6d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>Vytvoření triggeru HTTP ve službě Azure Functions s využitím vstupní vazby Azure Cosmos DB

Azure Cosmos DB je globálně distribuovaná databázová služba pro více modelů, bez schématu a bez serveru. Azure Functions je výpočetní služba bez serveru, která umožňuje spouštět kód na vyžádání. Spojením těchto dvou služeb Azure získáte základ architektury bez serveru, která vám umožní zaměřit se na vytváření skvělých aplikací bez starostí o zřizování a správu serverů pro vaše výpočetní a databázové požadavky.

Tento kurz vychází z kódu vytvořeného v tématu [Rychlý úvod k rozhraní Graph API pro .NET](create-graph-dotnet.md). V tomto kurzu se přidá funkce Azure Functions obsahující [trigger HTTP](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-bindings-http-webhook.md#http-trigger). Trigger HTTP pomocí [vstupní vazby](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-triggers-bindings.md) služby Azure Cosmos DB načítá data z databáze grafů vytvořené v rychlém startu. Tento konkrétní trigger HTTP dotazuje data ve službě Azure Cosmos DB, ale vstupní vazby ze služby Azure Cosmos DB je možné použít k načítání vstupních hodnot dat pro cokoli, co vaše funkce požaduje.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření projektu funkce Azure Functions 
> * Vytvoření triggeru HTTP
> * Publikování funkce Azure Functions
> * Připojení funkce Azure Functions k databázi Azure Cosmos DB

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017 verze 15.3](https://www.visualstudio.com/vs/preview/), včetně sady funkcí **Vývoj pro Azure**.

    ![Instalace sady Visual Studio 2017 se sadou funkcí Vývoj pro Azure](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- Po instalaci nebo upgradu sady Visual Studio 2017 na verzi 15.3 je potřeba ručně aktualizovat nástroje sady Visual Studio 2017 pro Azure Functions. Nástroje můžete aktualizovat přechodem do nabídky **Nástroje** v části **Rozšíření a aktualizace...** > **Aktualizace** > **Visual Studio Marketplace** > **Azure Functions and Web Jobs Tools** > **Aktualizovat**.

- Dokončete kurz [Vytvoření aplikace .NET využívající rozhraní Graph API](tutorial-develop-graph-dotnet.md) nebo získejte ukázkový kód z úložiště GitHub [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started) a sestavte projekt.
 
## <a name="build-a-function-in-visual-studio"></a>Vytvoření funkce v sadě Visual Studio

1. Přidejte do svého řešení projekt **Azure Functions** tím, že kliknete pravým tlačítkem na uzel řešení v **Průzkumníku řešení** a pak zvolíte **Přidat** > **Nový projekt**. V dialogovém okně zvolte **Azure Functions** a zadejte název **PeopleDataFunctions**.

   ![Přidání projektu funkce Azure Functions do řešení](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. Po vytvoření projektu Azure Functions je potřeba provést několik aktualizací a instalací souvisejících s NuGet. 

    a. Pokud chcete mít jistotu, že máte nejnovější sadu SDK služby Functions, pomocí správce NuGet aktualizujte balíček **Microsoft.NET.Sdk.Functions**. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Správa balíčků NuGet**. Na kartě **Nainstalováno** vyberte Microsoft.NET.Sdk.Functions a klikněte na **Aktualizovat**.

   ![Aktualizace balíčků NuGet](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. Na kartě **Procházet** zadáním **azure.graphs** vyhledejte balíček **Microsoft.Azure.Graphs** a pak klikněte na **Nainstalovat**. Tento balíček obsahuje klientskou sadu SDK rozhraní Graph API pro .NET.

   ![Instalace rozhraní Graph API](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    c. Na kartě **Procházet** zadáním **mono.csharp** vyhledejte balíček **Mono.CSharp** a pak klikněte na **Nainstalovat**.

   ![Instalace balíčku Mono.CSharp](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. Průzkumník řešení by teď měl obsahovat nainstalované balíčky, jak je vidět výše. 
   
   Dále musíme napsat nějaký kód, takže do projektu přidáme položku **Funkce Azure Functions**. 

    a. V **Průzkumníku řešení** klikněte pravým tlačítkem na uzel projektu a pak zvolte **Přidat** > **Nová položka**.   
    b. V dialogovém okně **Přidat novou položku** vyberte **Položky Visual C#**, pak **Funkce Azure Functions**, jako název projektu zadejte **Search** (Vyhledávání) a klikněte na **Přidat**.  
 
   ![Vytvoření nové funkce Search](./media/tutorial-functions-http-trigger/05-add-function.png)

4. Funkce Azure Functions bude reagovat na požadavky HTTP, takže je tady vhodné použít šablonu triggeru HTTP.
   
   V okně **Nová funkce Azure Functions** vyberte **Trigger HTTP**. Zároveň chceme, aby tato funkce Azure Functions byla zcela otevřená, takže nastavíme **Přístupová práva** na **Anonymní**, aby měl přístup kdokoli. Klikněte na **OK**.

   ![Nastavení přístupových práv na anonymní](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. Po přidání souboru Search.cs do projektu funkce Azure Functions zkopírujte následující příkazy **using** a nahraďte jimi stávající příkazy:

   ```csharp
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Microsoft.Azure.Documents.Linq;
   using Microsoft.Azure.Graphs;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.Http;
   using Microsoft.Azure.WebJobs.Host;
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Linq;
   using System.Net;
   using System.Net.Http;
   using System.Threading.Tasks;
   ```

6. Pak nahraďte kód třídy funkce Azure Functions následujícím kódem. Kód pomocí rozhraní Graph API vyhledá v databázi Azure Cosmos DB buď všechny lidi, nebo konkrétní osobu identifikovanou podle parametru řetězce dotazu `name`.

   ```csharp
   public static class Search
   {
       static string endpoint = ConfigurationManager.AppSettings["Endpoint"];
       static string authKey = ConfigurationManager.AppSettings["AuthKey"];

       [FunctionName("Search")]
       public static async Task<HttpResponseMessage> Run(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
           TraceWriter log)
       {
           log.Info("C# HTTP trigger function processed a request.");

           // the person objects are free-form in structure
           List<dynamic> results = new List<dynamic>();

           // open the client's connection
           using (DocumentClient client = new DocumentClient(
               new Uri(endpoint),
               authKey,
               new ConnectionPolicy
               {
                   ConnectionMode = ConnectionMode.Direct,
                   ConnectionProtocol = Protocol.Tcp
               }))
           {
               // get a reference to the database the console app created
               Database database = await client.CreateDatabaseIfNotExistsAsync(
                   new Database
                   {
                       Id = "graphdb"
                   });

               // get an instance of the database's graph
               DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
                   UriFactory.CreateDatabaseUri("graphdb"),
                   new DocumentCollection { Id = "graphcollz" },
                   new RequestOptions { OfferThroughput = 1000 });

               // build a gremlin query based on the existence of a name parameter
               string name = req.GetQueryNameValuePairs()
                   .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                   .Value;

               IDocumentQuery<dynamic> query = (!String.IsNullOrEmpty(name))
                   ? client.CreateGremlinQuery<dynamic>(graph, string.Format("g.V('{0}')", name))
                   : client.CreateGremlinQuery<dynamic>(graph, "g.V()");

               // iterate over all the results and add them to the list
               while (query.HasMoreResults)
                   foreach (dynamic result in await query.ExecuteNextAsync())
                       results.Add(result);
           }

           // return the list with an OK response
           return req.CreateResponse<List<dynamic>>(HttpStatusCode.OK, results);
       }
   }
   ```

   Kód využívá v podstatě stejnou logiku připojení jako v původní konzolové aplikaci, která naplnila databázi, a obsahuje jednoduchý dotaz pro načtení odpovídajících záznamů.

## <a name="debug-the-azure-function-locally"></a>Místní ladění funkce Azure Functions

Teď, když je kód dokončený, můžete pomocí emulátoru a nástrojů pro místní ladění funkce Azure Functions místně spustit kód a otestovat ho.

1. Aby se kód správně spustil, je potřeba nakonfigurovat jeho místní spuštění s použitím informací o připojení ke službě Azure Cosmos DB. Ke konfiguraci místního spuštění funkce Azure Functions můžete použít soubor local.settings.json v podstatě stejným způsobem, jako jste nakonfigurovali spuštění původní konzolové aplikace pomocí souboru App.config.

    Provedete to tak, že do souboru local.settings.json přidáte následující řádky kódu a pak do něj zkopírujete svůj koncový bod a autorizační klíč ze souboru App.Config v projektu GraphGetStarted, jak je znázorněno na následujícím obrázku.

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![Nastavení koncového bodu a autorizačního klíče v souboru local.settings.json](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. Změňte spouštěný projekt na novou aplikaci funkcí. V **Průzkumníku řešení** klikněte pravým tlačítkem na **PeopleDataFunctions** a vyberte **Nastavit jako spouštěný projekt**.

3. V **Průzkumníku řešení** klikněte pravým tlačítkem na **Závislosti** v projektu **PeopleDataFunctions** a pak klikněte na **Přidat referenci**. V seznamu vyberte System.Configuration a pak klikněte na **OK**.

3. Teď aplikaci spustíme. Stisknutím klávesy F5 spusťte nástroj pro místní ladění func.exe s hostovaným kódem funkce Azure Functions připraveným k použití.

   Na konci počátečního výstupu z func.exe vidíme, že je funkce Azure Functions hostovaná na adrese localhost:7071. Tato informace je užitečná k otestování v klientovi.

   ![Test klienta](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. K otestování funkce Azure Functions použijte [Visual Studio Code](http://code.visualstudio.com/) s rozšířením [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client), jehož autorem je Huachao Mao. REST Client nabízí možnosti místních nebo vzdálených požadavků HTTP jedním kliknutím pravým tlačítkem. 

    Pokud to chcete provést, vytvořte nový soubor test-function-locally.http a přidejte do něj následující kód:

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    Teď klikněte pravým tlačítkem na první řádek kódu a vyberte **Odeslat požadavek**, jak je znázorněno na následujícím obrázku.

   ![Odeslání požadavku REST z kódu v sadě Visual Studio](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   Zobrazí se nezpracovaná odpověď HTTP z hlaviček místně spuštěné funkce Azure Functions, obsah textu JSON, prostě vše.

   ![Odpověď REST](./media/tutorial-functions-http-trigger/10-general-results.png)

5. Teď vyberte druhý řádek kódu a vyberte **Odeslat požadavek**. Přidáním parametru řetězce dotazu `name` s hodnotou, která se s jistotou nachází v databázi, můžeme filtrovat výsledky, které funkce Azure Functions vrátí.

   ![Filtrování výsledků funkce Azure Functions](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

Po ověření správného fungování funkce Azure Functions je posledním krokem její publikování do služby Azure App Service a nakonfigurování pro spouštění v cloudu.

## <a name="publish-the-azure-function"></a>Publikování funkce Azure Functions

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

   ![Publikování nového projektu](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. Jsme připraveni publikovat projekt do cloudu a otestovat ho ve veřejně dostupném scénáři. Na kartě **Publikovat** vyberte **Aplikace funkcí Azure**, výběrem možnosti **Vytvořit novou** vytvořte ve svém předplatném funkci Azure Functions a pak klikněte na **Publikovat**.

   ![Vytvoření nové aplikace funkcí Azure](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. V dialogovém okně **Publikovat** proveďte následující:
   
    a. V části **Název aplikace** zadejte jedinečný název funkce.

    b. V části **Předplatné** vyberte předplatné Azure, které se má použít.
   
    c. V části **Skupina prostředků** vytvořte novou skupinu prostředků a použijte stejný název jako pro název aplikace.
   
    d. V části **Plán služby App Service** klikněte na **Nový** a vytvořte nový plán služby App Service založený na spotřebě, protože pro tuto funkci Azure Functions chceme použít metodu fakturace s platbami za použití. Na stránce **Konfigurovat plán služby App Service** ponechte výchozí nastavení a klikněte na **OK**.
   
    e. V části **Účet úložiště** také klikněte na **Nový** a vytvořte nový účet úložiště pro použití s funkcí Azure Functions v případě, že bychom někdy potřebovali pro aktivaci spouštění jiných funkcí podporu objektů blob, tabulek nebo front. Na stránce **Účet úložiště** ponechte výchozí nastavení a klikněte na **OK**.

    f. Pak kliknutím na tlačítko **Vytvořit** v dialogovém okně vytvořte všechny prostředky ve vašem předplatném Azure. Sada Visual Studio stáhne profil publikování (jednoduchý soubor XML), který použije při dalším publikování vašeho kódu funkce Azure Functions.

   ![Vytvoření účtu úložiště](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    Sada Visual Studio pak zobrazí stránku Publikovat, kterou můžete použít v případě, že funkci změníte a potřebujete ji znovu publikovat. Na této stránce teď není potřeba nic provádět.

4. Po publikování funkce Azure Functions můžete přejít na stránku webu [Azure Portal](https://portal.azure.com/) pro vaši funkci Azure Functions. Tam se zobrazí odkaz na **Nastavení aplikace** pro funkci Azure Functions. Otevřete tento odkaz a nakonfigurujte živou funkci Azure Functions pro připojení k databázi Azure Cosmos DB obsahující data osob.

   ![Kontrola nastavení aplikace](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. Stejně jako jste to udělali dříve u souboru App.config konzolové aplikace a souboru local.settings.json aplikace funkcí Azure je potřeba do publikované funkce přidat koncový bod a ověřovací klíč pro databázi Azure Cosmos DB. Díky tomu nebudete nikdy muset vracet se změnami kód konfigurace obsahující vaše klíče – můžete je nakonfigurovat na portálu a zajistit tak, že se neuloží do správy zdrojového kódu. Jednotlivé hodnoty přidáte tak, že kliknete na tlačítko **Přidat nové nastavení**, přidáte **Koncový bod** a vaši hodnotu ze souboru app.config, pak znovu kliknete na **Přidat nové nastavení** a přidáte **Autorizační klíč** s vlastní hodnotou. Po přidání a uložení hodnot by vaše nastavení mělo vypadat jako v následujícím příkladu.

   ![Konfigurace koncového bodu a autorizačního klíče](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. Jakmile je funkce Azure Functions správně nakonfigurovaná ve vašem předplatném Azure, můžete opět pomocí rozšíření REST Client sady Visual Studio dotazovat veřejně dostupnou adresu URL funkce Azure Functions. Do souboru test-function-locally.http přidejte následující dva řádky kódu a pak funkci otestujte spuštěním jednotlivých řádků. Nahraďte název funkce v adrese URL názvem vaší funkce.

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    Funkce jako odpověď vrátí data načtená ze služby Azure Cosmos DB.

    ![Použití rozšíření REST Client k dotazování funkce Azure Functions](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Vytvoření projektu funkce Azure Functions 
> * Vytvoření triggeru HTTP
> * Publikování funkce Azure Functions
> * Připojení funkce k databázi Azure Cosmos DB

Teď můžete přejít k části Koncepty, která obsahuje další informace o službě Cosmos DB.

> [!div class="nextstepaction"]
> [Globální distribuce](distribute-data-globally.md) 

Tento článek vychází z blogového příspěvku ze série [Schemaless & Serverless (Bez schématu a bez serveru) od Bradyho Gastera](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless). Další příspěvky z této série najdete na jeho blogu.
