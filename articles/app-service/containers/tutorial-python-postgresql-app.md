---
title: 'Kurz: nasazení Pythonu (Django) pomocí Postgres'
description: Naučte se, jak vytvořit aplikaci v Pythonu s databází PostgreSQL a nasadit ji pro Azure App Service v systému Linux. V tomto kurzu se používá ukázková aplikace Django pro ukázku.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085752"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Kurz: nasazení webové aplikace v Pythonu (Django) s PostgreSQL v Azure App Service

V tomto kurzu se dozvíte, jak nasadit webovou aplikaci v Pythonu řízeném daty (Django) pro [Azure App Service](app-service-linux-intro.md) a připojit ji k databázi Azure Database for PostgreSQL. App Service poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů.

![Nasazení webové aplikace Python Django do Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření databáze Azure Database for PostgreSQL
> * Nasazení kódu pro Azure App Service a připojení k Postgres
> * Aktualizace kódu a opětovné nasazení
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace v Azure Portal

Můžete postupovat podle kroků v tomto článku v macOS, Linux nebo Windows.

## <a name="install-dependencies"></a>Instalace závislostí

Než začnete s tímto kurzem:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).
- Nainstalujte [Git](https://git-scm.com/).
- Nainstalujte [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

V okně terminálu pomocí následujících příkazů naklonujte úložiště ukázkové aplikace a přejděte do kořenového adresáře úložiště:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Ukázkové úložiště djangoapp obsahuje aplikaci s [Django](https://www.djangoproject.com/) dotazy řízenými daty, kterou dostanete při [psaní první aplikace Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) v dokumentaci Django. Je zde k dispozici pro vaše pohodlí.

## <a name="prepare-app-for-app-service"></a>Příprava aplikace pro App Service

Stejně jako mnoho webových rozhraní Pythonu [vyžaduje Django určité změny, než je můžete spustit na provozním serveru](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)a nebude se lišit od App Service. Musíte změnit a přidat některá nastavení ve výchozím souboru *azuresite/Settings. py* , aby aplikace fungovala po nasazení na App Service. 

Podívejte se na *azuresite/produkční. py*, který provede nezbytnou konfiguraci pro App Service. Krátce to provede následující:

- Zdědit všechna nastavení z *azuresite/Settings. py*.
- Přidejte plně kvalifikovaný název domény App Service aplikace k povoleným hostitelům. 
- Použijte [WhiteNoise](https://whitenoise.evans.io/en/stable/) k povolení obsluhy statických souborů v produkčním prostředí, protože Django ve výchozím nastavení neslouží jako statické soubory v produkčním prostředí. Balíček WhiteNoise je již součástí *požadavků. txt*.
- Přidejte konfiguraci pro databázi PostgreSQL. Ve výchozím nastavení používá Django jako databázi sqlite3, ale není vhodná pro produkční aplikace. Balíček [psycopg2-Binary](https://pypi.org/project/psycopg2-binary/) již obsahuje *požadavky. txt*.
- Konfigurace Postgres používá proměnné prostředí. Později se dozvíte, jak nastavit proměnné prostředí v App Service.

*azuresite/produkční. py* je zahrnuté do úložiště pro usnadnění, ale zatím ho aplikace nepoužívá. Abyste se ujistili, že se v App Service používá jeho nastavení, musíte pro přístup k němu nakonfigurovat dva soubory, *Manage.py* a *azuresite/rozhraním WSGI. py*.

- V *Manage.py*změňte následující řádek:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Na následující kód:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Proměnnou `DJANGO_ENV` prostředí nastavíte později při konfiguraci aplikace App Service.

- V *azuresite/rozhraním WSGI. py*proveďte stejnou změnu, jak je uvedeno výše.

    V App Service použijete ke spuštění migrace databáze *Manage.py* a App Service používá *azuresite/rozhraním WSGI. py* ke spuštění vaší aplikace Django v produkčním prostředí. Tato změna v obou souborech zajišťuje, že se nastavení produkčního prostředí používá v obou případech.

## <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI

Měli byste už mít nainstalované rozhraní Azure CLI. [Azure CLI](/cli/azure/what-is-azure-cli) umožňuje pracovat s prostředky Azure z terminálu příkazového řádku. 

Pokud se chcete přihlásit k Azure, spusťte [`az login`](/cli/azure/reference-index#az-login) příkaz:

```azurecli
az login
```

Podle pokynů v terminálu se přihlaste k účtu Azure. Až budete hotovi, Vaše předplatná se zobrazí ve formátu JSON ve výstupu terminálu.

## <a name="create-postgres-database-in-azure"></a>Vytvoření databáze Postgres v Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

V této části vytvoříte Azure Database for PostgreSQL Server a databázi. Začněte tím, že nainstalujete `db-up` rozšíření pomocí následujícího příkazu:

```azurecli
az extension add --name db-up
```

Vytvořte databázi Postgres v Azure pomocí [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) příkazu, jak je znázorněno v následujícím příkladu. Nahraďte * \<PostgreSQL-Name>* *jedinečným* názvem (koncový bod serveru je *https://\<PostgreSQL-Name>. Postgres.Database.Azure.com*). Pro * \<správce – uživatelské jméno>* a * \<správce-heslo>* zadejte pověření k vytvoření uživatele správce pro tento Postgres Server.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Tento příkaz může chvíli trvat, protože je to následující:

- Vytvoří [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) s názvem `myResourceGroup`, pokud neexistuje. Každý prostředek Azure musí být v jednom z těchto. Parametr `--resource-group` je volitelný.
- Vytvoří server Postgres s administrativním uživatelem.
- Vytvoří `pollsdb` databázi.
- Umožňuje přístup z místní IP adresy.
- Umožňuje přístup ze služeb Azure.
- Vytvořte uživatele databáze s přístupem k `pollsdb` databázi.

Všechny kroky můžete provádět samostatně s ostatními `az postgres` příkazy a `psql`, ale `az postgres up` všechny jsou v jednom kroku.

Po dokončení příkazu Najděte výstupní řádky, které se nacházejí v `Ran Database Query:`. Zobrazují uživatele databáze, který vám byl vytvořen, s uživatelským jménem `root` a heslem. `Pollsdb1` Později je budete používat k připojení aplikace k databázi.

<!-- not all locations support az postgres up -->
> [!TIP]
> Pokud chcete zadat umístění serveru Postgres, uveďte argument `--location <location-name>`, kde `<location_name>` je jedna z [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pomocí [`az account list-locations`](/cli/azure/account#az-account-list-locations) příkazu můžete získat oblasti dostupné pro vaše předplatné.

## <a name="deploy-the-app-service-app"></a>Nasazení aplikace App Service

V této části vytvoříte aplikaci App Service. Tuto aplikaci připojíte k databázi Postgres, kterou jste vytvořili a nasadili svůj kód.

### <a name="create-the-app-service-app"></a>Vytvoření aplikace App Service

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Ujistěte se, že jste zpátky v kořenovém adresáři`djangoapp`úložiště (), protože aplikace bude nasazená z tohoto adresáře.

Pomocí [`az webapp up`](/cli/azure/webapp#az-webapp-up) příkazu vytvořte aplikaci App Service, jak je znázorněno v následujícím příkladu. Nahraďte * \<název aplikace>* *jedinečným* názvem (koncovým bodem serveru je *https://\<App-Name>. azurewebsites.NET*). Povolené znaky pro * \<název aplikace>* jsou `A` - `Z`, `0` - `9`a `-`.

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Tento příkaz může chvíli trvat, protože je to následující:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Automaticky vygeneruje [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) .
- Vytvoří [plán App Service](../overview-hosting-plans.md) *myAppServicePlan* v cenové úrovni Basic (B1), pokud neexistuje. `--plan`a `--sku` jsou volitelné.
- Vytvoří aplikaci App Service, pokud neexistuje.
- Povolí výchozí protokolování pro aplikaci, pokud ještě není povolené.
- Nahraje úložiště pomocí nasazení ZIP s povolenou automatizací sestavení.

Po dokončení nasazení se zobrazí výstup JSON podobný následujícímu:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Zkopírujte hodnotu * \<App-Resource-Group>*. Budete ho potřebovat ke konfiguraci aplikace později. 

> [!TIP]
> Stejný příkaz můžete použít později k nasazení změn a okamžitému povolení diagnostických protokolů pomocí:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

Vzorový kód je teď nasazený, ale aplikace se ještě nepřipojuje k databázi Postgres v Azure. Provedete to dál.

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Když aplikaci spouštíte místně, můžete nastavit proměnné prostředí v relaci Terminálové služby. V App Service pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) uděláte *nastavení aplikace*.

Spusťte následující příkaz, který určuje podrobnosti připojení databáze jako nastavení aplikace. Nahraďte * \<>* * \<App-Resource-Group>* a * \<PostgreSQL-Name>* vlastními hodnotami. Mějte na paměti, že `root` přihlašovací `Pollsdb1` údaje uživatele a byly pro `az postgres up`vás vytvořeny.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Informace o tom, jak váš kód přistupuje k těmto nastavením aplikace, najdete v tématu [přístup k proměnným prostředí](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Spustit migrace databáze

Pokud chcete spustit migrace databáze v App Service, otevřete v prohlížeči relaci SSH tak, že přejdete na *https://\<App-name>. SCM.azurewebsites.NET/webssh/Host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

V relaci SSH spusťte následující příkazy:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure

V prohlížeči přejděte do nasazené aplikace s adresou URL *http:\//\<App-Name>. azurewebsites.NET* . Měla by se zobrazit zpráva **žádná hlasování nejsou k dispozici**. 

Přejděte na adresu *http\//\<: název aplikace>. azurewebsites.NET/admin* a přihlaste se pomocí uživatele s rolí správce, kterého jste vytvořili v posledním kroku. Vyberte **Přidat** vedle **otázky**a vytvořte otázku dotazování s některými možnostmi.

Přejděte k nasazené aplikaci s adresou URL *http\//\<: App-Name>. azurewebsites.NET/admin*a vytvořte si nějaké otázky pro dotazování. Otázky můžete zobrazit na adrese *http:\//\<název aplikace>. azurewebsites.NET/*. 

![Spuštění aplikace Python Django v App Services v Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Přejděte k nasazené aplikaci s adresou URL *http\//\<: App-Name>. azurewebsites.NET* , abyste viděli otázku dotazování a odpověděli na otázku.

App Service zjistí v úložišti projekt Django tak, že v každém podadresáři vyhledá soubor *WSGI.py* , který `manage.py startproject` se ve výchozím nastavení vytvoří. Když App Service najde soubor, načte webovou aplikaci Django. Další informace o tom, jak App Service načítají aplikace v Pythonu, najdete v tématu [Konfigurace integrované image Pythonu](how-to-configure-python.md).

**Blahopřejeme!** Spouštíte webovou aplikaci Python (Django) v Azure App Service pro Linux.

## <a name="develop-app-locally-and-redeploy"></a>Místní vývoj a opětovné nasazení aplikace

V této části vyvíjíte svou aplikaci v místním prostředí a znovu nasadíte kód pro App Service.

### <a name="set-up-locally-and-run"></a>Nastavit místně a spustit

Pokud chcete nastavit místní vývojové prostředí a poprvé spustit ukázkovou aplikaci, spusťte následující příkazy:

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[PŘEPSAT](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Když je webová aplikace Django plně načtená, vrátí něco podobného jako následující zpráva:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

V prohlížeči přejdete na *http:\//localhost: 8000* . Měla by se zobrazit zpráva **žádná hlasování nejsou k dispozici**. 

Přejít na *http:\//localhost: 8000/admin* a přihlaste se pomocí uživatele správce, kterého jste vytvořili v posledním kroku. Vyberte **Přidat** vedle **otázky**a vytvořte otázku dotazování s některými možnostmi.

![Spuštění aplikace Python Django v App Services místně](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Přejděte na *http:\//localhost: 8000* znovu, abyste viděli otázku dotazování a odpověděli na otázku. Ukázková aplikace místní Django zapisuje a ukládá uživatelská data do místní databáze SQLite3, takže nemusíte si dělat starosti se sestavou provozní databáze. Pokud chcete, aby vývojové prostředí odpovídalo prostředí Azure, zvažte místo toho použití databáze Postgres místně.

Pokud chcete zastavit server Django, zadejte Ctrl + C.

### <a name="update-the-app"></a>Aktualizace aplikace

Pokud chcete vidět, jak funguje aktualizace aplikací, udělejte v `polls/models.py`nástroji malou změnu. Najít řádek:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

A změňte ji na:

```python
choice_text = models.CharField(max_length=100)
```

Změnou datového modelu je potřeba vytvořit novou migraci Django. Udělejte to pomocí následujícího příkazu:

```
python manage.py makemigrations
```

Změny můžete otestovat místně spuštěním migrace, spuštěním vývojového serveru a přechodem na *http:\//localhost: 8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Opětovné nasazení kódu do Azure

Chcete-li znovu nasadit změny, spusťte následující příkaz z kořenového adresáře úložiště:

```azurecli
az webapp up --name <app-name>
```

App Service zjistí, že aplikace existuje, a jenom nasadí kód.

### <a name="rerun-migrations-in-azure"></a>Znovu spustit migrace v Azure

Vzhledem k tomu, že jste provedli změny v datovém modelu, je nutné znovu spustit migrace databáze v App Service. V prohlížeči otevřete relaci SSH tak, že přejdete na *https://\<app-name>. SCM.azurewebsites.NET/webssh/Host*. Spusťte následující příkazy:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Kontrola aplikace v produkčním prostředí

Přejděte na adresu *http\//\<: App-Name>. azurewebsites.NET* a podívejte se, jaké změny běží živě v produkčním prostředí. 

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

Můžete přistupovat k protokolům konzoly generovaným zevnitř kontejneru.

> [!TIP]
> `az webapp up`zapne pro vás výchozí protokolování. Z důvodu výkonu se toto protokolování po určitou dobu vypne, ale zase se znovu spustí `az webapp up` . Pokud ho chcete zapnout ručně, spusťte následující příkaz:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Spuštěním následujícího příkazu rozhraní příkazového řádku Azure zobrazíte datový proud protokolu:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

> [!NOTE]
> Soubory protokolu můžete také zkontrolovat v prohlížeči na adrese `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Pokud chcete streamování protokolů kdykoli zastavit, zadejte `Ctrl` + `C`.

## <a name="manage-your-app-in-the-azure-portal"></a>Správa aplikace v Azure Portal

V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte aplikaci, kterou jste vytvořili.

![V Azure Portal přejděte do vaší aplikace Python Django](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Ve výchozím nastavení se na portálu zobrazí stránka s **přehledem** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Správa aplikace Python Django na stránce Přehled v Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupiny prostředků spuštěním následujících příkazů:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření databáze Azure Database for PostgreSQL
> * Nasazení kódu pro Azure App Service a připojení k Postgres
> * Aktualizace kódu a opětovné nasazení
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace v Azure Portal

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS do vaší aplikace:

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](../app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](how-to-configure-python.md)
