---
title: Vytvoření webové aplikace v Pythonu pomocí rozhraní API Azure Cosmos DB pro MongoDB
description: Prezentuje vzorový kód v Pythonu, který můžete použít k připojení a dotazování pomocí rozhraní Azure Cosmos DB API pro MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: a0545ea61879b30349403e019ead69ba14f75cb9
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486493"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Rychlý Start: Vytvoření aplikace v Pythonu pomocí rozhraní API Azure Cosmos DB pro MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

V tomto rychlém startu použijete Azure Cosmos DB pro účet rozhraní API služby Mongo DB nebo emulátor Azure Cosmos DB ke spuštění webové aplikace v To-Do Pythonu, která je klonována z GitHubu. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Případně můžete použít [emulátor Azure Cosmos DB](local-emulator.md). 
- [Python 3.6 +](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) s [rozšířením Pythonu](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní naklonujte Flask-MongoDB aplikaci z GitHubu, nastavíme připojovací řetězec a spustíme ji. Uvidíte, jak snadno se pracuje s daty prostřednictvím kódu programu.

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
4. Otevřete složku v nástroji Visual Studio Code.

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Spuštění webové aplikace](#run-the-web-app). 

Všechny následující fragmenty kódu jsou pořízeny ze souboru *App.py* a používá připojovací řetězec pro místní emulátor Azure Cosmos DB. Heslo je potřeba rozdělit, jak vidíte níže, aby se dala použít lomítka, která se jinak nedají analyzovat.

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
    
## <a name="run-the-web-app"></a>Spusťte webovou aplikaci

1. Zkontrolujte, jestli je spuštěný emulátor služby Azure Cosmos DB.

2. Otevřete okno a příkazem `cd` přejděte do adresáře, ve kterém je aplikace uložená.

3. Pak nastavte proměnnou prostředí pro aplikaci v baňce `set FLASK_APP=app.py` `$env:FLASK_APP = app.py` pro editory prostředí PowerShell, nebo `export FLASK_APP=app.py` Pokud používáte Mac. 

4. Spusťte aplikaci pomocí nástroje `flask run` a přejděte na adresu *http: \/ /127.0.0.1:5000/*.

5. Přidávejte a odebírejte úlohy a sledujte, jak se objevují a mění v rámci kolekce.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Pokud chcete otestovat kód proti účtu Live Azure Cosmos DB, vytvořte účet pomocí Azure Portal.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

K otestování kódu proti účtu Live Azure Cosmos DB Získejte informace o svém připojovacím řetězci. Potom ho zkopírujte do aplikace.

1. V Azure Cosmos DB účtu v Azure Portal v levém navigačním panelu vyberte **připojovací řetězec**a pak vyberte **klíče pro čtení i zápis**. Pomocí tlačítek kopírování na pravé straně obrazovky zkopírujte uživatelské jméno, připojovací řetězec a heslo. 

2. Otevřete soubor *app.py* v kořenovém adresáři.

3. Z portálu zkopírujte hodnotu **username** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu položky **name** v souboru *app.py*.

4. Pak z portálu Zkopírujte hodnotu **připojovacího řetězce** a nastavte ji jako hodnotu **MongoClient** v souboru *App.py* .

5. Nakonec z portálu zkopírujte hodnotu **password** a nastavte ji jako hodnotu **password** v souboru *app.py*.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. Můžete ji spustit stejným způsobem jako předtím.

## <a name="deploy-to-azure"></a>Nasazení do Azure

Pokud chcete tuto aplikaci nasadit, můžete v Azure vytvořit novou webovou aplikaci a povolit průběžné nasazování pomocí větvení tohoto úložiště GitHub. V tomto [kurzu](../app-service/deploy-continuous-deployment.md) nastavíte průběžné nasazování s GitHubem v Azure.

Při nasazování do Azure je potřeba odebrat klíče aplikace a ujistit se, následující část není označená jako komentář:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Potom musíte přidat do nastavení aplikace vaše položky MONGOURL, MONGO_PASSWORD a MONGO_USERNAME. V tomto [kurzu](../app-service/configure-common.md#configure-app-settings) se dozvíte další informace o nastavení aplikace ve službě Azure Web Apps.

Pokud nechcete vytvořit rozvětvení tohoto úložiště, můžete také vybrat tlačítko **nasadit do Azure** níže. Měli byste přejít do Azure a nastavit nastavení aplikace s využitím informací o účtu Azure Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Pokud máte v plánu ukládat kód do GitHubu nebo jiné možnosti správy zdrojového kódu, nezapomeňte odebrat připojovací řetězce z kódu. Ty je možné místo toho nastavit pomocí nastavení dané webové aplikace.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit Azure Cosmos DB pro účet rozhraní API služby Mongo DB a použít emulátor Azure Cosmos DB ke spuštění webové aplikace v To-Do Pythonu, která je klonována z GitHubu. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)