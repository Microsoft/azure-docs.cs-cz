---
title: Vytvoření aplikace více kontejnerů ve službě Web App for Containers – Azure App Service
description: Zjistěte, jak v Azure používat více kontejnerů s konfiguračními soubory Docker Compose a Kubernetes pomocí WordPressu a aplikace MySQL.
keywords: azure app service, web app, linux, docker, compose, multicontainer, multi-container, web app for containers, multiple containers, container, kubernetes, wordpress, azure db for mysql, production database with containers
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7feb168d9cbcb9fc19e6fcd320bbf87c15077a50
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251636"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Kurz: Vytvoření vícekontejnerové aplikace (Preview) ve službě Web App for Containers

Služba [Web App for Containers](app-service-linux-intro.md) poskytuje flexibilní způsob, jak používat image Dockeru. V tomto kurzu zjistíte, jak vytvořit vícekontejnerovou aplikaci pomocí WordPressu a MySQL. K dokončení tohoto kurzu použijete Cloud Shell. Ke spuštění těchto příkazů také můžete použít nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) (verze 2.0.32 nebo novější).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Převést konfiguraci Docker Compose tak, aby fungovala se službou Web App for Containers
> * Převést konfiguraci Kubernetes tak, aby fungovala se službou Web App for Containers
> * Nasadit vícekontejnerovou aplikaci do Azure
> * Přidat nastavení aplikace
> * Použít trvalé úložiště pro kontejnery
> * Připojit se ke službě Azure Database for MySQL
> * Řešení chyb

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musíte mít zkušenosti s [Docker Compose](https://docs.docker.com/compose/) nebo [Kubernetes](https://kubernetes.io/).

## <a name="download-the-sample"></a>Stažení ukázky

Pro účely tohoto kurzu použijete soubor compose z [Dockeru](https://docs.docker.com/compose/wordpress/#define-the-project), upravíte ho ale tak, aby zahrnoval Azure Database for MySQL, trvalé úložiště a Redis. Tento konfigurační soubor najdete mezi [ukázkami Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

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

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Ve službě Cloud Shell pomocí příkazu [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *Střed USA – jih*. Pokud chcete zobrazit všechna podporovaná umístění pro službu App Service v Linuxu na úrovni **Standard**, spusťte příkaz [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko.

Po dokončení příkazu se ve výstupu JSON zobrazí vlastnosti skupiny prostředků.

## <a name="create-an-azure-app-service-plan"></a>Vytvoření plánu služby Azure App Service

Ve službě Cloud Shell pomocí příkazu [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) vytvořte ve skupině prostředků plán služby App Service.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

Následující příklad vytvoří plán služby App Service s názvem `myAppServicePlan` v cenové úrovni **Standard** (`--sku S1`) a v kontejneru Linuxu (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Po vytvoření plánu služby App Service se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
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
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="docker-compose-configuration-options"></a>Možnosti konfigurace Docker Compose

Pro účely tohoto kurzu použijete soubor compose z [Dockeru](https://docs.docker.com/compose/wordpress/#define-the-project), upravíte ho ale tak, aby zahrnoval Azure Database for MySQL, trvalé úložiště a Redis. Alternativně můžete použít [konfiguraci Kubernetes](#use-a-kubernetes-configuration-optional). Tyto konfigurační soubory najdete mezi [ukázkami Azure](https://github.com/Azure-Samples/multicontainerwordpress).

Následující seznamy obsahují podporované a nepodporované možnosti konfigurace Docker Compose ve službě Web App for Containers:

### <a name="supported-options"></a>Podporované možnosti

* command
* entrypoint
* environment
* image
* ports
* restart
* services
* volumes

### <a name="unsupported-options"></a>Nepodporované možnosti

* build (nepovoleno)
* depends_on (ignorováno)
* networks (ignorováno)
* secrets (ignorováno)

> [!NOTE]
> Jakékoli jiné možnosti, které nejsou explicitně volané, se ve veřejné verzi Preview také ignorují.

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose s kontejnery WordPressu a MySQL

## <a name="create-a-docker-compose-app"></a>Vytvoření aplikace Docker Compose

Ve službě Cloud Shell pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte vícekontejnerovou [webovou aplikaci](app-service-linux-intro.md) v plánu služby App Service `myAppServicePlan`. Nezapomeňte nahradit _\<app_name>_ jedinečným názvem aplikace.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po vytvoření webové aplikace se ve službě Cloud Shell zobrazí podobný výstup jako v následujícímu příkladu:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app_name>.azurewebsites.net`. Načtení aplikace může pár minut trvat. Když se zobrazí chyba, ještě chvíli počkejte a pak aktualizujte prohlížeč. Pokud máte problémy a chcete je vyřešit, prohlédněte si [protokoly kontejneru](#find-docker-container-logs).

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

**Blahopřejeme**, vytvořili jste vícekontejnerovou aplikaci ve službě Web App for Containers. Dále aplikaci nakonfigurujete tak, aby používala službu Azure Database for MySQL. Tentokrát nebudete instalovat WordPress.

## <a name="connect-to-production-database"></a>Připojení k produkční databázi

Kontejnery databáze se nedoporučuje používat v produkčním prostředí. Místní kontejnery nejsou škálovatelné. Místo toho použijete službu Azure Database for MySQL, která je škálovatelná.

### <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL

Příkazem [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) vytvořte server Azure Database for MySQL.

V následujícím příkazu nahraďte zástupný text _&lt;mysql_server_name>_ názvem vašeho serveru MySQL (platné znaky jsou `a-z`, `0-9` a `-`). Tento název je součástí názvu hostitele serveru MySQL (`<mysql_server_name>.database.windows.net`) a musí být globálně jedinečný.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Vytvoření serveru může trvat několik minut. Po vytvoření serveru MySQL se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Pomocí příkazu [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) vytvořte pro svůj server MySQL pravidlo brány firewall umožňující klientská připojení. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Vytvoření databáze WordPressu

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Po vytvoření databáze se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Konfigurace proměnných databáze ve WordPressu

Kvůli připojení aplikace WordPress k tomuto novému serveru MySQL nakonfigurujete několik proměnných prostředí specifických pro WordPress včetně cesty k certifikační autoritě SSL definované pomocí `MYSQL_SSL_CA`. Níže uvedená [vlastní image](https://docs.microsoft.com/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations) obsahuje [kořenový certifikát Baltimore CyberTrust](https://www.digicert.com/digicert-root-certificates.htm) od společnosti [DigiCert](https://www.digicert.com/).

K provedení těchto změn použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
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
```

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Použití vlastní image pro MySQL SSL a jiné konfigurace

Služba Azure Database for MySQL používá standardně protokol SSL. WordPress vyžaduje pro použití protokolu SSL s MySQL dodatečnou konfiguraci. „Oficiální image“ WordPressu tuto dodatečnou konfiguraci neobsahuje, ale pro vaše pohodlí je připravená [vlastní image](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/). V praxi byste požadované změny přidali do své vlastní image.

Tato vlastní image vychází z „oficiální image“ [WordPressu v Centru Dockeru](https://hub.docker.com/_/wordpress/). Pro službu Azure Database for MySQL byly v této vlastní imagi provedeny následující změny:

* [Do MySQL byl přidán soubor kořenového certifikátu Baltimore Cyber Trust pro SSL](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61).
* [V souboru wp-config.php WordPressu se používá nastavení aplikace pro certifikát certifikační autority MySQL SSL](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163).
* [Byla přidána definice WordPressu pro MYSQL_CLIENT_FLAGS potřebná pro MySQL SSL](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164).

Následující změny byly provedeny pro Redis (bude použito později):

* [Bylo přidáno rozšíření PHP pro Redis verze 4.0.2](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35).
* [Byl přidán program unzip potřebný k rozbalení souborů](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71).
* [Byl přidán modul plug-in Mezipaměť objektů Redis 1.3.8 pro WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [V souboru wp-config.php WordPressu se používá nastavení aplikace pro název hostitele Redis](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162).

Abyste mohli tuto vlastní image použít, aktualizujete soubor docker-compose-wordpress.yml. Ve službě Cloud Shell otevřete textový editor nano zadáním příkazu `nano docker-compose-wordpress.yml`. `image: wordpress` změňte tak, aby se používalo `image: microsoft/multicontainerwordpress`. Kontejner databáze už nepotřebujete. Odeberte z konfiguračního souboru oddíly `db`, `environment`, `depends_on` a `volumes`. Soubor by měl připomínat následující kód:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Uložte změny a editor nano zavřete. K uložení použijte příkaz `^O` a k zavření příkaz `^X`.

### <a name="update-app-with-new-configuration"></a>Aktualizace aplikace o novou konfiguraci

Ve službě Cloud Shell změňte příkazem [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) konfiguraci vícekontejnerové [webové aplikace](app-service-linux-intro.md). Nezapomeňte nahradit _\<app_name>_ názvem dříve vytvořené webové aplikace.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po změně konfigurace aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app_name>.azurewebsites.net`. Aplikace teď používá službu Azure Database for MySQL.

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

## <a name="add-persistent-storage"></a>Přidání trvalého úložiště

Ve více kontejnerech se teď provozuje služba Web App for Containers. Pokud ale teď nainstalujete WordPress a později aplikaci restartujete, zjistíte, že instalace WordPressu zmizela. Konfigurace Docker Compose totiž momentálně ukazuje na umístění úložiště uvnitř vašeho kontejneru. Soubory nainstalované do tohoto kontejneru se po restartování aplikace nezachovají. V tomto oddílu přidáte do kontejneru WordPressu trvalé úložiště.

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Kvůli použití trvalého úložiště povolíte ve službě App Service toto nastavení. K provedení této změny použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
[
  < JSON data removed for brevity. >
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
```

### <a name="modify-configuration-file"></a>Úprava konfiguračního souboru

Ve službě Cloud Shell otevřete textový editor nano zadáním příkazu `nano docker-compose-wordpress.yml`.

Možnost `volumes` mapuje systém souborů na určitý adresář uvnitř kontejneru. `${WEBAPP_STORAGE_HOME}` je proměnná prostředí ve službě App Service, která je namapovaná na trvalé úložiště vaší aplikace. Tuto proměnnou prostředí použijete u možnosti volumes, aby se soubory WordPressu nainstalovaly do trvalého úložiště místo do kontejneru. Proveďte v tomto souboru následující změny:

Do sekce `wordpress` přidejte možnost `volumes` tak, aby vypadala jako následující kód:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Aktualizace aplikace o novou konfiguraci

Ve službě Cloud Shell změňte příkazem [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) konfiguraci vícekontejnerové [webové aplikace](app-service-linux-intro.md). Nezapomeňte nahradit _\<app_name>_ jedinečným názvem aplikace.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po spuštění příkazu se zobrazí výstup podobný následujícímu příkladu:

```json
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
```

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app_name>.azurewebsites.net`.

Kontejner WordPressu teď používá službu Azure Database for MySQL a trvalé úložiště.

## <a name="add-redis-container"></a>Přidání kontejneru Redis

 „Oficiální image“ WordPressu neobsahuje závislosti pro Redis. Tyto závislosti a dodatečná konfigurace potřebná pro použití Redis s WordPressem byly připraveny v této [vlastní imagi](https://github.com/Azure-Samples/multicontainerwordpress). V praxi byste požadované změny přidali do své vlastní image.

Tato vlastní image vychází z „oficiální image“ [WordPressu v Centru Dockeru](https://hub.docker.com/_/wordpress/). V této vlastní imagi byly provedeny následující změny pro Redis:

* [Bylo přidáno rozšíření PHP pro Redis verze 4.0.2](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35).
* [Byl přidán program unzip potřebný k rozbalení souborů](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71).
* [Byl přidán modul plug-in Mezipaměť objektů Redis 1.3.8 pro WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [V souboru wp-config.php WordPressu se používá nastavení aplikace pro název hostitele Redis](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162).

Přidejte kontejner Redis na konec konfiguračního souboru tak, aby vypadal jako v následujícím příkladu:

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Abyste mohli použít Redis, povolíte ve službě App Service nastavení `WP_REDIS_HOST`. Jde o *povinné nastavení*, které WordPressu umožňuje komunikovat s hostitelem Redis. K provedení této změny použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WP_REDIS_HOST="redis"
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>Aktualizace aplikace o novou konfiguraci

Ve službě Cloud Shell změňte příkazem [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) konfiguraci vícekontejnerové [webové aplikace](app-service-linux-intro.md). Nezapomeňte nahradit _\<app_name>_ jedinečným názvem aplikace.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Po spuštění příkazu se zobrazí výstup podobný následujícímu příkladu:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app_name>.azurewebsites.net`.

Dokončete tento postup a nainstalujte WordPress.

### <a name="connect-wordpress-to-redis"></a>Připojení WordPressu k Redis

Přihlaste se do správy WordPressu. V navigaci nalevo vyberte **Moduly plug-in** a pak vyberte **Nainstalované moduly plug-in**.

![Výběr modulů plug-in pro WordPress][2]

Zobrazí se všechny moduly plug-in.

Na stránce s moduly plug-in najděte **Mezipaměť objektů Redis** a klikněte na **Aktivovat**.

![Aktivace Redis][3]

Klikněte na **Nastavení**.

![Kliknutí na Nastavení][4]

Klikněte na tlačítko **Povolit mezipaměť objektů**.

![Kliknutí na tlačítko Povolit mezipaměť objektů][5]

WordPress se připojí k serveru Redis. Na stejné stránce se zobrazí **stav** připojení.

![WordPress se připojí k serveru Redis. Na stejné stránce se zobrazí **stav** připojení.][6]

**Blahopřejeme**, připojili jste WordPress k Redis. Aplikace připravená do produkce teď používá službu **Azure Database for MySQL, trvalé úložiště a Redis**. Kapacitu plánu služby App Service teď můžete horizontálně navýšit na více instancí.

## <a name="use-a-kubernetes-configuration-optional"></a>Použití konfigurace Kubernetes (volitelné)

V tomto oddílu zjistíte, jak k nasazení několika kontejnerů použít konfiguraci Kubernetes. Nezapomeňte podle dřívějšího postupu vytvořit [skupinu prostředků](#create-a-resource-group) a [plán služby App Service](#create-an-azure-app-service-plan). Protože se většina těchto kroků podobá krokům v oddílu pro Docker Compose, nachystali jsme vám konfigurační soubor.

### <a name="supported-kubernetes-options-for-multi-container"></a>Podporované možnosti Kubernetes pro více kontejnerů

* args
* command
* containers
* image
* jméno
* ports
* spec

> [!NOTE]
>Jakékoli jiné možnosti Kubernetes, které nejsou explicitně volané, se ve veřejné verzi Preview nepodporují.
>

### <a name="kubernetes-configuration-file"></a>Konfigurační soubor Kubernetes

V této části kurzu použijete soubor *kubernetes-wordpress.yml*. Tady si ho můžete prohlédnout:

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

### <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL

Pomocí příkazu [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) vytvořte v Azure Database for MySQL server.

V následujícím příkazu nahraďte zástupný text _&lt;mysql_server_name>_ názvem vašeho serveru MySQL (platné znaky jsou `a-z`, `0-9` a `-`). Tento název je součástí názvu hostitele serveru MySQL (`<mysql_server_name>.database.windows.net`) a musí být globálně jedinečný.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Po vytvoření serveru MySQL se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Pomocí příkazu [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) vytvořte pro svůj server MySQL pravidlo brány firewall umožňující klientská připojení. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Vytvoření databáze WordPressu

Pokud jste to ještě neudělali, vytvořte [server Azure Database for MySQL](#create-an-azure-database-for-mysql-server).

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Po vytvoření databáze se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="create-a-multi-container-app-kubernetes"></a>Vytvoření vícekontejnerové aplikace (Kubernetes)

Ve službě Cloud Shell pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte vícekontejnerovou [webovou aplikaci](app-service-linux-intro.md) ve skupině prostředků `myResourceGroup` a v plánu služby App Service `myAppServicePlan`. Nezapomeňte nahradit _\<app_name>_ jedinečným názvem aplikace.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

Po vytvoření webové aplikace se ve službě Cloud Shell zobrazí podobný výstup jako v následujícímu příkladu:

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
  < JSON data removed for brevity. >
}
```

### <a name="configure-database-variables-in-wordpress"></a>Konfigurace proměnných databáze ve WordPressu

Aplikaci WordPress připojíte k tomuto novému serveru MySQL tak, že nakonfigurujete několik proměnných prostředí specifických pro WordPress. K provedení této změny použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
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
  }
]
```

### <a name="add-persistent-storage"></a>Přidání trvalého úložiště

Ve více kontejnerech se teď provozuje služba Web App for Containers. Tato data se při restartování vymažou, protože soubory se nezachovají. V tomto oddílu přidáte do kontejneru WordPressu trvalé úložiště.

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Kvůli použití trvalého úložiště povolíte ve službě App Service toto nastavení. K provedení této změny použijte příkaz [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Po vytvoření nastavení aplikace se ve službě Cloud Shell zobrazí podobné informace jako v následujícím příkladu:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app_name>.azurewebsites.net`.

Aplikace teď používá několik kontejnerů ve službě Web App for Containers.

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

**Blahopřejeme**, vytvořili jste vícekontejnerovou aplikaci ve službě Web App for Containers.

Pokud chcete použít Redis, použijte postup v části [Připojení WordPressu k Redis](#connect-wordpress-to-redis).

## <a name="find-docker-container-logs"></a>Nalezení protokolů kontejneru Dockeru

Pokud při používání více kontejnerů narazíte na problémy, můžete přechodem na adresu `https://<app_name>.scm.azurewebsites.net/api/logs/docker` zpřístupnit protokoly kontejneru.

Zobrazí se výstup podobný následujícímu příkladu:

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

Můžete si prohlédnout protokoly pro jednotlivé kontejnery a další protokol pro nadřazený proces. Protokol zobrazíte tak, že do prohlížeče zkopírujete příslušnou hodnotu `href`.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Převést konfiguraci Docker Compose tak, aby fungovala se službou Web App for Containers
> * Převést konfiguraci Kubernetes tak, aby fungovala se službou Web App for Containers
> * Nasadit vícekontejnerovou aplikaci do Azure
> * Přidat nastavení aplikace
> * Použít trvalé úložiště pro kontejnery
> * Připojit se ke službě Azure Database for MySQL
> * Řešení chyb

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití vlastní image Dockeru pro službu Web App for Containers](tutorial-custom-docker-image.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
