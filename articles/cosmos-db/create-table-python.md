---
title: 'Rychlý start: Table API s využitím Pythonu – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Table API k vytvoření aplikace pomocí webu Azure Portal a Pythonu.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 9de9739efce13fc96bf550759eb0ef68d732af1e
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212691"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Rychlý start: Sestavení aplikace Table API pomocí Pythonu a Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

V tomto rychlém startu vytvoříte a spravujete účet Azure Cosmos DB rozhraní API pro tabulky z Azure Portal a ze sady Visual Studio s aplikací Python naklonované z GitHubu. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) můžete použít také s identifikátorem URI `https://localhost:8081` a klíčovým `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)s úlohami **vývoje pro Azure** a vývoj v **Pythonu** vybrané během instalace. 
- [Git](https://git-scm.com/downloads)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

> [!IMPORTANT] 
> Abyste mohli pracovat s obecně dostupnými sadami Table API SDK, musíte si vytvořit nový účet Table API. Obecně dostupné sady SDK nepodporují účty Table API vytvořené během období Preview.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Přidání tabulky

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci Table z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Potom otevřete soubor řešení v sadě Visual Studio. 

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na web Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází. 

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)vyberte **připojovací řetězec**. 

    ![Zobrazení a zkopírování PŘIPOJOVACÍho řetězce v nastavení připojovacího řetězce](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Pomocí tlačítka na pravé straně zkopírujte hodnotu NÁZEV ÚČTU.

3. Otevřete soubor *config.py* a vložte název účtu z portálu do hodnoty STORAGE_ACCOUNT_NAME na řádku 19.

4. Vraťte se na portál a zkopírujte PRIMÁRNÍ KLÍČ.

5. Vložte PRIMÁRNÍ KLÍČ z portálu do hodnoty STORAGE_ACCOUNT_KEY na řádku 20.

6. Uložte soubor *config.py* .

## <a name="run-the-app"></a>Spuštění aplikace

1. V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v **Průzkumník řešení**.

2. Vyberte aktuální prostředí Python a pak klikněte pravým tlačítkem myši.

2. Vyberte **nainstalovat balíček python**a pak zadejte *Azure-Storage-Table*.

3. Stisknutím klávesy F5 spusťte aplikaci. Aplikace se zobrazí v prohlížeči. 

Vám může nyní přejděte zpět do Průzkumníku dat naleznete v tématu, dotazování, upravit a pracovat s těmito novými daty. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos DB, vytvoření tabulky pomocí Průzkumník dat a spuštění aplikace v Pythonu v aplikaci Visual Studio pro přidání dat tabulky.  Teď můžete zadávat dotazy na svá data pomocí rozhraní API tabulky.  

> [!div class="nextstepaction"]
> [Import tabulkových dat do rozhraní Table API](table-import.md)
