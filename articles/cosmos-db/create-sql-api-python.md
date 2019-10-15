---
title: 'Rychlý Start: sestavení aplikace v Pythonu pomocí Azure Cosmos DB účtu rozhraní SQL API'
description: Tento článek představuje ukázku kódu Python, který můžete použít k připojení a dotazování služby Azure Cosmos DB přes rozhraní SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: f00d0e4f659c1ebe104dac1a25ea3688c9a1f2e5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327252"
---
# <a name="azure-cosmos-db-build-a-python-application-using-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB: sestavení aplikace v Pythonu pomocí Azure Cosmos DB účtu SQL API

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Můžete rychle vytvořit a zadat dotaz na databáze, klíč/hodnota, šířku sloupců a grafů. Všechny tyto operace využívají distribuci a škálování Azure Cosmos DB.

V tomto rychlém startu se dozvíte, jak vytvořit účet rozhraní [SQL API](sql-api-introduction.md) služby Azure Cosmos DB, databázi dokumentů a kontejner pomocí webu Azure Portal. Pak vytvoříte a spustíte aplikaci konzoly, která byla vytvořena pomocí sady Python SDK pro rozhraní [SQL API](sql-api-sdk-python.md). V tomto rychlém startu se používá verze 3,0 [sady Python SDK](https://pypi.org/project/azure-cosmos).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Předpoklady

* [Python 3,6](https://www.python.org/downloads/), ve `PATH` je k dispozici spustitelný soubor `python`.
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

1. V [Azure Portal](https://portal.azure.com/)v účtu Azure Cosmos v levém navigačním panelu vyberte **klíče**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky identifikátor **URI** a **primární klíč** do souboru `CosmosGetStarted.py`.

    ![Zobrazení a zkopírování přístupového klíče na portálu Azure Portal v okně Klíče](./media/create-sql-api-dotnet/keys.png)

2. Otevřete soubor `CosmosGetStarted.py` v \git-samples\azure-Cosmos-DB-Python-Getting-Started v Visual Studio Code.

3. Z portálu zkopírujte hodnotu identifikátoru **URI** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu klíče **endpoint** v souboru ``CosmosGetStarted.py``. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Pak z portálu zkopírujte hodnotu **PRIMÁRNÍ KLÍČ** a nastavte ji jako hodnotu **config.PRIMARYKEY** v souboru ``CosmosGetStarted.py``. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s databází Azure Cosmos. 

    `'PRIMARYKEY': 'FILLME',`

5. Uložte soubor ``CosmosGetStarted.py``.

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Přečtěte si informace o databázových prostředcích vytvořených v kódu nebo přeskočit k části [aktualizace připojovacího řetězce](#update-your-connection-string).

Poznámka: Pokud znáte předchozí verzi sady Python SDK, možná jste zvyklí na používání termínů „kolekce“ a „dokument“. Vzhledem k tomu, že Azure Cosmos DB podporuje více modelů rozhraní API, ve verzi 3.0 sady Python SDK a novějších se používají obecné termíny „kontejner“, což může být kolekce, graf nebo tabulka, a „položka“ popisující obsah kontejneru.

Všechny následující fragmenty kódu pocházejí ze souboru `CosmosGetStarted.py`.

* Inicializuje se CosmosClient. Nezapomeňte aktualizovat hodnoty "koncový bod" a "hlavní klíč", jak je popsáno v části [aktualizace připojovacího řetězce](#update-your-connection-string) . 

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* Vytvoří se nová databáze.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* Vytvoří se nový kontejner.

    ```python
    # Create container options
    options = {
        'offerThroughput': 400
    }

    # Create a container
    container = client.CreateContainer(db['_self'], container_definition, options)
    ```

* Do kontejneru se přidá několik položek.

    ```python
    # Create and add some items to the container
    item1 = client.CreateItem(container['_self'], {
        'serverId': 'server1',
        'Web Site': 0,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 1!'
        }
    )

    item2 = client.CreateItem(container['_self'], {
        'serverId': 'server2',
        'Web Site': 1,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 2!'
        }
    )
    ```

* Provede se dotaz v jazyce SQL.

    ```python
    query = {'query': 'SELECT * FROM server s'}

    options = {}
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryItems(container['_self'], query, options)
    for item in iter(result_iterable):
        print(item['message'])
    ```
   
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
    pip3 install azure-cosmos
    ```

    Pokud při pokusu o instalaci balíčku azure-cosmos dojde k chybě v podobě odepření přístupu, budete muset [spustit VS Code jako správce](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Spusťte následující příkaz, kterým spustíte ukázku a vytvoříte a uložíte nové dokumenty v Azure Cosmos dB.

    ```python
    python CosmosGetStarted.py
    ```

7. Pokud si chcete potvrdit, že nové položky byly vytvořeny a uloženy, vyberte na webu Azure Portal **Průzkumník dat**, rozbalte položku **coll**, rozbalte položku **Documents** a pak vyberte dokument **server1**. Obsah dokumentu server1 odpovídá obsahu vrácenému v okně integrovaného terminálu. 

    ![Zobrazení nových dokumentů na webu Azure Portal](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, vytvoření kontejneru pomocí Průzkumník dat a spuštění aplikace. Teď můžete do účtu databáze Cosmos importovat další data. 

> [!div class="nextstepaction"]
> [Import dat do databáze Azure Cosmos DB pro rozhraní SQL API](import-data.md)


