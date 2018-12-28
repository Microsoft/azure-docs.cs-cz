---
title: Vytvoření webové aplikace Flask pomocí rozhraní API služby Azure Cosmos DB pro MongoDB a sadě Python SDK
description: Představuje ukázku kódu Python Flask, kterou můžete použít k připojení a dotazování pomocí rozhraní API služby Azure Cosmos DB pro MongoDB.
services: cosmos-db
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 78fa871deb36b9f3596632976ce5a17b2f4d71fa
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792245"
---
# <a name="build-a-flask-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Sestavení aplikace Flask pomocí rozhraní API služby Azure Cosmos DB pro MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat dokument, klíč/hodnota a databáze grafů, které můžou využívat výhody globální distribuce a možností horizontálního škálování v jádru služby Cosmos DB.

Tato úvodní příručka používá následující [příklad Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) a ukazuje, jak vytvořit jednoduchou aplikaci seznamu úkolů Flask pomocí [emulátor služby Azure Cosmos DB](local-emulator.md) a rozhraní Azure Cosmos DB API pro MongoDB.

## <a name="prerequisites"></a>Požadavky

- Stáhněte si [emulátor služby Azure Cosmos DB](local-emulator.md). Emulátor je momentálně podporovaný jenom ve Windows. Tato ukázka vysvětluje, jak ukázku použít s produkčním klíčem z Azure, což se dá udělat na libovolné platformě.

- Pokud ještě nemáte nainstalovaný editor Visual Studio Code, můžete rychle nainstalovat editor [VS Code](https://code.visualstudio.com/Download) pro vaši platformu (Windows, Mac, Linux).

- Nezapomeňte přidat podporu jazyka Python instalací některého z oblíbených rozšíření Pythonu.
    1. Vyberte rozšíření.
    2. Nainstalujte rozšíření zadáním příkazu `ext install` do palety příkazů `Ctrl+Shift+P`.

    Příklady v tomto dokumentu využívají oblíbené [rozšíření Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) s plnou sadou funkcí, jehož autorem je Don Jayamanne.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci Flask-MongoDB z Githubu, nastavíme připojovací řetězec a spustíme ji. Uvidíte, jak snadno se pracuje s daty prostřednictvím kódu programu.

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

Pokud chcete otestovat na aktivní účet Cosmos, přejděte na web Azure Portal k vytvoření účtu služby a získat informace o připojovacím řetězci. Potom ho zkopírujte do aplikace.

1. V [webu Azure portal](https://portal.azure.com/), ve vašem účtu Cosmos v levém navigačním panelu klikněte na tlačítko **připojovací řetězec**a potom klikněte na tlačítko **klíče pro čtení i zápis**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky uživatelské jméno, heslo a hostitele do souboru Dal.cs.

2. Otevřete soubor **app.py** v kořenovém adresáři.

3. Z portálu zkopírujte hodnotu **username** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu položky **name** v souboru **app.py**.

4. Potom zkopírujte hodnotu **connection string** z portálu a nastavte ji jako hodnotu položky MongoClient v souboru **app.py**.

5. Nakonec z portálu zkopírujte hodnotu **password** a nastavte ji jako hodnotu **password** v souboru **app.py**.

Aplikaci jste teď aktualizovaný o všechny potřebné informace týkající se potřebuje ke komunikaci s Cosmos DB. Můžete ji spustit stejným způsobem jako předtím.

## <a name="deploy-to-azure"></a>Nasazení do Azure

Pokud chcete nasadit tuto aplikaci, můžete vytvořit novou webovou aplikaci v Azure a umožnit průběžné nasazování ve forku tohoto úložiště GitHub. Použít tento [kurzu](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) nastavit průběžné nasazování pomocí Githubu v Azure.

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

V tomto rychlém startu jste zjistili, jak vytvořit účet Cosmos a spuštění aplikace Flask. Teď můžete importovat další data k databázi Cosmos. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md)
