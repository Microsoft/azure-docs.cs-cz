---
title: Správa prostředků rozhraní SQL API Azure Cosmos DB pomocí sady .NET V4 SDK
description: Rychlý Start k vytvoření konzolové aplikace pomocí sady .NET V4 SDK pro správu Azure Cosmos DB prostředků účtů rozhraní SQL API.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: da4b2a96f9da2a7c1212ce68de61667e92a535f0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262072"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Rychlý Start: Vytvoření konzolové aplikace pomocí sady .NET V4 SDK pro správu Azure Cosmos DB prostředků účtů rozhraní SQL API.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Sada Java SDK v4](create-sql-api-java.md)
> * [Jarní data V3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Začínáme s klientskou knihovnou Azure Cosmos DB SQL API pro .NET Postupujte podle kroků v tomto dokumentu k instalaci balíčku .NET V4 (Azure. Cosmos), sestavení aplikace a vyzkoušení ukázkového kódu pro základní operace CRUD na datech uložených v Azure Cosmos DB. 

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování databáze klíčů/hodnot, dokumentů a grafů. Použijte klientskou knihovnu Azure Cosmos DB SQL API pro .NET pro:

* Vytvoření databáze Azure Cosmos a kontejneru
* Přidání ukázkových dat do kontejneru
* Vytváření dotazů na data 
* Odstranění databáze

[Zdrojový kód knihovny](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) nebo si můžete [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. 
* [Sada SDK .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core). Spuštěním sady můžete ověřit, která verze je ve vašem prostředí k dispozici `dotnet --version` .

## <a name="setting-up"></a>Nastavení

V této části se seznámíte s vytvořením účtu Azure Cosmos a nastavením projektu, který používá Azure Cosmos DB klientské knihovně rozhraní SQL API pro .NET ke správě prostředků. Vzorový kód popsaný v tomto článku vytvoří `FamilyDatabase` databázi a rodinné příslušníky (každý rodinný člen je položka) v této databázi. Každý rodinný člen má vlastnosti, jako je například `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . `LastName`Vlastnost se používá jako klíč oddílu pro kontejner. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Vytvoření účtu Azure Cosmos

Pokud k vytvoření účtu Azure Cosmos použijete možnost [vyzkoušet Azure Cosmos DB for Free](https://azure.microsoft.com/try/cosmosdb/) , je nutné vytvořit účet Azure Cosmos DB typu **SQL API**. Pro vás už je vytvořený testovací účet Azure Cosmos DB. Účet není nutné vytvářet explicitně, takže můžete tuto část přeskočit a přejít k další části.

Pokud máte vlastní předplatné Azure nebo jste předplatné vytvořili zdarma, měli byste účet Azure Cosmos vytvořit explicitně. Následující kód vytvoří účet Azure Cosmos s konzistencí relací. Účet je replikován v `South Central US` a `North Central US` .  

K vytvoření účtu Azure Cosmos můžete použít Azure Cloud Shell. Azure Cloud Shell je interaktivní prostředí pro správu prostředků Azure, které je po ověření dostupné z webového prohlížeče. Umožňuje flexibilně zvolit prostředí, které nejlépe vyhovuje vašemu stylu práce – Bash nebo PowerShell. Pro tento rychlý Start vyberte režim **bash** . Azure Cloud Shell také vyžaduje účet úložiště, můžete ho po zobrazení výzvy vytvořit.

Vyberte tlačítko **vyzkoušet** vedle následujícího kódu, zvolte režim **bash** , vyberte **vytvořit účet úložiště** a přihlaste se Cloud Shell. Další kopii a vložte následující kód, který Azure Cloud Shell a spustí. Název účtu Azure Cosmos musí být globálně jedinečný, před spuštěním příkazu se ujistěte, že jste aktualizovali `mysqlapicosmosdb` hodnotu.

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

Vytvoření účtu Azure Cosmos trvá chvilku, jakmile bude operace úspěšná, můžete zobrazit výstup potvrzení. Po úspěšném dokončení příkazu se přihlaste k [Azure Portal](https://portal.azure.com/) a ověřte, že účet Azure Cosmos se zadaným názvem existuje. Po vytvoření prostředku můžete okno Azure Cloud Shell zavřít. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Vytvoření nové aplikace .NET

Vytvořte novou aplikaci .NET v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). Otevřete příkazový řádek systému Windows nebo okno terminálu z místního počítače. Všechny příkazy v dalších částech budete spouštět z příkazového řádku nebo terminálu.  Spusťte následující příkaz dotnet New a vytvořte novou aplikaci s názvem `todo` . Parametr--langversion – nastaví vlastnost langversion – v souboru vytvořeného projektu.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

   ```bash
   cd todo
   dotnet build
   ```

Očekávaný výstup sestavení by měl vypadat přibližně takto:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalace balíčku Azure Cosmos DB

Stále v adresáři aplikace nainstalujte Azure Cosmos DB klientskou knihovnu pro .NET Core pomocí příkazu dotnet Add Package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů účtu Azure Cosmos z Azure Portal

Ukázková aplikace se musí ověřit pro váš účet Azure Cosmos. K ověřování byste měli do aplikace předat přihlašovací údaje účtu Azure Cosmos. Pomocí následujících kroků Získejte přihlašovací údaje k účtu Azure Cosmos:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Přejděte k účtu Azure Cosmos.

1. Otevřete podokno **klíče** a zkopírujte **identifikátor URI** a **primární klíč** svého účtu. V dalším kroku přidáte hodnoty identifikátoru URI a Keys do proměnné prostředí.

## <a name="object-model"></a><a id="object-model"></a>Objektový model

Než začnete sestavovat aplikaci, Podívejme se na hierarchii prostředků v Azure Cosmos DB a objektový model použitý k vytvoření a přístup k těmto prostředkům. Azure Cosmos DB vytvoří prostředky v následujícím pořadí:

* Účet Azure Cosmos 
* Databáze 
* Containers 
* Položky

Další informace o hierarchii různých entit najdete v tématu [práce s databázemi, kontejnery a položkami v Azure Cosmos DB](databases-containers-items.md) článku. K interakci s těmito prostředky použijete následující třídy .NET:

* CosmosClient – Tato třída poskytuje logickou reprezentaci na straně klienta pro službu Azure Cosmos DB. Objekt klienta se používá ke konfiguraci a provádění požadavků na službu.
* CreateDatabaseIfNotExistsAsync – Tato metoda vytvoří (Pokud neexistuje) nebo získá (Pokud již existuje) databázový prostředek jako asynchronní operaci. 
* CreateContainerIfNotExistsAsync – Tato metoda vytvoří (Pokud neexistuje) nebo získá (Pokud již existuje) kontejner jako asynchronní operace. Můžete zjistit stavový kód z odpovědi, abyste zjistili, zda byl kontejner nově vytvořen (201) nebo byl vrácen existující kontejner (200). 
* CreateItemAsync – Tato metoda vytvoří položku v rámci kontejneru.
* UpsertItemAsync – Tato metoda vytvoří položku v rámci kontejneru, pokud ještě neexistuje, nebo ji nahradí, pokud již existuje. 
* GetItemQueryIterator – Tato metoda vytvoří dotaz na položky v rámci kontejneru v databázi Azure Cosmos pomocí příkazu jazyka SQL s parametrizovanými hodnotami. 
* DeleteAsync – odstraní zadanou databázi ze svého účtu Azure Cosmos. `DeleteAsync` Metoda odstraní pouze databázi.

 ## <a name="code-examples"></a><a id="code-examples"></a>Příklady kódu

Vzorový kód popsaný v tomto článku vytvoří v Azure Cosmos DB databázi rodiny. Databáze rodiny obsahuje podrobnosti o rodině, jako je jméno, adresa, umístění, přidružené nadřazené položky, děti a domácí. Před naplněním dat do účtu Azure Cosmos definujte vlastnosti rodiny položek. Vytvořte novou třídu s názvem `Family.cs` na kořenové úrovni ukázkové aplikace a přidejte do ní následující kód:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Přidání direktivy using & definování objektu klienta

V adresáři projektu otevřete `Program.cs` soubor v editoru a v horní části aplikace přidejte následující direktivy using:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Do třídy přidejte následující globální proměnné `Program` . Tyto budou zahrnovat koncový bod a autorizační klíče, název databáze a kontejner, který vytvoříte. Nezapomeňte nahradit hodnoty koncových bodů a autorizačních klíčů podle vašeho prostředí. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Nakonec nahraďte `Main` metodu:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Vytvoření databáze 

Definujte `CreateDatabaseAsync` metodu v rámci `program.cs` třídy. Tato metoda vytvoří, `FamilyDatabase` Pokud ještě neexistuje.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Vytvoření kontejneru

Definujte `CreateContainerAsync` metodu v rámci `Program` třídy. Tato metoda vytvoří, `FamilyContainer` Pokud ještě neexistuje. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Vytvořit položku

Vytvořte položku rodiny přidáním `AddItemsToContainerAsync` metody s následujícím kódem. Pomocí `CreateItemAsync` metod nebo můžete `UpsertItemAsync` vytvořit položku:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Dotazování na položky

Po vložení položky můžete spustit dotaz a získat podrobnosti o řadě "Andersen". Následující kód ukazuje, jak spustit dotaz přímo pomocí dotazu SQL. Dotaz SQL pro získání podrobností o řadě "Anderson" je: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definujte `QueryItemsAsync` metodu v rámci `Program` třídy a přidejte do ní následující kód:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Nahrazení položky 

Přečtěte si položku rodiny a pak ji aktualizujte přidáním `ReplaceFamilyItemAsync` metody s následujícím kódem.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Odstranění položky 

Odstraňte položku rodiny přidáním `DeleteFamilyItemAsync` metody s následujícím kódem.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Odstranění databáze 

Nakonec můžete odstranit databázi přidáním `DeleteDatabaseAndCleanupAsync` metody s následujícím kódem:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Až přidáte všechny požadované metody, `Program` soubor uložte. 

## <a name="run-the-code"></a>Spuštění kódu

Další Sestavte a spusťte aplikaci k vytvoření prostředků Azure Cosmos DB.

   ```bash
   dotnet run
   ```

Při spuštění aplikace se vygeneruje následující výstup. Můžete se také přihlásit k Azure Portal a ověřit, zda jsou prostředky vytvořeny:

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

Můžete ověřit, že se data vytvoří, když se přihlásíte k Azure Portal a zobrazíte požadované položky v účtu Azure Cosmos. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání účtu Azure Cosmos a odpovídající skupiny prostředků použít Azure CLI nebo Azure PowerShell. Následující příkaz ukazuje, jak odstranit skupinu prostředků pomocí Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, vytvoření databáze a kontejneru pomocí aplikace .NET Core. Teď můžete do svého účtu Azure Cosmos importovat další data pomocí pokynů v následujícím článku. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
