---
title: Sestavení webové aplikace v baňce pomocí rozhraní Azure Cosmos DB API pro MongoDB a Python SDK
description: Prezentuje vzorový kód v Pythonu, který můžete použít k připojení a dotazování pomocí rozhraní Azure Cosmos DB API pro MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 2bd8fa81d0825e604c42c54c0f789b7939206804
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756942"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Rychlý Start: Vytvoření aplikace v Pythonu pomocí rozhraní API Azure Cosmos DB pro MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Můžete rychle vytvořit a dotazovat databáze dokumentů, klíčů a hodnot a grafů, z nichž všechno přináší výhody funkcí globální distribuce a horizontálního škálování v jádru Cosmos DB.

Tento průvodce rychlým startem používá následující [příklad baňky](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) a ukazuje, jak vytvořit jednoduchou aplikaci k baňce pomocí [emulátoru Azure Cosmos DB](local-emulator.md) a rozhraní API Azure Cosmos DB pro MongoDB.

## <a name="prerequisites"></a>Předpoklady

- Stáhněte si [emulátor služby Azure Cosmos DB](local-emulator.md). Emulátor je momentálně podporovaný jenom ve Windows. Tato ukázka vysvětluje, jak ukázku použít s produkčním klíčem z Azure, což se dá udělat na libovolné platformě.

- Pokud ještě nemáte nainstalovaný editor Visual Studio Code, můžete rychle nainstalovat editor [VS Code](https://code.visualstudio.com/Download) pro vaši platformu (Windows, Mac, Linux).

- Nezapomeňte přidat podporu jazyka Python instalací některého z oblíbených rozšíření Pythonu.
  1. Vyberte rozšíření.
  2. Nainstalujte rozšíření zadáním příkazu `ext install` do palety příkazů `Ctrl+Shift+P`.

     Příklady v tomto dokumentu využívají oblíbené [rozšíření Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) s plnou sadou funkcí, jehož autorem je Don Jayamanne.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujte aplikaci MongoDB z GitHubu, nastavíme připojovací řetězec a spustíme ji. Uvidíte, jak snadno se pracuje s daty prostřednictvím kódu programu.

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

3. Pak nastavte proměnnou prostředí pro aplikaci v baňce pomocí `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` editory PowerShellu nebo `export FLASK_APP=app.py`, pokud používáte Mac. 

4. Spusťte aplikaci pomocí příkazu `flask run` a přejděte na adresu [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Přidávejte a odebírejte úlohy a sledujte, jak se objevují a mění v rámci kolekce.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Pokud chcete otestovat kód proti Cosmos účtu v reálném čase, klikněte na Azure Portal pro vytvoření účtu a získání informací o připojovacím řetězci. Potom ho zkopírujte do aplikace.

1. V [Azure Portal](https://portal.azure.com/)v účtu Cosmos v levém navigačním panelu klikněte na **připojovací řetězec**a pak klikněte na **klíče pro čtení i zápis**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky uživatelské jméno, heslo a hostitele do souboru Dal.cs.

2. Otevřete soubor **app.py** v kořenovém adresáři.

3. Z portálu zkopírujte hodnotu **username** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu položky **name** v souboru **app.py**.

4. Potom zkopírujte hodnotu **connection string** z portálu a nastavte ji jako hodnotu položky MongoClient v souboru **app.py**.

5. Nakonec z portálu zkopírujte hodnotu **password** a nastavte ji jako hodnotu **password** v souboru **app.py**.

Nyní jste aktualizovali aplikaci o všechny informace, které potřebuje ke komunikaci s Cosmos DB. Můžete ji spustit stejným způsobem jako předtím.

## <a name="deploy-to-azure"></a>Nasadit do Azure

Pokud chcete tuto aplikaci nasadit, můžete v Azure vytvořit novou webovou aplikaci a povolit průběžné nasazování pomocí větvení tohoto úložiště GitHub. V tomto [kurzu](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) nastavíte průběžné nasazování s GitHubem v Azure.

Při nasazování do Azure je potřeba odebrat klíče aplikace a ujistit se, následující část není označená jako komentář:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Potom musíte přidat do nastavení aplikace vaše položky MONGOURL, MONGO_PASSWORD a MONGO_USERNAME. V tomto [kurzu](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) se dozvíte další informace o nastavení aplikace ve službě Azure Web Apps.

Pokud nechcete vytvořit fork tohoto úložiště, můžete také kliknout na níže uvedené tlačítko Nasazení do Azure. Pak byste měli přejít do Azure a upravit nastavení aplikace pomocí údajů o vašem účtu služby Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Pokud máte v plánu ukládat kód do GitHubu nebo jiné možnosti správy zdrojového kódu, nezapomeňte odebrat připojovací řetězce z kódu. Ty je možné místo toho nastavit pomocí nastavení dané webové aplikace.

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Cosmos a spuštění aplikace v baňce. Nyní můžete importovat další data do databáze Cosmos. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)
