---
title: Vytvoření webové aplikace Python a PostgreSQL v Azure App Service | Microsoft Docs
description: Zjistěte, jak v Azure spustit aplikaci Python řízenou daty s připojením k databázi PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435734"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Vytvoření webové aplikace Docker Python využívající databázi PostgreSQL v Azure

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento kurz ukazuje postup vytvoření webové aplikace řízené daty v Pythonu pomocí PostgreSQL jako back-endové databáze. Až budete hotoví, budete mít aplikaci Python Flask spuštěnou v kontejneru Dockeru ve službě App Service v Linuxu.

![Aplikace Docker Python Flask ve službě App Service v Linuxu](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit aplikaci Python k PostgreSQL
> * Nasadit aplikaci do Azure
> * Zobrazit diagnostické protokoly
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Spravovat aplikaci na webu Azure Portal

Podle kroků v tomto článku můžete postupovat v systému macOS. Pokyny pro Linux a Windows jsou ve většině případů stejné, ale odlišnosti nejsou v tomto kurzu podrobně popsané.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

1. [Nainstalovat Git](https://git-scm.com/).
1. [Nainstalovat Python](https://www.python.org/downloads/).
1. [Nainstalovat a spustit PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Test místní instalace PostgreSQL a vytvoření databáze

V místním okně terminálu spusťte příkaz `psql` a připojte se ke svému místnímu serveru PostgreSQL.

```bash
sudo -u postgres psql postgres
```

Pokud se zobrazí chybová zpráva podobná zprávě `unknown user: postgres`, vaše instalace PostgreSQL je možná nakonfigurovaná uživatelským jménem, pod kterým jste přihlášení. Zkuste místo toho použít tento příkaz.

```bash
psql postgres
```

Pokud se úspěšně připojíte, vaše databáze PostgreSQL je spuštěná. Pokud ne, podle pokynů pro váš operační systém na stránce [Downloads – PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Ke stažení – Distribuce jádra PostgreSQL) se ujistěte, že je vaše místní databáze PostgreSQL spuštěná.

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

Spuštěním následujících příkazů naklonujte ukázkové úložiště.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

Toto ukázkové úložiště obsahuje aplikaci [Flask](http://flask.pocoo.org/).

### <a name="run-the-app-locally"></a>Místní spuštění aplikace

Nainstalujte požadované balíčky a spusťte aplikaci.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![Místně spuštěná aplikace Python Flask](./media/tutorial-python-postgresql-app/local-app.png)

Ukázková aplikace Flask ukládá uživatelská data v databázi. Pokud úspěšně zaregistrujete uživatele, vaše aplikace zapíše data do místní databáze PostgreSQL.

Server Flask můžete kdykoli zastavit zadáním Ctrl+C v terminálu.

## <a name="create-a-production-postgresql-database"></a>Vytvoření produkční databáze PostgreSQL

V tomto kroku vytvoříte databázi PostgreSQL v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Pomocí příkazu [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) ve službě Cloud Shell vytvořte server PostgreSQL.

V následujícím ukázkovém příkazu nahraďte *\<postgresql_name>* jedinečným názvem serveru a *\<admin_username>* a *\<admin_password>* nahraďte přihlašovacími údaji požadovaného uživatele. Přihlašovací údaje uživatele jsou přihlašovací údaje k účtu správce databáze. Název serveru se používá jako součást koncového bodu PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), takže musí být jedinečný v rámci všech serverů v Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
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
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Zapamatujte si \<admin_username> a \<admin_password>, abyste je mohli později použít. Budete je potřebovat, abyste se mohli přihlásit k serveru Postgre a jeho databázím.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Vytvoření pravidel brány firewall pro server PostgreSQL

Spusťte v Cloud Shellu následující příkazy Azure CLI, abyste umožnili přístup k databázi z prostředků Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Toto nastavení umožňuje síťová připojení ze všech IP adres v síti Azure. Pro produkční prostředí zkuste nakonfigurovat co nejvíce omezující pravidla firewallu tak, že [použijete jen odchozí IP adresy, které používá vaše aplikace](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Spusťte v Cloud Shellu tento příkaz znovu, ale nahraďte *\<your_ip_address>* [vaší místní IP adresou IPv4](http://www.whatsmyip.org/), abyste měli přístup k databázi.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Připojení aplikace v Pythonu k produkční databázi

V tomto kroku připojíte ukázkovou aplikaci Flask k serveru Azure Database for PostgreSQL, který jste vytvořili.

### <a name="create-empty-database-and-user-access"></a>Vytvoření prázdné databáze a přístupu uživatelů

V okně místního terminálu se připojte k databázi tak, že spustíte příkaz uvedený níže. Když se zobrazí výzva k zadání hesla správce, použijte stejné heslo, které jste zadali v části [Vytvoření serveru Azure Database for PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Stejně jako v místním serveru Postgre vytvořte databázi a uživatele na serveru Azure Postgre.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

> [!NOTE]
> Nejlepším postupem je vytvořit uživatele databáze s oprávněními omezenými jen na určité aplikace namísto uživatele s rolí správce. V tomto příkladu má uživatel `manager` úplná oprávnění _jen_ k databázi `eventregistration`.

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

![Místně spuštěná aplikace Python Flask](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Nasazení do Azure

V tomto kroku nasadíte aplikaci Python připojenou k Postgre do služby Azure App Service.

### <a name="configure-repository"></a>Konfigurace úložiště

Když se v kořenu úložiště nachází soubor _application.py_, modul nasazení Git ve službě App Service vyvolá automatizaci `pip`. V tomto kurzu necháme modul nasazení, aby automatizaci spustil za vás. V okně místního terminálu přejděte do kořene úložiště, vytvořte zkušební soubor _application.py_ a potvrďte své změny.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

V dřívější části tohoto kurzu jste definovali proměnné prostředí pro připojení k vaší databázi PostgreSQL.

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v Cloud Shellu.

Následující příklad určí jako nastavení aplikace podrobnosti o připojení k databázi. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
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

### <a name="configure-entry-point"></a>Konfigurace vstupního bodu

Integrovaná image standardně hledá v kořenovém adresáři soubor _wsgi.py_ nebo _application.py_, který bývá vstupním bodem, ale váš vstupní bod je _app/app.py_. Soubor _application.py_, který jste přidali dříve, je prázdný a nic nedělá.

Spusťte v Cloud Shellu příkaz [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set), aby se nastavil vlastní spouštěcí skript.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

Parametr `--startup-file` přijímá vlastní příkaz nebo cestu k souboru, který vlastní příkaz obsahuje. Vlastní příkaz by měl mít následující formát:

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

Možnost `--chdir` se ve vlastním příkazu vyžaduje, když vstupní bod není v kořenovém adresáři, a `<subdirectory>` je podadresář. `<module>` je název souboru _.py_ a `<variable>` je proměnná v modulu, která představuje vaši webovou aplikaci.

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte do nasazené webové aplikace. Když se o aplikaci žádá poprvé, spuštění nějakou dobu trvá, protože se musí stáhnout a spustit kontejner. Pokud stránce vyprší časový limit nebo se na ní zobrazí chybová zpráva, počkejte několik minut a stránku aktualizujte.

```bash
http://<app_name>.azurewebsites.net
```

Zobrazí se dříve zaregistrovaní hosté, kteří se uložili do produkční databáze Azure v předchozím kroku.

![Aplikace Python Flash spuštěná v Azure](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Blahopřejeme!** Spustili jste pythonovou aplikaci ve službě App Service for Linux.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

Vzhledem k tomu, že pythonová aplikace běží v kontejneru, služba App Service v Linuxu vám umožňuje zobrazit protokoly konzoly, které se v daném kontejneru vygenerovaly. Pokud chcete tyto protokoly najít, přejděte na tuto adresu URL:

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Měly by se zobrazit dva objekty JSON, oba s vlastností `href`. Jedna vlastnost `href` vede na protokoly konzoly Dockeru (končí na `_docker.log`) a druhé `href` vede na protokoly konzoly vygenerované z kontejneru Pythonu. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Pokud chcete, můžete přejít na protokoly tak, že hodnotu `href` zkopírujete do okna prohlížeče. Protokoly se nestreamují, proto může docházet k určité prodlevě. Pokud si chcete zobrazit nové protokoly, aktualizujte stránku prohlížeče.

## <a name="update-data-model-and-redeploy"></a>Aktualizace datového modelu a opětovné nasazení

V tomto kroku přidáte ke každé registraci k události několik účastníků tak, že aktualizujete model `Guest`, a pak aktualizaci znovu nasadíte do Azure.

Pomocí následujícího příkazu git si v místním okně terminálu zarezervujte soubory z větve `modelChange`:

```bash
git checkout origin/modelChange -- .
```

Tato rezervace už provádí nezbytné změny modelu, zobrazení a kontrolerů. Zahrnuje také migraci databáze vygenerovanou přes *alembic* (`flask db migrate`). Všechny změny si můžete zobrazit následujícím příkazem git:

```bash
git diff master origin/modelChange
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

![Místně spuštěná aplikace Python Flask v kontejneru Dockeru](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

V místním okně terminálu potvrďte všechny změny v Gitu a potom nasdílejte změny kódu do Azure.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Přejděte do webové aplikace Azure a znovu vyzkoušejte nové funkce. Nezapomeňte aktualizovat stránku.

```bash
http://<app_name>.azurewebsites.net
```

![Aplikace Docker Python Flask ve službě Azure App Service](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Správa webové aplikace na portálu Azure Portal

Přejděte na web [Azure Portal](https://portal.azure.com) k webové aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Na začátku portál zobrazí stránku **Přehled** vaší webové aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit aplikaci Python k PostgreSQL
> * Nasadit aplikaci do Azure
> * Zobrazit diagnostické protokoly
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Spravovat aplikaci na webu Azure Portal

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Konfigurace integrované image Pythonu](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service-web-tutorial-custom-domain.md)

