---
title: Připojení aplikace MongoDB ke službě Azure Cosmos DB pomocí Node.js | Dokumentace Microsoftu
description: Naučte se připojovat stávající aplikaci MongoDB s podporou Node.js ke službě Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/19/2017
ms.author: sngun
ms.openlocfilehash: 69302a0291b267bd4d46ab9387d296673da17025
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Služba Azure Cosmos DB: Migrace stávající webové aplikace MongoDB s podporou Node.js 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos. 

Tento rychlý start popisuje způsob použití stávající aplikace MongoDB napsané v Node.js a její připojení k databázi Azure Cosmos DB, která podporuje klientská připojení MongoDB pomocí rozhraní [MongoDB API](mongodb-introduction.md). Jinými slovy: aplikace Node.js ví pouze to, že se připojuje k databázi pomocí rozhraní API MongoDB. V aplikaci se transparentně zobrazuje, že data jsou uložena ve službě Azure Cosmos DB.

Po dokončení budete mít ve službě [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) spuštěnou aplikaci MEAN (MongoDB, Express, Angular a Node.js). 

![Aplikace MEAN.js spuštěná v rámci služby Azure App Service](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Požadavky 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Abyste mohli spustit příkazy `npm` a `git`, musíte mít kromě rozhraní příkazového řádku Azure místně nainstalovaný [Node.js](https://nodejs.org/) a [Git](http://www.git-scm.com/downloads).

Měli byste mít praktickou znalost Node.js. Tento rychlý start není určen jako pomůcka pro všeobecný vývoj aplikací v Node.js.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Spuštěním následujících příkazů naklonujte ukázkové úložiště. Toto ukázkové úložiště obsahuje výchozí aplikaci [MEAN.js](http://meanjs.org/).

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
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Nainstalujte požadované balíčky a spusťte aplikaci.

```bash
cd mean
npm install
npm start
```
Aplikace se pokusí připojit ke zdroji MongoDB, selže a ukončí se, když se na výstupu vrátí chyba [MongoError: connect ECONNREFUSED 127.0.0.1:27017].

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud používáte nainstalované rozhraní příkazového řádku Azure, přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/reference-index#az_login) a postupujte podle pokynů na obrazovce. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Přidání modulu služby Azure Cosmos DB

Pokud používáte nainstalované rozhraní příkazového řádku Azure, zkontrolujte pomocí příkazu `az`, zda je komponenta `cosmosdb` už nainstalovaná. Pokud se komponenta `cosmosdb` v seznamu základních příkazů nachází, pokračujte k dalšímu příkazu. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

Pokud se komponenta `cosmosdb` v seznamu základních příkazů nenachází, přeinstalujte [rozhraní příkazového řádku Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte pomocí příkazu [az group create](/cli/azure/group#az_group_create) [skupinu prostředků](../azure-resource-manager/resource-group-overview.md). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako například webové aplikace, databáze a účty úložiště. 

Následující příklad vytvoří skupinu prostředků pro oblast Západní Evropa. Pro skupinu prostředků vyberte jedinečný název.

Pokud používáte Azure Cloud Shell, klikněte na **Vyzkoušet** a postupujte podle pokynů pro přihlášení uvedených na obrazovce, potom příkaz zkopírujte do příkazového řádku.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Pomocí příkazu [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) vytvořte účet služby Azure Cosmos DB.

V následujícím příkazu nahraďte zástupný symbol `<cosmosdb-name>` vlastním jedinečným názvem účtu služby Azure Cosmos DB. Tento jedinečný název se použije jako součást koncového bodu služby Azure Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`). Název proto musí být jedinečný ve všech účtech služby Azure Cosmos DB platformy Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametr `--kind MongoDB` umožňuje klientská připojení MongoDB.

Po vytvoření účtu služby Azure Cosmos DB zobrazí rozhraní příkazového řádku Azure podobné informace jako v následujícím příkladu. 

> [!NOTE]
> Tento příklad jako formát výstupu Azure CLI používá výchozí JSON. Pokud chcete použít jiný formát výstupu, přečtěte si téma [Formáty výstupu pro příkazy Azure CLI 2.0](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

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

V tomto kroku připojíte ukázkovou aplikaci MEAN.js k právě vytvořené databázi služby Azure Cosmos DB, a to pomocí připojovacího řetězce MongoDB. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurace připojovacího řetězce v aplikaci Node.js

V úložišti MEAN.js otevřete `config/env/local-development.js`.

Obsah tohoto souboru nahraďte následujícím kódem. Nezapomeňte nahradit také dva zástupné symboly `<cosmosdb-name>` názvem vašeho účtu služby Azure Cosmos DB.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Načtení klíče

Aby bylo možné se připojit k databázi služby Azure Cosmos DB, potřebujete klíč databáze. Pro načtení primárního klíče použijte příkaz [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys).

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
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

V prohlížeči přejděte na `http://localhost:3000`. V horní nabídce klikněte na **Zaregistrovat se** a pokuste se vytvořit dva fiktivní uživatele. 

Ukázková aplikace MEAN.js ukládá data uživatelů v databázi. Pokud budete úspěšní a aplikace MEAN.js automaticky zaregistruje vytvořeného uživatele, bude připojení Azure Cosmos DB fungovat. 

![Aplikace MEAN.js se úspěšně připojí k databázi MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Zobrazení dat v Průzkumníku dat

Data uložená službou Azure Cosmos DB je možné zobrazovat a je možné se na ně dotazovat a spouštět na ně obchodní logiku na portálu Azure Portal.

Pokud chcete zobrazovat uživatelská data vytvořená v předchozím kroku, zadávat na ně dotazy a pracovat s nimi, přihlaste se k portálu [Azure Portal](https://portal.azure.com) ve webovém prohlížeči.

V horním vyhledávacím poli zadejte Azure Cosmos DB. Po otevření okna účtu služby Cosmos DB vyberte účet Cosmos DB. V levém navigačním panelu klikněte na Průzkumník dat. Rozbalte kolekci v podokně Kolekce. Pak můžete zobrazovat dokumenty v kolekci, dotazovat se na data a dokonce vytvářet a spouštět uložené procedury, triggery a funkce UDF. 

![Průzkumník dat na portálu Azure Portal](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Nasaďte aplikaci Node.js do platformy Azure

V tomto kroku se nasazuje aplikace Node.js připojená k databázi MongoDB ke službě Azure Cosmos DB.

Pravděpodobně jste si všimli, že dříve změněný soubor konfigurace je určený pro vývojové prostředí (`/config/env/local-development.js`). Pokud nasadíte aplikaci do služby App Service, bude se ve výchozím nastavení spouštět v produkčním prostředí. Teď tedy musíte provést stejné změny v příslušném souboru konfigurace.

V úložišti MEAN.js otevřete `config/env/production.js`.

V objektu `db` nahraďte hodnotu `uri` tak, jak je uvedeno v následujícím příkladu. Nezapomeňte nahradit zástupné symboly jako v předcházejícím případě.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Možnost `ssl=true` je důležitá, protože [služba Azure Cosmos DB vyžaduje SSL](connect-mongodb-account.md#connection-string-requirements). 
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

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu databáze služby Azure Cosmos DB a vytvoření kolekce MongoDB pomocí Průzkumníku dat. Teď můžete migrovat data MongoDB do služby Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importování dat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md)
