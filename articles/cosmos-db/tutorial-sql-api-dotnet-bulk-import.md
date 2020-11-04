---
title: Hromadné importy dat do Azure Cosmos DB účtu rozhraní SQL API pomocí sady .NET SDK
description: Naučte se importovat nebo ingestovat data Azure Cosmos DB vytvořením konzolové aplikace .NET, která optimalizuje zřízenou propustnost (RU/s) potřebnou pro import dat.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 09/21/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 6e50fe1f5b1658458a2d561f8157f04d1cbceb41
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341229"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Hromadné importy dat do Azure Cosmos DB účtu rozhraní SQL API pomocí sady .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V tomto kurzu se dozvíte, jak vytvořit konzolovou aplikaci .NET, která optimalizuje zřízenou propustnost (RU/s) potřebnou k importu dat do Azure Cosmos DB. V tomto článku načtete data z ukázkového zdroje dat a naimportujete ho do kontejneru Azure Cosmos.
V tomto kurzu se používá [verze 3.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) sady Azure Cosmos DB .NET SDK, na kterou se dá cílit .NET Framework nebo .NET Core.

Tento kurz zahrnuje:

> [!div class="checklist"]
> * Vytvoření účtu Azure Cosmos
> * Konfigurace projektu
> * Připojení k účtu Azure Cosmos s povolenou hromadnou podporou
> * Provést import dat prostřednictvím souběžných operací vytvoření

## <a name="prerequisites"></a>Předpoklady

Než budete postupovat podle pokynů v tomto článku, ujistěte se, že máte následující zdroje:

* Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Sada SDK .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core). Spuštěním sady můžete ověřit, která verze je ve vašem prostředí k dispozici `dotnet --version` .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB

[Vytvořte účet Azure Cosmos DB SQL API](create-cosmosdb-resources-portal.md) z Azure Portal nebo ho můžete vytvořit pomocí [emulátoru Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Krok 2: nastavení projektu .NET

Otevřete příkazový řádek systému Windows nebo okno terminálu z místního počítače. Všechny příkazy v dalších částech budete spouštět z příkazového řádku nebo terminálu. Spusťte následující příkaz dotnet New a vytvořte novou aplikaci s názvem *Hromadná ukázka-import-demo*. `--langVersion`Parametr nastaví vlastnost *langversion –* v souboru vytvořeného projektu.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Očekávaný výstup sestavení by měl vypadat přibližně takto:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Krok 3: Přidání balíčku Azure Cosmos DB

Stále v adresáři aplikace nainstalujte Azure Cosmos DB klientskou knihovnu pro .NET Core pomocí příkazu dotnet Add Package.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Krok 4: získání přihlašovacích údajů k účtu Azure Cosmos

Ukázková aplikace se musí ověřit pro váš účet Azure Cosmos. K ověřování byste měli do aplikace předat přihlašovací údaje účtu Azure Cosmos. Pomocí následujících kroků Získejte přihlašovací údaje k účtu Azure Cosmos:

1.  Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
1.  Přejděte k účtu Azure Cosmos.
1.  Otevřete podokno **klíče** a zkopírujte **identifikátor URI** a **primární klíč** svého účtu.

Pokud používáte emulátor Azure Cosmos DB, Získejte [přihlašovací údaje emulátoru z tohoto článku](local-emulator.md#authenticate-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Krok 5: inicializace objektu CosmosClient s podporou hromadného provádění

Otevřete vygenerovaný `Program.cs` soubor v editoru kódu. Vytvoříte novou instanci CosmosClient s povoleným hromadným spouštěním a použijete ji k provádění operací s Azure Cosmos DB. 

Pojďme začít přepsáním výchozí `Main` metody a definováním globálních proměnných. Tyto globální proměnné budou zahrnovat koncový bod a autorizační klíč, název databáze, kontejner, který budete vytvářet, a počet položek, které budete hromadně vkládat. Nezapomeňte nahradit hodnoty endpointURL a autorizační klíč podle vašeho prostředí. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

V rámci `Main` metody přidejte následující kód pro inicializaci objektu CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Po povolení hromadného spuštění CosmosClient interně seskupuje souběžné operace do jediného volání služby. Tímto způsobem optimalizuje využití propustnosti distribucí volání služby napříč oddíly a nakonec přiřadíte k původním volajícím jednotlivé výsledky.

Pak můžete vytvořit kontejner pro uložení všech položek.  Definujte `/pk` jako klíč oddílu 50000 ru/s jako zřízenou propustnost a vlastní zásadu indexování, která vyloučí všechna pole, která budou optimalizovat propustnost zápisu. Po příkazu inicializace CosmosClient přidejte následující kód:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Krok 6: naplnění seznamu souběžných úloh

Chcete-li využít podporu hromadného provádění, vytvořte seznam asynchronních úloh založených na zdroji dat a operací, které chcete provést, a použijte `Task.WhenAll` je k souběžnému spuštění.
Začněte tím, že použijete "falešně" data k vygenerování seznamu položek z našeho datového modelu. V reálné aplikaci se položky podávají z požadovaného zdroje dat.

Nejdřív přidejte do řešení falešně vydaný balíček pomocí příkazu dotnet Add Package.

   ```bash
   dotnet add package Bogus
   ```

Definujte definici položek, které chcete uložit. Je nutné definovat `Item` třídu v rámci `Program.cs` souboru:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Dále vytvořte pomocnou funkci uvnitř `Program` třídy. Tato pomocná funkce Získá počet položek, které jste definovali pro vložení, a vygeneruje náhodná data:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Přečtěte si položky a serializovat je do instancí streamu pomocí `System.Text.Json` třídy. Z důvodu povahy automaticky generovaných dat provádíte serializaci dat jako proudů. Instanci položky můžete také použít přímo, ale jejich převodem na datové proudy můžete využít výkon rozhraní API streamu v CosmosClient. Obvykle můžete data použít přímo, pokud znáte klíč oddílu. 


Chcete-li převést data na instance služby Stream v rámci `Main` metody, přidejte po vytvoření kontejneru hned následující kód:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Dále pomocí datových proudů vytvořte souběžné úlohy a naplňte seznam úkolů pro vložení položek do kontejneru. Chcete-li provést tuto operaci, přidejte do třídy následující kód `Program` :

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Všechny tyto operace s souběžným bodem se spustí společně (to je hromadně), jak je popsáno v úvodních částech.

## <a name="step-7-run-the-sample"></a>Krok 7: spuštění ukázky

Chcete-li spustit ukázku, můžete to provést jednoduše pomocí `dotnet` příkazu:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Získání kompletní ukázky

Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jen chcete stáhnout ukázky kódu, můžete je získat z [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Po klonování projektu nezapomeňte aktualizovat požadované přihlašovací údaje v [program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25).

Ukázku můžete spustit tak, že změníte adresář úložiště a použijete `dotnet` :

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující kroky:

> [!div class="checklist"]
> * Vytvoření účtu Azure Cosmos
> * Konfigurace projektu
> * Připojení k účtu Azure Cosmos s povolenou hromadnou podporou
> * Provést import dat prostřednictvím souběžných operací vytvoření

Teď můžete přejít k dalšímu kurzu:

> [!div class="nextstepaction"]
>[Dotazování Azure Cosmos DB pomocí rozhraní SQL API](tutorial-query-sql-api.md)