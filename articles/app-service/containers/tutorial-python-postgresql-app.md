---
title: 'Kurz: webová aplikace Python (Django) s PostgreSQL v systému Linux – Azure App Service'
description: Naučte se, jak spustit webovou aplikaci Python řízenou daty (Django) v Azure s připojením k databázi PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: gwallace
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: c816d2ee76002f60963415b1027579eb6db94089
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329995"
---
# <a name="build-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Vytvoření webové aplikace Python (Django) s PostgreSQL v Azure App Service

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci v Pythonu řízeném daty (Django) pomocí PostgreSQL jako back-endu databáze. Po dokončení budete mít webovou aplikaci Django běžící v Azure App Service v systému Linux.

![Webová aplikace Python Django v App Service v systému Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojení webové aplikace v Pythonu k PostgreSQL
> * Nasazení webové aplikace v Pythonu do Azure
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace v Pythonu v Azure Portal

> [!NOTE]
> Než začnete vytvářet Azure Database for PostgreSQL, zkontrolujte, [Jaké výpočetní generace je ve vaší oblasti k dispozici](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Postup uvedený v tomto článku najdete na macOS, Linux a pokyny pro Windows jsou ve většině případů stejné, ale rozdíly nejsou popsané v tomto kurzu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu je potřeba:

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

Vytvořte databázi s názvem *pollsdb* a nastavte samostatného uživatele databáze s názvem *správce* s heslem *supersecretpass*.

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

Toto ukázkové úložiště obsahuje aplikaci [Django](https://www.djangoproject.com/) . Jedná se o stejnou aplikaci založenou na datech, kterou byste získali pomocí [kurzu Začínáme v dokumentaci k Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Tento kurz vám Django, ale ukáže vám, jak nasadit a spustit webovou aplikaci Django (nebo jinou aplikaci v Pythonu založené na datech) k Azure App Service.

### <a name="configure-environment"></a>Konfigurace prostředí

Vytvořte virtuální prostředí Python a pomocí skriptu nastavte nastavení připojení k databázi.

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

Proměnné prostředí definované v *env.sh* a *env. ps1* se používají v _azuresite/Settings. py_ k definování nastavení databáze.

### <a name="run-app-locally"></a>Místní spuštění aplikace

Nainstalujte požadované balíčky, [Spusťte migrace Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) a [Vytvořte uživatele s právy pro správu](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Po vytvoření uživatele s oprávněními správce spusťte server Django.

```bash
python manage.py runserver
```

Po úplném načtení webové aplikace Django se zobrazí zpráva podobná této:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

V prohlížeči přejdete na `http://localhost:8000`. Měla by se zobrazit zpráva `No polls are available.`. 

Vyberte @no__t – 0 a přihlaste se pomocí uživatele správce, kterého jste vytvořili v posledním kroku. Vyberte **Přidat** vedle **otázky** a vytvořte otázku dotazování s některými možnostmi.

![Místně spuštěná aplikace Python Django](./media/tutorial-python-postgresql-app/django-admin-local.png)

Znovu přejděte na `http://localhost:8000` a podívejte se na zobrazenou otázku dotazování.

Ukázková aplikace Django ukládá uživatelská data do databáze. Pokud jste při přidávání otázky na dotazování úspěšně přidali otázku dotazování, aplikace zapisuje data do místní databáze PostgreSQL.

Pokud chcete server Django kdykoli zastavit, zadejte v terminálu kombinaci kláves CTRL + C.

## <a name="create-a-production-postgresql-database"></a>Vytvoření produkční databáze PostgreSQL

V tomto kroku vytvoříte databázi PostgreSQL v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Pomocí příkazu [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) ve službě Cloud Shell vytvořte server PostgreSQL.

V následujícím příkladu příkazu nahraďte *\<postgresql-name >* jedinečným názvem serveru a nahraďte *\<admin-username >* a *\<admin-Password >* pomocí požadovaných přihlašovacích údajů uživatele. Přihlašovací údaje uživatele jsou přihlašovací údaje k účtu správce databáze. Název serveru se používá jako součást koncového bodu PostgreSQL (`https://<postgresql-name>.postgres.database.azure.com`), takže musí být jedinečný v rámci všech serverů v Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Po vytvoření serveru Azure Database for PostgreSQL se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
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
> Nezapomeňte \<admin-username > a \<admin-Password > pro pozdější verzi. Budete je potřebovat, abyste se mohli přihlásit k serveru Postgre a jeho databázím.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Vytvoření pravidel brány firewall pro server PostgreSQL

Spusťte v Cloud Shellu následující příkazy Azure CLI, abyste umožnili přístup k databázi z prostředků Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Toto nastavení umožňuje síťová připojení ze všech IP adres v síti Azure. Pro produkční prostředí zkuste nakonfigurovat co nejvíce omezující pravidla firewallu tak, že [použijete jen odchozí IP adresy, které používá vaše aplikace](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

V Cloud Shell znovu spusťte příkaz pro povolení přístupu z místního počítače, a to tak, že nahradíte *\<your-IP-address >* s [místní IP adresou IPv4](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Připojení aplikace v Pythonu k produkční databázi

V tomto kroku připojíte webovou aplikaci v Django k serveru Azure Database for PostgreSQL, který jste vytvořili.

### <a name="create-empty-database-and-user-access"></a>Vytvoření prázdné databáze a přístupu uživatelů

V Cloud Shell se připojte k databázi spuštěním příkazu níže. Když se zobrazí výzva k zadání hesla správce, použijte stejné heslo, které jste zadali v části [Vytvoření serveru Azure Database for PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
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

V okně místního terminálu změňte proměnné prostředí databáze (které jste předtím nakonfigurovali spuštěním *env.sh* nebo *env. ps1*):

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
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

Znovu přejít na @no__t – 0. Měla by se zobrazit zpráva `No polls are available.` znovu. 

Vyberte `http://localhost:8000/admin` a přihlaste se pomocí vámi vytvořeného správce a vytvořte otázku dotazování, jako byste předtím.

![Místně spuštěná aplikace Python Django](./media/tutorial-python-postgresql-app/django-admin-local.png)

Znovu přejděte na `http://localhost:8000` a podívejte se na zobrazenou otázku dotazování. Vaše aplikace teď zapisuje data do databáze v Azure.

## <a name="deploy-to-azure"></a>Nasadit do Azure

V tomto kroku nasadíte aplikaci Python připojenou k Postgre do služby Azure App Service.

### <a name="configure-repository"></a>Konfigurace úložiště

Django ověří hlavičku `HTTP_HOST` v příchozích požadavcích. Aby webová aplikace Django fungovala v App Service, musíte do povolených hostitelů přidat plně kvalifikovaný název domény aplikace. Otevřete _azuresite/Settings. py_ a vyhledejte nastavení `ALLOWED_HOSTS`. Změňte řádek na:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

V dalším kroku Django nepodporuje [obsluhu statických souborů v produkčním](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)prostředí, takže je potřeba tuto možnost povolit ručně. V tomto kurzu použijete [WhiteNoise](https://whitenoise.evans.io/en/stable/). Balíček WhiteNoise je již součástí _požadavků. txt_. Stačí nakonfigurovat Django, abyste ho mohli používat. 

V _azuresite/Settings. py_najděte nastavení `MIDDLEWARE` a přidejte do seznamu middleware `whitenoise.middleware.WhiteNoiseMiddleware`, a to hned pod middlewaru `django.middleware.security.SecurityMiddleware`. Nastavení `MIDDLEWARE` by mělo vypadat takto:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Na konci _azuresite/Settings. py_přidejte následující řádky.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Další informace o konfiguraci WhiteNoise najdete v [dokumentaci k WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> V části nastavení databáze již následuje postup zabezpečení při používání proměnných prostředí. Kompletní doporučení k nasazení najdete v [dokumentaci k Django: kontrolní seznam nasazení](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Potvrďte změny do úložiště.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvořte webovou aplikaci

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

V dřívější části tohoto kurzu jste definovali proměnné prostředí pro připojení k vaší databázi PostgreSQL.

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v Cloud Shellu.

Následující příklad určí jako nastavení aplikace podrobnosti o připojení k databázi. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Informace o tom, jak jsou tato nastavení aplikace ve vašem kódu dostupná, najdete v tématu věnovaném [přístupu k proměnným prostředí](how-to-configure-python.md#access-environment-variables).

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
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

Server pro nasazení App Service uvidí _požadavky. txt_ v kořenovém adresáři úložiště a spustí správu balíčků Pythonu automaticky po `git push`.

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure

Přejděte k nasazené aplikaci. Když se o aplikaci žádá poprvé, spuštění nějakou dobu trvá, protože se musí stáhnout a spustit kontejner. Pokud stránce vyprší časový limit nebo se na ní zobrazí chybová zpráva, počkejte několik minut a stránku aktualizujte.

```bash
http://<app-name>.azurewebsites.net
```

Měla by se zobrazit otázka pro dotazování, kterou jste vytvořili dříve. 

App Service detekuje projekt Django v úložišti tím, že hledá _WSGI.py_ v každém podadresáři, který je ve výchozím nastavení vytvořený pomocí `manage.py startproject`. Když soubor najde, načte webovou aplikaci Django. Další informace o tom, jak App Service načítají aplikace v Pythonu, najdete v tématu [Konfigurace integrované image Pythonu](how-to-configure-python.md).

Použijte `<app-name>.azurewebsites.net` a přihlaste se pomocí stejného vytvořeného uživatele správce. Pokud chcete, zkuste vytvořit další otázky pro dotazování.

![Místně spuštěná aplikace Python Django](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Blahopřejeme!** Spouštíte webovou aplikaci Python (Django) v Azure App Service pro Linux.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Správa aplikace v Azure Portal

Pokud si chcete zobrazit aplikaci, kterou jste vytvořili, přejděte na [Azure Portal](https://portal.azure.com) .

V nabídce vlevo vyberte **App Services**a pak vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/tutorial-python-postgresql-app/app-resource.png)

Ve výchozím nastavení se na portálu zobrazí stránka s **přehledem** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojení webové aplikace v Pythonu k PostgreSQL
> * Nasazení webové aplikace v Pythonu do Azure
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace v Pythonu v Azure Portal

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](../app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](how-to-configure-python.md)
