---
title: Nastavení kanálu CI/CD pomocí úlohy sestavení emulátoru Azure Cosmos DB
description: Kurz týkající se nastavení pracovního postupu sestavení a verzí v Azure DevOps pomocí úlohy sestavení emulátoru služby Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 521d5d8d587b39cf573dedc37ea9f6fd53646e66
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410956"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Nastavení kanálu CI/CD pomocí úlohy sestavení emulátoru služby Azure Cosmos DB v Azure DevOps

Emulátor služby Azure Cosmos DB zajistí místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Emulátor umožňuje vyvíjet a testovat aplikace místně bez vytváření předplatného Azure a bez jakýchkoli nákladů. 

Úloha sestavení emulátoru služby Azure Cosmos DB pro Azure DevOps vám umožňuje provádět stejné úlohy v prostředí CI. Pomocí úlohy sestavení můžete spouštět testy emulátoru jako součást vašich pracovních postupů sestavení a verzí. Úloha se spustí v kontejneru Dockeru s už spuštěným emulátorem a poskytuje koncový bod, který lze použít zbývající částí definice sestavení. Můžete vytvořit a spustit tolik instancí emulátoru, kolik potřebujete, každá z nich poběží v samostatném kontejneru. 

Tento článek ukazuje, jak v Azure DevOps nastavit kanál CI pro aplikaci ASP.NET, která ke spouštění testů používá úlohu sestavení emulátoru služby Cosmos DB. Podobný přístup můžete použít k nastavení kanálu CI pro node.js nebo aplikaci Pythonu. 

## <a name="install-the-emulator-build-task"></a>Instalace úlohy sestavení emulátoru

Abychom mohli použít úlohu sestavení, musíme ji nejprve nainstalovat do naší organizace Azure DevOps. Rozšíření **Emulátor Azure Cosmos DB** vyhledejte na webu [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) a klikněte na tlačítko pro **získání zdarma**.

![Vyhledání a instalace úlohy sestavení emulátoru služby Azure Cosmos DB na webu Azure DevOps Marketplace](./media/tutorial-setup-ci-cd/addExtension_1.png)

Potom vyberte organizaci, do které se má rozšíření nainstalovat. 

