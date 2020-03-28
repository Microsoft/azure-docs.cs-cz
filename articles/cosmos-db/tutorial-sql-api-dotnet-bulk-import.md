---
title: Hromadný import dat do účtu SQL API Služby Azure Cosmos DB pomocí sady .Net SDK
description: Zjistěte, jak importovat nebo ingestovat data do Služby Azure Cosmos DB vytvořením aplikace konzoly .NET, která optimalizuje zřízenou propustnost (RU/s) požadovanou pro import dat
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77587562"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Hromadný import dat do účtu SQL API Služby Azure Cosmos DB pomocí sady .NET SDK

Tento kurz ukazuje, jak vytvořit aplikaci konzoly .NET, která optimalizuje zřízenou propustnost (RU/s) potřebnou k importu dat do Azure Cosmos DB. V tomto článku budete číst data ze zdroje ukázkových dat a importovat je do kontejneru Azure Cosmos.
Tento kurz používá [verzi 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) sady Azure Cosmos DB .NET SDK, která může být cílená na rozhraní .NET Framework nebo .NET Core.

Tento kurz zahrnuje:

> [!div class="checklist"]
> * Vytvoření účtu Azure Cosmos
> * Konfigurace projektu
> * Připojení k účtu Azure Cosmos s povolenou hromadnou podporou
> * Provedení importu dat prostřednictvím souběžných operací vytváření

## <a name="prerequisites"></a>Požadavky

Než budete postupujte podle pokynů v tomto článku, ujistěte se, že máte následující zdroje:

* Aktivní účet Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Spuštěním aplikace můžete ověřit, která `dotnet --version`verze je ve vašem prostředí k dispozici.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB

[Vytvořte účet Azure Cosmos DB SQL API](create-cosmosdb-resources-portal.md) z portálu Azure nebo můžete vytvořit účet pomocí [emulátoru Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Krok 2: Nastavení projektu .NET

Otevřete příkazový řádek systému Windows nebo okno terminálu z místního počítače. Všechny příkazy v následujících částech spustíte z příkazového řádku nebo terminálu. Spusťte následující dotnet nový příkaz k vytvoření nové aplikace s názvem *hromadné import-demo*. Parametr `--langVersion` nastaví vlastnost *LangVersion* v vytvořeném souboru projektu.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Očekávaný výstup z sestavení by měl vypadat nějak takto:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Krok 3: Přidání balíčku Azure Cosmos DB

Zatímco ještě v adresáři aplikace, nainstalujte klientské knihovny Azure Cosmos DB pro .NET Core pomocí příkazu dotnet add package.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Krok 4: Získejte přihlašovací údaje k účtu Azure Cosmos

Ukázková aplikace potřebuje k ověření vašeho účtu Azure Cosmos. K ověření, měli byste předat přihlašovací údaje účtu Azure Cosmos do aplikace. Získejte přihlašovací údaje k účtu Azure Cosmos takto:

1.  Přihlaste se k [portálu Azure](https://portal.azure.com/).
1.  Přejděte na svůj účet Azure Cosmos.
1.  Otevřete podokno **Klíče** a zkopírujte **identifikátor URI** a **primární klíč** svého účtu.

Pokud používáte emulátor Azure Cosmos DB, získejte [pověření emulátoru z tohoto článku](local-emulator.md#authenticating-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Krok 5: Inicializovat objekt CosmosClient s podporou hromadného spuštění

Otevřete generovaný `Program.cs` soubor v editoru kódu. Vytvoříte novou instanci CosmosClient s povoleným hromadným spuštěním a použijete ji k provádění operací s Azure Cosmos DB. 

Začněme přepsáním `Main` výchozí metody a definováním globálních proměnných. Tyto globální proměnné budou zahrnovat koncový bod a autorizační klíče, název databáze, kontejner, který vytvoříte, a počet položek, které budete vkládat hromadně. Nezapomeňte nahradit hodnoty endpointURL a autorizačního klíče podle vašeho prostředí. 


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

Uvnitř `Main` metody přidejte následující kód pro inicializaci objektu CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Po povolení hromadného spuštění CosmosClient interně seskupí souběžné operace do volání jedné služby. Tímto způsobem optimalizuje využití propustnosti distribucí volání služby mezi oddíly a nakonec přiřazení jednotlivých výsledků původním volajícím.

Poté můžete vytvořit kontejner pro uložení všech našich položek.  Definujte `/pk` jako klíč oddílu, 50000 RU/s jako zřízenou propustnost a vlastní zásady indexování, které vyloučí všechna pole pro optimalizaci propustnosti zápisu. Za příkaz inicializace klienta CosmosClient přidejte následující kód:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Krok 6: Naplnění seznamu souběžných úloh

Chcete-li využít podporu hromadného spuštění, vytvořte seznam asynchronních úloh založených na zdroji dat a `Task.WhenAll` operacích, které chcete provést, a použijte k jejich souběžnému spuštění.
Začněme pomocí dat "Bogus" pro generování seznamu položek z našeho datového modelu. V aplikaci reálného světa by položky pocházejí z požadovaného zdroje dat.

Nejprve přidejte balíček Bogus do řešení pomocí příkazu dotnet add package.

   ```bash
   dotnet add package Bogus
   ```

Definujte definici položek, které chcete uložit. Je třeba definovat `Item` třídu `Program.cs` v souboru:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Dále vytvořte pomocnou funkci `Program` uvnitř třídy. Tato pomocná funkce získá počet položek, které jste definovali pro vložení a generuje náhodná data:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Přečtěte si položky a serializovat je `System.Text.Json` do instancí datového proudu pomocí třídy. Vzhledem k povaze automaticky generovaných dat serializujete data jako datové proudy. Můžete také použít instanci položky přímo, ale jejich převodem na datové proudy, můžete využít výkon datových proudů API v CosmosClient. Obvykle můžete použít data přímo tak dlouho, jak znáte klíč oddílu. 


Chcete-li převést data na `Main` instance datového proudu, přidejte v rámci metody následující kód ihned po vytvoření kontejneru:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Dále použijte datové proudy k vytvoření souběžných úloh a naplnění seznamu úkolů pro vložení položek do kontejneru. Chcete-li provést tuto operaci, `Program` přidejte do třídy následující kód:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Všechny tyto souběžné bodové operace budou prováděny společně (to je hromadně), jak je popsáno v úvodní části.

## <a name="step-7-run-the-sample"></a>Krok 7: Spuštění ukázky

Chcete-li spustit ukázku, můžete to `dotnet` udělat jednoduše příkazem:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Získejte kompletní vzorek

Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jen chcete stáhnout ukázky kódu, můžete je získat z [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Po klonování projektu nezapomeňte aktualizovat požadovaná pověření uvnitř [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25).

Ukázku lze spustit změnou do adresáře `dotnet`úložiště a pomocí :

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
> * Provedení importu dat prostřednictvím souběžných operací vytváření

Nyní můžete přistoupit k dalšímu kurzu:

> [!div class="nextstepaction"]
>[Dotaz na Azure Cosmos DB pomocí rozhraní SQL API](tutorial-query-sql-api.md)