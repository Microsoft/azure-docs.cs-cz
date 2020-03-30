---
title: Správa prostředků sql api Azure Cosmos DB pomocí sady .Net V4 SDK
description: Úvodní příručka k vytvoření konzolové aplikace pomocí sady .Net V4 SDK ke správě prostředků účtu SQL API Azure Cosmos DB.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77585930"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Úvodní příručka: Vytvořte konzolovou aplikaci pomocí sady .Net V4 SDK ke správě prostředků účtu SQL API Azure Cosmos DB.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Začínáme s klientskou knihovnou Rozhraní SQL API Azure Cosmos DB pro rozhraní .NET. Podle pokynů v tomto dokumentu nainstalujte balíček .NET V4 (Azure.Cosmos), vytvořte aplikaci a vyzkoušejte ukázkový kód pro základní operace CRUD na datech uložených v Azure Cosmos DB. 

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování na klíčové nebo hodnotové, dokumentové a grafové databáze. Pomocí klientské knihovny rozhraní SQL API Azure Cosmos DB pro rozhraní .NET:

* Vytvoření databáze Azure Cosmos a kontejneru
* Přidání ukázkových dat do kontejneru
* Vytváření dotazů na data 
* Odstranění databáze

[Balíček zdrojového kódu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [knihovny (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si jedno zdarma](https://azure.microsoft.com/free/) nebo můžete [bezplatně vyzkoušet Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Spuštěním aplikace můžete ověřit, která `dotnet --version`verze je ve vašem prostředí k dispozici.

## <a name="setting-up"></a>Nastavení

Tato část vás provede vytvořením účtu Azure Cosmos a nastavením projektu, který používá klientskou knihovnu Azure Cosmos DB SQL API pro .NET ke správě prostředků. Ukázkový kód popsaný v tomto `FamilyDatabase` článku vytvoří databázi a členy rodiny (každý člen rodiny je položka) v rámci této databáze. Každý člen rodiny `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`má vlastnosti, například . Vlastnost `LastName` se používá jako klíč oddílu pro kontejner. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Vytvoření účtu Azure Cosmos

Pokud k vytvoření účtu Azure Cosmos použijete bezplatnou možnost [Try Azure Cosmos DB,](https://azure.microsoft.com/try/cosmosdb/) musíte vytvořit účet Azure Cosmos DB typu **SQL API**. Testovací účet Azure Cosmos DB už je vytvořen pro vás. Účet nemusíte explicitně vytvářet, takže můžete tuto část přeskočit a přejít na další část.

Pokud máte vlastní předplatné Azure nebo jste zdarma vytvořili předplatné, měli byste explicitně vytvořit účet Azure Cosmos. Následující kód vytvoří účet Azure Cosmos s konzistencí relace. Účet je replikován v `South Central US` a . `North Central US`  

Azure Cloud Shell můžete použít k vytvoření účtu Azure Cosmos. Azure Cloud Shell je interaktivní prostředí pro správu prostředků Azure, které je po ověření dostupné z webového prohlížeče. Umožňuje flexibilně zvolit prostředí, které nejlépe vyhovuje vašemu stylu práce – Bash nebo PowerShell. Pro tento rychlý start zvolte **Režim Bash.** Azure Cloud Shell také vyžaduje účet úložiště, můžete vytvořit jeden po zobrazení výzvy.

Vyberte tlačítko **Try It** vedle následujícího kódu, zvolte **Režim Bash** vyberte vytvořit **účet úložiště** a přihlásit se do Cloud Shell. Dále zkopírujte a vložte následující kód do cloudshellu Azure a spusťte jej. Název účtu Azure Cosmos musí být globálně jedinečný, `mysqlapicosmosdb` nezapomeňte aktualizovat hodnotu před spuštěním příkazu.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Vytvoření účtu Azure Cosmos chvíli trvá, jakmile je operace úspěšná, můžete zobrazit výstup potvrzení. Po úspěšném dokončení příkazu se přihlaste na [portál Azure](https://portal.azure.com/) a ověřte, zda existuje účet Azure Cosmos se zadaným názvem. Okno Azure Cloud Shell můžete po vytvoření prostředku zavřít. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Vytvoření nové aplikace .NET

Vytvořte novou aplikaci .NET v upřednostňovaném editoru nebo rozhraní IDE. Otevřete příkazový řádek systému Windows nebo okno terminálu z místního počítače. Všechny příkazy v následujících částech spustíte z příkazového řádku nebo terminálu.  Spusťte následující dotnet nový příkaz k `todo`vytvoření nové aplikace s názvem . Parametr --langVersion nastaví vlastnost LangVersion v vytvořeném souboru projektu.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

   ```bash
   cd todo
   dotnet build
   ```

Očekávaný výstup z sestavení by měl vypadat nějak takto:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalace balíčku Azure Cosmos DB

Zatímco ještě v adresáři aplikace, nainstalujte klientské knihovny Azure Cosmos DB pro .NET Core pomocí příkazu dotnet add package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopírování přihlašovacích údajů k účtu Azure Cosmos z webu Azure Portal

Ukázková aplikace potřebuje k ověření vašeho účtu Azure Cosmos. K ověření, měli byste předat přihlašovací údaje účtu Azure Cosmos do aplikace. Získejte přihlašovací údaje k účtu Azure Cosmos takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Přejděte na svůj účet Azure Cosmos.

1. Otevřete podokno **Klíče** a zkopírujte **identifikátor URI** a **primární klíč** svého účtu. Hodnoty URI a klíče přidáte do proměnné prostředí v dalším kroku.

## <a name="object-model"></a><a id="object-model"></a>Objektový model

Než začnete vytvářet aplikaci, podívejme se na hierarchii prostředků v Azure Cosmos DB a objektový model, který se používá k vytvoření a přístupu k těmto prostředkům. Azure Cosmos DB vytváří prostředky v následujícím pořadí:

* Účet Azure Cosmos 
* Databáze 
* Kontejnery 
* Items

Další informace o hierarchii různých entit najdete v [článku práce s databázemi, kontejnery a položkami.](databases-containers-items.md) K interakci s těmito prostředky budete používat následující třídy .NET:

* CosmosClient – tato třída poskytuje logické znázornění služby Azure Cosmos DB na straně klienta. Objekt klienta se používá ke konfiguraci a spuštění požadavků proti službě.
* CreateDatabaseIfNotExistsAsync - Tato metoda vytvoří (pokud neexistuje) nebo získá (pokud již existuje) databázový prostředek jako asynchronní operace. 
* CreateContainerIfNotExistsAsync - Tato metoda vytvoří (pokud neexistuje) nebo získá (pokud již existuje) kontejner jako asynchronní operace. Můžete zkontrolovat stavový kód z odpovědi k určení, zda byl kontejner nově vytvořen (201) nebo byl vrácen existující kontejner (200). 
* CreateItemAsync - Tato metoda vytvoří položku v rámci kontejneru.
* UpsertItemAsync - Tato metoda vytvoří položku v kontejneru, pokud již neexistuje nebo nahradí položku, pokud již existuje. 
* GetItemQueryIterator - Tato metoda vytvoří dotaz na položky v rámci kontejneru v databázi Azure Cosmos pomocí příkazu SQL s parametrizovanými hodnotami. 
* DeleteAsync – odstraní zadanou databázi z vašeho účtu Azure Cosmos. `DeleteAsync`metoda pouze odstraní databázi.

 ## <a name="code-examples"></a><a id="code-examples"></a>Příklady kódu

Ukázkový kód popsaný v tomto článku vytvoří rodinnou databázi v Azure Cosmos DB. Rodinná databáze obsahuje rodinné údaje, jako je jméno, adresa, umístění, přidružené rodiče, děti a domácí zvířata. Před vyplněním dat do účtu Azure Cosmos definujte vlastnosti položky rodiny. Vytvořte novou `Family.cs` třídu pojmenovanou na kořenové úrovni ukázkové aplikace a přidejte do ní následující kód:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Přidání direktiv using & definování objektu klienta

V adresáři projektu `Program.cs` otevřete soubor v editoru a přidejte následující pomocí direktiv v horní části aplikace:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Přidejte do třídy `Program` následující globální proměnné. Ty budou zahrnovat koncový bod a autorizační klíče, název databáze a kontejner, který vytvoříte. Nezapomeňte nahradit hodnoty koncového bodu a autorizačních klíčů podle vašeho prostředí. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Nakonec nahraďte metodu: `Main`

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Vytvoření databáze 

Definujte `CreateDatabaseAsync` metodu `program.cs` v rámci třídy. Tato metoda `FamilyDatabase` vytvoří, pokud ještě neexistuje.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Vytvoření kontejneru

Definujte `CreateContainerAsync` metodu `Program` v rámci třídy. Tato metoda `FamilyContainer` vytvoří, pokud ještě neexistuje. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Vytvoření položky

Vytvořte položku rodiny přidáním `AddItemsToContainerAsync` metody s následujícím kódem. K vytvoření `CreateItemAsync` položky můžete použít metody nebo: `UpsertItemAsync`

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Dotaz na položky

Po vložení položky můžete spustit dotaz a získat podrobnosti o rodině "Andersen". Následující kód ukazuje, jak spustit dotaz pomocí dotazu SQL přímo. Dotaz SQL získat podrobnosti rodiny "Anderson" je: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definujte `QueryItemsAsync` metodu `Program` v rámci třídy a přidejte do ní následující kód:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Nahrazení položky 

Přečtěte si položku rodiny `ReplaceFamilyItemAsync` a potom ji aktualizujte přidáním metody s následujícím kódem.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Odstranění položky 

Odstraňte položku rodiny přidáním `DeleteFamilyItemAsync` metody s následujícím kódem.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Odstranění databáze 

Nakonec můžete odstranit databázi `DeleteDatabaseAndCleanupAsync` přidání metody s následujícím kódem:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Po přidání všech požadovaných metod `Program` soubor uložte. 

## <a name="run-the-code"></a>Spuštění kódu

Další sestavení a spuštění aplikace k vytvoření prostředků Azure Cosmos DB.

   ```bash
   dotnet run
   ```

Následující výstup je generován při spuštění aplikace. Můžete se taky přihlásit k portálu Azure a ověřit, že se prostředky vytvoří:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Můžete ověřit, že data se vytvoří po přihlášení k portálu Azure a zobrazí požadované položky ve vašem účtu Azure Cosmos. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít Azure CLI nebo Azure PowerShell k odebrání účtu Azure Cosmos a odpovídající skupiny prostředků. Následující příkaz ukazuje, jak odstranit skupinu prostředků pomocí azure cli:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos, vytvořit databázi a kontejner pomocí aplikace .NET Core. Teď můžete importovat další data do svého účtu Azure Cosmos s pokyny v následujícím článku. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
