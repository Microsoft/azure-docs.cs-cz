---
title: 'Kurz: Nasazení Pythonu (Django) s Postgresem'
description: Zjistěte, jak vytvořit aplikaci Pythonu s databází PostgreSQL a nasadit ji do Azure App Service na Linuxu. Kurz používá ukázkovou aplikaci Django pro demonstraci.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085752"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Kurz: Nasazení webové aplikace Python (Django) s PostgreSQL ve službě Azure App Service

Tento kurz ukazuje, jak nasadit webovou aplikaci Python (Django) založenou na datech do [služby Azure App Service](app-service-linux-intro.md) a připojit ji k databázi Azure Database for PostgreSQL. App Service poskytuje vysoce škálovatelnou webhostingovou službu s vlastní mise.

![Nasazení webové aplikace Python Django do služby Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření databáze Azure pro postgreSQL databáze
> * Nasazení kódu do služby Azure App Service a připojení k Postgresu
> * Aktualizace kódu a opětovné nasazení
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace na webu Azure Portal

Postup v tomto článku o macOS, Linuxu nebo Windows můžete postupovat podle pokynů.

## <a name="install-dependencies"></a>Instalace závislostí

Než začnete tento kurz:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Nainstalujte [azure cli](/cli/azure/install-azure-cli).
- Nainstalujte [Git](https://git-scm.com/).
- Nainstalujte [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

V okně terminálu spusťte následující příkazy, chcete-li klonovat ukázkové úložiště aplikací, a změňte na kořen úložiště:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Ukázkové úložiště djangoapp obsahuje datově řízenou aplikaci [Django,](https://www.djangoproject.com/) kterou získáte podle [psaní první aplikace Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) v dokumentaci Django. Je zde k dispozici pro vaše pohodlí.

## <a name="prepare-app-for-app-service"></a>Příprava aplikace pro službu App Service

Stejně jako mnoho webových rámců Pythonu vyžaduje Django [určité změny, než je lze spustit na produkčním serveru](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/), a s app service se nijak neliší. Musíte změnit a přidat některá nastavení ve výchozím souboru *azuresite/settings.py,* aby aplikace fungovala po nasazení do služby App Service. 

Podívejte se na *azuresite/production.py*, který dělá potřebnou konfiguraci pro službu App Service. Stručně řečeno, to dělá následující:

- Zdědit všechna nastavení z *azuresite/settings.py*.
- Přidejte plně kvalifikovaný název domény aplikace App Service do povolených hostitelů. 
- Použijte [WhiteNoise](https://whitenoise.evans.io/en/stable/) k povolení zobrazování statických souborů v produkčním prostředí, protože Django ve výchozím nastavení neslouží statickým souborům v produkčním prostředí. Balíček WhiteNoise je již součástí *souboru requirements.txt*.
- Přidejte konfiguraci pro databázi PostgreSQL. Ve výchozím nastavení Django používá Sqlite3 jako databázi, ale není vhodný pro produkční aplikace. Psychologický binární balíček [je](https://pypi.org/project/psycopg2-binary/) již součástí *souboru requirements.txt*.
- Konfigurace Postgres používá proměnné prostředí. Později zjistíte, jak nastavit proměnné prostředí ve službě App Service.

*azuresite/production.py* je součástí úložiště pro pohodlí, ale ještě není používán aplikací. Chcete-li se ujistit, že jeho nastavení se používá ve službě App Service, je třeba nakonfigurovat dva soubory, *manage.py* a *azuresite/wsgi.py*, pro přístup k němu.

- V *manage.py*změňte následující řádek:

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

- V *azuresite/wsgi.py*proveďte stejnou změnu jako výše.

    Ve službě App Service používáte *manage.py* ke spuštění migrace databáze a služba App Service používá *azuresite/wsgi.py* ke spuštění aplikace Django v produkčním prostředí. Tato změna v obou souborech zajišťuje, že nastavení výroby se používají v obou případech.

## <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI

Azure CLI by už měl mít nainstalovaný. [Azure CLI](/cli/azure/what-is-azure-cli) umožňuje pracovat s prostředky Azure z terminálu příkazového řádku. 

Pokud se chcete přihlásit [`az login`](/cli/azure/reference-index#az-login) do Azure, spusťte příkaz:

```azurecli
az login
```

Podle pokynů v terminálu se přihlaste ke svému účtu Azure. Po dokončení se vaše odběry zobrazí ve formátu JSON ve výstupu terminálu.

## <a name="create-postgres-database-in-azure"></a>Vytvoření databáze Postgres v Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

V této části vytvoříte databázi Azure pro PostgreSQL server a databázi. Chcete-li začít, nainstalujte `db-up` rozšíření pomocí následujícího příkazu:

```azurecli
az extension add --name db-up
```

Vytvořte databázi Postgres [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) v Azure pomocí příkazu, jak je znázorněno v následujícím příkladu. Nahraďte * \<>postgresql název* *jedinečným* názvem (koncový bod serveru je *\<https:// postgresql-name>.postgres.database.azure.com*). Pro * \<admin-uživatelské jméno>* a * \<admin-password>*, zadejte pověření pro vytvoření správce uživatele pro tento server Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Tento příkaz může chvíli trvat, protože provádí následující akce:

- Vytvoří [skupinu](../../azure-resource-manager/management/overview.md#terminology) `myResourceGroup`prostředků s názvem , pokud neexistuje. Každý prostředek Azure musí být v jednom z nich. Parametr `--resource-group` je volitelný.
- Vytvoří server Postgres s uživatelem pro správu.
- Vytvoří `pollsdb` databázi.
- Umožňuje přístup z místní IP adresy.
- Umožňuje přístup ze služeb Azure.
- Vytvořte uživatele databáze s `pollsdb` přístupem k databázi.

Všechny kroky můžete provést samostatně `az postgres` s `psql`jinými `az postgres up` příkazy a , ale všechny z nich v jednom kroku za vás.

Po dokončení příkazu vyhledejte výstupní řádky, které jsou s `Ran Database Query:`. Zobrazují uživatele databáze, který je vytvořen pro vás, s uživatelským jménem `root` a heslem `Pollsdb1`. Později je použijete k připojení aplikace k databázi.

<!-- not all locations support az postgres up -->
> [!TIP]
> Chcete-li určit umístění serveru Postgres, `--location <location-name>`uveďte argument , kde `<location_name>` je jedna z oblastí [Azure](https://azure.microsoft.com/global-infrastructure/regions/). [`az account list-locations`](/cli/azure/account#az-account-list-locations) Pomocí příkazu můžete získat oblasti, které jsou k dispozici pro vaše předplatné.

## <a name="deploy-the-app-service-app"></a>Nasazení aplikace App Service

V této části vytvoříte aplikaci App Service. Připojíte tuto aplikaci k databázi Postgres, kterou jste vytvořili, a nasadíte svůj kód.

### <a name="create-the-app-service-app"></a>Vytvoření aplikace App Service

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Ujistěte se, že jste zpátky`djangoapp`v kořenovém adresáři úložiště ( ), protože aplikace bude nasazena z tohoto adresáře.

Vytvořte aplikaci služby App Service s příkazem, [`az webapp up`](/cli/azure/webapp#az-webapp-up) jak je znázorněno v následujícím příkladu. Nahraďte * \<>názvu aplikace* *jedinečným* názvem (koncový bod serveru je *https://\<název aplikace>.azurewebsites.net).* Povolené znaky * \<* pro>`A` - `Z`názvů `0` - `9`aplikací `-`jsou , a .

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Tento příkaz může chvíli trvat, protože provádí následující akce:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Automaticky vygeneruje [skupinu prostředků.](../../azure-resource-manager/management/overview.md#terminology)
- Vytvoří [plán služby App Service](../overview-hosting-plans.md) *myAppServicePlan* v základní cenové úrovni (B1), pokud neexistuje. `--plan`a `--sku` jsou volitelné.
- Vytvoří aplikaci App Service, pokud neexistuje.
- Povolí výchozí protokolování pro aplikaci, pokud již není povoleno.
- Nahraje úložiště pomocí nasazení ZIP s povolenou automatizací sestavení.

Po dokončení nasazení se zobrazí výstup JSON jako následující:

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

Zkopírujte hodnotu * \<>skupiny prostředků aplikace *. Potřebujete ji k pozdější konfiguraci aplikace. 

> [!TIP]
> Stejný příkaz můžete použít později k nasazení všech změn a okamžitépovolení diagnostických protokolů pomocí:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

Ukázkový kód se teď nasadí, ale aplikace se ještě nepřipojí k databázi Postgres v Azure. Uděláš to příště.

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Když aplikaci spustíte místně, můžete nastavit proměnné prostředí v terminálové relaci. Ve službě App Service to děláte s *nastavením aplikace*pomocí příkazu [nastavit nastavení konfigurace aplikace az webapp.](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)

Spusťte následující příkaz a určete podrobnosti o připojení k databázi jako nastavení aplikace. Nahraďte * \<>názvů aplikací *, * \<>skupiny prostředků aplikace *a * \<>název postgresql* vlastními hodnotami. Nezapomeňte, že pověření `root` `Pollsdb1` uživatele a byly `az postgres up`vytvořeny pro vás .

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Informace o tom, jak váš kód přistupuje k těmto nastavením aplikace, najdete v [tématu Proměnné prostředí aplikace Access](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Spuštění migrace databáze

Chcete-li spustit migrace databází ve službě App Service, otevřete relaci SSH v prohlížeči tak, že přejdete na *https://\<název aplikace>.scm.azurewebsites.net/webssh/host*:

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

### <a name="browse-to-the-azure-app"></a>Přejděte do aplikace Azure

Přejděte na nasazenou aplikaci s adresou URL *http:\//\<název aplikace>.azurewebsites.net* v prohlížeči. Měla by se zobrazit zpráva **Žádné ankety jsou k dispozici**. 

Přejděte na *http:\//\<název aplikace>.azurewebsites.net/admin* a přihlaste se pomocí správce, který jste vytvořili v posledním kroku. Vyberte **Přidat** vedle **otázky**a vytvořte anketní otázku s některými možnostmi.

Přejděte na nasazenou aplikaci s adresou URL *http:\//\<název aplikace>.azurewebsites.net/admin*a vytvořte několik otázek týkajících se hlasování. Otázky naleznete na *adrese\//\<http: app-name>.azurewebsites.net/*. 

![Spuštění aplikace Python Django ve službách App Services v Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Přejděte na nasazenou aplikaci s adresou URL *http:\//\<název aplikace>.azurewebsites.net* znovu zobrazit otázku hlasování a odpovědět na otázku.

Služba App Service detekuje projekt Django ve vašem *wsgi.py* úložišti tím, že `manage.py startproject` hledá wsgi.py soubor v každém podadresáři, který se ve výchozím nastavení vytvoří. Když služba App Service soubor najde, načte webovou aplikaci Django. Další informace o tom, jak služba App Service načítá aplikace pythonu, najdete [v tématu Konfigurace předdefinované bitové kopie Pythonu](how-to-configure-python.md).

**Blahopřejeme!** Ve službě Azure App Service pro Linux používáte webovou aplikaci Python (Django).

## <a name="develop-app-locally-and-redeploy"></a>Vývoj aplikace místně a opětovné nasazení

V této části vyvíjíte aplikaci v místním prostředí a znovu nasadíte kód do služby App Service.

### <a name="set-up-locally-and-run"></a>Nastavit místně a spustit

Chcete-li nastavit místní vývojové prostředí a poprvé spustit ukázkovou aplikaci, spusťte následující příkazy:

# <a name="bash"></a>[Bash](#tab/bash)

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

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

Když je webová aplikace Django plně načtena, vrátí něco jako následující zpráva:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Přejděte v prohlížeči na *\/http: /localhost:8000.* Měla by se zobrazit zpráva **Žádné ankety jsou k dispozici**. 

Přejděte na *\/http: /localhost:8000/admin* a přihlaste se pomocí správce, který jste vytvořili v posledním kroku. Vyberte **Přidat** vedle **otázky**a vytvořte anketní otázku s některými možnostmi.

![Spuštění aplikace Python Django ve službách aplikací místně](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Přejděte na *http:\//localhost:8000* znovu vidět anketu otázku a odpovědět na otázku. Místní ukázková aplikace Django zapisuje a ukládá uživatelská data do místní databáze Sqlite3, takže se nemusíte starat o zpackání produkční databáze. Chcete-li, aby vaše vývojové prostředí odpovídalo prostředí Azure, zvažte místo toho použití databáze Postgres místně.

Chcete-li server Django zastavit, zadejte kombinaci kláves Ctrl+C.

### <a name="update-the-app"></a>Aktualizace aplikace

Chcete-li zjistit, jak funguje provádění aktualizací `polls/models.py`aplikací, proveďte malou změnu v aplikaci . Najít řádek:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

A změňte ji na:

```python
choice_text = models.CharField(max_length=100)
```

Změnou datového modelu je třeba vytvořit novou migraci Django. Udělejte to pomocí následujícího příkazu:

```
python manage.py makemigrations
```

Změny můžete otestovat místně spuštěním migrace, spuštěním vývojového serveru a přechodem na *\/http: /localhost:8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Opětovné nasazení kódu do Azure

Chcete-li změny znovu nasadit, spusťte následující příkaz z kořenového adresáře úložiště:

```azurecli
az webapp up --name <app-name>
```

Služba App Service zjistí, že aplikace existuje, a pouze nasadí kód.

### <a name="rerun-migrations-in-azure"></a>Opětovné spuštění migrace v Azure

Vzhledem k tomu, že jste provedli změny datového modelu, je třeba znovu spustit migrace databáze ve službě App Service. Otevřete relaci SSH v prohlížeči tak, že přejdete na *https://\<název aplikace>.scm.azurewebsites.net/webssh/host*. Spusťte následující příkazy:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Kontrola aplikace v produkčním prostředí

Přejděte na *http:\//\<název aplikace>.azurewebsites.net* a podívejte se na změny spuštěné živě v produkčním prostředí. 

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

Můžete přistupovat k protokolům konzoly generovaným z kontejneru.

> [!TIP]
> `az webapp up`zapíná výchozí protokolování za vás. Z důvodů výkonu se toto protokolování po určité době samo `az webapp up` vypne, ale při každém opětovném spuštění se opět vypne. Chcete-li ji zapnout ručně, spusťte následující příkaz:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Spuštěním následujícího příkazu Azure CLI zobrazíte datový proud protokolu:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

> [!NOTE]
> Můžete také zkontrolovat soubory protokolu z `https://<app-name>.scm.azurewebsites.net/api/logs/docker`prohlížeče na adrese .

Chcete-li kdykoli zastavit streamování `Ctrl` + `C`protokolů, zadejte příkaz .

## <a name="manage-your-app-in-the-azure-portal"></a>Správa aplikace na webu Azure Portal

Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte aplikaci, kterou jste vytvořili.

![Přechod na aplikaci Python Django na webu Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Ve výchozím nastavení se na portálu zobrazuje stránka **Přehled** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Zde můžete také provádět základní úlohy správy, jako je procházení, zastavení, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Správa aplikace Python Django na stránce Přehled na webu Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neočekáváte, že budete tyto prostředky v budoucnu potřebovat, odstraňte skupiny prostředků spuštěním následujících příkazů:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření databáze Azure pro postgreSQL databáze
> * Nasazení kódu do služby Azure App Service a připojení k Postgresu
> * Aktualizace kódu a opětovné nasazení
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace na webu Azure Portal

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS do aplikace:

> [!div class="nextstepaction"]
> [Kurz: Mapování vlastního názvu DNS do aplikace](../app-service-web-tutorial-custom-domain.md)

Nebo se podívejte na další zdroje:

> [!div class="nextstepaction"]
> [Konfigurace aplikace Python](how-to-configure-python.md)