> [!NOTE]
> Chcete-li nainstalovat rozšíření pro organizaci Azure DevOps, musíte být vlastníkem účtu nebo správcem kolekce projektu. Pokud nemáte oprávnění, ale jste členem účtu, můžete o rozšíření požádat. [Další informace](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

![Zvolte organizaci Azure DevOps, do které chcete nainstalovat rozšíření.](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Vytvoření definice sestavení

Když je teď rozšíření nainstalované, přihlaste se ke svému účtu Azure DevOps a na řídicím panelu projektů vyhledejte svůj projekt. Do svého projektu můžete přidat [kanál sestavení](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) nebo můžete upravit existující kanál sestavení. Pokud už máte kanál sestavení, můžete přeskočit k části [Přidání úlohy sestavení emulátoru do definice sestavení](#addEmulatorBuildTaskToBuildDefinition).

1. Pokud chcete vytvořit novou definici sestavení, přejděte v Azure DevOps na kartu **Builds** (Sestavení). Vyberte **+Nový.** \> **Nový kanál sestavení**

   ![Vytvoření nového kanálu sestavení](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Vyberte požadovaný **zdroj**, **Týmový projekt**, **Úložiště** a **Výchozí větev pro ruční a plánovaná sestavení**. Až zvolíte požadované možnosti, vyberte **Pokračovat**.

   ![Výběr týmového projektu, úložiště a větve pro kanál sestavení](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Nakonec vyberte požadovanou šablonu pro kanál sestavení. V tomto kurzu vybereme šablonu **ASP.NET**. Teď máte kanál sestavení, který můžete nastavit pro použití úlohy sestavení emulátoru Azure Cosmos DB. 

> [!NOTE]
> Fond agentů, který má být vybrán pro toto ci by měl mít nainstalovaný Docker pro Windows, pokud není instalace provedena ručně v předchozí úloze jako součást ci. V článku [hostované agenty společnosti Microsoft](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml) najdete výběr fondů agentů. doporučujeme začít `Hosted VS2017`s .

Emulátor Azure Cosmos DB aktuálně nepodporuje fond agentů Hostované hodované vS2019. Emulátor je však již dodáván s nainstalovaným VS2019 a můžete jej použít spuštěním emulátoru s následujícími rutinami prostředí PowerShell. Pokud při používání VS2019 narazíte na nějaké problémy, opomocte tým [Azure DevOps:](https://developercommunity.visualstudio.com/spaces/21/index.html)

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Přidání úlohy do kanálu sestavení

1. Před přidáním úlohy do kanálu sestavení byste měli přidat úlohu agenta. Přejděte do kanálu sestavení, vyberte **...** a zvolte **Přidat úlohu agenta**.

1. Dále vyberte **+** symbol vedle úlohy agenta a přidejte úlohu sestavení emulátoru. Ve vyhledávacím poli vyhledejte **cosmos**, vyberte **Emulátor služby Azure Cosmos DB** a přidejte ho k úloze agenta. Úloha sestavení spustí kontejner s již spuštěnou instancí emulátoru služby Cosmos DB. Úloha emulátoru služby Azure Cosmos DB se musí nacházet před všemi ostatními úlohami, které očekávají, že je emulátor spuštěný.

   ![Přidání úlohy sestavení emulátoru k definici sestavení](./media/tutorial-setup-ci-cd/addExtension_3.png)

V tomto kurzu přidáte úlohu na začátek, abyste zajistili, že emulátor bude dostupný před provedením testů.

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurace testů pro použití emulátoru

Teď nakonfigurujeme naše testy, aby používaly emulátor. Úloha sestavení emulátoru exportuje proměnnou prostředí – CosmosDbEmulator.Endpoint – aby jakékoli další úlohy v kanálu sestavení mohly žádost znovu vydat. 

V tomto kurzu použijeme [úkol nástroje Visual Studio Test](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) ke spouštění testů jednotek nakonfigurovaných prostřednictvím souboru **.runsettings**. Pokud se chcete o nastavení testu jednotek dozvědět více, přečtěte si [dokumentaci](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017). Kompletní ukázka kódu aplikace Todo, kterou používáte v tomto dokumentu, je k dispozici na [GitHubu.](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

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

Pokud nastavujete kanál CI/CD pro aplikaci, která používá rozhraní API Azure Cosmos DB pro MongoDB, připojovací řetězec ve výchozím nastavení obsahuje číslo portu 10255. Tento port však není aktuálně otevřen, jako alternativu byste měli použít port 10250 k navázání připojení. Rozhraní API Azure Cosmos DB pro připojovací řetězec MongoDB zůstává stejné s tím rozdílem, že číslo podporovaného portu je 10250 místo 10255.

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

V úkolu nástroje Visual Studio Test přejděte na Execution Options (Možnosti spuštění). V možnosti **Settings file** (Soubor nastavení) specifikujte, že testy jsou nakonfigurovány pomocí souboru **.runsettings**. V možnosti **Override test run parameters** (Přepsat parametry testovacího běhu) přidejte `-endpoint $(CosmosDbEmulator.Endpoint)`. Nakonfigurujete tak úkol testu, aby odkazoval na koncový bod úlohy sestavení emulátoru, nikoli na bod definovaný v souboru **.runsettings**.  

![Přepsání proměnné koncového bodu koncovým bodem úlohy sestavení emulátoru](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Spuštění sestavení

Teď pro sestavení vyberte **Uložit a vložit do fronty**. 

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/runBuild_1.png)

Jakmile se sestavení spustí, podívejte se, že úloha emulátoru Cosmos DB začala stahovat image Dockeru s nainstalovaným emulátorem. 

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/runBuild_4.png)

Jakmile se sestavení dokončí, podívejte se, že vaše testy byly úspěšné a spustily se v emulátoru Cosmos DB z úlohy sestavení.

![Uložení a spuštění sestavení](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="set-up-using-yaml"></a>Nastavení pomocí YAML

Pokud nastavujete kanál CI/CD pomocí úlohy YAML, můžete definovat úlohu YAML, jak je znázorněno v následujícím kódu:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět další informace o použití emulátoru pro místní vývoj a testování, přečtěte si článek o [použití emulátoru služby Azure Cosmos DB pro místní vývoj a testování](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Exportovat certifikáty TLS/SSL emulátoru najdete v [tématu Export certifikátů emulátoru Azure Cosmos DB pro použití s jazykem Java, Pythona a Node.js.](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
