---
title: Vytvoření webové aplikace Python a PostgreSQL v Azure | Microsoft Docs
description: Zjistěte, jak v Azure zprovoznit aplikaci Python s připojením k databázi PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: f53ffdaa6c99d63bdab91f30ffa6b2b182c53848
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>Kurz: Vytvoření webové aplikace Python s připojením k databázi PostgreSQL v Azure

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení služby App Service v _Linuxu_ je popsané v tématu[Vytvoření webové aplikace Docker Python a PostgreSQL v Azure](./containers/tutorial-docker-python-postgresql-app.md).
>

[Azure App Service ](app-service-web-overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak vytvořit základní webovou aplikaci Python v Azure. Tuto aplikaci připojíte k databázi PostgreSQL. Až budete hotovi, budete mít aplikaci Python Flask spuštěnou ve službě App Service.

![Aplikace Python Flask ve službě App Service v Linuxu](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit aplikaci Python k MySQL
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Spravovat aplikaci na webu Azure Portal

V systému macOS mžete postupovat podle kroků v tomto kurzu. Pokyny pro Linux a Windows jsou ve většině případů stejné, ale odlišnosti nejsou v tomto kurzu podrobně popsané.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

1. [Nainstalovat Git](https://git-scm.com/).
1. [Nainstalovat Python](https://www.python.org/downloads/).
1. [Nainstalovat a spustit PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Otestování místní instalace PostgreSQL a vytvoření databáze

Otevřete okno terminálu a spuštěním příkazu `psql` se připojte ke svému místnímu serveru PostgreSQL.

```bash
sudo -u postgres psql
```

Pokud se úspěšně připojíte, vaše databáze PostgreSQL je spuštěná. Pokud ne, podle postupu na stránce [Downloads – PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Ke stažení – Distribuce jádra PostgreSQL) se ujistěte, že je vaše místní databáze PostgreSQL spuštěná.

Vytvořte databázi *eventregistration* a nastavte samostatného uživatele databáze *manager* s heslem *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Zadáním příkazu `\q` ukončete klienta PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Vytvoření místní aplikace Python Flask

V tomto kroku nastavíte místní projekt Python Flask.

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Otevřete okno terminálu a pomocí příkazu `CD` přejděte do pracovního adresáře.

Spuštěním následujících příkazů naklonujte ukázkové úložiště a vraťte se k potvrzení počáteční aplikace (před `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

Toto ukázkové úložiště obsahuje aplikaci [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Spuštění aplikace

Nainstalujte požadované balíčky a spusťte aplikaci.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Po úplném načtení aplikace se zobrazí zpráva podobná této:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

V prohlížeči přejděte na `http://localhost:5000`. Klikněte na **Register!** (Zaregistrovat) a vytvořte testovacího uživatele.

![Místně spuštěná aplikace Python Flask](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Ukázková aplikace Flask ukládá uživatelská data v databázi. Pokud úspěšně zaregistrujete uživatele, vaše aplikace zapíše data do místní databáze PostgreSQL.

Server Flask můžete kdykoli zastavit zadáním Ctrl+C v terminálu. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Vytvoření PostgreSQL v Azure

V tomto kroku vytvoříte databázi PostgreSQL v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Vytvoření serveru PostgreSQL

Vytvořte server PostgreSQL pomocí příkazu [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

V následujícím příkazu nahraďte zástupný symbol *\<postgresql_name>* jedinečným názvem serveru a zástupný symbol *\<admin_username>* uživatelským jménem. Název serveru se používá jako součást koncového bodu PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), takže musí být jedinečný v rámci všech serverů v Azure. Uživatelské jméno je určené pro počáteční uživatelský účet správce databáze. Zobrazí se výzva k výběru hesla pro tohoto uživatele.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

Po vytvoření serveru Azure Database for PostgreSQL se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Spuštěním následujícího příkazu Azure CLI povolte přístup k databázi ze všech IP adres. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0 --name AllowAzureIPs
```

Azure CLI potvrdí vytvoření pravidla brány firewall a zobrazí výstup jako v následujícím příkladu:

```json
{
  "endIpAddress": "0.0.0.0",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAzureIPs",
  "name": "AllowAzureIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

### <a name="create-a-production-database-and-user"></a>Vytvoření produkční databáze a uživatele

Vytvořte uživatele databáze s přístupem pouze k jedné databázi. Pomocí těchto přihlašovacích údajů zabráníte tomu, aby aplikace měla úplný přístup k serveru.

Připojte se k databázi (zobrazí se výzva k zadání vašeho hesla správce).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Vytvořte databázi a uživatele z rozhraní příkazového řádku PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

### <a name="test-app-locally-with-azure-postgresql"></a>Místní testování aplikace s Azure PostgreSQL

Když se teď vrátíte do složky *app* naklonovaného úložiště GitHub, můžete spustit aplikaci Python Flask tak, že aktualizujete proměnné prostředí databáze.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Po úplném načtení aplikace se zobrazí zpráva podobná této:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

V prohlížeči přejděte na http://localhost:5000. Klikněte na **Register!** (Zaregistrovat) a vytvořte testovací registraci. Právě zapisujete data do databáze v Azure.

![Místně spuštěná aplikace Python Flask](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Nasazení do Azure

V tomto kroku nasadíte aplikaci Python připojenou k PostgreSQL do služby Azure App Service.

Úložiště Git již obsahuje následující soubory potřebné ke spouštění webové aplikace Flask ve službě App Service:

- `.deployment`: určuje vlastní skript nasazení, který se má spustit.
- `deploy.cmd`: skript nasazení. V něm je spuštěný příkaz `pip install`.
- `web.config`: určuje skript vstupního bodu ke spuštění v `httpPlatformHandler` ve službě IIS.
- `run_waitress_server.py`: skript vstupního bodu. Spustí webovou aplikaci Flask na serveru [`waitress`](https://docs.pylonsproject.org/projects/waitress).

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Instalace Pythonu

V tomto kroku nainstalujete Python 3.6.2 s [rozšířením webu](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) ve službě App Service. Použijete přihlašovací údaje nakonfigurované v [Konfigurace uživatele nasazení](#configure-a-deployment-user) pro ověření proti koncovému bodu REST.

V Cloud Shellu spuštěním následujícího příkazu získáte informace o balíčku Python 3.6.2. Nahraďte *\<deployment_user>* nakonfigurovaným uživatelským jménem nasazení a *\<app_name>* názvem aplikace. Po zobrazení výzvy použijte nakonfigurované heslo nasazení.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

V Cloud Shellu spuštěním následujícího příkazu nainstalujte balíček Python. Nahraďte *\<deployment_user>* nakonfigurovaným uživatelským jménem nasazení a *\<app_name>* názvem aplikace. Po zobrazení výzvy použijte nakonfigurované heslo nasazení.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

Z výstupu příkazu je zřejmé, že je balíček Python nainstalován v umístění `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Konfigurace nastavení databáze

V dřívější části tohoto kurzu jste definovali proměnné prostředí pro připojení k vaší databázi PostgreSQL.

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Následující příklad určí jako nastavení aplikace podrobnosti o připojení k databázi. Nahraďte *\<app_name>* názvem aplikace a *\<postgresql_name>* názvem serveru PostgreSQL.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure 

Ve webovém prohlížeči přejděte k nasazené webové aplikaci. 

```bash 
http://<app_name>.azurewebsites.net 
```

Zobrazí se dříve zaregistrovaní hosté, kteří se uložili do produkční databáze Azure v předchozím kroku.

![Místně spuštěná aplikace Python Flask](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Blahopřejeme!** Spustili jste aplikaci Python Flask ve službě Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Aktualizace datového modelu a opětovné nasazení

V tomto kroku prostřednictvím aktualizace modelu `Guest` přidáte ke každé registraci na událost několik účastníků.

Rezervujte soubory označené potvrzením `modelChange`:

```bash
git checkout modelChange -- *
```

V této verzi už jsou provedené nezbytné změny zobrazení, kontrolerů a modelu. Zahrnuje také migraci databáze vygenerovanou přes *alembic* (`flask db migrate`). Můžete zobrazit změny všech souborů v [zobrazení potvrzení GitHubu](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e).

### <a name="test-your-changes-locally"></a>Místní test provedených změn

Spusťte následující příkazy a místně otestujte provedené změny spuštěním serveru Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

V prohlížeči přejděte na adresu http://localhost:5000 a zobrazte změny. Vytvořte testovací registraci.

![Místně spuštěná aplikace Python Flask](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

V místním okně terminálu potvrďte všechny změny v Gitu a potom nasdílejte změny kódu do Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Přejděte do webové aplikace Azure a znovu vyzkoušejte nové funkce. Vytvořte další registraci na událost.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplikace Python Flask ve službě Azure App Service](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Správa webové aplikace Azure

Přejděte na web [Azure Portal](https://portal.azure.com) k webové aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

Na začátku portál zobrazí stránku **Přehled** vaší webové aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](app-service-web-tutorial-custom-domain.md)
