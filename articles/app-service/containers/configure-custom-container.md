---
title: Konfigurace vlastního kontejneru Linux
description: Přečtěte si, jak nakonfigurovat vlastní kontejner Linux v Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280141"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurace vlastního kontejneru Linux pro Azure App Service

V tomto článku se dozvíte, jak nakonfigurovat vlastní kontejner pro Linux ke spuštění na Azure App Service.

Tato příručka poskytuje klíčové koncepty a pokyny pro kontejnerování aplikací pro Linux v App Service. Pokud jste nikdy Azure App Service nepoužili, postupujte jako první v prvním [rychlém startu vlastního kontejneru](quickstart-docker-go.md) a [kurzu](tutorial-custom-docker-image.md) . K dispozici je také rychlý Start a [kurz](tutorial-multi-container-app.md) [aplikace pro více kontejnerů](quickstart-multi-container.md) .

## <a name="configure-port-number"></a>Konfigurace čísla portu

Webový server ve vlastní imagi může používat jiný port než 80. Azure o portu, který používá vlastní kontejner, sdělíte pomocí nastavení aplikace `WEBSITES_PORT`. Stránka GitHubu pro [ukázku Pythonu v tomto kurzu](https://github.com/Azure-Samples/docker-django-webapp-linux) ukazuje, že je potřeba nastavit `WEBSITES_PORT` na _8000_. Můžete ji nastavit spuštěním příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v Cloud Shell. Příklad:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Vlastní kontejner může používat proměnné prostředí, které je třeba zadat externě. Můžete je předat spuštěním příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v Cloud Shell. Příklad:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Tato metoda funguje jak pro aplikace s jedním kontejnerem, tak pro aplikace s více kontejnery, kde jsou proměnné prostředí určeny v souboru *Docker-Compose. yml* .

## <a name="use-persistent-shared-storage"></a>Použití trvalého sdíleného úložiště

Pomocí adresáře */Home* v systému souborů vaší aplikace můžete uchovávat soubory mezi restarty a sdílet je mezi instancemi. K dispozici je `/home` vaší aplikace, aby mohla vaše aplikace kontejneru přistupovat k trvalému úložišti.

Pokud je trvalé úložiště zakázané, pak se zápisy do adresáře `/home` neukládají mezi restarty aplikace nebo mezi několika instancemi. Jedinou výjimkou je adresář `/home/LogFiles`, který se používá k ukládání protokolů Docker a kontejner. Když je povolené trvalé úložiště, všechny zápisy do adresáře `/home` jsou trvalé a můžou k němu mít pøístup všechny instance aplikace s možností horizontálního rozšíření kapacity.

Ve výchozím nastavení je trvalé úložiště *povolené* a nastavení se v nastavení aplikace nezveřejňuje. Pokud ho chcete zakázat, nastavte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace spuštěním příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v Cloud Shell. Příklad:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Můžete také [nakonfigurovat vlastní trvalé úložiště](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Povolit SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Aby mohl vlastní kontejner podporovat SSH, musíte ho přidat do samotného souboru dockerfileu.

> [!TIP]
> Všechny integrované kontejnery Linux přidaly instrukce SSH do úložišť imagí. Pomocí následujících pokynů můžete v [úložišti Node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) zjistit, jak je tato funkce povolená.

- Použijte instrukci [Run](https://docs.docker.com/engine/reference/builder/#run) k instalaci serveru SSH a nastavte heslo pro kořenový účet na `"Docker!"`. Například pro Image založenou na systému [Alpine Linux](https://hub.docker.com/_/alpine)budete potřebovat následující příkazy:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Tato konfigurace neumožňuje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím `https://<app-name>.scm.azurewebsites.net` a ověřena s přihlašovacími údaji pro publikování.

- Přidejte [Tento soubor sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) do úložiště imagí a pomocí instrukce [copy](https://docs.docker.com/engine/reference/builder/#copy) zkopírujte soubor do adresáře */etc/ssh/* . Další informace o *sshd_config* souborů najdete v [dokumentaci k OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Soubor *sshd_config* musí obsahovat následující položky:
    > - `Ciphers` musí obsahovat alespoň jednu položku v tomto seznamu: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` musí obsahovat alespoň jednu položku v tomto seznamu: `hmac-sha1,hmac-sha1-96`.

- K otevření portu 2222 v kontejneru použijte instrukci [vystavení](https://docs.docker.com/engine/reference/builder/#expose) . I když je známé heslo ke kořenovému adresáři, není port 2222 přístupný z Internetu. Je přístupná pouze kontejnery v rámci mostu sítě privátní virtuální sítě.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- V spouštěcím skriptu pro svůj kontejner spusťte server SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Příklad naleznete v tématu Jak výchozí [kontejner Node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) SPUSTÍ Server SSH.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurace aplikací s více kontejnery

- [Použít trvalé úložiště v Docker Compose](#use-persistent-storage-in-docker-compose)
- [Omezení verze Preview](#preview-limitations)
- [Možnosti Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Použít trvalé úložiště v Docker Compose

Aplikace s více kontejnery, jako je WordPress, potřebují pro správné fungování trvalé úložiště. Pokud ho chcete povolit, vaše konfigurace Docker Compose musí odkazovat na umístění úložiště *mimo* váš kontejner. Umístění úložiště ve vašem kontejneru neukládají změny po restartování aplikace.

Nastavení aplikace `WEBSITES_ENABLE_APP_SERVICE_STORAGE` pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v Cloud Shell povolte trvalé úložiště.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

V souboru *Docker-Compose. yml* namapujte možnost `volumes` na `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` je proměnná prostředí ve službě App Service, která je namapovaná na trvalé úložiště vaší aplikace. Příklad:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Omezení verze Preview

Vícenásobný kontejner je aktuálně ve verzi Preview. Následující funkce App Service platformy nejsou podporovány:

- Ověřování/autorizace
- Spravované identity

### <a name="docker-compose-options"></a>Možnosti Docker Compose

Následující seznamy obsahují podporované a nepodporované možnosti konfigurace Docker Compose:

#### <a name="supported-options"></a>Podporované možnosti

- Příkaz
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nepodporované možnosti

- build (nepovoleno)
- depends_on (ignorováno)
- networks (ignorováno)
- secrets (ignorováno)
- jiné porty než 80 a 8080 (ignorováno)

> [!NOTE]
> Všechny další možnosti, které nejsou explicitně vyvolány, jsou v Public Preview ignorovány.

## <a name="configure-vnet-integration"></a>Konfigurace integrace virtuální sítě

Použití vlastního kontejneru s integrací virtuální sítě může vyžadovat další konfiguraci kontejneru. Viz [integrace aplikace s Virtual Network Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: nasazení z privátního úložiště kontejnerů](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Kurz: aplikace pro více kontejnerů WordPress](tutorial-multi-container-app.md)
