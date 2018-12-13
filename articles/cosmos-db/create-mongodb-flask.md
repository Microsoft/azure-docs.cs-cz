---
title: 'Azure Cosmos DB: Vytvoření webové aplikace Flask pomocí Pythonu a MongoDB API služby Azure Cosmos DB'
description: Představuje ukázku kódu Python Flask, kterou můžete použít k připojení a dotazování do rozhraní API MongoDB služby Azure Cosmos DB.
services: cosmos-db
author: slyons
ms.author: sclyon
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quick start connect, mvc, seodec18
ms.devlang: python
ms.topic: quickstart
ms.date: 10/02/2017
ms.openlocfilehash: 09c19f57a2993c0faad3dca708f67526f99d13df
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165108"
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB: Sestavení aplikace Flask pomocí rozhraní API MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos.

Tato úvodní příručka používá následující [příklad Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) a ukazuje, jak vytvořit jednoduchou aplikaci seznamu úkolů Flask pomocí [emulátoru služby Azure Cosmos DB](local-emulator.md) a rozhraní [MongoDB API](mongodb-introduction.md) služby Azure Cosmos DB místo MongoDB.

## <a name="prerequisites"></a>Požadavky

- Stáhněte si [emulátor služby Azure Cosmos DB](local-emulator.md). Emulátor je momentálně podporovaný jenom ve Windows. Tato ukázka vysvětluje, jak ukázku použít s produkčním klíčem z Azure, což se dá udělat na libovolné platformě.

- Pokud ještě nemáte nainstalovaný editor Visual Studio Code, můžete rychle nainstalovat editor [VS Code](https://code.visualstudio.com/Download) pro vaši platformu (Windows, Mac, Linux).

- Nezapomeňte přidat podporu jazyka Python instalací některého z oblíbených rozšíření Pythonu.
    1. Vyberte rozšíření.
    2. Nainstalujte rozšíření zadáním příkazu `ext install` do palety příkazů `Ctrl+Shift+P`.

    Příklady v tomto dokumentu využívají oblíbené [rozšíření Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) s plnou sadou funkcí, jehož autorem je Don Jayamanne.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci rozhraní API Flask-MongoDB z Githubu, nastavíme připojovací řetězec a spustíme ji. Uvidíte, jak snadno se pracuje s daty prostřednictvím kódu programu.

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
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Spuštěním následujícího příkazu nainstalujte moduly Pythonu.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Otevřete složku otevřete v editoru Visual Studio Code.

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Spuštění webové aplikace](#run-the-web-app). 

Všechny následující fragmenty kódu pocházejí ze souboru app.py a používají připojovací řetězec pro místní emulátor služby Azure Cosmos DB. Heslo je potřeba rozdělit, jak vidíte níže, aby se dala použít lomítka, která se jinak nedají analyzovat.

* Inicializujte klienta MongoDB, načtěte databázi a proveďte ověření.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Načtěte kolekci nebo ji vytvořte, pokud ještě neexistuje.

    ```python
    todos = db.todo #Select the collection
    ```

* Vytvoření aplikace

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Spuštění webové aplikace

1. Zkontrolujte, jestli je spuštěný emulátor služby Azure Cosmos DB.

2. Otevřete okno a příkazem `cd` přejděte do adresáře, ve kterém je aplikace uložená.

3. Potom nastavte proměnnou prostředí pro aplikaci Flask na `set FLASK_APP=app.py`, případně `export FLASK_APP=app.py`, pokud používáte systém Mac.

4. Spusťte aplikaci pomocí příkazu `flask run` a přejděte na adresu [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Přidávejte a odebírejte úlohy a sledujte, jak se objevují a mění v rámci kolekce.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Pokud chcete otestovat na aktivním účtu služby Azure Cosmos DB, přejděte na portál Azure, vytvořte účet a získejte připojovací řetězec. Potom ho zkopírujte do aplikace.

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte v účtu databáze Azure Cosmos v levém navigačním panelu na možnost **Připojovací řetězec** a potom klikněte na **Klíče pro čtení i zápis**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky uživatelské jméno, heslo a hostitele do souboru Dal.cs.

2. Otevřete soubor **app.py** v kořenovém adresáři.

3. Z portálu zkopírujte hodnotu **username** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu položky **name** v souboru **app.py**.

4. Potom zkopírujte hodnotu **connection string** z portálu a nastavte ji jako hodnotu položky MongoClient v souboru **app.py**.

5. Nakonec z portálu zkopírujte hodnotu **password** a nastavte ji jako hodnotu **password** v souboru **app.py**.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. Můžete ji spustit stejným způsobem jako předtím.

## <a name="deploy-to-azure"></a>Nasazení do Azure

Pokud chcete nasadit tuto aplikaci, můžete vytvořit novou webovou aplikaci v Azure a umožnit průběžné nasazování ve forku tohoto úložiště GitHub. Použít tento [kurzu](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment) nastavit průběžné nasazování pomocí Githubu v Azure.

Při nasazování do Azure je potřeba odebrat klíče aplikace a ujistit se, následující část není označená jako komentář:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Potom musíte přidat do nastavení aplikace vaše položky MONGOURL, MONGO_PASSWORD a MONGO_USERNAME. V tomto [kurzu](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) se dozvíte další informace o nastavení aplikace ve službě Azure Web Apps.

Pokud nechcete vytvořit fork tohoto úložiště, můžete také kliknout na níže uvedené tlačítko Nasazení do Azure. Pak byste měli přejít do Azure a upravit nastavení aplikace pomocí údajů o vašem účtu služby Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Pokud máte v plánu uložit svůj kód na Githubu nebo jiných možností správy zdrojového kódu, nezapomeňte z kódu odebrat připojovací řetězce. Ty je možné místo toho nastavit pomocí nastavení dané webové aplikace.

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu služby Azure Cosmos DB a spuštění aplikace Flask pomocí rozhraní API pro MongoDB. Teď můžete do svého účtu služby Cosmos DB importovat další data.

> [!div class="nextstepaction"]
> [Import dat do databáze Azure Cosmos pro rozhraní API MongoDB](mongodb-migrate.md)
