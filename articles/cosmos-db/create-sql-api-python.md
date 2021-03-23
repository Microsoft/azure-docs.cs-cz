---
title: 'Rychlý Start: sestavení aplikace v Pythonu pomocí Azure Cosmos DB účtu rozhraní SQL API'
description: Tento článek představuje ukázku kódu Python, který můžete použít k připojení a dotazování služby Azure Cosmos DB přes rozhraní SQL API.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: fee0591622c1ee07b6e954b3cadc208a300ab6a5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798761"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Rychlý Start: sestavení aplikace v Pythonu pomocí Azure Cosmos DB účtu rozhraní SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Sada Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete Azure Cosmos DB účet rozhraní SQL API z Azure Portal a z Visual Studio Code v aplikaci Python naklonované z GitHubu. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

- Účet Cosmos DB. Máte tyto možnosti:
    * V rámci předplatného Azure Active:
        * [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free) nebo použijte stávající předplatné. 
        * [Měsíční kredity sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB úroveň Free](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Bez aktivního předplatného Azure:
        * [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/), což je testovací prostředí, které trvá po dobu 30 dnů.
        * [Emulátor služby Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 nebo 3.6 +](https://www.python.org/downloads/), se `python` spustitelným souborem v `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- [Přípona Pythonu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidání kontejneru

Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření databáze a kontejneru. 

1. Vyberte **Průzkumník dat**  >  **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Průzkumník dat na webu Azure Portal s podoknem Přidat kontejner":::

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *Tasks*. Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat ani mezeru `/, \\, #, ?` . Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Položky|Jako název nového kontejneru zadejte *položky* . Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá jako klíč oddílu */Category* .|
    
    Kromě předchozích nastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením jedinečné zásady klíčů při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci SQL API z GitHubu, nastavíme připojovací řetězec a spustíme ji. V tomto rychlém startu se používá verze 4 [sady Python SDK](https://pypi.org/project/azure-cosmos/#history).

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

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)v levém navigačním panelu vyberte **klíče** . Pomocí tlačítek pro kopírování na pravé straně obrazovky zkopírujte **identifikátor URI** a **primární klíč** do souboru *cosmos_get_started. py* v dalším kroku.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Získání přístupového klíče a identifikátoru URI v nastavení klíčů v Azure Portal":::

2. V Visual Studio Code otevřete soubor *cosmos_get_started. py* v *\git-samples\azure-Cosmos-DB-Python-Getting-Started*.

3. Z portálu Zkopírujte hodnotu **identifikátoru URI** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu proměnné **koncového bodu** v *cosmos_get_started. py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Pak z portálu Zkopírujte hodnotu **primárního klíče** a nastavte ji jako hodnotu **klíče** v *cosmos_get_started. py*. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

    `key = 'FILLME'`

5. Uložte soubor *cosmos_get_started. py* .

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Přečtěte si informace o databázových prostředcích vytvořených v kódu nebo přeskočit k části [aktualizace připojovacího řetězce](#update-your-connection-string).

Všechny následující fragmenty kódu jsou pořízeny ze souboru *cosmos_get_started. py* .

* Inicializuje se CosmosClient. Nezapomeňte aktualizovat hodnoty "Endpoint" a "Key", jak je popsáno v části [aktualizace připojovacího řetězce](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Vytvoří se nová databáze.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Vytvoří se nový kontejner s 400 RU/s [zřízené propustností](request-units.md). Zvolíme `lastName` jako [klíč oddílu](partitioning-overview.md#choose-partitionkey), což nám umožní provádět efektivní dotazy, které tuto vlastnost filtrují. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Do kontejneru se přidá několik položek. Kontejnery jsou kolekce položek (dokumenty JSON), které mohou mít různé schéma. Pomocné metody ```get_[name]_family_item``` vracejí reprezentace řady, která je uložena v Azure Cosmos DB jako dokumenty JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Čtení bodů (klíčová vyhledávání hodnot) se provádí pomocí `read_item` metody. Vytiskněte [poplatek](request-units.md) za každou operaci.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Dotaz se provádí pomocí syntaxe dotazu SQL. Vzhledem k tomu, že v klauzuli WHERE používáme klíčové hodnoty oddílu ```lastName``` , Azure Cosmos DB bude efektivně směrovat tento dotaz na relevantní oddíly a zlepší výkon.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Spuštění aplikace

1. V Visual Studio Code vyberte **Zobrazit**  >  **paleta příkazů**. 

2. Do příkazového řádku zadejte **Python: Select Interpreter** a pak vyberte verzi jazyka Python, kterou chcete používat.

    Zápatí v aplikaci Visual Studio Code se aktualizuje a bude označovat, že je vybraný interpret. 

3. Výběrem **Zobrazit**  >  **integrovaný terminál** otevřete Visual Studio Code integrovaný terminál.

4. V okně integrovaného terminálu se ujistěte, že jste ve složce *Azure-Cosmos-DB-Python-Začínáme-Started* . Pokud ne, spusťte následující příkaz, abyste do složky ukázky přešli. 

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

7. Pokud chcete potvrdit, že se nové položky vytvořily a uložily, vyberte v Azure Portal **Průzkumník dat**  >    >  **položky** AzureSampleFamilyDatabase. Zobrazit položky, které byly vytvořeny. Například tady je ukázkový dokument JSON pro Andersen rodinu:
   
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

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos DB, vytvoření kontejneru pomocí Průzkumník dat a spuštění aplikace v Pythonu v Visual Studio Code. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat do databáze Azure Cosmos DB pro rozhraní SQL API](import-data.md)
