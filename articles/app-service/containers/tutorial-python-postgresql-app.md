---
title: 'Výuka: Aplikace Linux Python s Postgre'
description: Zjistěte, jak ve službě Azure App Service pracovat aplikaci Linux Python s připojením k databázi PostgreSQL v Azure. Django se používá v tomto kurzu.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523920"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Kurz: Spuštění webové aplikace Python (Django) s PostgreSQL ve službě Azure App Service

[Azure App Service ](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento kurz ukazuje, jak připojit webovou aplikaci Python Django založenou na datech k databázi Azure database for PostgreSQL a nasadit a spustit aplikaci ve službě Azure App Service.

![Webová aplikace Python Django ve službě Azure App Service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření databáze Azure pro PostgreSQL a připojení webové aplikace k ní
> * Nasazení webové aplikace do služby Azure App Service
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace na webu Azure Portal

Postup v tomto článku o macOS, Linuxu nebo Windows můžete postupovat podle pokynů. Kroky jsou podobné ve většině případů, i když rozdíly nejsou podrobně popsány v tomto kurzu. Většina níže uvedených `bash` příkladů používá terminálové okno na Linuxu. 

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Nainstalujte [Git](https://git-scm.com/).
- Nainstalujte [Python 3](https://www.python.org/downloads/).
- Nainstalujte a [spusťte PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Test instalace PostgreSQL a vytvoření databáze

Nejprve se připojte k místnímu serveru PostgreSQL a vytvořte databázi: 

V místním okně terminálu spusťte `psql` připojení k místnímu serveru `postgres` PostgreSQL jako vestavěný uživatel.

```bash
sudo su - postgres
psql
```
– nebo –
```PowerShell
psql -U postgres
```

Pokud se úspěšně připojíte, vaše databáze PostgreSQL je spuštěná. Pokud ne, podle pokynů pro váš operační systém na stránce [Downloads – PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Ke stažení – Distribuce jádra PostgreSQL) se ujistěte, že je vaše místní databáze PostgreSQL spuštěná.

Vytvořte novou databázi nazvanou *pollsdb*a nastavte databázového uživatele s názvem *správce* s *heslem supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Vytvoření a spuštění místní aplikace Python

Dále nastavte a spusťte ukázkovou webovou aplikaci Python Django.

Ukázkové úložiště [djangoapp](https://github.com/Azure-Samples/djangoapp) obsahuje datově řízenou aplikaci [Django,](https://www.djangoproject.com/) kterou získáte podle [psaní první aplikace Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) v dokumentaci Django.

### <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

V okně terminálu spusťte následující příkazy, chcete-li klonovat ukázkové úložiště aplikací, a změňte na nový pracovní adresář:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Konfigurace virtuálního prostředí Pythonu

Vytvořte a aktivujte virtuální prostředí Pythonu pro spuštění aplikace.

```bash
python3 -m venv venv
source venv/bin/activate
```
– nebo –
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

V `venv` prostředí spusťte *env.sh* nebo *env.ps1* a nastavte proměnné prostředí, které *azuresite/settings.py* použije pro nastavení připojení k databázi.

```bash
source ./env.sh
```
– nebo –
```PowerShell
.\env.ps1
```

Nainstalujte požadované balíčky z *souboru requirements.txt*, spusťte [migrace Django](https://docs.djangoproject.com/en/2.1/topics/migrations/)a [vytvořte uživatele správce](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Spuštění webové aplikace

Po vytvoření správce spusťte server Django.

```bash
python manage.py runserver
```

Když je webová aplikace Django plně načtena, vrátí něco jako následující zpráva:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Přejděte v prohlížeči na *\/http: /localhost:8000.* Měla by se zobrazit zpráva **Žádné ankety jsou k dispozici**. 

Přejděte na *\/http: /localhost:8000/admin* a přihlaste se pomocí správce, který jste vytvořili v posledním kroku. Vyberte **Přidat** vedle **otázky**a vytvořte anketní otázku s některými možnostmi.

![Spuštění aplikace Python Django ve službách aplikací místně](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Přejděte na *http:\//localhost:8000* znovu vidět anketu otázku a odpovědět na otázku. Místní ukázková aplikace Django zapisuje a ukládá uživatelská data do místní databáze PostgreSQL.

Chcete-li server Django zastavit, zadejte do terminálu kombinaci kláves Ctrl+C.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Většina zbývajících kroků v tomto článku používá příkazy Azure CLI v prostředí Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Vytvoření a připojení k Azure Database pro PostgreSQL

V této části vytvoříte databázi Azure pro postgreSQL server a databázi a připojíte k ní webovou aplikaci. Když nasadíte webovou aplikaci do služby Azure App Service, aplikace používá tuto cloudovou databázi. 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Můžete vytvořit novou skupinu prostředků pro azure databázi pro postgresql server nebo použít existující skupinu prostředků. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Vytvoříte postgresql server s příkazem [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) v cloudovém prostředí.

> [!NOTE]
> Před vytvořením databáze Azure pro PostgreSQL server zkontrolujte, které [generování výpočetních prostředků](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) je dostupné ve vaší oblasti. Pokud vaše oblast nepodporuje hardware Gen4, změňte *--sku-name* v následujícím příkazovém řádku na hodnotu, která je podporována ve vaší oblasti, například Gen5. 

V následujícím příkazu nahraďte * \<>postgresql název* jedinečným názvem serveru. Název serveru je součástí koncového bodu PostgreSQL *https://\<postgresql-name>.postgres.database.azure.com*, takže název musí být jedinečný na všech serverech v Azure. 

Nahraďte * \<název skupiny prostředků>* a * \<>oblasti* názvem a oblastí skupiny prostředků, kterou chcete použít. Pro * \<>a*>hesla * \<správce *vytvořte pověření uživatele pro účet správce databáze. Nezapomeňte * \<* na uživatelské jméno správce>a * \<heslo správce>, které se později* použije pro přihlášení k postgresql serveru a databázím.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Když je vytvořena databáze Azure pro PostgreSQL server, Azure CLI vrátí kód JSON jako v následujícím příkladu:

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

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Vytvoření pravidel brány firewall pro azure databázi pro postgreSQL server

Spusťte příkaz [vytvořit pravidlo brány firewall serveru az postgres,](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) který umožní přístup k databázi z prostředků Azure. Nahraďte * \<>postgresql název* a * \<zástupné symboly název skupiny prostředků>* hodnoty.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Předchozí nastavení umožňuje síťová připojení ze všech IP adres v rámci sítě Azure. Pro produkční použití se pokuste nakonfigurovat nejrestriktivnější možná pravidla brány firewall [tím, že povolíte jenom odchozí IP adresy, které vaše aplikace používá](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Chcete-li `firewall-rule create` povolit přístup z místního počítače, spusťte příkaz znovu. * \<Nahraďte>adresy IP* [místní IP adresou IPv4](https://www.whatsmyip.org/). Nahraďte * \<>postgresql název* a * \<zástupné symboly název skupiny prostředků>* vlastními hodnotami.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Vytvoření databáze Azure database for PostgreSQL a připojení k ní

Připojte se k azure databázi pro postgresql server spuštěním následujícího příkazu. Použijte vlastní * \<postgresql-name>* a * \<admin-uživatelské jméno>* a přihlaste se pomocí hesla, které jste vytvořili.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Stejně jako na místním serveru PostgreSQL vytvořte databázi a uživatele v Azure Database for PostgreSQL server:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Je osvědčeným postupem vytvářet uživatele databáze s omezenými oprávněními pro konkrétní aplikace namísto použití správce. Uživatel `manager` má úplná *only* oprávnění `pollsdb` pouze k databázi.

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testování připojení aplikace k databázi Azure PostgreSQL

Upravte místní *soubor env.sh* nebo *env.ps1* tak, aby přecházel na cloudovou databázi PostgreSQL, a nahradil>* \<název postgresql* s názvem serveru Azure Database for PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
– nebo –
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

V `venv` prostředí v místním okně terminálu spusťte upravené *env.sh* nebo *env.ps1*. 
```bash
source ./env.sh
```
– nebo –
```PowerShell
.\env.ps1
```

Spusťte migraci Django do databáze Azure a vytvořte uživatele správce.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Po vytvoření správce spusťte server Django.

```bash
python manage.py runserver
```

V prohlížeči přejděte na *http:\//localhost:8000*a měli byste vidět zprávu **Žádné ankety jsou** opět k dispozici. 

Přejděte na *\/http: /localhost:8000/admin*, přihlaste se pomocí správce, který jste vytvořili, a vytvořte anketu jako předtím.

![Spuštění aplikace Python Django ve službách aplikací místně](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Přejděte na *http:\//localhost:8000* znovu a podívejte se na otázku hlasování zobrazena. Vaše aplikace teď zapisuje data do databáze Azure Database for PostgreSQL.

Chcete-li server Django zastavit, zadejte do terminálu kombinaci kláves Ctrl+C.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Nasazení webové aplikace do služby Azure App Service

V tomto kroku nasadíte databázi Azure pro databázi PostgreSQL připojené aplikace Pythonu do služby Azure App Service.

### <a name="configure-repository"></a>Konfigurace úložiště

Vzhledem k tomu, že tento kurz používá ukázku Django, je třeba změnit a přidat některá nastavení v souboru *djangoapp/azuresite/settings.py* pro práci se službou Azure App Service. 

1. Django ověří `HTTP_HOST` hlavičku v příchozích požadavcích. Aby vaše webová aplikace Django fungovala ve službě App Service, musíte do povolených hostitelů přidat plně kvalifikovaný název domény aplikace. 
   
   Úpravou *azuresite/settings.py* `ALLOWED_HOSTS` změňte řádek takto:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django nepodporuje [zobrazování statických souborů ve výrobě](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). V tomto kurzu můžete použít [WhiteNoise](https://whitenoise.evans.io/en/stable/) povolit zobrazování souborů. Balíček WhiteNoise byl již nainstalován s *souborem requirements.txt*. 
   
   Chcete-li nakonfigurovat Django používat WhiteNoise, v *azuresite/settings.py*, najít `MIDDLEWARE` nastavení a přidat `whitenoise.middleware.WhiteNoiseMiddleware` do seznamu, hned za `django.middleware.security.SecurityMiddleware` řádek. Nastavení `MIDDLEWARE` by mělo vypadat takto:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Na konec *azuresite/settings.py*přidejte následující řádky:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Další informace o konfiguraci whitenoise naleznete v [dokumentaci WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Oddíl Nastavení databáze již dodržuje osvědčené postupy zabezpečení při používání proměnných prostředí. Kompletní doporučení pro nasazení najdete v [tématu Django Dokumentace: kontrolní seznam nasazení](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Potvrďte své změny do vidlice úložiště *djangoapp:*

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

Ve službě Azure App Service nastavíte proměnné prostředí jako *nastavení aplikace*pomocí příkazu [nastavit nastavení konfigurace aplikace az webapp.](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)

V prostředí Azure Cloud Shell spusťte následující příkaz a určete podrobnosti o připojení k databázi jako nastavení aplikace. Nahraďte * \<>názvu aplikace *, * \<>název skupiny prostředků *a * \<>název postgresql* vlastními hodnotami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Informace o tom, jak váš kód přistupuje k těmto nastavením aplikace, najdete v [tématu Proměnné prostředí aplikace Access](how-to-configure-python.md#access-environment-variables).

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

Server pro nasazení služby App Service vidí *soubor requirements.txt* v `git push`kořenovém adresáři úložiště a po spuštění správy balíčků pythonu automaticky spustí.

### <a name="browse-to-the-azure-app"></a>Přejděte do aplikace Azure

Přejděte na nasazenou aplikaci s adresou URL *http:\//\<název aplikace>.azurewebsites.net*. Spuštění trvá nějakou dobu, protože kontejner musí být stažen a spuštěn při prvním požadavku aplikace. Pokud stránce vyprší časový limit nebo se na ní zobrazí chybová zpráva, počkejte několik minut a stránku aktualizujte.

Měli byste vidět otázky hlasování, které jste vytvořili dříve. 

Služba App Service detekuje projekt Django ve vašem *wsgi.py* úložišti tím, že `manage.py startproject` hledá wsgi.py soubor v každém podadresáři, který se ve výchozím nastavení vytvoří. Když služba App Service soubor najde, načte webovou aplikaci Django. Další informace o tom, jak služba App Service načítá aplikace pythonu, najdete [v tématu Konfigurace předdefinované bitové kopie Pythonu](how-to-configure-python.md).

Přejděte na *http:\//\<název aplikace>.azurewebsites.net/admin* a přihlaste se pomocí uživatele správce, který jste vytvořili. Pokud chcete, vytvořte další otázky k anketě.

![Spuštění aplikace Python Django ve službách App Services v Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Blahopřejeme!** Ve službě Azure App Service pro Linux používáte webovou aplikaci Python (Django).

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Správa aplikace na webu Azure Portal

Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte aplikaci, kterou jste vytvořili.

![Přechod na aplikaci Python Django na webu Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Ve výchozím nastavení se na portálu zobrazuje stránka **Přehled** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Zde můžete také provádět základní úlohy správy, jako je procházení, zastavení, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Správa aplikace Python Django na stránce Přehled na webu Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS do aplikace:

> [!div class="nextstepaction"]
> [Kurz: Mapování vlastního názvu DNS do aplikace](../app-service-web-tutorial-custom-domain.md)

Nebo se podívejte na další zdroje:

> [!div class="nextstepaction"]
> [Konfigurace aplikace Python](how-to-configure-python.md)
