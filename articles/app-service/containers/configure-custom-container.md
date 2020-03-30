---
title: Konfigurace vlastního kontejneru Linuxu
description: Přečtěte si, jak nakonfigurovat vlastní linuxový kontejner ve službě Azure App Service. Tento článek ukazuje nejběžnější úlohy konfigurace.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280141"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurace vlastního kontejneru Linuxu pro azure app service

Tento článek ukazuje, jak nakonfigurovat vlastní kontejner Linuxu pro spuštění ve službě Azure App Service.

Tato příručka obsahuje klíčové koncepty a pokyny pro kontejnerizaci aplikací pro Linux ve službě App Service. Pokud jste službu Azure App Service nikdy nepoužívali, postupujte nejprve podle [vlastního rychlého startu kontejneru](quickstart-docker-go.md) a [kurzu.](tutorial-custom-docker-image.md) K dispozici je také [aplikace s více kontejnery a úvodní](quickstart-multi-container.md) [kurz](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Konfigurace čísla portu

Webový server ve vlastní bitové kopii může používat jiný port než 80. Sdělte Azure o portu, který `WEBSITES_PORT` používá váš vlastní kontejner pomocí nastavení aplikace. Stránka GitHubu pro [ukázku Pythonu v tomto kurzu](https://github.com/Azure-Samples/docker-django-webapp-linux) ukazuje, že je potřeba nastavit `WEBSITES_PORT` na _8000_. Můžete ji nastavit [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) spuštěním příkazu v prostředí Cloud Shell. Například:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Vlastní kontejner může používat proměnné prostředí, které je třeba dodat externě. Můžete předat je spuštěním [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) příkazu v prostředí Cloud Shell. Například:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Tato metoda funguje jak pro aplikace s jedním kontejnerem, nebo pro aplikace s více kontejnery, kde jsou proměnné prostředí zadány v souboru *docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Použití trvalého sdíleného úložiště

Pomocí adresáře */home* v systému souborů aplikace můžete ukládat soubory mezi restartováními a sdílet je mezi instancemi. V `/home` aplikaci je k dispozici k tomu, aby vaše aplikace kontejneru přístup k trvaléúložiště.

Pokud je trvalé úložiště zakázáno, zápisy do `/home` adresáře se neudály v rámci restartování aplikace nebo ve více instancích. Jedinou výjimkou `/home/LogFiles` je adresář, který se používá k ukládání dockeru a kontejneru protokoly. Pokud je povoleno trvalé úložiště, `/home` všechny zápisy do adresáře jsou trvalé a lze přistupovat všechny instance škálované aplikace.

Ve výchozím nastavení je trvalé úložiště *povoleno* a nastavení není vystaveno v nastavení aplikace. Chcete-li ji `WEBSITES_ENABLE_APP_SERVICE_STORAGE` zakázat, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) nastavte nastavení aplikace spuštěním příkazu v prostředí Cloud Shell. Například:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Můžete také [nakonfigurovat vlastní trvalé úložiště](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Povolit SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. V pořadí pro vlastní kontejner pro podporu SSH, je nutné jej přidat do samotného souboru Dockerfile.

> [!TIP]
> Všechny vestavěné linuxové kontejnery přidaly instrukce SSH do svých úložišť obrázků. Pomocí [úložiště Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) můžete projít následující pokyny a zjistit, jak je v něm povoleno.

- Pomocí instrukce [RUN](https://docs.docker.com/engine/reference/builder/#run) nainstalujte server SSH a nastavte heslo `"Docker!"`pro kořenový účet na . Například pro obrázek založený na [Alpine Linuxu](https://hub.docker.com/_/alpine)potřebujete následující příkazy:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Tato konfigurace neumožňuje externí připojení ke kontejneru. SSH je k `https://<app-name>.scm.azurewebsites.net` dispozici pouze prostřednictvím a ověřena s přihlašovacími údaji pro publikování.

- Přidejte [tento soubor sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) do úložiště obrázků a pomocí instrukce [COPY](https://docs.docker.com/engine/reference/builder/#copy) zkopírujte soubor do adresáře */etc/ssh/.* Další informace o sshd_config souborů najdete *v* [dokumentaci k OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Soubor *sshd_config* musí obsahovat následující položky:
    > - `Ciphers` musí obsahovat alespoň jednu položku v tomto seznamu: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` musí obsahovat alespoň jednu položku v tomto seznamu: `hmac-sha1,hmac-sha1-96`.

- Pomocí příkazu [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) otevřete port 2222 v kontejneru. Přestože je kořenové heslo známo, port 2222 je z internetu nepřístupný. Je přístupný pouze kontejnery v rámci sítě mostu privátní virtuální sítě.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Ve skriptu pro spuštění kontejneru spusťte server SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Příklad naleznete v tématu, jak výchozí [kontejner Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) spustí server SSH.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurace aplikací s více kontejnery

- [Použití trvalého úložiště v Docker Compose](#use-persistent-storage-in-docker-compose)
- [Omezení náhledu](#preview-limitations)
- [Možnosti skládání dockeru](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Použití trvalého úložiště v Docker Compose

Aplikace s více kontejnery, jako je WordPress, potřebují trvalé úložiště, aby správně fungovaly. Chcete-li ji povolit, musí konfigurace Docker Compose ukazovat na umístění úložiště *mimo* váš kontejner. Umístění úložiště uvnitř kontejneru nezachovávají změny po restartování aplikace.

Povolte trvalé úložiště `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavením nastavení aplikace pomocí příkazu nastavení konfigurace aplikace [az webapp](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v prostředí Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

V souboru *docker-compose.yml* `volumes` namapujte možnost na `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` je proměnná prostředí ve službě App Service, která je namapovaná na trvalé úložiště vaší aplikace. Například:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Omezení náhledu

Multi-kontejner je v současné době ve verzi preview. Následující funkce platformy Služby App Service nejsou podporovány:

- Ověřování / autorizace
- Spravované identity

### <a name="docker-compose-options"></a>Možnosti skládání dockeru

V následujících seznamech jsou uvedeny podporované a nepodporované možnosti konfigurace Docker Compose:

#### <a name="supported-options"></a>Podporované možnosti

- command
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
- porty jiné než 80 a 8080 (ignorováno)

> [!NOTE]
> Všechny ostatní možnosti, které nejsou explicitně vyzvávány, jsou v náhledu Public Preview ignorovány.

## <a name="configure-vnet-integration"></a>Konfigurace integrace virtuální sítě

Použití vlastního kontejneru s integrací virtuální sítě může vyžadovat další konfiguraci kontejneru. Viz [Integrace aplikace s virtuální sítí Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nasazení z úložiště soukromých kontejnerů](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Kurz: Aplikace WordPress s více kontejnery](tutorial-multi-container-app.md)
