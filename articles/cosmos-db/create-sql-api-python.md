---
title: 'Rychlý Start: sestavení aplikace v Pythonu pomocí Azure Cosmos DB účtu rozhraní SQL API'
description: Tento článek představuje ukázku kódu Python, který můžete použít k připojení a dotazování služby Azure Cosmos DB přes rozhraní SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 92175e3b9776f90bd50e356a700b2a2d475c8a85
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602458"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Rychlý Start: sestavení aplikace v Pythonu pomocí Azure Cosmos DB účtu rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu se dozvíte, jak vytvořit účet rozhraní [SQL API](sql-api-introduction.md) služby Azure Cosmos DB, databázi dokumentů a kontejner pomocí webu Azure Portal. Pak vytvoříte a spustíte aplikaci konzoly, která byla vytvořena pomocí sady Python SDK pro rozhraní [SQL API](sql-api-sdk-python.md).

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Můžete rychle vytvořit a zadat dotaz na databáze, klíč/hodnota, šířku sloupců a grafů. Všechny tyto operace využívají distribuci a škálování Azure Cosmos DB.

V tomto rychlém startu se používá verze 4 [sady Python SDK](https://pypi.org/project/azure-cosmos/#history).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Požadavky

* [Python 3,6](https://www.python.org/downloads/), ve vaší `PATH`je k dispozici spustitelný soubor `python`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Rozšíření Python pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidat kontejner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci SQL API z GitHubu, nastavíme připojovací řetězec a spustíme ji.

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

    ```cmd
    md "git-samples"
    ```
   Pokud používáte příkazový řádek bash, měli byste místo toho použít následující příkaz:

   ```bash
   mkdir "git-samples"
   ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. V [Azure Portal](https://portal.azure.com/)v účtu Azure Cosmos v levém navigačním panelu vyberte **klíče**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky identifikátor **URI** a **primární klíč** do souboru `cosmos_get_started.py`.

    ![Získání přístupového klíče a identifikátoru URI v nastavení klíčů v Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. V \git-samples\azure-Cosmos-DB-Python-Getting-Started otevřete soubor `cosmos_get_started.py` v Visual Studio Code.

3. Z portálu Zkopírujte hodnotu **identifikátoru URI** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu proměnné **koncového bodu** v ``cosmos_get_started.py``. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Pak z portálu Zkopírujte hodnotu **primárního klíče** a nastavte ji jako hodnotu **klíče** v ``cosmos_get_started.py``. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

    `key = 'FILLME'`

5. Uložte soubor ``cosmos_get_started.py``.

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Přečtěte si informace o databázových prostředcích vytvořených v kódu nebo přeskočit k části [aktualizace připojovacího řetězce](#update-your-connection-string).

Všechny následující fragmenty kódu pocházejí ze souboru `cosmos_get_started.py`.

* Inicializuje se CosmosClient. Nezapomeňte aktualizovat hodnoty "Endpoint" a "Key", jak je popsáno v části [aktualizace připojovacího řetězce](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Vytvoří se nová databáze.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Vytvoří se nový kontejner s 400 RU/s [zřízené propustností](request-units.md). Jako [klíč oddílu](partitioning-overview.md#choose-partitionkey)zvolíme `lastName`, což nám umožní provádět efektivní dotazy, které tuto vlastnost filtrují. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Do kontejneru se přidá několik položek. Kontejnery jsou kolekce položek (dokumenty JSON), které mohou mít různé schéma. Pomocné metody ```get_[name]_family_item``` vracet reprezentace řady, které jsou uloženy v Azure Cosmos DB jako dokumenty JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Čtení bodů (klíčová vyhledávání hodnot) se provádí pomocí metody `read_item`. Vytiskněte [poplatek](request-units.md) za každou operaci.
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Dotaz se provádí pomocí syntaxe dotazu SQL. Vzhledem k tomu, že používáme v klauzuli WHERE hodnoty klíče oddílu ```lastName```, Azure Cosmos DB efektivně směrovat tento dotaz na relevantní oddíly a zlepší výkon.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Spusťte aplikaci

1. V aplikaci Visual Studio Code vyberte **Zobrazení** > **Paleta příkazů**. 

2. Do příkazového řádku zadejte **Python: Select Interpreter** a pak vyberte verzi jazyka Python, kterou chcete používat.

    Zápatí v aplikaci Visual Studio Code se aktualizuje a bude označovat, že je vybraný interpret. 

3. Vyberte **Zobrazit** > **Integrovaný terminál**, aby se otevřel integrovaný terminál aplikace Visual Studio Code.

4. V okně integrovaného terminálu zkontrolujte, že jste ve složce azure-cosmos-db-python-getting-started. Pokud ne, spusťte následující příkaz, abyste do složky ukázky přešli. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Spuštěním následujícího příkazu nainstalujte balíček azure-cosmos. 

    ```python
    pip3 install azure-cosmos==4.0.0b5
    ```

    Pokud při pokusu o instalaci balíčku azure-cosmos dojde k chybě v podobě odepření přístupu, budete muset [spustit VS Code jako správce](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Spuštěním následujícího příkazu spusťte ukázku a vytvořte a uložte nové dokumenty v Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Chcete-li ověřit, zda byly nové položky vytvořeny a uloženy, v Azure Portal vyberte položku **Průzkumník dat** > **AzureSampleFamilyDatabase** > **položky**. Zobrazit položky, které byly vytvořeny. Například tady je ukázkový dokument JSON pro Andersen rodinu:

```json
{
    "id": "Andersen-1569479288379",
    "lastName": "Andersen",
    "district": "WA5",
    "parents": [
        {
            "familyName": null,
            "firstName": "Thomas"
        },
        {
            "familyName": null,
            "firstName": "Mary Kay"
        }
    ],
    "children": null,
    "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
    },
    "registered": true,
    "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
    "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
    "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
    "_attachments": "attachments/",
    "_ts": 1569479288
}
```

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, vytvoření kontejneru pomocí Průzkumník dat a spuštění aplikace. Teď můžete do účtu Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat do databáze Azure Cosmos DB pro rozhraní SQL API](import-data.md)


