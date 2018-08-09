---
title: Vytvoření webové aplikace Node.js využívající databázi MongoDB ve službě Azure App Service v Linuxu | Microsoft Docs
description: Zjistěte, jak ve službě Azure App Service v Linuxu zprovoznit aplikaci Node.js s připojením k databázi Cosmos DB pomocí připojovacího řetězce MongoDB.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 3380322286740e3b87df11107ac5ade62ffa535d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432061"
---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure-app-service-on-linux"></a>Vytvoření webové aplikace Node.js využívající databázi MongoDB ve službě Azure App Service v Linuxu

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Nasazení do služby App Service ve _Windows_ je popsané v tématu [Vytvoření webové aplikace Node.js využívající databázi MongoDB v Azure](../app-service-web-tutorial-nodejs-mongodb-app.md).
>

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci Node.js, místně ji připojit k databázi MongoDB a pak ji nasadit do Azure s připojením k databázi Cosmos DB pomocí rozhraní MongoDB API. Po dokončení budete mít ve službě App Service v Linuxu spuštěnou aplikaci MEAN (MongoDB, Express, AngularJS a Node.js). Pro zjednodušení používá ukázková aplikace [webovou architekturu MEAN.js](http://meanjs.org/).

![Aplikace MEAN.js spuštěná v rámci služby Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Naučíte se:

> [!div class="checklist"]
> * Vytvořit v Azure databázi Cosmos DB pomocí rozhraní MongoDB API
> * Připojit aplikaci Node.js k MongoDB
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

1. [Nainstalovat Git](https://git-scm.com/).
1. [Nainstalovat Node.js v6.0 nebo novější a NPM](https://nodejs.org/)
1. [Nainstalovat Gulp.js](http://gulpjs.com/) (požadavek pro [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Nainstalovat a spustit MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)

## <a name="test-local-mongodb"></a>Test místní databáze MongoDB

Otevřete okno terminálu a pomocí příkazu `cd` přejděte do adresáře `bin` vaší instalace MongoDB. Toto okno terminálu můžete používat ke spuštění všech příkazů v tomto kurzu.

Spuštěním příkazu `mongo` v terminálu se připojte ke svému místnímu serveru MongoDB.

```bash
mongo
```

Pokud se úspěšně připojíte, vaše databáze MongoDB je již spuštěná. Pokud ne, postupujte podle kroků v článku [Install MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (Instalace MongoDB Community Edition) a ujistěte se, že je vaše místní databáze MongoDB spuštěná. Často se stává, že se databáze MongoDB nainstaluje, ale přesto ji musíte spustit pomocí příkazu `mongod`.

Až dokončíte testování své databáze MongoDB, zadejte v terminálu `Ctrl+C`.

## <a name="create-local-nodejs-app"></a>Vytvoření místní aplikace Node.js

V tomto kroku nastavíte místní projekt Node.js.

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

V okně terminálu přejděte pomocí příkazu `cd` do pracovního adresáře.

Ukázkové úložiště naklonujete spuštěním následujícího příkazu.

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Toto ukázkové úložiště obsahuje kopii [úložiště MEAN.js](https://github.com/meanjs/mean). To je upravené pro spouštění ve službě App Service (další informace najdete v [souboru README](https://github.com/Azure-Samples/meanjs/blob/master/README.md) v úložišti MEAN.js).

### <a name="run-the-application"></a>Spuštění aplikace

Spuštěním následujících příkazů nainstalujte požadované balíčky a spusťte aplikaci.

```bash
cd meanjs
npm install
npm start
```

Upozornění config.domain ignorujte. Po úplném načtení aplikace se zobrazí zpráva podobná této:

```txt
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

V prohlížeči přejděte na `http://localhost:3000`. V horní nabídce klikněte na **Zaregistrovat se** a vytvořte testovacího uživatele. 

Ukázková aplikace MEAN.js ukládá data uživatelů v databázi. Pokud úspěšně vytvoříte uživatele a přihlásíte se, znamená to, že vaše aplikace zapisuje data do místní databáze MongoDB.

![Aplikace MEAN.js se úspěšně připojí k databázi MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Vyberte **Admin > Manage Articles** (Správa > Správa článků) a přidejte několik článků.

Node.js můžete kdykoli zastavit stisknutím `Ctrl+C` v terminálu.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Vytvoření provozní databáze MongoDB

V tomto kroku vytvoříte databázi MongoDB v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

Pro MongoDB tento kurz používá službu [Azure Cosmos DB](/azure/documentdb/). Cosmos DB podporuje klientská připojení MongoDB.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB

Ve službě Cloud Shell vytvořte účet služby Cosmos DB pomocí příkazu [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create).

V následujícím příkazu nahraďte zástupný text *\<cosmosdb_name>* jedinečným názvem služby Cosmos DB. Tento název se používá jako součást koncového bodu služby Cosmos DB (`https://<cosmosdb_name>.documents.azure.com/`), takže musí být jedinečný v rámci všech účtů služby Cosmos DB v Azure. Název smí obsahovat jen malá písmena, číslice a znak spojovníku (-) a musí mít délku 3 až 50 znaků.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

Parametr *--kind MongoDB* umožňuje klientská připojení MongoDB.

Po vytvoření účtu služby Cosmos DB se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies":
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Připojení aplikace k provozní databázi MongoDB

V tomto kroku připojíte ukázkovou aplikaci MEAN.js k právě vytvořené databázi Cosmos DB, a to pomocí připojovacího řetězce MongoDB.

### <a name="retrieve-the-database-key"></a>Načtení klíče databáze

Aby bylo možné se připojit k databázi Cosmos DB, potřebujete klíč databáze. Ve službě Cloud Shell pomocí příkazu [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) načtěte primární klíč.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

V Azure CLI se zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Zkopírujte hodnotu `primaryMasterKey`. Tyto informace budete potřebovat v dalším kroku.

<a name="devconfig"></a>

### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurace připojovacího řetězce v aplikaci Node.js

V místním úložišti MEAN.js ve složce _config/env/_ vytvořte soubor _local-production.js_. Soubor _.gitignore_ obsahuje konfiguraci, která tento soubor uchovává mimo úložiště.

Zkopírujte do něj následující kód. Nezapomeňte nahradit také dva zástupné texty *\<cosmosdb_name>* názvem vaší databáze Cosmos DB a zástupný text *\<primary_master_key>* klíčem, který jste zkopírovali v předchozím kroku.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

Možnost `ssl=true` je povinná, protože [služba Cosmos DB vyžaduje SSL](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements).

Uložte provedené změny.

### <a name="test-the-application-in-production-mode"></a>Test aplikace v provozním režimu

V místním okně terminálu spusťte následující příkaz, který minimalizuje a zabalí skripty pro produkční prostředí. Tento proces vygeneruje soubory potřebné pro produkční prostředí.

```bash
gulp prod
```

V místním okně terminálu spusťte následující příkaz, který použije připojovací řetězec nakonfigurovaný v souboru _config/env/local-production.js_. Chybu certifikátu a upozornění config.domain ignorujte.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` nastaví proměnnou prostředí, která informuje Node.js, že se má spustit v produkčním prostředí.  `node server.js` spustí server Node.js pomocí souboru `server.js` v kořenovém adresáři vašeho úložiště. Tímto způsobem se vaše aplikace Node.js načte do Azure.

Po načtení aplikace se ujistěte, že je spuštěná v produkčním prostředí:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

V prohlížeči přejděte na `http://localhost:8443`. V horní nabídce klikněte na **Zaregistrovat se** a vytvořte testovacího uživatele. Pokud úspěšně vytvoříte uživatele a přihlásíte se, znamená to, že vaše aplikace zapisuje data do databáze Cosmos DB v Azure.

V terminálu zastavte Node.js zadáním `Ctrl+C`.

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

V tomto kroku nasadíte svou aplikaci Node.js připojenou k databázi MongoDB do služby Azure App Service.

### <a name="configure-local-git-deployment"></a>Konfigurace nasazení místního gitu

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Konfigurace proměnné prostředí

Ve výchozím nastavení projekt MEAN.js uchovává soubor _config/env/local-production.js_ mimo úložiště Git. Proto pro vaši webovou aplikaci použijete k definování připojovacího řetězce MongoDB nastavení aplikace.

Nastavit nastavení aplikace můžete pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell.

V následujícím příkladu se konfiguruje proměnná `MONGODB_URI` aplikace ve webové aplikaci Azure. Nahraďte zástupné texty *\<app_name>*, *\<cosmosdb_name>* a *\<primary_master_key>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

V kódu Node.js přistupujete k těmto nastavením aplikace pomocí `process.env.MONGODB_URI`, stejně jako byste přistupovali k proměnné prostředí. 

V místním úložišti MEAN.js otevřete soubor _config/env/production.js_ (ne _config/env/local-production.js_), který obsahuje konfiguraci specifickou pro produkční prostředí. Výchozí aplikace MEAN.js je již nakonfigurovaná tak, aby používala proměnnou prostředí `MONGODB_URI`, kterou jste vytvořili.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

Můžete si všimnout, že proces nasazení po příkazu `npm install` spustí [Gulp](http://gulpjs.com/). Služba App Service během nasazení nespouští úlohy Gulp ani Grunt, takže toto ukázkové úložiště obsahuje v kořenovém adresáři dva další soubory, které je povolují:

- _.deployment_ – Tento soubor informuje službu App Service, že má jako vlastní skript nasazení spustit `bash deploy.sh`.
- _deploy.sh_ – Vlastní skript nasazení. Když se do souboru podíváte, zjistíte, že po příkazech `npm install` a `bower install` spouští příkaz `gulp prod`.

Pomocí tohoto postupu můžete přidat libovolný krok nasazení z Gitu. Pokud svou webovou aplikaci Azure kdykoli restartujete, služba App Service tyto úlohy automatizace znovu nespustí.

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Ve webovém prohlížeči přejděte k nasazené webové aplikaci.

```bash
http://<app_name>.azurewebsites.net
```

V horní nabídce klikněte na **Zaregistrovat se** a vytvořte fiktivního uživatele.

Pokud budete úspěšní a aplikace se automaticky přihlásí k vytvořenému uživateli, znamená to, že se vaše aplikace MEAN.js v Azure může připojit k rozhraní MongoDB API databáze Cosmos DB.

![Aplikace MEAN.js spuštěná v rámci služby Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Vyberte **Admin > Manage Articles** (Správa > Správa článků) a přidejte několik článků.

**Blahopřejeme!** Teď máte ve službě Azure App Service v Linuxu spuštěnou aplikaci Node.js řízenou daty.

## <a name="update-data-model-and-redeploy"></a>Aktualizace datového modelu a opětovné nasazení

V tomto kroku změníte datový model `article` a publikujete provedené změny do Azure.

### <a name="update-the-data-model"></a>Aktualizace datového modelu

V místním úložišti MEAN.js otevřete soubor _modules/articles/server/models/article.server.model.js_.

Do `ArticleSchema` přidejte typ `String` s názvem `comment`. Až skončíte, měl by váš kód schématu vypadat takto:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Aktualizace kódu článků

Aktualizujte zbytek kódu `articles` tak, aby používal `comment`.

Je potřeba upravit pět souborů: kontroler serveru a čtyři zobrazení klienta. 

Otevřete soubor _modules/articles/server/controllers/articles.server.controller.js_.

Do funkce `update` přidejte přiřazení pro `article.comment`. Následující kód ukazuje dokončenou funkci `update`:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Otevřete soubor _modules/articles/client/views/view-article.client.view.html_.

Přímo nad uzavírací značku `</section>` přidejte následující řádek, který spolu se zbývajícími daty článku zobrazí i `comment`:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Otevřete soubor _modules/articles/client/views/list-articles.client.view.html_.

Přímo nad uzavírací značku `</a>` přidejte následující řádek, který spolu se zbývajícími daty článku zobrazí i `comment`:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Otevřete soubor _modules/articles/client/views/admin/list-articles.client.view.html_.

Uvnitř prvku `<div class="list-group">` a přímo nad uzavírací značku `</a>` přidejte následující řádek, který spolu se zbývajícími daty článku zobrazí i `comment`:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Otevřete soubor _modules/articles/client/views/admin/form-article.client.view.html_.

Vyhledejte prvek `<div class="form-group">` obsahující tlačítko pro odeslání, který vypadá přibližně takto:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Přímo nad tuto značku přidejte další prvek `<div class="form-group">`, který lidem umožní upravit pole `comment`. Váš nový prvek by měl vypadat přibližně takto:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Místní test provedených změn

Uložte všechny provedené změny.

V místním okně terminálu znovu otestujte provedené změny v provozním režimu.

```bash
gulp prod
NODE_ENV=production node server.js
```

V prohlížeči přejděte na adresu `http://localhost:8443` a ujistěte se, že jste přihlášeni.

Vyberte **Admin > Manage Articles** (Správa > Správa článků) a pak výběrem tlačítka **+** přidejte článek.

Nyní se zobrazí nové textové pole `Comment`.

![Přidané pole komentáře ke článkům](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

V terminálu zastavte Node.js zadáním `Ctrl+C`.

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

Potvrďte změny v Gitu a potom nasdílejte změny kódu do Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Po dokončení operace `git push` přejděte do webové aplikace Azure a vyzkoušejte nové funkce.

![Změny modelu a databáze publikované v Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Pokud jste dříve přidali články, můžete je stále zobrazit. Stávající data ve službě Cosmos DB nebudou ztracena. Kromě toho vaše aktualizace ovlivňují schéma dat a nechávají existující data netknutá.

## <a name="manage-your-azure-web-app"></a>Správa webové aplikace Azure

Přejděte na web [Azure Portal](https://portal.azure.com) k webové aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Na začátku portál zobrazí stránku **Přehled** vaší webové aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Vytvořit v Azure databázi Cosmos DB pomocí rozhraní MongoDB API
> * Připojit aplikaci Node.js k MongoDB
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat protokoly z Azure do terminálu
> * Spravovat aplikaci na webu Azure Portal

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
