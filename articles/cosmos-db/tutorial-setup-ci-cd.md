---
title: Nastavení kanálu CI/CD pomocí úlohy sestavení emulátoru služby Azure Cosmos DB
description: Kurz týkající se nastavení pracovního postupu sestavení a verzí v Azure DevOps pomocí úlohy sestavení emulátoru služby Cosmos DB
services: cosmos-db
keywords: Emulátor služby Azure Cosmos DB
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 11/02/2018
ms.author: dech
ms.openlocfilehash: e0b1fe3b58d5781ce1e2d7a0701c58c31e6054a6
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678929"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Nastavení kanálu CI/CD pomocí úlohy sestavení emulátoru služby Azure Cosmos DB v Azure DevOps

Emulátor služby Azure Cosmos DB zajistí místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Emulátor umožňuje vyvíjet a testovat aplikace místně bez vytváření předplatného Azure a bez jakýchkoli nákladů. 

Úloha sestavení emulátoru služby Azure Cosmos DB pro Azure DevOps vám umožňuje provádět stejné úlohy v prostředí CI. Pomocí úlohy sestavení můžete spouštět testy emulátoru jako součást vašich pracovních postupů sestavení a verzí. Úloha se spustí v kontejneru Dockeru s už spuštěným emulátorem a poskytuje koncový bod, který lze použít zbývající částí definice sestavení. Můžete vytvořit a spustit tolik instancí emulátoru, kolik potřebujete, každá z nich poběží v samostatném kontejneru. 

Tento článek ukazuje, jak v Azure DevOps nastavit kanál CI pro aplikaci ASP.NET, která ke spouštění testů používá úlohu sestavení emulátoru služby Cosmos DB. Podobný přístup můžete použít k nastavení kanálu CI pro Node.js nebo aplikace v Pythonu. 

## <a name="install-the-emulator-build-task"></a>Instalace úlohy sestavení emulátoru

Abychom mohli použít úlohu sestavení, musíme ji nejprve nainstalovat do naší organizace Azure DevOps. Rozšíření **Emulátor Azure Cosmos DB** vyhledejte na webu [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) a klikněte na tlačítko pro **získání zdarma**.

![Vyhledání a instalace úlohy sestavení emulátoru služby Azure Cosmos DB na webu Azure DevOps Marketplace](./media/tutorial-setup-ci-cd/addExtension_1.png)

Potom vyberte organizaci, do které se má rozšíření nainstalovat. 

> [!NOTE]
> Pokud chcete do organizace Azure DevOps nainstalovat rozšíření, musíte být vlastníkem účtu nebo správcem kolekce projektů. Pokud nemáte oprávnění, ale jste členem účtu, můžete o rozšíření požádat. [Další informace](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions)

![Výběr organizace Azure DevOps, do které se má rozšíření nainstalovat](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Vytvoření definice sestavení

Když je teď rozšíření nainstalované, přihlaste se ke svému účtu Azure DevOps a na řídicím panelu projektů vyhledejte svůj projekt. Do svého projektu můžete přidat [kanál sestavení](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) nebo můžete upravit existující kanál sestavení. Pokud už máte kanál sestavení, můžete přeskočit k části [Přidání úlohy sestavení emulátoru do definice sestavení](#addEmulatorBuildTaskToBuildDefinition).

1. Pokud chcete vytvořit novou definici sestavení, přejděte v Azure DevOps na kartu **Builds** (Sestavení). Vyberte **+New** (+Nový). > **Nový kanál sestavení**

   ![Vytvoření nového kanálu sestavení](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Vyberte požadovaný **zdroj**, **Týmový projekt**, **Úložiště** a **Výchozí větev pro ruční a plánovaná sestavení**. Až zvolíte požadované možnosti, vyberte **Pokračovat**.

   ![Výběr týmového projektu, úložiště a větve pro kanál sestavení ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Nakonec vyberte požadovanou šablonu pro kanál sestavení. V tomto kurzu vybereme šablonu **ASP.NET**. 

Teď máme kanál sestavení, který můžeme nastavit pro používání úlohy sestavení emulátoru služby Azure Cosmos DB. 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Přidání úlohy do kanálu sestavení

1. Před přidáním úlohy do kanálu sestavení byste měli přidat úlohu agenta. Přejděte do kanálu sestavení, vyberte **...** a zvolte **Přidat úlohu agenta**.

1. Potom vyberte symbol **+** vedle úlohy agenta a přidejte úlohu sestavení emulátoru. Ve vyhledávacím poli vyhledejte **cosmos**, vyberte **Emulátor služby Azure Cosmos DB** a přidejte ho k úloze agenta. Úloha sestavení spustí kontejner s již spuštěnou instancí emulátoru služby Cosmos DB. Úloha emulátoru služby Azure Cosmos DB se musí nacházet před všemi ostatními úlohami, které očekávají, že je emulátor spuštěný.

   ![Přidání úlohy sestavení emulátoru k definici sestavení](./media/tutorial-setup-ci-cd/addExtension_3.png)

V tomto kurzu přidáte úlohu na začátek, abyste zajistili, že emulátor bude dostupný před provedením testů.

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurace testů pro použití emulátoru

Teď nakonfigurujeme naše testy, aby používaly emulátor. Úloha sestavení emulátoru exportuje proměnnou prostředí – CosmosDbEmulator.Endpoint – aby jakékoli další úlohy v kanálu sestavení mohly žádost znovu vydat. 

V tomto kurzu použijeme [úkol nástroje Visual Studio Test](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) ke spouštění testů jednotek nakonfigurovaných prostřednictvím souboru **.runsettings**. Pokud se chcete o nastavení testu jednotek dozvědět více, přečtěte si [dokumentaci](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017).

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

Pokud jsou nastavení kanálu CI/CD pro aplikaci, která používá MongoDB API služby Azure Cosmos DB, připojovacího řetězce MongoDB ve výchozím nastavení obsahuje číslo portu 10255. Ale tento port není otevřen, místo toho byste měli použít port 10250 k navázání připojení. Připojovací řetězec MongoDB API zůstává stejná s tím rozdílem, je číslo portu podporované 10250 místo 10255.

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

Teď pro sestavení vyberte **Uložit a vložit do fronty**. 

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/runBuild_1.png)

Jakmile se sestavení spustí, podívejte se, že úloha emulátoru Cosmos DB začala stahovat image Dockeru s nainstalovaným emulátorem. 

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/runBuild_4.png)

Jakmile se sestavení dokončí, podívejte se, že vaše testy byly úspěšné a spustily se v emulátoru Cosmos DB z úlohy sestavení.

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Další postup

Pokud se chcete dozvědět další informace o použití emulátoru pro místní vývoj a testování, přečtěte si článek o [použití emulátoru služby Azure Cosmos DB pro místní vývoj a testování](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Pokud chcete exportovat certifikáty SSL emulátoru, přečtěte si článek o [exportu certifikátů emulátoru služby Azure Cosmos DB pro použití s Javou, Pythonem a Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates).
