---
title: 'Kurz: nasazení aplikace v Django s App Service a Azure Database for PostgreSQLm flexibilním serverem (ve verzi Preview) ve službě Virtual Network'
description: Nasazení aplikace Django pomocí App Manifestuu a Azure Database for PostgreSQL-flexibilního serveru (Preview) ve službě Virtual Network
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490097"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Kurz: nasazení aplikace v Django s využitím App Service a Azure Database for PostgreSQLm flexibilním serverem (Preview)

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

V tomto kurzu se naučíte, jak nasadit aplikaci Django v Azure pomocí App Services a Azure Database for PostgreSQL flexibilního serveru ve virtuální síti.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/authenticate-azure-cli) . Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Nahraďte vlastnost **ID předplatného** z výstupu **AZ Login** pro vaše předplatné na zástupný symbol ID předplatného.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Klonování nebo stažení ukázkové aplikace

# <a name="git-clone"></a>[Klon Git](#tab/clone)

Naklonujte ukázkové úložiště:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Pak přejít do této složky:

```terminal
cd djangoapp
```

# <a name="download"></a>[Stáhnout](#tab/download)

Přejděte na [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , vyberte **klonovat** a pak vyberte **Stáhnout ZIP**.

Rozbalte soubor ZIP do složky s názvem *djangoapp*.

Pak v této složce *djangoapp* otevřete okno terminálu.

---

Ukázka djangoapp obsahuje aplikaci s dotazy založenými na datech Django, kterou dostanete při [psaní první aplikace Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) v dokumentaci Django. Dokončená aplikace je zde k dispozici pro vaši pohodlí.

Ukázka se také upraví tak, aby běžela v produkčním prostředí, jako je App Service:

- Nastavení výroby jsou v souboru *azuresite/produkční. py* . Podrobnosti o vývoji jsou v *azuresite/Settings. py*.
- Pokud `DJANGO_ENV` je proměnná prostředí nastavená na produkční, aplikace používá nastavení produkčního prostředí. Tuto proměnnou prostředí vytvoříte později v kurzu spolu s dalšími uživateli používanými pro konfiguraci databáze PostgreSQL.

Tyto změny jsou specifické pro konfiguraci Django, aby běžely v jakémkoli provozním prostředí, a nekonkrétně se App Service. Další informace najdete v tématu [Kontrolní seznam nasazení Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Vytvoření PostgreSQL flexibilního serveru v nové virtuální síti

Pomocí následujícího příkazu vytvořte privátní server a databázi ve virtuální síti (VNET):
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Tento příkaz provede následující akce, což může trvat několik minut:

- Vytvořte skupinu prostředků, pokud ještě neexistuje.
- Vygeneruje název serveru, pokud není k dispozici.
- Vytvořte novou virtuální síť pro nový server postgreSQL. **Poznamenejte si název virtuální sítě a název podsítě** vytvořené pro váš server, protože potřebujete přidat webovou aplikaci ke stejné virtuální síti.
- Vytvoří uživatelské jméno správce a heslo pro váš server, pokud není k dispozici. **Poznamenejte si uživatelské jméno a heslo** , které chcete použít v dalším kroku.
- Vytvořte databázi ```postgres``` , kterou lze použít pro vývoj. Můžete spustit [ **psql** pro připojení k databázi](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) a vytvořit tak jinou databázi.

> [!NOTE]
> Poznamenejte si heslo, které vám bude vygenerováno, pokud není k dispozici. Pokud zapomenete heslo, museli byste heslo resetovat pomocí příkazu. ``` az postgres flexible-server update```


## <a name="deploy-the-code-to-azure-app-service"></a>Nasaďte kód pro Azure App Service

V této části vytvoříte hostitele aplikací v aplikaci App Service, připojíte tuto aplikaci k databázi Postgres a potom do tohoto hostitele nasadíte svůj kód.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Vytvoření webové aplikace v App Service ve virtuální síti

V terminálu se ujistěte, že jste v kořenovém adresáři úložiště ( `djangoapp` ), který obsahuje kód aplikace.

Vytvořte aplikaci App Service (hostitelský proces) pomocí [`az webapp up`](/cli/azure/webapp#az-webapp-up) příkazu:

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- Pro `--location` argument použijte stejné umístění jako u databáze v předchozí části.
- Nahraďte *\<app-name>* jedinečným názvem v rámci všech Azure (koncový bod serveru je `https://\<app-name>.azurewebsites.net` ). Povolené znaky pro *\<app-name>* jsou `A` - `Z` , `0` - `9` a `-` . Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.
- Pokud neexistuje, vytvořte plán [App Service](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial* v cenové úrovni Basic (B1). `--plan` a `--sku` jsou volitelné.
- Pokud neexistuje, vytvořte aplikaci App Service.
- Povolí výchozí protokolování pro aplikaci, pokud ještě není povolené.
- Nahrajte úložiště pomocí nasazení ZIP s povoleným automatizací buildu.
- **AZ WebApp VNet-Integration** příkaz přidá webovou aplikaci ve stejné virtuální síti jako server Postgres.
- Kód aplikace očekává nalezení informací o databázi v řadě proměnných prostředí. K nastavení proměnných prostředí v App Service vytvoříte nastavení aplikace pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

> [!TIP]
> Mnoho příkazů rozhraní příkazového řádku Azure v mezipaměti běžně ukládá společné parametry, jako je název skupiny prostředků a plán App Service, do souboru *. Azure/config*. V důsledku toho nemusíte zadávat stejný parametr s pozdějšími příkazy. Například pro opětovné nasazení aplikace po provedení změn můžete spustit `az webapp up` znovu bez parametrů.

### <a name="run-django-database-migrations"></a>Spustit migrace databáze Django

Migrace databáze Django zajišťují, že schéma v PostgreSQL ve službě Azure Database se shoduje s hodnotami popsanými v kódu.

1. V prohlížeči otevřete relaci SSH tak, že přejdete na *https:// \<app-name> . SCM.azurewebsites.NET/webssh/Host* a přihlásíte se pomocí svých přihlašovacích údajů k účtu Azure (nejedná se o přihlašovací údaje k databázovému serveru).

1. V relaci SSH spusťte následující příkazy (příkazy můžete vložit pomocí **kombinace kláves CTRL** + **SHIFT** + **V**):

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. `createsuperuser`Příkaz vás vyzve k zadání přihlašovacích údajů pro uživatele. Pro účely tohoto kurzu použijte výchozí uživatelské jméno `root` , stiskněte klávesu **ENTER** pro e-mailovou adresu, aby byla ponechána prázdná, a zadejte `postgres1` heslo.

### <a name="create-a-poll-question-in-the-app"></a>Vytvoření otázky dotazování v aplikaci

1. V prohlížeči otevřete adresu URL *http: \/ / \<app-name> . azurewebsites.NET*. Aplikace by měla zobrazovat zprávu "nejsou k dispozici žádná hlasování", protože v databázi zatím nejsou žádná konkrétní hlasování.

1. Přejděte na *http: \/ / \<app-name> . azurewebsites.NET/admin*. Přihlaste se pomocí přihlašovacích údajů naduživatelem z předchozí části ( `root` a `postgres1` ). V části **cyklické dotazování** vyberte **Přidat** vedle **otázky** a vytvořte otázku dotazování s některými možnostmi.

1. Přejděte znovu na *http: \/ / \<app-name> . azurewebsites.NET/* a potvrďte, že se teď otázky zobrazují uživateli. Odpovězte na otázky, ale chcete v databázi generovat nějaká data.

**Blahopřejeme!** Spouštíte webovou aplikaci Python Django v Azure App Service pro Linux s aktivní databází Postgres.

> [!NOTE]
> App Service detekuje projekt Django hledáním souboru *WSGI.py* v každé podsložce, která se `manage.py startproject` ve výchozím nastavení vytvoří. Když App Service najde tento soubor, načte webovou aplikaci Django. Další informace najdete v tématu [Konfigurace integrované image Pythonu](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Provádění změn kódu a opětovné nasazení

V této části provedete místní změny aplikace a znovu nasadíte kód do App Service. V tomto procesu nastavíte virtuální prostředí Python, které podporuje průběžnou práci.

### <a name="run-the-app-locally"></a>Místní spuštění aplikace

V okně terminálu spusťte následující příkazy. Při vytváření naduživatelem nezapomeňte postupovat podle pokynů:

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
Po úplném načtení webové aplikace poskytuje vývojový server Django v této zprávě adresu URL místní aplikace – "spuštění vývojového serveru na adrese http://127.0.0.1:8000/ . Ukončete Server pomocí kombinace kláves CTRL + BREAK.

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Příklad výstupu vývojového serveru Django":::

Otestujte aplikaci místně pomocí následujících kroků:

1. V prohlížeči otevřete *http: \/ /localhost: 8000* , ve kterém by se měla zobrazit zpráva "žádné dotazy nejsou k dispozici".

1. Přejít na *http: \/ /localhost: 8000/admin* a přihlaste se pomocí uživatele s oprávněními, kterého jste vytvořili dříve. V části **cyklické dotazování** znovu vyberte **Přidat** další **otázky** a vytvořte otázku dotazování s některými možnostmi.

1. Přejít na *http: \/ /localhost: 8000* znovu a odpovědět na dotaz pro otestování aplikace.

1. Zastavte Server Django stisknutím **kombinace kláves CTRL** + **C**.

Při místním spuštění aplikace používá místní databázi sqlite3 a nekoliduje s vaší provozní databází. V případě potřeby můžete také místní databázi PostgreSQL použít k lepšímu simulaci provozního prostředí.



### <a name="update-the-app"></a>Aktualizace aplikace

V nástroji `polls/models.py` Najděte řádek, který začíná `choice_text` a změňte `max_length` parametr na 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Vzhledem k tomu, že jste změnili datový model, vytvořte novou migraci Django a migrujte databázi:

```
python manage.py makemigrations
python manage.py migrate
```

Spusťte vývojový server znovu s `python manage.py runserver` a otestujte aplikaci na adrese *http: \/ /localhost: 8000/admin*:

Zastavte webový server Django znovu s **kombinací CTRL** + **C**.


### <a name="redeploy-the-code-to-azure"></a>Opětovné nasazení kódu do Azure

V kořenovém adresáři úložiště spusťte následující příkaz:

```azurecli
az webapp up
```

Tento příkaz používá parametry uložené v mezipaměti v souboru *. Azure/config* . Vzhledem k tomu, že aplikace App Service zjistí, že aplikace již existuje, pouze znovu nasadí kód.



### <a name="rerun-migrations-in-azure"></a>Znovu spustit migrace v Azure

Vzhledem k tomu, že jste provedli změny v datovém modelu, je nutné znovu spustit migrace databáze v App Service.

Otevřete znovu relaci SSH v prohlížeči tak, že přejdete na *https:// \<app-name> . SCM.azurewebsites.NET/webssh/Host*. Potom spusťte následující příkazy:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Kontrola aplikace v produkčním prostředí

Přejděte na *http: \/ / \<app-name> . azurewebsites.NET* a otestujte aplikaci znovu v produkčním prostředí. (Vzhledem k tomu, že jste změnili jenom délku databázového pole, změna se dá poznamenat jenom v případě, že se při vytváření otázky pokusíte zadat delší odpověď.)

> [!TIP]
> [Django úložiště](https://django-storages.readthedocs.io/en/latest/backends/azure.html) můžete použít k ukládání statických & mediálních prostředků ve službě Azure Storage. Pro gzipping statické soubory můžete použít Azure CDN.


## <a name="manage-your-app-in-the-azure-portal"></a>Správa aplikace v Azure Portal

V [Azure Portal](https://portal.azure.com)vyhledejte název aplikace a ve výsledcích vyberte aplikaci.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="V Azure Portal přejděte do vaší aplikace Python Django":::

Ve výchozím nastavení na portálu se zobrazí stránka **Přehled** vaší aplikace, která poskytuje obecné zobrazení výkonu. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Správa aplikace Python Django na stránce Přehled v Azure Portal":::


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete aplikaci zachovat nebo pokračovat v dalším kurzu, přeskočte dopředu k [dalším krokům](#next-steps). Jinak, abyste se vyhnuli průběžným poplatkům, můžete odstranit skupinu prostředků vytvořenou pro tento kurz:

```azurecli
az group delete -g myresourcegroup
```

Příkaz používá název skupiny prostředků uložený v souboru *. Azure/config* . Odstraněním skupiny prostředků také zrušíte přidělení a odstraníte všechny prostředky, které jsou v ní obsažené.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak namapovat vlastní název DNS na aplikaci:

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](../../app-service/app-service-web-tutorial-custom-domain.md)

Přečtěte si, jak App Service spouští aplikaci v Pythonu:

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](../../app-service/configure-language-python.md)
