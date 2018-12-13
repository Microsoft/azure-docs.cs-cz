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
ms.date: 11/29/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 3963e2ffb521a4b4732814e9b2992f4e83af1835
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865620"
---
# <a name="build-a-python-and-postgresql-web-app-in-azure-app-service"></a>Vytvoření webové aplikace Python a PostgreSQL v Azure App Service

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento kurz ukazuje postup vytvoření webové aplikace řízené daty v Pythonu pomocí PostgreSQL jako back-endové databáze. Jakmile budete hotovi, budete mít ve službě App Service v Linuxu spuštěnou aplikaci Django.

![Aplikaci Python Django ve službě App Service v Linuxu](./media/tutorial-python-postgresql-app/django-admin-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit aplikaci Python k PostgreSQL
> * Nasadit aplikaci do Azure
> * Zobrazit diagnostické protokoly
> * Spravovat aplikaci na webu Azure Portal

Podle kroků v tomto článku můžete postupovat v systému macOS. Pokyny pro Linux a Windows jsou ve většině případů stejné, ale odlišnosti nejsou v tomto kurzu podrobně popsané.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalovat Python](https://www.python.org/downloads/).
3. [Nainstalovat a spustit PostgreSQL](https://www.postgresql.org/download/)

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

Vytvořit databázi s názvem *pollsdb* a nastavte samostatného uživatele databáze s názvem *správce* heslem *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Vytvoření místní aplikace v Pythonu

V tomto kroku nastavíte místní projekt Python Django.

### <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Otevřete okno terminálu a pomocí příkazu `CD` přejděte do pracovního adresáře.

Spuštěním následujících příkazů naklonujte ukázkové úložiště.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Toto ukázkové úložiště obsahuje [Django](https://www.djangoproject.com/) aplikace. Je stejná aplikace řízené daty by získáte pomocí následujících [úvodní kurz v dokumentaci k Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). V tomto kurzu není naučit vás Django, ale popisuje, jak využít nasazení a spuštění aplikace Django (nebo jiné aplikace v Pythonu s daty) do služby App Service.

### <a name="configure-environment"></a>Konfigurace prostředí

Vytvořit virtuální prostředí Python a pro nastavení připojení databáze použít skript.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

Proměnné prostředí definované v *env.sh* a *env.ps1* se používají v _azuresite/settings.py_ k definování nastavení databáze.

### <a name="run-app-locally"></a>Místní spuštění aplikace

Nainstalujte požadované balíčky [spusťte Django migrace](https://docs.djangoproject.com/en/2.1/topics/migrations/) a [vytvořit uživateli s právy](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Po vytvoření uživatele s rolí správce spusťte Django server.

```bash
python manage.py runserver
```

Po úplném načtení aplikace se zobrazí zpráva podobná této:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

V prohlížeči přejděte na `http://localhost:8000`. Zobrazí se zpráva `No polls are available.`. 

Přejděte na `http://localhost:8000/admin` a přihlaste se pomocí uživatele s rolí správce jste vytvořili v předchozím kroku. Klikněte na tlačítko **přidat** vedle **dotazy** a vytvořit dotaz cyklického dotazování některé možnosti.

![Místně spuštěná aplikace Python Django](./media/tutorial-python-postgresql-app/django-admin-local.png)

Přejděte na `http://localhost:8000` znovu a podívejte se zobrazí dotaz cyklického dotazování.

Ukázková aplikace Django ukládá uživatelská data v databázi. Pokud jste úspěšně přidat dotaz cyklického dotazování, vaše aplikace zapisuje data do místní databáze PostgreSQL.

Zastavit Django server kdykoli, zadáním kombinace kláves Ctrl + C v terminálu.

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

V tomto kroku připojíte k Azure Database for PostgreSQL, který jste vytvořili ukázkové aplikace Django.

### <a name="create-empty-database-and-user-access"></a>Vytvoření prázdné databáze a přístupu uživatelů

Ve službě Cloud Shell připojení k databázi spuštěním následujícího příkazu. Když se zobrazí výzva k zadání hesla správce, použijte stejné heslo, které jste zadali v části [Vytvoření serveru Azure Database for PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Stejně jako v místním serveru Postgre vytvořte databázi a uživatele na serveru Azure Postgre.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Zadáním příkazu `\q` ukončete klienta PostgreSQL.

> [!NOTE]
> Nejlepším postupem je vytvořit uživatele databáze s oprávněními omezenými jen na určité aplikace namísto uživatele s rolí správce. V tomto příkladu má uživatel `manager` úplná oprávnění _jen_ k databázi `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>Test připojení aplikace k provozní databázi

V okně místního terminálu změnit proměnné prostředí databáze (které jste nakonfigurovali v předchozích krocích spuštěním *env.sh* nebo *env.ps1*):

```bash
# Bash
export DBHOST="<postgresql_name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql_name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql_name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql_name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Spustit migraci Django do Azure databáze a vytvořit uživateli s právy.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Po vytvoření uživatele s rolí správce spusťte Django server.

```bash
python manage.py runserver
```

Přejděte na `http://localhost:8000` v akci. Zobrazí se zpráva `No polls are available.` znovu. 

Přejděte na `http://localhost:8000/admin` a přihlaste se pomocí vytvořeného uživatele s rolí správce a vytvořit dotaz cyklického dotazování stejně jako předtím.

![Místně spuštěná aplikace Python Django](./media/tutorial-python-postgresql-app/django-admin-local.png)

Přejděte na `http://localhost:8000` znovu a podívejte se zobrazí dotaz cyklického dotazování. Vaše aplikace je nyní zapisuje data do databáze v Azure.

## <a name="deploy-to-azure"></a>Nasazení do Azure

V tomto kroku nasadíte aplikaci Python připojenou k Postgre do služby Azure App Service.

### <a name="configure-repository"></a>Konfigurace úložiště

Ověří Django `HTTP_HOST` záhlaví v příchozí požadavky. Pro vaši aplikaci Django pro práci ve službě App Service budete muset přidat do povolených hostitele plně kvalifikovaný název domény aplikace. Otevřít _azuresite/settings.py_ a najít `ALLOWED_HOSTS` nastavení. Změňte řádek na:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

V dalším kroku Django nepodporuje [obsluhy statických souborů v produkčním prostředí](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), takže budete muset ručně povolit. V tomto kurzu použijete [WhiteNoise](http://whitenoise.evans.io/en/stable/). Balíček WhiteNoise je již součástí _souboru requirements.txt_. Potřebujete nakonfigurovat Django jeho použití. 

V _azuresite/settings.py_, vyhledejte `MIDDLEWARE` nastavením a přidejte `whitenoise.middleware.WhiteNoiseMiddleware` middlewaru, který má v seznamu přímo pod `django.middleware.security.SecurityMiddleware` middlewaru. Vaše `MIDDLEWARE` nastavení by měl vypadat přibližně takto:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Na konci _azuresite/settings.py_, přidejte následující řádky.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Další informace o konfiguraci WhiteNoise, najdete v článku [WhiteNoise dokumentaci](http://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> V části Nastavení databáze již následující doporučené postupy zabezpečení pomocí proměnné prostředí. Doporučení pro dokončení nasazení, najdete v části [Django dokumentaci: kontrolní seznam nasazení](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).


Potvrďte změny do úložiště.

```bash
git commit -am "configure for App Service"
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
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
   06b6df4..6520eea  master -> master
```  

Server nasazení služby App Service se zobrazí _souboru requirements.txt_ v kořenovém adresáři úložiště a správy balíčků Pythonu spustí automaticky po `git push`.

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte do nasazené webové aplikace. Když se o aplikaci žádá poprvé, spuštění nějakou dobu trvá, protože se musí stáhnout a spustit kontejner. Pokud stránce vyprší časový limit nebo se na ní zobrazí chybová zpráva, počkejte několik minut a stránku aktualizujte.

```bash
http://<app_name>.azurewebsites.net
```

Měli byste vidět dotaz cyklického dotazování, který jste vytvořili dříve. 

App Service zjistí projektu Django ve vašem úložišti tím, že hledají _wsgi.py_ v každé podadresář, který je vytvořen `manage.py startproject` ve výchozím nastavení. Pokud se najde soubor, načte aplikaci Django. Další informace o tom, jak služby App Service načte aplikace v Pythonu najdete v tématu [konfigurovat integrované image Python](how-to-configure-python.md).

Přejděte na `<app_name>.azurewebsites.net` a přihlaste se pomocí stejného uživatele správce jste vytvořili. Pokud chcete, můžete zkuste vytvořit některé další dotazy cyklického dotazování.

![Místně spuštěná aplikace Python Django](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Blahopřejeme!** Spustili jste pythonovou aplikaci ve službě App Service for Linux.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

Ve službě App Service v Linuxu aplikace běží uvnitř kontejneru z výchozí image Dockeru. Protokoly konzoly z v rámci kontejneru může přistupovat k. Pokud chcete získat protokoly, nejprve zapnout protokolování kontejneru spuštěním následujícího příkazu ve službě Cloud Shell:

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Po zapnutí protokolování kontejneru spuštěním následujícího příkazu zobrazíte datový proud protokolu:

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

## <a name="manage-your-web-app-in-the-azure-portal"></a>Správa webové aplikace na portálu Azure Portal

Přejděte na web [Azure Portal](https://portal.azure.com) k webové aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Na začátku portál zobrazí stránku **Přehled** vaší webové aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit aplikaci Python k PostgreSQL
> * Nasadit aplikaci do Azure
> * Zobrazit diagnostické protokoly
> * Spravovat aplikaci na webu Azure Portal

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Konfigurovat předdefinované Pythonu obrázků a řešení potíží s chybami](how-to-configure-python.md)

