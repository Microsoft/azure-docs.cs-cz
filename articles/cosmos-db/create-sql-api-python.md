---
title: 'Úvodní příručka: Vytvoření aplikace Pythonu pomocí účtu Azure Cosmos DB SQL API'
description: Tento článek představuje ukázku kódu Python, který můžete použít k připojení a dotazování služby Azure Cosmos DB přes rozhraní SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 10247e22b3fbe1250a15b06a0cce974905ca6b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942616"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Úvodní příručka: Vytvoření aplikace Pythonu pomocí účtu SQL API Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete účet Azure Cosmos DB SQL API z webu Azure Portal a z kódu Visual Studia s aplikací Pythonu naklonotou z GitHubu. Azure Cosmos DB je vícemodelová databázová služba, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, hodnot klíče a grafů s možností globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Můžete také použít [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) `https://localhost:8081` s identifikátorem URI a klíč `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Python 3.6+](https://www.python.org/downloads/), `python` s spustitelným souborem ve vašem `PATH`.
- [Visual Studio kód](https://code.visualstudio.com/).
- [Rozšíření Pythonu pro kód sady Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidání kontejneru

Teď můžete použít nástroj Průzkumník dat na webu Azure Portal k vytvoření databáze a kontejneru. 

1. Vyberte nový kontejner **průzkumníka** > **dat**. 
    
    Oblast **Přidat kontejner** se zobrazí zcela vpravo, možná budete muset posunout doprava, abyste ji viděli.

    ![Průzkumník dat na portálu Azure Portal – podokno Přidat kontejner](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. Na stránce **Přidat kontejner** zadejte nastavení nového kontejneru.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *Tasks*. Názvy databází musí obsahovat 1 až 255 znaků a nemohou obsahovat `/, \\, #, ?`nebo koncové mezery. Zkontrolujte možnost **Propustnost zřizování databáze,** umožňuje sdílet propustnost zřízenou do databáze ve všech kontejnerech v databázi. Tato možnost také pomáhá s úsporami nákladů. |
    |**Propustnost**|400|Ponechte propustnost na 400 jednotek požadavku za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Items|Jako název nového kontejneru zadejte *položky.* Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá */category* jako klíč oddílu.|
    
    Kromě předchozínastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením zásady jedinečného klíče při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci SQL API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Tento rychlý start používá verzi 4 [sady Python SDK](https://pypi.org/project/azure-cosmos/#history).

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

    ```cmd
    md "git-samples"
    ```
   Pokud používáte bash prompt, měli byste místo toho použít následující příkaz:

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

1. Ve svém účtu Azure Cosmos DB na [webu Azure Portal](https://portal.azure.com/)vyberte **klíče** z levé navigace. Pomocí tlačítek kopírování na pravé straně obrazovky zkopírujte **identifikátor URI** a **primární klíč** do souboru *cosmos_get_started.py* v dalším kroku.

    ![Získání přístupového klíče a identifikátoru URI v nastavení klíčů na webu Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. V kódu Visual Studia otevřete soubor *cosmos_get_started.py* v *\git-samples\azure-cosmos-db-python-getting-started*.

3. Zkopírujte hodnotu **URI** z portálu (pomocí tlačítka kopírování) a znějte hodnotu proměnné **koncového bodu** v *souboru cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Potom zkopírujte hodnotu **primárního klíče** z portálu a udělejte z něj hodnotu **klíče** v *souboru cosmos_get_started.py*. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

    `key = 'FILLME'`

5. Uložte soubor *cosmos_get_started.py.*

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Získejte informace o databázových prostředcích vytvořených v kódu nebo přejděte na [aktualizaci připojovacího řetězce](#update-your-connection-string).

Všechny následující úryvky jsou převzaty ze souboru *cosmos_get_started.py.*

* Inicializuje se CosmosClient. Nezapomeňte aktualizovat hodnoty "koncový bod" a "klíč", jak je popsáno v části [Aktualizovat připojovací řetězec.](#update-your-connection-string) 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Vytvoří se nová databáze.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Vytvoří se nový kontejner s 400 RU/s [zřízené propustnosti](request-units.md). Volíme `lastName` jako [klíč oddílu](partitioning-overview.md#choose-partitionkey), který nám umožňuje dělat efektivní dotazy, které filtrují na této vlastnosti. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Do kontejneru se přidá několik položek. Kontejnery jsou kolekce položek (dokumenty JSON), které mohou mít různé schéma. Pomocné metody ```get_[name]_family_item``` vrátí reprezentace rodiny, které jsou uložené v Azure Cosmos DB jako dokumenty JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Čtení bodů (vyhledávání hodnot klíče) se `read_item` provádí pomocí metody. Vytiskneme [žp.](request-units.md)

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Dotaz se provádí pomocí syntaxe dotazu SQL. Vzhledem k tomu, že ```lastName``` používáme hodnoty klíče oddílu v klauzuli WHERE, Azure Cosmos DB efektivně směruje tento dotaz do příslušných oddílů a zlepší výkon.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Spuštění aplikace

1. V kódu sady Visual Studio vyberte **Zobrazit** > **paletu příkazů**. 

2. Do příkazového řádku zadejte **Python: Select Interpreter** a pak vyberte verzi jazyka Python, kterou chcete používat.

    Zápatí v aplikaci Visual Studio Code se aktualizuje a bude označovat, že je vybraný interpret. 

3. Výběrem **možnosti Zobrazit** > **integrovaný terminál** otevřete integrovaný terminál Visual Studio Code.

4. V okně integrovaného terminálu se ujistěte, že jste ve složce *azure-cosmos-db-python-getting-started.* Pokud ne, spusťte následující příkaz, abyste do složky ukázky přešli. 

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

7. Pokud chcete potvrdit, že nové položky byly vytvořeny a uloženy, vyberte na webu Azure Portal **položky Data Explorer** > **UAzureSampleFamilyDatabaseDatabase** > **.** Zobrazení položek, které byly vytvořeny. Zde je například ukázkový dokument JSON pro rodinu Andersen:
   
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

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos DB, vytvořit kontejner pomocí Průzkumníka dat a spustit aplikaci Pythonu v kódu Visual Studia. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat do databáze Azure Cosmos DB pro rozhraní SQL API](import-data.md)


