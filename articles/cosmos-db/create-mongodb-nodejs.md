---
title: 'Rychlý Start: připojení aplikace Node.js MongoDB k Azure Cosmos DB'
description: Tento rychlý Start ukazuje, jak připojit existující aplikaci MongoDB napsanou v Node.js k Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 5af929991e96d1307c32d219a9807c6e6a58387c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91253288"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Rychlý Start: migrace stávající webové aplikace MongoDB Node.js do Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

V tomto rychlém startu vytvoříte a spravujete Azure Cosmos DB pro účet rozhraní API služby Mongo DB pomocí Azure Cloud Shell a se střední (MongoDB, expresní, úhlovou) a Node.jsou aplikaci naklonovaná z GitHubu. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Můžete také použít [emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) spolu s připojovacím řetězcem `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Node.js](https://nodejs.org/)a praktické znalosti Node.js.
- [Git](https://git-scm.com/downloads).
- Pokud nechcete používat Azure Cloud Shell, [Azure CLI 2.0 +](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Spuštěním následujících příkazů naklonujte ukázkové úložiště. Toto ukázkové úložiště obsahuje výchozí aplikaci [MEAN.js](https://meanjs.org/).

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Tato aplikace MongoDB napsaná v Node.js se připojuje k databázi Azure Cosmos DB, která podporuje klienta MongoDB. Jinými slovy je transparentní pro aplikaci, že jsou data uložena v databázi Azure Cosmos DB.

Nainstalujte požadované balíčky a spusťte aplikaci.

```bash
cd mean
npm install
npm start
```
Aplikace se pokusí připojit ke zdroji MongoDB, selže a ukončí se, když se na výstupu vrátí chyba [MongoError: connect ECONNREFUSED 127.0.0.1:27017].

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace rozhraní příkazového řádku Azure]. 

Pokud používáte nainstalované rozhraní příkazového řádku Azure, přihlaste se ke svému předplatnému Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) a postupujte podle pokynů na obrazovce. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Přidání modulu služby Azure Cosmos DB

Pokud používáte nainstalované rozhraní příkazového řádku Azure, zkontrolujte pomocí příkazu `az`, zda je komponenta `cosmosdb` už nainstalovaná. Pokud se komponenta `cosmosdb` v seznamu základních příkazů nachází, pokračujte k dalšímu příkazu. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

Pokud se komponenta `cosmosdb` v seznamu základních příkazů nenachází, přeinstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků](../azure-resource-manager/management/overview.md) pomocí nástroje [AZ Group Create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako například webové aplikace, databáze a účty úložiště. 

Následující příklad vytvoří skupinu prostředků pro oblast Západní Evropa. Pro skupinu prostředků vyberte jedinečný název.

Pokud používáte Azure Cloud Shell, vyberte **vyzkoušet**, postupujte podle pokynů na obrazovce pro přihlášení a pak zkopírujte příkaz do příkazového řádku.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Pomocí příkazu [AZ cosmosdb Create](/cli/azure/cosmosdb#az-cosmosdb-create) vytvořte účet Cosmos.

V následujícím příkazu nahraďte zástupný symbol vlastním jedinečným názvem účtu Cosmos `<cosmosdb-name>` . Tento jedinečný název se použije jako součást Cosmos DBho koncového bodu ( `https://<cosmosdb-name>.documents.azure.com/` ), takže název musí být jedinečný ve všech účtech Cosmos v Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametr `--kind MongoDB` umožňuje klientská připojení MongoDB.

Po vytvoření účtu služby Azure Cosmos DB zobrazí rozhraní příkazového řádku Azure podobné informace jako v následujícím příkladu. 

