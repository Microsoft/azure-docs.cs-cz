---
title: 'Úvodní příručka: Gremlin API s Pythonem – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Gremlin API k vytvoření aplikace pomocí portálu Azure a jazyka Python.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: lbosq
ms.openlocfilehash: b1286daaa76c71f88d44ea387a92876a8676783c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062219"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Úvodní příručka: Vytvoření databáze grafů v Azure Cosmos DB pomocí Pythonu a portálu Azure

> [!div class="op_single_selector"]
> * [Konzola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

V tomto rychlém startu vytvoříte a spravujete účet rozhraní API Azure Cosmos DB Gremlin (graf) z webu Azure Portal a přidáte data pomocí aplikace Pythonu naklonované z GitHubu. Azure Cosmos DB je vícemodelová databázová služba, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, hodnot klíče a grafů s možností globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadavky
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure.
- [Python 3.5+](https://www.python.org/downloads/) včetně instalačního [programu pip](https://pip.pypa.io/en/stable/installing/) package.
- [Ovladač Pythonu pro Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
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

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Všechny úryvky jsou převzaty ze souboru *connect.py* ve složce *C:\git-samples\azure-cosmos-db-graph-python-getting-started.\\ * Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-information). 

* Gremlin `client` je inicializován v řádku 104 v *connect.py*:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* Řada kroků Gremlin jsou deklarovány na začátku souboru *connect.py.* K jejich provedení se použije metoda `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Aktualizace informací o připojení

Teď se vraťte na portál Azure, kde najdete informace o připojení, a zkopírujte je do aplikace. Tato nastavení umožní aplikaci komunikovat s hostovanou databází.

1. Ve svém účtu Azure Cosmos DB na [webu Azure Portal](https://portal.azure.com/)vyberte **Keys**. 

    Zkopírujte první část hodnoty identifikátoru URI.

    ![Zobrazení a zkopírování přístupového klíče na webu Azure Portal na stránce Klíče](./media/create-graph-python/keys.png)

2. Otevřete soubor *connect.py* a na řádek 104 vložte hodnotu `<YOUR_ENDPOINT>` URI sem:

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

4. Na stránce **Klíče** zkopírujte pomocí tlačítka kopírovat primární klíč `<YOUR_PASSWORD>` a `password=<YOUR_PASSWORD>` vložte jej do parametru.

    Celá definice objektu `client` by měla vypadat nějak takhle:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Uložte *soubor connect.py.*

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

Po vložení vrcholů a hran se nyní můžete vrátit do Průzkumníka dat a zobrazit vrcholy přidané do grafu a přidat další datové body.

1. Ve svém účtu Azure Cosmos DB na webu Azure Portal vyberte **Průzkumník dat**, rozbalte **ukázkový graf**, vyberte **Graf**a pak vyberte **Použít filtr**. 

   ![Vytváření nových dokumentů v Průzkumníku dat na webu Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. V seznamu **Výsledky** všimněte si, že do grafu jsou přidáni tři noví uživatelé. Vrcholy můžete přesouvat přetahováním, přibližovat a oddalovat můžete pomocí kolečka myši a zvětšit plochu grafu můžete pomocí obousměrné šipky. 

   ![Nové vrcholy v grafu v Průzkumníku dat na webu Azure Portal](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Teď přidáme několik nových uživatelů. Vyberte tlačítko **Nový vrchol,** chcete-li do grafu přidat data.

   ![Vytváření nových dokumentů v Průzkumníku dat na webu Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. Zadejte popisek *person* (osoba).

5. Vyberte **Přidat vlastnost,** chcete-li přidat každou z následujících vlastností. Všimněte si, že pro každou osobu v grafu můžete vytvořit jedinečné vlastnosti. Vyžaduje se pouze klíč id.

    key|value|Poznámky
    ----|----|----
    Pk|/pk| 
    id|ashley|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|female (žena)| 
    tech (technologie) | java | 

    > [!NOTE]
    > V tomto rychlém startu se vytváří kolekce bez oddílů. Pokud však vytvoříte dělenou kolekci zadáním klíče oddílu při vytváření kolekce, pak každý nový vrchol bude muset zahrnovat klíč oddílu jako klíč. 

6. Vyberte **OK**. Možná bude nutné zvětšit obrazovku, aby se tlačítko **OK** zobrazilo v dolní části obrazovky.

7. Znovu vyberte **Nový vrchol a** přidejte dalšího nového uživatele. 

8. Zadejte popisek *person* (osoba).

9. Vyberte **Přidat vlastnost,** chcete-li přidat každou z následujících vlastností:

    key|value|Poznámky
    ----|----|----
    Pk|/pk| 
    id|rakesh|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|male (muž)| 
    school (škola)|MIT| 

10. Vyberte **OK**. 

11. Chcete-li zobrazit všechny `g.V()` hodnoty v **grafu,** vyberte tlačítko Použít filtr s výchozím filtrem. Teď se v seznamu **Výsledky** zobrazí všichni uživatelé. 

    S přidáváním dalších dat můžete pomocí filtrů omezit výsledky. Průzkumník dat ve výchozím nastavení pomocí filtru `g.V()` načte všechny vrcholy v grafu. Můžete ho změnit na jiný [dotaz grafu](tutorial-query-graph.md), třeba `g.V().count()`, který vrátí počet všech vrcholů v grafu ve formátu JSON. Pokud jste filtr změnili, změňte filtr zpět na `g.V()` a vyberte Použít **filtr,** aby se znovu zobrazily všechny výsledky.

12. Teď můžeme propojit uživatele rakesh a ashley. Ujistěte se, **že ashley** je vybraná v seznamu **Výsledky,** a pak vyberte tlačítko upravit vedle **cíle** na pravé dolní straně. Možná budete muset rozšířit okno, aby se zobrazila oblast **Vlastnosti**.

    ![Změna cíle vrcholu v grafu](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. V poli **Cíl** *zadejte rakesh*a v poli **Popisek okraje** *typ ví*a pak vyberte zaškrtnutí.

    ![Přidání propojení mezi uživateli ashley a rakesh v Průzkumníku dat](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Teď vyberte ze seznamu výsledků uživatele **rakesh** a zobrazí se propojení mezi uživateli ashley a rakesh. 

    ![Dva propojené vrcholy v Průzkumníku dat](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

Tím je dokončená část tohoto kurzu věnovaná vytváření prostředků. Můžete pokračovat přidáním vrcholů do grafu, úpravou existujících vrcholů nebo změnou dotazů. Teď si projdeme metriky, které služba Azure Cosmos DB nabízí, a potom vyčistíme prostředky. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos DB, vytvořit graf pomocí Průzkumníka dat a spustit aplikaci Pythonu pro přidání dat do grafu. Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)

