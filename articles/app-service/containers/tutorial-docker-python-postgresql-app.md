---
title: Vytvoření webové aplikace Python a PostgreSQL v Azure App Service | Microsoft Docs
description: Zjistěte, jak v Azure spustit aplikaci Python řízenou daty s připojením k databázi PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 20b549914daf71c0d23235b5c20ebb6f14367471
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172030"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Vytvoření webové aplikace Docker Python využívající databázi PostgreSQL v Azure

Web App for Containers je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento kurz ukazuje postup vytvoření webové aplikace řízené daty v Pythonu pomocí PostgreSQL jako back-endové databáze. Až budete hotovi, budete mít aplikaci Python Flask spuštěnou v kontejneru Dockeru ve službě [App Service v Linuxu](app-service-linux-intro.md).

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

V místním okně terminálu spusťte příkaz `psql` a připojte se ke svému místnímu serveru PostgreSQL.

```bash
sudo -u postgres psql
```

Pokud se úspěšně připojíte, vaše databáze PostgreSQL je spuštěná. Pokud ne, podle postupu na stránce [Downloads – PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Ke stažení – Distribuce jádra PostgreSQL) se ujistěte, že je vaše místní databáze PostgreSQL spuštěná.

Vytvořte databázi *eventregistration* a nastavte samostatného uživatele databáze *manager* s heslem *supersecretpass*.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Zadáním příkazu `\q` ukončete klienta PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-app"></a>Vytvoření místní aplikace v Pythonu

V tomto kroku nastavíte místní projekt Python Flask.

### <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Otevřete okno terminálu a pomocí příkazu `CD` přejděte do pracovního adresáře.

Spuštěním následujících příkazů naklonujte ukázkové úložiště a přejděte do verze *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Toto ukázkové úložiště obsahuje aplikaci [Flask](http://flask.pocoo.org/). 

### <a name="run-the-app-locally"></a>Místní spuštění aplikace

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

Pomocí příkazu [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) ve službě Cloud Shell vytvořte server PostgreSQL.

V následujícím ukázkovém příkazu nahraďte *\<postgresql_name>* jedinečným názvem serveru a *\<admin_username>* a *\<admin_password>* nahraďte přihlašovacími údaji požadovaného uživatele. Název serveru se používá jako součást koncového bodu PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), takže musí být jedinečný v rámci všech serverů v Azure. Přihlašovací údaje uživatele jsou přihlašovací údaje k uživatelskému účtu správce databáze. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
```

Po vytvoření serveru Azure Database for PostgreSQL se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "<admin_username>",
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

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>Vytvoření pravidla brány firewall pro server PostgreSQL

Spuštěním následujícího příkazu Azure CLI ve službě Cloud Shell povolte přístup k databázi ze všech IP adres. Pokud je jako počáteční i koncová IP adresa nastavená hodnota `0.0.0.0`, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

Spusťte ve službě Cloud Shell tento příkaz znovu, ale nahraďte *\<you_ip_address>* [vaší místní IP adresou IPv4](https://whatismyipaddress.com/), abyste měli přístup k databázi ze svého místního počítače. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Připojení aplikace v Pythonu k produkční databázi

V tomto kroku připojíte ukázkovou aplikaci Flask k serveru Azure Database for PostgreSQL, který jste vytvořili.

### <a name="create-empty-database-and-user-access"></a>Vytvoření prázdné databáze a přístupu uživatelů

Spuštěním příkazu `psql` ve službě Cloud Shell se připojte k databázi. Když se zobrazí výzva k zadání hesla správce, použijte stejné heslo, které jste zadali v části [Vytvoření serveru Azure Database for PostgreSQL](#create-an-azure-database-for-postgresql-server).

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

### <a name="test-app-connectivity-to-production-database"></a>Test připojení aplikace k provozní databázi

V místním okně terminálu spusťte následující příkazy, aby se spustila migrace databáze Flask a server Flask.

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

## <a name="upload-app-to-a-container-registry"></a>Nahrání aplikace do registru kontejneru

V tomto kroku vytvoříte image Dockeru a nahrajete ji do služby Azure Container Registry. Můžete použít i oblíbené registry jako Centrum Dockeru.

### <a name="build-the-docker-image-and-test-it"></a>Sestavení a test image Dockeru

Sestavte v místním okně terminálu image Dockeru.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker zobrazí potvrzení, že image úspěšně vytvořil.

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

Spusťte image místně v kontejneru Dockeru. Následující příkaz určí soubor proměnných prostředí a namapuje výchozí port Flask 5000 na místní port 5000.

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

Když jste ověřili, že kontejner místně funguje, odstraňte _db.env_. Ve službě Azure App Service použijete nastavení aplikace k definování proměnných prostředí.  

### <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Spuštěním následujícího příkazu ve službě Cloud Shell vytvořte v Azure Container Registry registr kontejneru a *\<registry_name >* nahraďte jedinečným názvem registru.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>Získání přihlašovacích údajů registru

Spuštěním následujících příkazů ve službě Cloud Shell získáte přihlašovací údaje registru. Potřebujete je k nahrání a stáhnutí imagí.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Ve výstupu se zobrazí dvě hesla. Poznamenejte si uživatelské jméno (což je ve výchozím nastavení název registru) a první heslo.

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

### <a name="upload-docker-image-to-registry"></a>Nahrání image Dockeru do registru kontejneru

Příkazem `docker` se z místního okna terminálu přihlaste do nového registru kontejneru. Po zobrazení výzvy zadejte heslo, které jste načetli.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Odešlete svou image Dockeru do registru.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>Vytvoření webové aplikace z nahrané image

V tomto kroku vytvoříte ve službě Azure App Service aplikaci a nakonfigurujete ji tak, aby použila image Dockeru nahranou ve službě Azure Container Registry.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Pomocí příkazu [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) ve službě Cloud Shell vytvořte v plánu služby App Service *myAppServicePlan* webovou aplikaci.

V následujícím příkazu nahraďte zástupný text *\<app_name>* jedinečným názvem aplikace. Tento název je součástí výchozí adresy URL webové aplikace, proto musí být mezi všemi aplikacemi ve službě Azure App Service jedinečný.

```azurecli-interactive
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

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

