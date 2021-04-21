---
title: 'Kurz: Node.js aplikace pomocí MongoDB'
description: Naučte se, jak získat aplikaci Node.js v Azure pracovat s připojením k databázi MongoDB v Azure (Cosmos DB). V tomto kurzu se používá MEAN.js.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b1dcd413f301f25460cb29f1bb20e67a37ac6ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767311"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Kurz: Vytvoření aplikace Node.js a MongoDB v Azure

::: zone pivot="platform-windows"  

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak vytvořit aplikaci Node.js v App Service ve Windows a připojit ji k databázi MongoDB. Po dokončení budete mít ve službě [Azure App Service](overview.md) spuštěnou aplikaci MEAN (MongoDB, Express, AngularJS a Node.js). Pro zjednodušení používá ukázková [webovou architekturu MEAN.js](https://meanjs.org/).

::: zone-end

::: zone pivot="platform-linux"


[Azure App Service](overview.md) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto kurzu se dozvíte, jak vytvořit aplikaci Node.js v App Service v systému Linux, připojit ji místně k databázi MongoDB a pak ji nasadit do databáze v rozhraní API Azure Cosmos DB pro MongoDB. Po dokončení budete mít ve službě App Service v Linuxu spuštěnou aplikaci MEAN (MongoDB, Express, AngularJS a Node.js). Pro zjednodušení používá ukázková [webovou architekturu MEAN.js](https://meanjs.org/).

::: zone-end

![Aplikace MEAN.js spuštěná v rámci služby Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Naučíte se:

> [!div class="checklist"]
> * Vytvoření databáze MongoDB v Azure
> * Připojení aplikace Node.js k MongoDB
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Nainstalovat Git](https://git-scm.com/).
- [Nainstalovat Node.js a NPM](https://nodejs.org/).
- [Nainstalovat Bower](https://bower.io/) (požadavek pro [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [Nainstalovat Gulp.js](https://gulpjs.com/) (požadavek pro [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [Instalace a spuštění MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

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

<pre>
--
MEAN.JS – vývojové prostředí

Prostředí: vývojový server:          http://0.0.0.0:3000 databáze: MongoDB://localhost/Mean-dev verze aplikace: 0.5.0 MEAN.JS verze: 0.5.0--
</pre>

V prohlížeči přejděte na `http://localhost:3000`. V horní nabídce klikněte na **Zaregistrovat se** a vytvořte testovacího uživatele. 

Ukázková aplikace MEAN.js ukládá data uživatelů v databázi. Pokud úspěšně vytvoříte uživatele a přihlásíte se, znamená to, že vaše aplikace zapisuje data do místní databáze MongoDB.

![Aplikace MEAN.js se úspěšně připojí k databázi MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Vyberte **Admin > Manage Articles** (Správa > Správa článků) a přidejte několik článků.

Node.js můžete kdykoli zastavit stisknutím `Ctrl+C` v terminálu. 

## <a name="create-production-mongodb"></a>Vytvoření provozní databáze MongoDB

V tomto kroku vytvoříte databázi MongoDB v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

Pro MongoDB tento kurz používá službu [Azure Cosmos DB](/azure/documentdb/). Cosmos DB podporuje klientská připojení MongoDB.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB

> [!NOTE]
> Za vytvoření databází Azure Cosmos DB z tohoto kurzu ve vlastním předplatném Azure se účtuje poplatek. Pokud chcete použít bezplatný účet služby Azure Cosmos DB po dobu sedmi dnů, můžete využít [bezplatné vyzkoušení služby Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Stačí kliknout na tlačítko **Vytvořit** na dlaždici MongoDB a vytvořit v Azure bezplatnou databázi MongoDB. Po vytvoření databáze přejděte na portálu do části **Připojovací řetězec** a získejte připojovací řetězec vaší služby Azure Cosmos DB, který použijete v pozdější části tohoto kurzu.
>

V Cloud Shell vytvořte účet Cosmos DB pomocí [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) příkazu.

V následujícím příkazu nahraďte zástupný symbol jedinečným názvem Cosmos DB *\<cosmosdb-name>* . Tento název se používá jako součást koncového bodu služby Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`), takže musí být jedinečný v rámci všech účtů služby Cosmos DB v Azure. Název smí obsahovat jen malá písmena, číslice a znak spojovníku (-) a musí mít délku 3 až 50 znaků.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametr *--kind MongoDB* umožňuje klientská připojení MongoDB.

Po vytvoření účtu služby Cosmos DB se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>Připojení aplikace k provozní databázi MongoDB

V tomto kroku připojíte ukázkovou aplikaci MEAN.js k právě vytvořené databázi Cosmos DB, a to pomocí připojovacího řetězce MongoDB. 

### <a name="retrieve-the-database-key"></a>Načtení klíče databáze

Aby bylo možné se připojit k databázi Cosmos DB, potřebujete klíč databáze. V Cloud Shell použijte [`az cosmosdb list-keys`](/cli/azure/cosmosdb#az_cosmosdb_list_keys) příkaz k načtení primárního klíče.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

V Azure CLI se zobrazí podobné informace jako v následujícím příkladu:

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

Zkopírujte hodnotu `primaryMasterKey`. Tyto informace budete potřebovat v dalším kroku.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurace připojovacího řetězce v aplikaci Node.js

V místním úložišti MEAN.js ve složce _config/env/_ vytvořte soubor _local-production.js_. _přípona. gitignore_ je už nakonfigurovaná, aby se tento soubor zachoval mimo úložiště. 

Zkopírujte do něj následující kód. Nezapomeňte nahradit tyto dva *\<cosmosdb-name>* zástupné symboly názvem vaší Cosmos DB databáze a zástupný symbol nahraďte *\<primary-master-key>* klíčem, který jste zkopírovali v předchozím kroku.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

Možnost `ssl=true` je povinná, protože [služba Cosmos DB vyžaduje SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Uložte provedené změny.

### <a name="test-the-application-in-production-mode"></a>Test aplikace v provozním režimu 

V místním okně terminálu spusťte následující příkaz, který minimalizuje a zabalí skripty pro produkční prostředí. Tento proces vygeneruje soubory potřebné pro produkční prostředí.

```bash
gulp prod
```

V místním okně terminálu spusťte následující příkaz, který použije připojovací řetězec nakonfigurovaný v souboru _config/env/local-production.js_. Chybu certifikátu a upozornění config.domain ignorujte.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` nastaví proměnnou prostředí, která informuje Node.js, že se má spustit v produkčním prostředí.  `node server.js` spustí server Node.js pomocí souboru `server.js` v kořenovém adresáři vašeho úložiště. Tímto způsobem se vaše aplikace Node.js načte do Azure. 

Po načtení aplikace se ujistěte, že je spuštěná v produkčním prostředí:

<pre>
--
MEAN.JS

Prostředí: provozní server: http://0.0.0.0:8443 databáze: MongoDB:// &lt; cosmosdb-name &gt; : &lt; Primary-Master-Key &gt; @ &lt; cosmosdb-name &gt; . Documents.Azure.com:10250/Mean?SSL=true&sslverifycertificate = false verze aplikace: 0.5.0 MEAN.JS verze: 0.5.0
</pre>

V prohlížeči přejděte na `http://localhost:8443`. V horní nabídce klikněte na **Zaregistrovat se** a vytvořte testovacího uživatele. Pokud úspěšně vytvoříte uživatele a přihlásíte se, znamená to, že vaše aplikace zapisuje data do databáze Cosmos DB v Azure. 

V terminálu zastavte Node.js zadáním `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

V tomto kroku nasadíte svou aplikaci Node.js připojenou k databázi MongoDB do služby Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Vytvoření webové aplikace

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>Konfigurace proměnné prostředí

Ve výchozím nastavení projekt MEAN.js uchovává soubor _config/env/local-production.js_ mimo úložiště Git. Takže pro vaši aplikaci Azure použijete nastavení aplikace k definování připojovacího řetězce MongoDB.

Nastavení aplikace nastavíte pomocí [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) příkazu v Cloud Shell. 

Následující příklad nakonfiguruje `MONGODB_URI` nastavení aplikace v aplikaci Azure. Nahraďte *\<app-name>* *\<cosmosdb-name>* *\<primary-master-key>* zástupné symboly, a.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

V Node.js kódu získáte [přístup k tomuto nastavení aplikace](configure-language-nodejs.md#access-environment-variables) pomocí nástroje `process.env.MONGODB_URI` stejně, jako byste měli přístup k libovolné proměnné prostředí. 

V místním úložišti MEAN.js otevřete soubor _config/env/production.js_ (ne _config/env/local-production.js_), který obsahuje konfiguraci specifickou pro produkční prostředí. Výchozí aplikace MEAN.js je již nakonfigurovaná tak, aby používala proměnnou prostředí `MONGODB_URI`, kterou jste vytvořili.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

Můžete si všimnout, že proces nasazení po příkazu `npm install` spustí [Gulp](https://gulpjs.com/). Služba App Service během nasazení nespouští úlohy Gulp ani Grunt, takže toto ukázkové úložiště obsahuje v kořenovém adresáři dva další soubory, které je povolují: 

- _.deployment_ – Tento soubor informuje službu App Service, že má jako vlastní skript nasazení spustit `bash deploy.sh`.
- _deploy.sh_ – Vlastní skript nasazení. Když se do souboru podíváte, zjistíte, že po příkazech `npm install` a `bower install` spouští příkaz `gulp prod`. 

Pomocí tohoto postupu můžete přidat libovolný krok nasazení z Gitu. Pokud aplikaci Azure restartujete kdykoli, App Service se tyto úlohy Automation znovu nespustí. Další informace najdete v tématu [spuštění grunt/Bower/Gulp](configure-language-nodejs.md#run-gruntbowergulp).

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure 

Přejděte do nasazené aplikace pomocí webového prohlížeče. 

```bash 
http://<app-name>.azurewebsites.net 
``` 

V horní nabídce klikněte na **Zaregistrovat se** a vytvořte fiktivního uživatele. 

Pokud budete úspěšní a aplikace se automaticky přihlásí k vytvořenému uživateli, znamená to, že se vaše aplikace MEAN.js v Azure může připojit k databázi MongoDB (Cosmos DB). 

![Aplikace MEAN.js spuštěná v rámci služby Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Vyberte **Admin > Manage Articles** (Správa > Správa článků) a přidejte několik článků. 

**Blahopřejeme!** Používáte aplikaci Node.js řízenou daty ve službě Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Aktualizace datového modelu a opětovné nasazení

V tomto kroku změníte datový model `article` a publikujete provedené změny do Azure.

### <a name="update-the-data-model"></a>Aktualizace datového modelu

V místním úložišti MEAN.js otevřete soubor _modules/articles/server/models/article.server.model.js_.

Do `ArticleSchema` přidejte typ `String` s názvem `comment`. Až skončíte, měl by váš kód schématu vypadat takto:

```javascript
const ArticleSchema = new Schema({
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
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Otevřete soubor _modules/articles/client/views/view-article.client.view.html_.

Přímo nad uzavírací značku `</section>` přidejte následující řádek, který spolu se zbývajícími daty článku zobrazí i `comment`:

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

Otevřete soubor _modules/articles/client/views/list-articles.client.view.html_.

Přímo nad uzavírací značku `</a>` přidejte následující řádek, který spolu se zbývajícími daty článku zobrazí i `comment`:

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Otevřete soubor _modules/articles/client/views/admin/list-articles.client.view.html_.

Uvnitř prvku `<div class="list-group">` a přímo nad uzavírací značku `</a>` přidejte následující řádek, který spolu se zbývajícími daty článku zobrazí i `comment`:

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Otevřete soubor _modules/articles/client/views/admin/form-article.client.view.html_.

Vyhledejte prvek `<div class="form-group">` obsahující tlačítko pro odeslání, který vypadá přibližně takto:

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Přímo nad tuto značku přidejte další prvek `<div class="form-group">`, který lidem umožní upravit pole `comment`. Váš nový prvek by měl vypadat přibližně takto:

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Místní test provedených změn

Uložte všechny provedené změny.

V místním okně terminálu znovu otestujte provedené změny v provozním režimu.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

V prohlížeči přejděte na adresu `http://localhost:8443` a ujistěte se, že jste přihlášeni.

Vyberte **Admin > Manage Articles** (Správa > Správa článků) a pak výběrem tlačítka **+** přidejte článek.

Nyní se zobrazí nové textové pole `Comment`.

![Přidané pole komentáře ke článkům](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

V terminálu zastavte Node.js zadáním `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

V místním okně terminálu potvrďte změny v Gitu a potom nasdílejte změny kódu do Azure.

```bash
git commit -am "added article comment"
git push azure main
```

Až `git push` to bude hotové, přejděte do aplikace Azure a vyzkoušejte nové funkce.

![Změny modelu a databáze publikované v Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Pokud jste dříve přidali články, můžete je stále zobrazit. Stávající data ve službě Cosmos DB nebudou ztracena. Kromě toho vaše aktualizace ovlivňují schéma dat a nechávají existující data netknutá.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů 

::: zone pivot="platform-windows"  

Zatímco je vaše aplikace Node.js spuštěná v Azure App Service, můžete směrovat protokoly konzoly do svého terminálu. Tímto způsobem můžete získat stejné diagnostické zprávy, které vám pomůžou ladit chyby aplikace.

Chcete-li spustit streamování protokolů, použijte [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) příkaz v Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

Po spuštění streamování protokolů aktualizujte aplikaci Azure v prohlížeči, abyste získali nějaký webový provoz. Nyní se zobrazí protokoly konzoly směrované do terminálu.

Streamování můžete kdykoli zastavit zadáním `Ctrl+C`. 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>Správa aplikace Azure

Pokud si chcete zobrazit aplikaci, kterou jste vytvořili, přejděte na [Azure Portal](https://portal.azure.com) .

V nabídce vlevo klikněte na **App Services** a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Ve výchozím nastavení se na portálu zobrazí stránka s **přehledem** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Vytvoření databáze MongoDB v Azure
> * Připojení aplikace Node.js k MongoDB
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat protokoly z Azure do terminálu
> * Spravovat aplikaci na webu Azure Portal

V dalším kurzu se dozvíte, jak namapovat na aplikaci vlastní název DNS.

> [!div class="nextstepaction"] 
> [Mapování existujícího vlastního názvu DNS na Azure App Service](app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace aplikace Node.js](configure-language-nodejs.md)
