---
title: Vytvoření webové aplikace Python Flask pomocí rozhraní API Azure Cosmos DB pro MongoDB
description: Představuje ukázku kódu Aplikace Python Flask, kterou můžete použít k připojení a dotazování pomocí rozhraní API Služby Azure Cosmos DB pro MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 43f4cf7e4008aa01a26c48a8e99f7465eeeb234b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77061739"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Úvodní příručka: Vytvoření aplikace Pythonu pomocí rozhraní API Azure Cosmos DB pro MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

V tomto rychlém startu použijete účet rozhraní API Azure Cosmos DB pro Mongo DB nebo emulátor Azure Cosmos DB ke spuštění webové aplikace Python Flask To-Do naklonované z GitHubu. Azure Cosmos DB je vícemodelová databázová služba, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, hodnot klíče a grafů s možností globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Nebo můžete použít [emulátor Azure Cosmos DB](local-emulator.md). 
- [Python 3.6+](https://www.python.org/downloads/)
- [Visual Studio Kód](https://code.visualstudio.com/Download) s [rozšířením Pythonu](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní naklonujme aplikaci Flask-MongoDB z GitHubu, nastavíme připojovací řetězec a spusťme ji. Uvidíte, jak snadno se pracuje s daty prostřednictvím kódu programu.

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

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

Následující úryvky jsou převzaty ze souboru *app.py* a používá připojovací řetězec pro místní emulátor Azure Cosmos DB. Heslo je potřeba rozdělit, jak vidíte níže, aby se dala použít lomítka, která se jinak nedají analyzovat.

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

3. Pak nastavte proměnnou prostředí pro `set FLASK_APP=app.py`aplikaci Flask s `$env:FLASK_APP = app.py` `export FLASK_APP=app.py` , pro editory PowerShellu nebo pokud používáte Mac. 

4. Spusťte `flask run` aplikaci s a přejděte na *http:\//127.0.0.1:5000/*.

5. Přidávejte a odebírejte úlohy a sledujte, jak se objevují a mění v rámci kolekce.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Pokud chcete otestovat kód proti účtu Azure Cosmos DB, přejděte na portál Azure a vytvořte účet.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Chcete-li otestovat kód proti účtu Azure Cosmos DB, získejte informace o připojovacím řetězci. Potom ho zkopírujte do aplikace.

1. Ve svém účtu Azure Cosmos DB na webu Azure portal v levém navigačním řetězci vyberte **Připojovací řetězec**a pak vyberte **Klíče pro čtení a zápis**. Pomocí tlačítek kopírování na pravé straně obrazovky zkopírujete uživatelské jméno, připojovací řetězec a heslo. 

2. Otevřete soubor *app.py* v kořenovém adresáři.

3. Z portálu zkopírujte hodnotu **username** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu položky **name** v souboru *app.py*.

4. Potom zkopírujte hodnotu **připojovacího řetězce** z portálu a udělejte z něj hodnotu **mongoklienta** v *souboru app.py.*

5. Nakonec z portálu zkopírujte hodnotu **password** a nastavte ji jako hodnotu **password** v souboru *app.py*.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. Můžete ji spustit stejným způsobem jako předtím.

## <a name="deploy-to-azure"></a>Nasazení do Azure

Chcete-li tuto aplikaci nasadit, můžete v Azure vytvořit novou webovou aplikaci a povolit průběžné nasazování s roztoužem tohoto úložiště GitHub. Podle tohoto [kurzu](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) nastavte průběžné nasazení s GitHubem v Azure.

Při nasazování do Azure je potřeba odebrat klíče aplikace a ujistit se, následující část není označená jako komentář:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Potom musíte přidat do nastavení aplikace vaše položky MONGOURL, MONGO_PASSWORD a MONGO_USERNAME. V tomto [kurzu](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) se dozvíte další informace o nastavení aplikace ve službě Azure Web Apps.

Pokud nechcete vytvořit rozdlanou položku tohoto repo, můžete také vybrat **tlačítko Nasadit do Azure** níže. Pak byste měli přejít do Azure a nastavit nastavení aplikace s informacemi o účtu Azure Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Pokud máte v plánu uložit kód v GitHubu nebo jiných možnostech správy zdrojového kódu, nezapomeňte odebrat připojovací řetězce z kódu. Ty je možné místo toho nastavit pomocí nastavení dané webové aplikace.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit Azure Cosmos DB pro účet rozhraní API Mongo DB a použít emulátor Azure Cosmos DB ke spuštění webové aplikace Python Flask To-Do naklonované z GitHubu. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)
