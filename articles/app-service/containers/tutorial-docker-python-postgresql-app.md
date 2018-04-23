---
title: Vytvoření webové aplikace Docker Python využívající databázi PostgreSQL v Azure | Microsoft Docs
description: Zjistěte, jak v Azure zprovoznit aplikaci Docker Python s připojením k databázi PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 673564ef7b5ff02a3809154a4dcf1669c2ed798b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Vytvoření webové aplikace Docker Python využívající databázi PostgreSQL v Azure

Web App for Containers je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak vytvořit základní webovou aplikaci Docker Python v Azure. Tuto aplikaci připojíte k databázi PostgreSQL. Až budete hotovi, budete mít aplikaci Python Flask spuštěnou v kontejneru Dockeru ve službě [App Service v Linuxu](app-service-linux-intro.md).

![Aplikace Docker Python Flask ve službě App Service v Linuxu](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit aplikaci Python k PostgreSQL
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Spravovat aplikaci na webu Azure Portal

Podle kroků v tomto článku můžete postupovat v systému macOS. Pokyny pro Linux a Windows jsou ve většině případů stejné, ale odlišnosti nejsou v tomto kurzu podrobně popsané.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

1. [Nainstalovat Git](https://git-scm.com/).
1. [Nainstalovat Python](https://www.python.org/downloads/).
1. [Nainstalovat a spustit PostgreSQL](https://www.postgresql.org/download/)
1. [Nainstalovat Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Test místní instalace PostgreSQL a vytvoření databáze

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

Spuštěním následujících příkazů naklonujte ukázkové úložiště a přejděte do verze *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Toto ukázkové úložiště obsahuje aplikaci [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Spuštění aplikace

> [!NOTE] 
> V pozdějším kroku tento proces zjednodušíte sestavením kontejneru Dockeru pro použití s produkční databází.

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

![Místně spuštěná aplikace Python Flask](./media/tutorial-docker-python-postgresql-app/local-app.png)

Ukázková aplikace Flask ukládá uživatelská data v databázi. Pokud úspěšně zaregistrujete uživatele, vaše aplikace zapíše data do místní databáze PostgreSQL.

Server Flask můžete kdykoli zastavit zadáním Ctrl+C v terminálu. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Vytvoření produkční databáze PostgreSQL

V tomto kroku vytvoříte databázi PostgreSQL v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

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

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Vytvoření pravidla brány firewall pro server Azure Database for PostgreSQL

Spuštěním následujícího příkazu Azure CLI povolte přístup k databázi ze všech IP adres. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
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

## <a name="connect-your-python-flask-application-to-the-database"></a>Připojení aplikace Python Flask k databázi

V tomto kroku připojíte ukázkovou aplikaci Python Flask k serveru Azure Database for PostgreSQL, který jste vytvořili.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Vytvoření prázdné databáze a nastavení nového uživatele databázové aplikace

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

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Místní test aplikace proti databázi Azure PostgreSQL

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

![Místně spuštěná aplikace Python Flask](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Spuštění aplikace z kontejneru Dockeru

Sestavte image kontejneru Dockeru.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker zobrazí potvrzení, že úspěšně vytvořil kontejner.

```bash
Successfully built 7548f983a36b
```

Do kořenového adresáře úložiště přidejte soubor proměnných prostředí _db.env_ a pak do něj přidejte následující proměnné prostředí databáze. Aplikace se připojí k produkční databázi Azure Database for PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Spusťte aplikaci z kontejneru Dockeru. Následující příkaz určí soubor proměnných prostředí a namapuje výchozí port Flask 5000 na místní port 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Výstup je podobný předchozímu příkladu. Počáteční migraci databáze však již není potřeba provádět a proto se přeskočí.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Databáze již obsahuje registraci, kterou jste vytvořili dříve.

![Místně spuštěná aplikace Python Flask v kontejneru Dockeru](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Nahrání kontejneru Dockeru do registru kontejnerů

V tomto kroku nahrajete kontejner Dockeru do registru kontejnerů. Použijte službu Azure Container Registry. Můžete však použít i jiné oblíbené služby, jako je například Docker Hub.

### <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

V následujícím příkazu pro vytvoření registru kontejnerů nahraďte *\<registry_name>* libovolným jedinečným názvem registru kontejnerů Azure.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Výstup

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Načtení přihlašovacích údajů registru pro odesílání a stahování imagí Dockeru

Pokud chcete zobrazit přihlašovací údaje registru, zapněte nejprve režim správce.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Zobrazí se dvě hesla. Poznamenejte si uživatelské jméno a první heslo.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Nahrání kontejneru Dockeru do služby Azure Container Registry

Přihlaste se ke svému registru. Po zobrazení výzvy zadejte heslo, které jste načetli.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Odešlete svou image Dockeru do registru.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Nasazení aplikace Docker Python Flask do Azure

V tomto kroku nasadíte svou aplikaci Python Flask v kontejneru Dockeru do služby Azure App Service.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Pomocí příkazu [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) vytvořte v plánu služby App Service *myAppServicePlan* webovou aplikaci.

Tato webová aplikace poskytuje prostor hostitele pro nasazení kódu a adresu URL, na které můžete nasazenou aplikaci zobrazit. Pomocí  vytvořte webovou aplikaci.

V následujícím příkazu nahraďte zástupný text *\<app_name>* jedinečným názvem aplikace. Tento název je součástí výchozí adresy URL webové aplikace, proto musí být mezi všemi aplikacemi ve službě Azure App Service jedinečný.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

Po vytvoření webové aplikace se v rozhraní příkazového řádku Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Konfigurace proměnných prostředí databáze

V dřívější části tohoto kurzu jste definovali proměnné prostředí pro připojení k vaší databázi PostgreSQL.

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Následující příklad určí jako nastavení aplikace podrobnosti o připojení k databázi. Pomocí proměnné *PORT* také mapuje port 5000 z vašeho kontejneru Dockeru na příjem provozu HTTP na portu 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Konfigurace nasazení kontejneru Dockeru

App Service dokáže automaticky stáhnout a spustit kontejner Dockeru.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Po každé aktualizaci kontejneru Dockeru nebo změně nastavení aplikaci restartujte. Restartování zajistí, že se použijí všechna nastavení a z registru se stáhne nejnovější kontejner.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure 

Ve webovém prohlížeči přejděte k nasazené webové aplikaci. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Načtení webové aplikace trvá déle, protože po změně konfigurace kontejneru je kontejner potřeba stáhnout a spustit.

Zobrazí se dříve zaregistrovaní hosté, kteří se uložili do produkční databáze Azure v předchozím kroku.

![Místně spuštěná aplikace Python Flask v kontejneru Dockeru](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Blahopřejeme!** Teď máte ve službě Azure App Service spuštěnou aplikaci Python Flask v kontejneru Dockeru.

## <a name="update-data-model-and-redeploy"></a>Aktualizace datového modelu a opětovné nasazení

V tomto kroku prostřednictvím aktualizace modelu Guest přidáte ke každé registraci na událost několik účastníků.

Pomocí následujícího příkazu git si zarezervujte verzi *0.2-migration*:

```bash
git checkout tags/0.2-migration
```

V této verzi už jsou provedené nezbytné změny zobrazení, kontrolerů a modelu. Zahrnuje také migraci databáze vygenerovanou přes *alembic* (`flask db migrate`). Všechny provedené změny můžete zobrazit následujícím příkazem git:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Místní test provedených změn

Spusťte následující příkazy a místně otestujte provedené změny spuštěním serveru Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

V prohlížeči přejděte na adresu http://localhost:5000 a zobrazte změny. Vytvořte testovací registraci.

![Místně spuštěná aplikace Python Flask v kontejneru Dockeru](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

Sestavte novou image Dockeru, odešlete ji do registru kontejnerů a restartujte aplikaci.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Přejděte do webové aplikace Azure a znovu vyzkoušejte nové funkce. Vytvořte další registraci na událost.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplikace Docker Python Flask ve službě Azure App Service](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Správa webové aplikace Azure

Přejděte na web [Azure Portal](https://portal.azure.com) k webové aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Na začátku portál zobrazí stránku **Přehled** vaší webové aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Další kroky

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
