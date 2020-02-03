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
ms.openlocfilehash: 1e0016e8ce6bc9178befd1a5ca96aa2554929ac5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719530"
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

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a zadat dotaz na databáze, klíč/hodnota, šířku sloupců a grafů. Všechny tyto operace využívají distribuci a škálování Azure Cosmos DB.

V tomto rychlém startu se používá verze 4 [sady Python SDK](https://pypi.org/project/azure-cosmos/#history).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Předpoklady

* [Python 3.6 +](https://www.python.org/downloads/), ve vaší `PATH`je k dispozici spustitelný soubor `python`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Rozšíření Pythonu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidat kontejner

Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření databáze a kontejneru. 

1. Vyberte **Průzkumník dat** > **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    ![Azure Portal Průzkumník dat, přidat podokno kontejneru](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *ToDoList* . Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat `/, \\, #, ?`ani mezeru. Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Items|Jako název nového kontejneru zadejte *položky* . ID kontejnerů mají stejné požadavky na znaky jako názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá jako klíč oddílu */Category* .|
    
    Kromě předchozích nastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením jedinečné zásady klíčů při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.

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

Teď se vraťte zpátky na web Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. V [Azure Portal](https://portal.azure.com/)v účtu Azure Cosmos v levém navigačním panelu vyberte **klíče**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky identifikátor **URI** a **primární klíč** do souboru `cosmos_get_started.py`.

    ![Získání přístupového klíče a identifikátoru URI v nastavení klíčů v Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. V \git-samples\azure-Cosmos-DB-Python-Getting-Started otevřete soubor `cosmos_get_started.py` v Visual Studio Code.

3. Z portálu Zkopírujte hodnotu **identifikátoru URI** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu proměnné **koncového bodu** v ``cosmos_get_started.py``. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Pak z portálu Zkopírujte hodnotu **primárního klíče** a nastavte ji jako hodnotu **klíče** v ``cosmos_get_started.py``. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s databází Azure Cosmos. 

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
    pip install --pre azure-cosmos
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

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, vytvoření kontejneru pomocí Průzkumník dat a spuštění aplikace. Teď můžete do účtu databáze Cosmos importovat další data. 

> [!div class="nextstepaction"]
> [Import dat do databáze Azure Cosmos DB pro rozhraní SQL API](import-data.md)


