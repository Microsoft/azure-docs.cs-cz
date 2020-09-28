---
title: 'Rychlý Start: Gremlin API pomocí Pythonu – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Gremlin API k vytvoření aplikace pomocí portálu Azure a jazyka Python.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: jasonh
ms.custom: devx-track-python
ms.openlocfilehash: 624aa946df5e637c98a11ada3b455c50dec896f5
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409336"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Rychlý Start: vytvoření databáze grafů v Azure Cosmos DB pomocí Pythonu a Azure Portal

> [!div class="op_single_selector"]
> * [Konzola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

V tomto rychlém startu vytvoříte a spravujete účet rozhraní API pro Azure Cosmos DB Gremlin (Graph) z Azure Portal a přidáte data pomocí aplikace Python naklonované z GitHubu. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadavky
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure.
- [Python 3.5 +](https://www.python.org/downloads/) včetně instalačního balíčku balíčku [PIP](https://pip.pypa.io/en/stable/installing/)
- [Ovladač Pythonu pro Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python)
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Tento rychlý start vyžaduje účet databáze grafů vytvořený po 20. prosinci 2017. Existující účty budou podporovat Python, jakmile se migrují do všeobecné dostupnosti.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi grafu, je potřeba pomocí služby Azure Cosmos DB vytvořit účet databáze Gremlin (Graph).

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Přidání grafu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Pojďme naklonovat aplikaci rozhraní Gremlin API z GitHubu, nastavit připojovací řetězec a spustit ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu.  

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do složky, do které chcete nainstalovat ukázkovou aplikaci.  

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Všechny fragmenty kódu jsou pořízeny ze souboru *Connect.py* ve *složce \\ C:\git-samples\azure-Cosmos-DB-Graph-Python-Getting-Started* . Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-information). 

* Gremlin `client` je inicializován v řádku 104 v *Connect.py*:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* Řada kroků Gremlin je deklarována na začátku souboru *Connect.py* . K jejich provedení se použije metoda `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Aktualizace informací o připojení

Teď se vraťte na portál Azure, kde najdete informace o připojení, a zkopírujte je do aplikace. Tato nastavení umožní aplikaci komunikovat s hostovanou databází.

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)vyberte **klíče**. 

    Zkopírujte první část hodnoty identifikátoru URI.

    :::image type="content" source="./media/create-graph-python/keys.png" alt-text="Zobrazení a zkopírování přístupového klíče na webu Azure Portal na stránce Klíče":::

2. Otevřete soubor *Connect.py* a na řádku 104 vložte hodnotu identifikátoru URI `<YOUR_ENDPOINT>` do tohoto umístění:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    Část URI objektu klienta by měla vypadat nějak takto:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Změňte druhý parametr objektu `client` a nahraďte řetězce `<YOUR_DATABASE>` a `<YOUR_COLLECTION_OR_GRAPH>`. Pokud jste použili navrhované hodnoty, parametr by měl vypadat přibližně takto:

    `username="/dbs/sample-database/colls/sample-graph"`

    Celý objekt `client` by měl vypadat nějak takhle:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. Na stránce **klíče** použijte tlačítko Kopírovat a ZKOPÍRUJTE primární klíč a vložte ho `<YOUR_PASSWORD>` do `password=<YOUR_PASSWORD>` parametru.

    Celá definice objektu `client` by měla vypadat nějak takhle:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Uložte soubor *Connect.py* .

## <a name="run-the-console-app"></a>Spuštění aplikace konzoly

1. V okně terminálu Git přejděte příkazem `cd` do složky azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. V okně terminálu Git pomocí následujícího příkazu nainstalujte požadované balíčky Pythonu.

   ```
   pip install -r requirements.txt
   ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci v Pythonu.
    
    ```
    python connect.py
    ```

    V okně terminálu se zobrazí vrcholy a hrany, které se přidávají do grafu. 
    
    Pokud dochází k chybám časového limitu, zkontrolujte, že jste v části [Aktualizace informací o připojení](#update-your-connection-information) správně aktualizovali informace o připojení, a zkuste poslední příkaz spustit znovu. 
    
    Po zastavení programu stiskněte Enter a pak v internetovém prohlížeči přejděte zpátky na portál Azure.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Kontrola a přidání ukázkových dat

Po vložení vrcholů a hran se teď můžete vrátit na Průzkumník dat a zobrazit vrcholy přidané do grafu a přidat další datové body.

1. V Azure Cosmos DB účtu v Azure Portal vyberte možnost **Průzkumník dat**, rozbalte položku **Ukázka-graf**, vyberte možnost **graf**a pak vyberte možnost **použít filtr**. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png" alt-text="Vytváření nových dokumentů v Průzkumníku dat na webu Azure Portal":::

2. V seznamu **výsledků** si všimněte, že se do grafu přidávají tři noví uživatelé. Vrcholy můžete přesouvat přetahováním, přibližovat a oddalovat můžete pomocí kolečka myši a zvětšit plochu grafu můžete pomocí obousměrné šipky. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png" alt-text="Nové vrcholy v grafu v Průzkumníku dat na webu Azure Portal":::

3. Teď přidáme několik nových uživatelů. Vyberte tlačítko **nový vrchol** a přidejte do grafu data.

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Vytváření nových dokumentů v Průzkumníku dat na webu Azure Portal":::

4. Zadejte popisek *person* (osoba).

5. Vyberte **Přidat vlastnost** a přidejte každou z následujících vlastností. Všimněte si, že pro každou osobu v grafu můžete vytvořit jedinečné vlastnosti. Vyžaduje se pouze klíč id.

    key|hodnota|Poznámky
    ----|----|----
    PK|/pk| 
    id|ashley|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|female (žena)| 
    tech (technologie) | java | 

    > [!NOTE]
    > V tomto rychlém startu se vytváří kolekce bez oddílů. Pokud však vytvoříte dělenou kolekci zadáním klíče oddílu při vytváření kolekce, pak každý nový vrchol bude muset zahrnovat klíč oddílu jako klíč. 

6. Vyberte **OK**. Možná bude nutné zvětšit obrazovku, aby se tlačítko **OK** zobrazilo v dolní části obrazovky.

7. Znovu vyberte **nový vrchol** a přidejte dalšího nového uživatele. 

8. Zadejte popisek *person* (osoba).

9. Vyberte **Přidat vlastnost** a přidejte každou z následujících vlastností:

    key|hodnota|Poznámky
    ----|----|----
    PK|/pk| 
    id|rakesh|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|male (muž)| 
    school (škola)|MIT| 

10. Vyberte **OK**. 

11. Vyberte tlačítko **použít filtr** s výchozím `g.V()` filtrem pro zobrazení všech hodnot v grafu. Teď se v seznamu **Výsledky** zobrazí všichni uživatelé. 

    S přidáváním dalších dat můžete pomocí filtrů omezit výsledky. Průzkumník dat ve výchozím nastavení pomocí filtru `g.V()` načte všechny vrcholy v grafu. Můžete ho změnit na jiný [dotaz grafu](tutorial-query-graph.md), třeba `g.V().count()`, který vrátí počet všech vrcholů v grafu ve formátu JSON. Pokud jste filtr změnili, změňte filtr zpět na `g.V()` a vyberte **použít filtr** pro zobrazení všech výsledků znovu.

12. Teď můžeme propojit uživatele rakesh a ashley. V seznamu **výsledků** ověřte, že je vybraná možnost **Ashley** , a pak vyberte tlačítko Upravit vedle **cílů** na pravé dolní straně. Možná budete muset rozšířit okno, aby se zobrazila oblast **Vlastnosti**.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Změna cíle vrcholu v grafu":::

13. Do pole **cíl** zadejte *Rakesh*a do pole **popisek hrany** zadejte text *zná*a zaškrtněte políčko.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png" alt-text="Přidání propojení mezi uživateli ashley a rakesh v Průzkumníku dat":::

14. Teď vyberte ze seznamu výsledků uživatele **rakesh** a zobrazí se propojení mezi uživateli ashley a rakesh. 

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer.png" alt-text="Dva propojené vrcholy v Průzkumníku dat":::

Tím je dokončená část tohoto kurzu věnovaná vytváření prostředků. Můžete pokračovat přidáním vrcholů do grafu, úpravou existujících vrcholů nebo změnou dotazů. Teď si projdeme metriky, které služba Azure Cosmos DB nabízí, a potom vyčistíme prostředky. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure Cosmos DB, vytvořit graf pomocí Průzkumník dat a spustit aplikaci v Pythonu pro přidání dat do grafu. Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)

