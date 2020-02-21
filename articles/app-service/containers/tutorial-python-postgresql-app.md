---
title: 'Kurz: aplikace pro Linux v Pythonu s Postgre'
description: Naučte se, jak získat aplikaci pro Linux v Pythonu pracující v Azure App Service s připojením k databázi PostgreSQL v Azure. V tomto kurzu se používá Django.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523920"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Kurz: spuštění webové aplikace v Pythonu (Django) s PostgreSQL v Azure App Service

[Azure App Service ](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak připojit datovou webovou aplikaci v Pythonu Django k databázi Azure Database for PostgreSQL a nasadit a spustit aplikaci na Azure App Service.

![Webová aplikace Python Django v Azure App Service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření databáze Azure Database for PostgreSQL a připojení webové aplikace k ní
> * Nasazení webové aplikace do Azure App Service
> * Zobrazení diagnostických protokolů
> * Správa webové aplikace v Azure Portal

Můžete postupovat podle kroků v tomto článku v macOS, Linux nebo Windows. Postup je ve většině případů podobný, i když rozdíly nejsou v tomto kurzu podrobně popsané. Většina příkladů níže používá `bash` okno terminálu na platformě Linux. 

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Nainstalujte [Git](https://git-scm.com/).
- Nainstalujte [Python 3](https://www.python.org/downloads/).
- Nainstalujte a spusťte [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Test instalace PostgreSQL a vytvoření databáze

Nejdřív se připojte k místnímu serveru PostgreSQL a vytvořte databázi: 

V okně místního terminálu spusťte `psql` a připojte se k místnímu serveru PostgreSQL jako vestavěný `postgres` uživatel.

```bash
sudo su - postgres
psql
```
nebo
```PowerShell
psql -U postgres
```

Pokud se úspěšně připojíte, vaše databáze PostgreSQL je spuštěná. Pokud ne, podle pokynů pro váš operační systém na stránce [Downloads – PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Ke stažení – Distribuce jádra PostgreSQL) se ujistěte, že je vaše místní databáze PostgreSQL spuštěná.

Vytvořte novou databázi s názvem *pollsdb*a nastavte uživatele databáze s názvem *Manager* s heslem *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Vytvoření a spuštění místní aplikace v Pythonu

Dále nastavte a spusťte ukázkovou webovou aplikaci Python Django.

Ukázkové úložiště [djangoapp](https://github.com/Azure-Samples/djangoapp) obsahuje aplikaci s [Django](https://www.djangoproject.com/) dotazy řízenými daty, kterou dostanete při [psaní první aplikace Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) v dokumentaci Django.

### <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

V okně terminálu pomocí následujících příkazů naklonujte úložiště ukázkové aplikace a přejděte do nového pracovního adresáře:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Konfigurace virtuálního prostředí Python

Vytvořte a aktivujte virtuální prostředí Python pro spuštění vaší aplikace.

```bash
python3 -m venv venv
source venv/bin/activate
```
nebo
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

V prostředí `venv` spusťte *env.sh* nebo *env. ps1* , abyste nastavili proměnné prostředí, které *azuresite/Settings. py* použije pro nastavení připojení databáze.

```bash
source ./env.sh
```
nebo
```PowerShell
.\env.ps1
```

Nainstalujte požadované balíčky z části *požadavky. txt*, spusťte [migrace Django](https://docs.djangoproject.com/en/2.1/topics/migrations/)a [Vytvořte uživatele s právy pro správu](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Spuštění webové aplikace

Po vytvoření uživatele s oprávněními správce spusťte server Django.

```bash
python manage.py runserver
```

Když je webová aplikace Django plně načtená, vrátí něco podobného jako následující zpráva:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

V prohlížeči přejdete na *http:\//localhost: 8000* . Měla by se zobrazit zpráva **žádná hlasování nejsou k dispozici**. 

Přejít na *http:\//localhost: 8000/admin* a přihlaste se pomocí uživatele správce, kterého jste vytvořili v posledním kroku. Vyberte **Přidat** vedle **otázky**a vytvořte otázku dotazování s některými možnostmi.

![Spuštění aplikace Python Django v App Services místně](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Přejděte na adresu *http:\//localhost: 8000* znovu, abyste viděli otázku dotazování a odpověděli na otázku. Ukázková aplikace místní Django zapisuje a ukládá uživatelská data do místní databáze PostgreSQL.

Chcete-li zastavit server Django, zadejte v terminálu kombinaci kláves CTRL + C.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Většina zbývajících kroků v tomto článku používá příkazy rozhraní příkazového řádku Azure CLI v Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Vytvoření a připojení k Azure Database for PostgreSQL

V této části vytvoříte Azure Database for PostgreSQL Server a databázi a připojíte k ní svou webovou aplikaci. Když nasadíte webovou aplikaci na Azure App Service, aplikace použije tuto cloudovou databázi. 

### <a name="create-a-resource-group"></a>Vytvořit skupinu prostředků

Pro server Azure Database for PostgreSQL můžete vytvořit novou skupinu prostředků, nebo použít existující skupinu prostředků. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Server PostgreSQL vytvoříte pomocí příkazu [AZ Postgres Server Create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) v Cloud Shell.

> [!NOTE]
> Před vytvořením serveru Azure Database for PostgreSQL ověřte, jaká [výpočetní generace](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) je k dispozici ve vaší oblasti. Pokud vaše oblast nepodporuje COMPUTE GEN4 – hardware, změňte *--SKU-Name* na následujícím příkazovém řádku na hodnotu podporovanou ve vaší oblasti, jako je například Gen5. 

V následujícím příkazu nahraďte *\<PostgreSQL-name >* jedinečným názvem serveru. Název serveru je součástí vašeho PostgreSQL koncového bodu *https://\<PostgreSQL-name >. Postgres. Database. Azure. com*, takže tento název musí být jedinečný napříč všemi servery v Azure. 

Nahraďte *\<Resource-name >* a *\<region >* s názvem a oblastí skupiny prostředků, kterou chcete použít. Pro *\<admin-username >* a *\<>* pro správce databáze Vytvořte přihlašovací údaje uživatele pro účet správce databáze. Mějte na paměti, že *\<admin-username >* a *\<>* pro přihlašování k serveru a databázím PostgreSQL později použít.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Po vytvoření serveru Azure Database for PostgreSQL vrátí Azure CLI kód JSON podobný následujícímu příkladu:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Vytvoření pravidel brány firewall pro Azure Database for PostgreSQL Server

Spuštěním příkazu [AZ Postgres server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) povolte přístup k databázi z prostředků Azure. Nahraďte *\<PostgreSQL-name >* a *\<autosource-Name >* zástupné symboly hodnotami.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Předchozí nastavení umožňuje síťová připojení ze všech IP adres v síti Azure. V případě použití v produkčním prostředí zkuste nakonfigurovat nejvíc omezující pravidla brány firewall tím, že [povolíte jenom odchozí IP adresy, které vaše aplikace používá](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Opětovným spuštěním příkazu `firewall-rule create` povolte přístup z místního počítače. Pomocí [místní IP adresy IPv4](https://www.whatsmyip.org/)nahraďte *\<IP adresy >* . Nahraďte *\<PostgreSQL-name >* a *\<název zdrojové složky >* zástupné symboly vlastními hodnotami.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Vytvoření databáze Azure Database for PostgreSQL a připojení k ní

Spusťte následující příkaz, abyste se připojili k serveru Azure Database for PostgreSQL. Použijte vlastní *\<PostgreSQL-name >* a *\<správce-username >* a přihlaste se pomocí hesla, které jste vytvořili.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Stejně jako na místním serveru PostgreSQL vytvořte databázi a uživatele na serveru Azure Database for PostgreSQL:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Je osvědčeným postupem vytvořit uživatele databáze s omezenými oprávněními pro konkrétní aplikace namísto použití uživatele s právy pro správu. Uživatel `manager` má úplná oprávnění *pouze* k databázi `pollsdb`.

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testování připojení aplikace k databázi Azure PostgreSQL

Upravte svůj místní soubor *env.sh* nebo *env. ps1* tak, aby odkazoval na databázi Cloud PostgreSQL, a to tak, že nahradíte *\<PostgreSQL-Name >* názvem serveru Azure Database for PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
nebo
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

V prostředí `venv` v místním okně terminálu spusťte upravený *env.sh* nebo *env. ps1*. 
```bash
source ./env.sh
```
nebo
```PowerShell
.\env.ps1
```

Spusťte migraci Django do databáze Azure a vytvořte uživatele s právy pro správu.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Po vytvoření uživatele s oprávněními správce spusťte server Django.

```bash
python manage.py runserver
```

V prohlížeči přejděte na *http:\//localhost: 8000*a v případě, že se zobrazí zpráva **žádná hlasování nejsou k dispozici** . 

Přejít na *http:\//localhost: 8000/admin*, přihlaste se pomocí vámi vytvořeného uživatele pro správu a vytvořte otázku dotazování jako dřív.

![Spuštění aplikace Python Django v App Services místně](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Přejděte na adresu *http:\//localhost: 8000* znovu a podívejte se na zobrazenou otázku dotazování. Vaše aplikace teď zapisuje data do databáze Azure Database for PostgreSQL.

Chcete-li zastavit server Django, zadejte v terminálu kombinaci kláves CTRL + C.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Nasazení webové aplikace do Azure App Service

V tomto kroku nasadíte aplikaci Pythonu Azure Database for PostgreSQL připojenou k databázi do Azure App Service.

### <a name="configure-repository"></a>Konfigurace úložiště

Vzhledem k tomu, že tento kurz používá ukázku Django, musíte změnit a přidat některá nastavení do souboru *djangoapp/azuresite/Settings. py* pro práci s Azure App Service. 

1. Django ověří hlavičku `HTTP_HOST` v příchozích požadavcích. Aby webová aplikace Django fungovala v App Service, musíte do povolených hostitelů přidat plně kvalifikovaný název domény aplikace. 
   
   Úpravou *azuresite/Settings. py* změňte `ALLOWED_HOSTS` řádek následujícím způsobem:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django nepodporuje [obsluhu statických souborů v produkčním](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)prostředí. V tomto kurzu použijete [WhiteNoise](https://whitenoise.evans.io/en/stable/) k povolení obsluhy souborů. Balíček WhiteNoise už je nainstalovaný s *požadavky. txt*. 
   
   Pokud chcete nakonfigurovat Django pro použití WhiteNoise, v *azuresite/Settings. py*najděte nastavení `MIDDLEWARE` a do seznamu přidejte `whitenoise.middleware.WhiteNoiseMiddleware` hned za `django.middleware.security.SecurityMiddleware` řádek. Nastavení `MIDDLEWARE` by mělo vypadat takto:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Na konci *azuresite/Settings. py*přidejte následující řádky:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Další informace o konfiguraci WhiteNoise najdete v [dokumentaci k WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> V části nastavení databáze již následuje postup zabezpečení při používání proměnných prostředí. Kompletní doporučení k nasazení najdete v [dokumentaci k Django: kontrolní seznam nasazení](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Potvrďte své změny do rozvětvení úložiště *djangoapp* :

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

V dřívější části tohoto kurzu jste definovali proměnné prostředí pro připojení k vaší databázi PostgreSQL.

V Azure App Service jste pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) nastavili proměnné prostředí jako *nastavení aplikace*.

V Azure Cloud Shell spusťte následující příkaz, který určí podrobnosti připojení databáze jako nastavení aplikace. Nahraďte *\<App-name >* , *\<název zdrojové složky >* a *\<PostgreSQL-Name >* s vašimi vlastními hodnotami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Informace o tom, jak váš kód přistupuje k těmto nastavením aplikace, najdete v tématu [přístup k proměnným prostředí](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

Server pro nasazení App Service uvidí *požadavky. txt* v kořenovém adresáři úložiště a spustí správu balíčků Pythonu automaticky po `git push`.

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure

Přejděte do nasazené aplikace s adresou URL *http:\//\<App-name >. azurewebsites. NET*. Spuštění může nějakou dobu trvat, protože kontejner se musí stáhnout a spustit při prvním vyžádání aplikace. Pokud stránce vyprší časový limit nebo se na ní zobrazí chybová zpráva, počkejte několik minut a stránku aktualizujte.

Měli byste vidět otázky pro dotazování, které jste vytvořili dříve. 

App Service zjistí v úložišti projekt Django tak, že v každém podadresáři vyhledá soubor *WSGI.py* , který `manage.py startproject` vytvoří ve výchozím nastavení. Když App Service najde soubor, načte webovou aplikaci Django. Další informace o tom, jak App Service načítají aplikace v Pythonu, najdete v tématu [Konfigurace integrované image Pythonu](how-to-configure-python.md).

Přejít na *http:\//\<App-name >. azurewebsites. NET/Administrator* a přihlaste se pomocí vámi vytvořeného uživatele s oprávněními správce. Pokud chcete, vytvořte další otázky pro dotazování.

![Spuštění aplikace Python Django v App Services v Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Blahopřejeme!** Spouštíte webovou aplikaci Python (Django) v Azure App Service pro Linux.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Správa aplikace v Azure Portal

V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte aplikaci, kterou jste vytvořili.

![V Azure Portal přejděte do vaší aplikace Python Django](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Ve výchozím nastavení se na portálu zobrazí stránka s **přehledem** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Správa aplikace Python Django na stránce Přehled v Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS do vaší aplikace:

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](../app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](how-to-configure-python.md)
