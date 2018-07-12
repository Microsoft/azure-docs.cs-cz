---
title: 'Rychlý start: Graph API s využitím PHP – Azure Cosmos DB | Microsoft Docs'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Graph API k vytvoření aplikace pomocí portálu Azure a jazyka PHP.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.custom: quick start connect, mvc
ms.devlang: php
ms.topic: quickstart
ms.date: 01/05/2018
ms.author: lbosq
ms.openlocfilehash: 2dabaf5650619ed2ec8d73c88deec136c25e544e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38310267"
---
# <a name="azure-cosmos-db-create-a-graph-database-using-php-and-the-azure-portal"></a>Azure Cosmos DB: Vytvoření databáze grafu pomocí PHP a portálu Azure

Tento rychlý start ukazuje, jak pomocí jazyka PHP a rozhraní [Graph API](graph-introduction.md) služby Azure Cosmos DB sestavit aplikaci konzoly naklonováním příkladu z GitHubu. Tento rychlý start vás také provede vytvořením účtu Azure Cosmos DB pomocí portálu Azure.   

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat databáze dokumentů, párů klíč-hodnota a grafů, které tak můžou využívat výhody možnosti globální distribuce a horizontálního škálování v jádru služby Azure Cosmos DB.  

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternativně můžete [vyzkoušet službu Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků.

Navíc platí:
* [PHP](http://php.net/) 5.6 nebo novější
* [Composer](https://getcomposer.org/download/)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi grafu, je potřeba pomocí služby Azure Cosmos DB vytvořit účet databáze Gremlin (Graph).

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Přidání grafu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Naklonujeme aplikaci Graph API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu.  

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, třeba Git Bash, a pomocí příkazu `cd` přejděte do složky, do které chcete nainstalovat ukázkovou aplikaci.  

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-php-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Všechny fragmenty kódu pocházejí ze souboru connect.php ve složce C:\git-samples\azure-cosmos-db-graph-php-getting-started\. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-information). 

* Objekt `connection` konzoly Gremlin se inicializuje na začátku souboru `connect.php` pomocí objektu `$db`.

    ```php
    $db = new Connection([
        'host' => '<your_server_address>.graphs.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

* Pomocí metody `$db->send($query);` se provede série kroků konzoly Gremlin.

    ```php
    $query = "g.V().drop()";
    ...
    $result = $db->send($query);
    $errors = array_filter($result);
    }
    ```

## <a name="update-your-connection-information"></a>Aktualizace informací o připojení

Teď se vraťte na portál Azure, kde najdete informace o připojení, a zkopírujte je do aplikace. Tato nastavení umožní aplikaci komunikovat s hostovanou databází.

1. Na [portálu Azure](http://portal.azure.com/) klikněte na **Klíče**. 

    Zkopírujte první část hodnoty identifikátoru URI.

    ![Zobrazení a zkopírování přístupového klíče na portálu Azure na stránce Klíče](./media/create-graph-php/keys.png)
2. Otevřete soubor `connect.php` a na řádek 8 místo `your_server_address` vložte hodnotu identifikátoru URI.

    Inicializace objektu Connection by teď měla vypadat podobně jako v následujícím kódu:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

3. Pokud se účet databáze grafů vytvořil 20. prosince 2017 nebo později, v názvu hostitele změňte `graphs.azure.com` na `gremlin.cosmosdb.azure.com`.

4. Změňte parametr `username` v objektu Connection na název vaší databáze a grafu. Pokud jste použili doporučené hodnoty `sample-database` a `sample-graph`, měl by vypadat jako v následujícím kódu:

    `'username' => '/dbs/sample-database/colls/sample-graph'`

    Celý objekt Connection by teď měl vypadat jako v následujícím kódu:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => 'your_primary_key',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

5. Na portálu Azure pomocí tlačítka pro kopírování zkopírujte PRIMÁRNÍ KLÍČ a vložte ho místo `your_primary_key` v parametru password.

    Inicializace objektu Connection by teď měla vypadat jako v následujícím kódu:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => '2Ggkr662ifxz2Mg==',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

6. Uložte soubor `connect.php`.

## <a name="run-the-console-app"></a>Spuštění aplikace konzoly

1. V okně terminálu Git přejděte příkazem `cd` do složky azure-cosmos-db-graph-php-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-php-getting-started"
    ```

2. V okně terminálu Git pomocí následujícího příkazu nainstalujte požadované závislosti jazyka PHP.

   ```
   composer install
   ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci v jazyce PHP.
    
    ```
    php connect.php
    ```

    V okně terminálu se zobrazí vrcholy, které se přidávají do grafu. 
    
    Pokud dochází k chybám časového limitu, zkontrolujte, jestli jste v části [Aktualizace informací o připojení](#update-your-connection-information) správně aktualizovali informace o připojení, a zkuste poslední příkaz spustit znovu. 
    
    Po zastavení programu stiskněte Enter a pak v internetovém prohlížeči přejděte zpátky na portál Azure. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Kontrola a přidání ukázkových dat

Teď můžete přejít zpět do Průzkumníku dat a zobrazit vrcholy přidané do grafu a přidat další datové body.

1. Klikněte na **Průzkumník dat**, rozbalte **sample-graph**, klikněte na **Graf** a potom klikněte na **Použít filtr**. 

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-graph-php/azure-cosmosdb-data-explorer-expanded.png)

2. V seznamu **Výsledky** si všimněte nových uživatelů přidaných do grafu. Vyberte uživatele **ben** a všimněte si, že je propojený s uživatelem robin. Vrcholy můžete přesouvat přetahováním, přibližovat a oddalovat můžete pomocí kolečka myši a zvětšit plochu grafu můžete pomocí obousměrné šipky. 

   ![Nové vrcholy v grafu v Průzkumníku dat na webu Azure Portal](./media/create-graph-php/azure-cosmosdb-graph-explorer-new.png)

3. Teď přidáme několik nových uživatelů. Klikněte na tlačítko **Nový vrchol** a přidejte do grafu data.

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-graph-php/azure-cosmosdb-data-explorer-new-vertex.png)

4. Zadejte popisek *person* (osoba).

5. Kliknutím na **Přidat vlastnost** přidejte následující vlastnosti. Všimněte si, že pro každou osobu v grafu můžete vytvořit jedinečné vlastnosti. Vyžaduje se pouze klíč id.

    key|hodnota|Poznámky
    ----|----|----
    id|ashley|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|female (žena)| 
    tech (technologie) | java | 

    > [!NOTE]
    > V tomto rychlém startu vytvoříte kolekci bez oddílů. Pokud však vytvoříte dělenou kolekci zadáním klíče oddílu při vytváření kolekce, pak každý nový vrchol bude muset zahrnovat klíč oddílu jako klíč. 

6. Klikněte na **OK**. Možná bude nutné zvětšit obrazovku, aby se tlačítko **OK** zobrazilo v dolní části obrazovky.

7. Znovu klikněte na **Nový vrchol** a přidejte dalšího nového uživatele. 

8. Zadejte popisek *person* (osoba).

9. Kliknutím na **Přidat vlastnost** přidejte následující vlastnosti:

    key|hodnota|Poznámky
    ----|----|----
    id|rakesh|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|male (muž)| 
    school (škola)|MIT| 

10. Klikněte na **OK**. 

11. Klikněte na tlačítko **Použít filtr** s výchozím filtrem `g.V()` a zobrazte v grafu všechny hodnoty. Teď se v seznamu **Výsledky** zobrazí všichni uživatelé. 

    S přidáváním dalších dat můžete pomocí filtrů omezit výsledky. Průzkumník dat ve výchozím nastavení pomocí filtru `g.V()` načte všechny vrcholy v grafu. Můžete ho změnit na jiný [dotaz grafu](tutorial-query-graph.md), třeba `g.V().count()`, který vrátí počet všech vrcholů v grafu ve formátu JSON. Pokud jste filtr změnili, změňte ho zpátky na `g.V()`, klikněte na **Použít filtr** a znovu zobrazte všechny výsledky.

12. Teď můžete propojit uživatele rakesh a ashley. Ujistěte se, že v seznamu **Výsledky** je vybraný uživatel **ashley**, a potom klikněte na tlačítko Upravit vedle položky **Cíle** vpravo dole. Možná budete muset rozšířit okno, aby se zobrazila oblast **Vlastnosti**.

   ![Změna cíle vrcholu v grafu](./media/create-graph-php/azure-cosmosdb-data-explorer-edit-target.png)

13. Do pole **Cíl** zadejte *rakesh*, do pole **Popisek hrany** zadejte *knows* (zná) a potom klikněte na symbol zaškrtnutí.

   ![Přidání propojení mezi uživateli ashley a rakesh v Průzkumníku dat](./media/create-graph-php/azure-cosmosdb-data-explorer-set-target.png)

14. Teď vyberte ze seznamu výsledků uživatele **rakesh** a zobrazí se propojení mezi uživateli ashley a rakesh. 

   ![Dva propojené vrcholy v Průzkumníku dat](./media/create-graph-php/azure-cosmosdb-graph-explorer.png)

   Tím je dokončená část tohoto rychlého startu, která se věnuje vytváření prostředků. Můžete pokračovat přidáním vrcholů do grafu, úpravou existujících vrcholů nebo změnou dotazů. Teď si projdeme metriky, které služba Azure Cosmos DB nabízí, a pak vyčistíme prostředky. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu databáze Azure Cosmos, vytvoření grafu pomocí Průzkumníku dat a spuštění aplikace. Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)