> [!NOTE]
> Tento příklad jako formát výstupu Azure CLI používá výchozí JSON. Pokud chcete použít jiný formát výstupu, přečtěte si téma [Formáty výstupu pro příkazy Azure CLI](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Připojení aplikace Node.js k databázi

V tomto kroku připojíte ukázkovou aplikaci MEAN.js k účtu databáze Azure Cosmos DB, který jste právě vytvořili. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurace připojovacího řetězce v aplikaci Node.js

V úložišti MEAN.js otevřete `config/env/local-development.js`.

Obsah tohoto souboru nahraďte následujícím kódem. Nezapomeňte také nahradit tyto dva `<cosmosdb-name>` zástupné symboly názvem vašeho účtu Cosmos.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Načtení klíče

Aby bylo možné se připojit k databázi Cosmos, potřebujete klíč databáze. K načtení primárního klíče použijte příkaz [AZ cosmosdb Keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) .

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Výstupní informace rozhraní příkazového řádku Azure jsou podobné jako v následujícím příkladu. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Zkopírujte hodnotu `primaryMasterKey`. Vložte ji místo `<primary_master_key>` v souboru `local-development.js`.

Uložte provedené změny.

### <a name="run-the-application-again"></a>Spusťte aplikaci znovu.

Spusťte `npm start` znovu. 

```bash
npm start
```

Zpráva konzoly by vás teď měla informovat o tom, že vývojové prostředí je připravené a spuštěné. 

V prohlížeči přejdete na `http://localhost:3000` . V horní nabídce vyberte možnost **zaregistrovat** se a zkuste vytvořit dva fiktivní uživatele. 

Ukázková aplikace MEAN.js ukládá data uživatelů v databázi. Pokud budete úspěšní a aplikace MEAN.js automaticky zaregistruje vytvořeného uživatele, bude připojení Azure Cosmos DB fungovat. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="Aplikace MEAN.js se úspěšně připojí k databázi MongoDB":::

## <a name="view-data-in-data-explorer"></a>Zobrazení dat v Průzkumníku dat

Data uložená v databázi Cosmos jsou k dispozici pro zobrazení a dotazování v Azure Portal.

Pokud chcete zobrazovat uživatelská data vytvořená v předchozím kroku, zadávat na ně dotazy a pracovat s nimi, přihlaste se k portálu [Azure Portal](https://portal.azure.com) ve webovém prohlížeči.

Do vyhledávacího pole nahoře zadejte **Azure Cosmos DB**. Když se otevře okno účtu Cosmos, vyberte svůj účet Cosmos. V levém navigačním panelu vyberte **Průzkumník dat**. Rozbalte kolekci v podokně Kolekce. Pak můžete zobrazovat dokumenty v kolekci, dotazovat se na data a dokonce vytvářet a spouštět uložené procedury, triggery a funkce UDF. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Aplikace MEAN.js se úspěšně připojí k databázi MongoDB":::


## <a name="deploy-the-nodejs-application-to-azure"></a>Nasaďte aplikaci Node.js do platformy Azure

V tomto kroku nasadíte Node.js aplikaci pro Cosmos DB.

Pravděpodobně jste si všimli, že dříve změněný soubor konfigurace je určený pro vývojové prostředí (`/config/env/local-development.js`). Pokud nasadíte aplikaci do služby App Service, bude se ve výchozím nastavení spouštět v produkčním prostředí. Teď tedy musíte provést stejné změny v příslušném souboru konfigurace.

V úložišti MEAN.js otevřete `config/env/production.js`.

V objektu `db` nahraďte hodnotu `uri` tak, jak je uvedeno v následujícím příkladu. Nezapomeňte nahradit zástupné symboly jako v předcházejícím případě.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> `ssl=true`Možnost je důležitá z důvodu Cosmos DB požadavků. Další informace najdete v tématu [požadavky na připojovací řetězec](connect-mongodb-account.md#connection-string-requirements).
>
>

V terminálu potvrďte do systému Git všechny změny. Můžete zkopírovat oba příkazy a spouštět je společně.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit Azure Cosmos DB účet rozhraní API MongoDB pomocí Azure Cloud Shell a vytvořit a spustit MEAN.js aplikaci pro přidání uživatelů k účtu. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data.

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)
