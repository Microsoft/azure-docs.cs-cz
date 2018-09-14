---
title: Nastavení kanálu CI/CD pomocí úlohy sestavení emulátoru služby Azure Cosmos DB
description: Kurz týkající se nastavení sestavení a pracovního postupu verzí ve Visual Studio Team Services (VSTS) pomocí úlohy sestavení emulátoru služby Cosmos DB
services: cosmos-db
keywords: Emulátor služby Azure Cosmos DB
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783712"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Nastavení kanálu CI/CD pomocí úlohy sestavení emulátoru služby Azure Cosmos DB ve Visual Studio Team Services

Emulátor služby Azure Cosmos DB zajistí místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Emulátor umožňuje vyvíjet a testovat aplikace místně bez vytváření předplatného Azure a bez jakýchkoli nákladů. 

Úloha sestavení emulátoru služby Azure Cosmos DB pro Visual Studio Team Services (VSTS) vám umožňuje provádět stejné úlohy v prostředí CI. Pomocí úlohy sestavení můžete spouštět testy emulátoru jako součást vašich pracovních postupů sestavení a verzí. Úloha se spustí v kontejneru Dockeru s už spuštěným emulátorem a poskytuje koncový bod, který lze použít zbývající částí definice sestavení. Můžete vytvořit a spustit tolik instancí emulátoru, kolik potřebujete, každá z nich poběží v samostatném kontejneru. 

Tento článek ukazuje, jak nastavit kanál CI ve VSTS pro aplikaci ASP.NET, která ke spouštění testů používá úlohu sestavení emulátoru Cosmos DB. 

## <a name="install-the-emulator-build-task"></a>Instalace úlohy sestavení emulátoru

Abychom mohli použít úlohu sestavení, musíme ji nejprve nainstalovat do vaší organizace VSTS. Rozšíření **Emulátor Azure Cosmos DB** vyhledejte na webu [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) a klikněte na tlačítko pro **získání zdarma**.

![Vyhledání a instalace úlohy sestavení emulátoru služby Azure Cosmos DB na webu VSTS Marketplace](./media/tutorial-setup-ci-cd/addExtension_1.png)

Potom vyberte organizaci, do které se má rozšíření nainstalovat. 

> [!NOTE]
> Pokud chcete do organizace VSTS nainstalovat rozšíření, musíte být vlastníkem účtu nebo správcem kolekce projektů. Pokud nemáte oprávnění, ale jste členem účtu, můžete o rozšíření požádat. [Další informace](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![Výběr organizace VSTS, do které se má rozšíření nainstalovat](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Vytvoření definice sestavení

Teď, když je rozšíření nainstalováno, potřebujeme ho přidat do [definice sestavení.](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav) Můžete upravit existující definici sestavení nebo vytvořit novou. Pokud už máte existující definici sestavení, můžete přeskočit k části [Přidání úlohy sestavení emulátoru do definice sestavení](#addEmulatorBuildTaskToBuildDefinition).

Pokud chcete vytvořit novou definici sestavení, přejděte na kartu **Build and Release** (Sestavení a verze) ve VSTS. Vyberte **+New** (+Nový).

![Vytvoření nové definice sestavení](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) Vyberte požadovaný týmový projekt, úložiště a větev pro povolení sestavení. 

![Výběr týmového projektu, úložiště a větve pro definici sestavení ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

Nakonec vyberte požadovanou šablonu pro definici sestavení. V tomto kurzu vybereme šablonu **ASP.NET**. 

![Výběr požadované šablony definice sestavení ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

Teď máme definici sestavení, kterou můžeme nastavit pro používání v úloze sestavení emulátoru Azure Cosmos DB, která vypadá podobně jako ta následující. 

![Šablona definice sestavení ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Přidání úlohy do definice sestavení

Pokud chcete přidat úlohu sestavení emulátoru, zadejte do vyhledávacího pole **cosmos** a vyberte **Add** (Přidat). Úloha sestavení spustí kontejner s již běžící instancí emulátoru Cosmos DB, proto je potřeba umístit úlohu před jakékoli další úlohy, které očekávají, že emulátor je spuštěný.

![Přidání úlohy sestavení emulátoru do definice sestavení](./media/tutorial-setup-ci-cd/addExtension_3.png) V tomto kurzu přidáme úlohu na začátek fáze 1, abychom zajistili, že bude emulátor přístupný před zahájením našich testů.
Kompletní definice sestavení vypadá takto. 

![Šablona definice sestavení ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurace testů pro použití emulátoru
Teď nakonfigurujeme naše testy, aby používaly emulátor. Úloha sestavení emulátoru exportuje proměnnou prostředí – CosmosDbEmulator.Endpoint – aby jakékoli další úlohy v kanálu sestavení mohly žádost znovu vydat. 

V tomto kurzu použijeme [úkol nástroje Visual Studio Test](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md) ke spouštění testů jednotek nakonfigurovaných prostřednictvím souboru **.runsettings**. Pokud se chcete o nastavení testu jednotek dozvědět více, přečtěte si [dokumentaci](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017).

Níže je uvedený příklad souboru **.runsettings**, který definuje parametry předávané do testů jednotek aplikace. Všimněte si, že použitá proměnná `authKey` je [dobře známý klíč](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) pro emulátor. Tento klíč `authKey` je očekávaný úlohou sestavení emulátoru a měl by být definovaný ve vašem souboru **.runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```
Tyto parametry `TestRunParameters` jsou odkazovány prostřednictvím vlastnosti `TestContext` v projektu testů aplikace. Tady je příklad testu, který se spouští ve službě Cosmos DB. 

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

V úkolu nástroje Visual Studio Test přejděte na Execution Options (Možnosti spuštění). V možnosti **Settings file** (Soubor nastavení) specifikujte, že testy jsou nakonfigurovány pomocí souboru **.runsettings**. V možnosti **Override test run parameters** (Přepsat parametry testovacího běhu) přidejte ` -endpoint $(CosmosDbEmulator.Endpoint)`. Nakonfigurujete tak úkol testu, aby odkazoval na koncový bod úlohy sestavení emulátoru, nikoli na bod definovaný v souboru **.runsettings**.  

![Přepsání proměnné koncového bodu koncovým bodem úlohy sestavení emulátoru](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Spuštění sestavení
Teď sestavení uložte a vložte do fronty. 

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/runBuild_1.png)

Jakmile se sestavení spustí, podívejte se, že úloha emulátoru Cosmos DB začala stahovat image Dockeru s nainstalovaným emulátorem. 

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/runBuild_4.png)

Jakmile se sestavení dokončí, podívejte se, že vaše testy byly úspěšné a spustily se v emulátoru Cosmos DB z úlohy sestavení.

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět další informace o použití emulátoru pro místní vývoj a testování, přečtěte si článek o [použití emulátoru služby Azure Cosmos DB pro místní vývoj a testování](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Pokud chcete exportovat certifikáty SSL emulátoru, přečtěte si článek o [exportu certifikátů emulátoru služby Azure Cosmos DB pro použití s Javou, Pythonem a Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates).