V dřívější části tohoto kurzu jste definovali proměnné prostředí pro připojení k vaší databázi PostgreSQL.

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Následující příklad určí jako nastavení aplikace podrobnosti o připojení k databázi. Používá také proměnnou *WEBSITES_PORT* pro port kontejneru 5000, která kontejneru umožňuje přijímat provoz protokolu HTTP na portu 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>Konfigurace nasazení vlastního kontejneru

I když jste už název image kontejneru zadali, stále je potřeba určit vlastní adresu URL registru a přihlašovací údaje uživatele. Spusťte ve službě Cloud Shell příkaz [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Restartujte aplikaci ve službě Cloud Shell. Restartování zajistí, že se použijí všechna nastavení a z registru se stáhne nejnovější kontejner.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure 

Přejděte do nasazené webové aplikace. 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> První spuštění webové aplikace nějaký čas trvá, protože se musí stáhnout a spustit kontejner. Pokud se po delší době zobrazí chyba, stránku obnovte.

Zobrazí se dříve zaregistrovaní hosté, kteří se uložili do produkční databáze Azure v předchozím kroku.

![Místně spuštěná aplikace Python Flask v kontejneru Dockeru](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Blahopřejeme!** Ve službě Web App for Containers vám běží aplikace v Pythonu.

## <a name="update-data-model-and-redeploy"></a>Aktualizace datového modelu a opětovné nasazení

V tomto kroku prostřednictvím aktualizace modelu `Guest` přidáte ke každé registraci na událost několik účastníků.

Pomocí následujícího příkazu git si v místním okně terminálu zarezervujte verzi *0.2-migration*:

```bash
git checkout tags/0.2-migration
```

V této verzi už jsou provedené nezbytné změny modelu, zobrazení a kontrolerů. Zahrnuje také migraci databáze vygenerovanou přes *alembic* (`flask db migrate`). Všechny provedené změny můžete zobrazit následujícím příkazem git:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Místní test provedených změn

Spusťte následující příkazy v místním okně terminálu a místně otestujte provedené změny spuštěním serveru Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

V prohlížeči přejděte na adresu http://localhost:5000 a zobrazte změny. Vytvořte testovací registraci.

![Místně spuštěná aplikace Python Flask v kontejneru Dockeru](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

Sestavte v místním okně terminálu novou image Dockeru a nahrajte ji do svého registru kontejneru.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

Ve službě Cloud Shell restartujte aplikaci, abyste se přesvědčili, že se z registru kontejneru stáhne nejnovější kontejner.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
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
