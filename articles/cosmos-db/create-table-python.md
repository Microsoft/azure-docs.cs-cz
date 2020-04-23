---
title: 'Úvodní příručka: Rozhraní API tabulky s Pythonem – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Table API k vytvoření aplikace pomocí webu Azure Portal a Pythonu.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 673155706c3b7d44b41562c2dc8ce24a94ac6a58
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083627"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Rychlý start: Sestavení aplikace Table API pomocí Pythonu a Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

V tomto rychlém startu vytvoříte a spravujete účet rozhraní API azure cosmos DB table api z webu Azure portal a z Visual Studia s aplikací Pythonu naklonotou z GitHubu. Azure Cosmos DB je vícemodelová databázová služba, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, hodnot klíče a grafů s možností globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadované součásti

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Můžete také použít [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) `https://localhost:8081` s identifikátorem URI a klíč `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)s úlohami **vývoje Azure** a **Pythonu** vybranými během instalace. 
- [Git](https://git-scm.com/downloads).

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

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

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

> ! To je v pořádku. Podrobnější návod podobného kódu najdete v článku ukázkový článek [rozhraní API tabulky Cosmos DB.](table-storage-how-to-use-python.md)

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází. 

1. Ve svém účtu Azure Cosmos DB na [webu Azure Portal](https://portal.azure.com/)vyberte Spojovací **řetězec**. 

    ![Zobrazení a kopírování připojovacího řetězce v nastavení připojovacího řetězce](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Pomocí tlačítka na pravé straně zkopírujte hodnotu NÁZEV ÚČTU.

3. Otevřete *soubor config.py* a vložte název účtu z portálu do STORAGE_ACCOUNT_NAME hodnoty na řádku 19.

4. Vraťte se na portál a zkopírujte PRIMÁRNÍ KLÍČ.

5. Vložte PRIMÁRNÍ KLÍČ z portálu do hodnoty STORAGE_ACCOUNT_KEY na řádku 20.

6. Uložte *soubor config.py.*

## <a name="run-the-app"></a>Spuštění aplikace

1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**.

2. Vyberte aktuální prostředí Pythonu a klikněte pravým tlačítkem myši.

2. Vyberte **Nainstalovat balíček Pythonu**a zadejte *tabulku azure-storage .*

3. Stisknutím klávesy F5 spusťte aplikaci. Aplikace se zobrazí v prohlížeči. 

Nyní se můžete vrátit do Průzkumníka dat a zobrazit tato nová data, dotazovat se, upravovat je a pracovat s ním. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos DB, vytvořit tabulku pomocí Průzkumníka dat a spustit aplikaci Pythonu ve Visual Studiu a přidat data tabulky.  Teď můžete zadávat dotazy na svá data pomocí rozhraní API tabulky.  

> [!div class="nextstepaction"]
> [Import tabulkových dat do rozhraní Table API](table-import.md)
