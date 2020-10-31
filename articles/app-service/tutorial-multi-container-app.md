---
title: 'Kurz: Vytvoření aplikace s více kontejnery'
description: Naučte se, jak pomocí sestavení aplikace s více kontejnery na Azure App Service, která obsahuje aplikaci WordPress a kontejner MySQL, nakonfigurovat aplikaci WordPress.
keywords: Azure App Service, Web App, Linux, Docker, sestavení, více kontejnerů, Web App for Containers, více kontejnerů, kontejnerů, WordPress, Azure DB pro MySQL, provozní databáze s kontejnery
author: msangapu-msft
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: msangapu
ms.custom: cli-validate, devx-track-azurecli
ms.openlocfilehash: f2f1713866eb06b4b514ff988ef3e010491e1efc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131339"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Kurz: Vytvoření vícekontejnerové aplikace (verze Preview) ve službě Web App for Containers

> [!NOTE]
> Vícenásobný kontejner je ve verzi Preview.

Služba [Web App for Containers](overview.md#app-service-on-linux) poskytuje flexibilní způsob, jak používat image Dockeru. V tomto kurzu zjistíte, jak vytvořit vícekontejnerovou aplikaci pomocí WordPressu a MySQL. K dokončení tohoto kurzu použijete Cloud Shell. Ke spuštění těchto příkazů také můžete použít nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) (verze 2.0.32 nebo novější).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Převést konfiguraci Docker Compose tak, aby fungovala se službou Web App for Containers
> * Nasadit vícekontejnerovou aplikaci do Azure
> * Přidání nastavení aplikace
> * Použít trvalé úložiště pro kontejnery
> * Připojit se ke službě Azure Database for MySQL
> * Řešení chyb

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete zkušenosti s [Docker Compose](https://docs.docker.com/compose/).

## <a name="download-the-sample"></a>Stažení ukázky

V tomto kurzu použijete soubor pro vytvoření z [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), ale upravíte ho tak, aby zahrnoval Azure Database for MySQL, trvalé úložiště a Redis. Tento konfigurační soubor najdete mezi [ukázkami Azure](https://github.com/Azure-Samples/multicontainerwordpress). Podporované možnosti konfigurace najdete v tématu [možnosti Docker Compose](configure-custom-container.md#docker-compose-options).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Ve službě Cloud Shell vytvořte adresář kurzu a přejděte do něj.

```bash
mkdir tutorial

cd tutorial
```

Potom spusťte následující příkaz, pomocí kterého do tohoto adresáře kurzu naklonujete úložiště ukázkové aplikace. Pak přejděte do adresáře `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

V Cloud Shell vytvořte skupinu prostředků pomocí [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) příkazu. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *USA – středojih* . Pokud chcete zobrazit všechna podporovaná umístění pro službu App Service v Linuxu na úrovni **Standard** , spusťte příkaz [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko.

Po dokončení příkazu se ve výstupu JSON zobrazí vlastnosti skupiny prostředků.

## <a name="create-an-azure-app-service-plan"></a>Vytvoření plánu služby Azure App Service

V Cloud Shell vytvořte pomocí příkazu plán App Service ve skupině prostředků [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) .

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

Následující příklad vytvoří plán služby App Service s názvem `myAppServicePlan` v cenové úrovni **Standard** (`--sku S1`) a v kontejneru Linuxu (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Po vytvoření plánu služby App Service se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose s kontejnery WordPressu a MySQL

## <a name="create-a-docker-compose-app"></a>Vytvoření aplikace Docker Compose

Ve službě Cloud Shell pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte vícekontejnerovou [webovou aplikaci](overview.md) v plánu služby App Service `myAppServicePlan`. Nezapomeňte nahradit _\<app-name>_ jedinečným názvem aplikace.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po vytvoření webové aplikace se ve službě Cloud Shell zobrazí podobný výstup jako v následujícímu příkladu:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app-name>.azurewebsites.net`. Načtení aplikace může pár minut trvat. Když se zobrazí chyba, ještě chvíli počkejte a pak aktualizujte prohlížeč. Pokud máte problémy a chcete je vyřešit, prohlédněte si [protokoly kontejneru](#find-docker-container-logs).

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

**Blahopřejeme** , vytvořili jste vícekontejnerovou aplikaci ve službě Web App for Containers. Dále aplikaci nakonfigurujete tak, aby používala službu Azure Database for MySQL. Tentokrát nebudete instalovat WordPress.

## <a name="connect-to-production-database"></a>Připojení k produkční databázi

Kontejnery databáze se nedoporučuje používat v produkčním prostředí. Místní kontejnery nejsou škálovatelné. Místo toho použijete službu Azure Database for MySQL, která je škálovatelná.

### <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL

Pomocí příkazu vytvořte server Azure Database for MySQL [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) .

V následujícím příkazu nahraďte název serveru MySQL, kde se zobrazí zástupný symbol _&lt; MySQL-Server-Name>_ (platné znaky jsou `a-z` , `0-9` a `-` ). Tento název je součástí názvu hostitele serveru MySQL (`<mysql-server-name>.database.windows.net`) a musí být globálně jedinečný.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen5_1 --version 5.7
```

Vytvoření serveru může trvat několik minut. Po vytvoření serveru MySQL se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

<pre>
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "southcentralus",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
</pre>

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Vytvořte pravidlo brány firewall pro server MySQL, aby bylo možné připojit klienta pomocí [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) příkazu. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Vytvoření databáze WordPressu

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Po vytvoření databáze se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

<pre>
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
</pre>

### <a name="configure-database-variables-in-wordpress"></a>Konfigurace proměnných databáze ve WordPressu

Kvůli připojení aplikace WordPress k tomuto novému serveru MySQL nakonfigurujete několik proměnných prostředí specifických pro WordPress včetně cesty k certifikační autoritě SSL definované pomocí `MYSQL_SSL_CA`. Níže uvedená [vlastní image](#use-a-custom-image-for-mysql-ssl-and-other-configurations) obsahuje [kořenový certifikát Baltimore CyberTrust](https://www.digicert.com/digicert-root-certificates.htm) od společnosti [DigiCert](https://www.digicert.com/).

K provedení těchto změn použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

<pre>
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "&lt;mysql-server-name&gt;.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
</pre>

Další informace o proměnných prostředí najdete v tématu [konfigurace proměnných prostředí](configure-custom-container.md#configure-environment-variables).

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Použití vlastní image pro MySQL SSL a jiné konfigurace

Služba Azure Database for MySQL používá standardně protokol SSL. WordPress vyžaduje pro použití protokolu SSL s MySQL dodatečnou konfiguraci. Oficiální image WordPressu neposkytuje další konfiguraci, ale [vlastní image](https://github.com/Azure-Samples/multicontainerwordpress) je připravená pro vaše pohodlí. V praxi byste požadované změny přidali do své vlastní image.

Tato vlastní image vychází z „oficiální image“ [WordPressu v Centru Dockeru](https://hub.docker.com/_/wordpress/). Pro službu Azure Database for MySQL byly v této vlastní imagi provedeny následující změny:

* [Do MySQL byl přidán soubor kořenového certifikátu Baltimore Cyber Trust pro SSL](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61).
* [V souboru wp-config.php WordPressu se používá nastavení aplikace pro certifikát certifikační autority MySQL SSL](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163).
* [Byla přidána definice WordPressu pro MYSQL_CLIENT_FLAGS potřebná pro MySQL SSL](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164).

Následující změny byly provedeny pro Redis (bude použito později):

* [Bylo přidáno rozšíření PHP pro Redis verze 4.0.2](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35).
* [Byl přidán program unzip potřebný k rozbalení souborů](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71).
* [Byl přidán modul plug-in Mezipaměť objektů Redis 1.3.8 pro WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [V souboru wp-config.php WordPressu se používá nastavení aplikace pro název hostitele Redis](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162).

Abyste mohli tuto vlastní image použít, aktualizujete soubor docker-compose-wordpress.yml. Ve službě Cloud Shell otevřete textový editor nano zadáním příkazu `nano docker-compose-wordpress.yml`. `image: wordpress` změňte tak, aby se používalo `image: mcr.microsoft.com/azuredocs/multicontainerwordpress`. Kontejner databáze už nepotřebujete. Odeberte z konfiguračního souboru oddíly `db`, `environment`, `depends_on` a `volumes`. Soubor by měl připomínat následující kód:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Uložte změny a editor nano zavřete. K uložení použijte příkaz `^O` a k zavření příkaz `^X`.

### <a name="update-app-with-new-configuration"></a>Aktualizace aplikace o novou konfiguraci

Ve službě Cloud Shell změňte příkazem [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) konfiguraci vícekontejnerové [webové aplikace](overview.md). Nezapomeňte nahradit _\<app-name>_ názvem webové aplikace, kterou jste vytvořili dříve.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po změně konfigurace aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app-name>.azurewebsites.net`. Aplikace teď používá službu Azure Database for MySQL.

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

## <a name="add-persistent-storage"></a>Přidání trvalého úložiště

Ve více kontejnerech se teď provozuje služba Web App for Containers. Pokud ale teď nainstalujete WordPress a později aplikaci restartujete, zjistíte, že instalace WordPressu zmizela. Konfigurace Docker Compose totiž momentálně ukazuje na umístění úložiště uvnitř vašeho kontejneru. Soubory nainstalované do tohoto kontejneru se po restartování aplikace nezachovají. V této části přidáte do svého kontejneru WordPress [trvalé úložiště](configure-custom-container.md#use-persistent-shared-storage) .

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Pokud chcete použít trvalé úložiště, povolte toto nastavení v rámci App Service. K provedení této změny použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
</pre>

### <a name="modify-configuration-file"></a>Úprava konfiguračního souboru

Ve službě Cloud Shell otevřete textový editor nano zadáním příkazu `nano docker-compose-wordpress.yml`.

Možnost `volumes` mapuje systém souborů na určitý adresář uvnitř kontejneru. `${WEBAPP_STORAGE_HOME}` je proměnná prostředí ve službě App Service, která je namapovaná na trvalé úložiště vaší aplikace. Tuto proměnnou prostředí použijete u možnosti volumes, aby se soubory WordPressu nainstalovaly do trvalého úložiště místo do kontejneru. Proveďte v tomto souboru následující změny:

Do sekce `wordpress` přidejte možnost `volumes` tak, aby vypadala jako následující kód:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Aktualizace aplikace o novou konfiguraci

Ve službě Cloud Shell změňte příkazem [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) konfiguraci vícekontejnerové [webové aplikace](overview.md). Nezapomeňte nahradit _\<app-name>_ jedinečným názvem aplikace.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po spuštění příkazu se zobrazí výstup podobný následujícímu příkladu:

<pre>
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app-name>.azurewebsites.net`.

Kontejner WordPressu teď používá službu Azure Database for MySQL a trvalé úložiště.

## <a name="add-redis-container"></a>Přidání kontejneru Redis

 „Oficiální image“ WordPressu neobsahuje závislosti pro Redis. Tyto závislosti a dodatečná konfigurace potřebná pro použití Redis s WordPressem byly připraveny v této [vlastní imagi](https://github.com/Azure-Samples/multicontainerwordpress). V praxi byste požadované změny přidali do své vlastní image.

Tato vlastní image vychází z „oficiální image“ [WordPressu v Centru Dockeru](https://hub.docker.com/_/wordpress/). V této vlastní imagi byly provedeny následující změny pro Redis:

* [Bylo přidáno rozšíření PHP pro Redis verze 4.0.2](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35).
* [Byl přidán program unzip potřebný k rozbalení souborů](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71).
* [Byl přidán modul plug-in Mezipaměť objektů Redis 1.3.8 pro WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [V souboru wp-config.php WordPressu se používá nastavení aplikace pro název hostitele Redis](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162).

Přidejte kontejner Redis na konec konfiguračního souboru tak, aby vypadal jako v následujícím příkladu:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always

   redis:
     image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
     environment: 
      - ALLOW_EMPTY_PASSWORD=yes
     restart: always
```

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Abyste mohli použít Redis, povolíte ve službě App Service nastavení `WP_REDIS_HOST`. Jde o *povinné nastavení* , které WordPressu umožňuje komunikovat s hostitelem Redis. K provedení této změny použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
</pre>

### <a name="update-app-with-new-configuration"></a>Aktualizace aplikace o novou konfiguraci

Ve službě Cloud Shell změňte příkazem [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) konfiguraci vícekontejnerové [webové aplikace](overview.md). Nezapomeňte nahradit _\<app-name>_ jedinečným názvem aplikace.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Po spuštění příkazu se zobrazí výstup podobný následujícímu příkladu:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app-name>.azurewebsites.net`.

Dokončete tento postup a nainstalujte WordPress.

### <a name="connect-wordpress-to-redis"></a>Připojení WordPressu k Redis

Přihlaste se ke Správci WordPress. V levém navigačním panelu vyberte **moduly plug** -in a pak vyberte **nainstalované moduly plug-in** .

![Výběr modulů plug-in pro WordPress][2]

Zobrazí se všechny moduly plug-in.

Na stránce s moduly plug-in najděte **Mezipaměť objektů Redis** a klikněte na **Aktivovat** .

![Aktivace Redis][3]

Klikněte na **Nastavení** .

![Kliknutí na Nastavení][4]

Klikněte na tlačítko **Povolit mezipaměť objektů** .

![Kliknutí na tlačítko Povolit mezipaměť objektů][5]

WordPress se připojí k serveru Redis. Na stejné stránce se zobrazí **stav** připojení.

![WordPress se připojí k serveru Redis. Na stejné stránce se zobrazí **stav** připojení.][6]

**Blahopřejeme** , připojili jste WordPress k Redis. Aplikace připravená do produkce teď používá službu **Azure Database for MySQL, trvalé úložiště a Redis** . Kapacitu plánu služby App Service teď můžete horizontálně navýšit na více instancí.

## <a name="find-docker-container-logs"></a>Nalezení protokolů kontejneru Dockeru

Pokud při používání více kontejnerů narazíte na problémy, můžete přechodem na adresu `https://<app-name>.scm.azurewebsites.net/api/logs/docker` zpřístupnit protokoly kontejneru.

Zobrazí se výstup podobný následujícímu příkladu:

<pre>
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://&lt;app-name&gt;.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
</pre>

Můžete si prohlédnout protokoly pro jednotlivé kontejnery a další protokol pro nadřazený proces. Protokol zobrazíte tak, že do prohlížeče zkopírujete příslušnou hodnotu `href`.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Převést konfiguraci Docker Compose tak, aby fungovala se službou Web App for Containers
> * Nasadit vícekontejnerovou aplikaci do Azure
> * Přidání nastavení aplikace
> * Použít trvalé úložiště pro kontejnery
> * Připojit se ke službě Azure Database for MySQL
> * Řešení chyb

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
