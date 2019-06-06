---
title: Konfigurace vlastního kontejneru – Azure App Service | Dokumentace Microsoftu
description: Další informace o konfiguraci aplikací v Node.js v Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 9bfd6c34d81e193fe31610f840474f1e4c91170d
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430927"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurace vlastního kontejneru Linuxu pro službu Azure App Service

V tomto článku se dozvíte, jak nakonfigurovat vlastní kontejner Linux ke spuštění ve službě Azure App Service.

Tato příručka obsahuje klíčové koncepty a pokyny, jak kontejnerizace Linuxové aplikace ve službě App Service. Pokud jste nikdy použili službu Azure App Service, postupujte [vlastní kontejner rychlý Start](quickstart-docker-go.md) a [kurzu](tutorial-custom-docker-image.md) první. K dispozici je také [rychlý úvod k aplikacím vícekontejnerová](quickstart-multi-container.md) a [kurzu](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Konfigurovat číslo portu

Webový server do své vlastní image může používat jiný port než 80. Azure můžete sdělit port, který používá vlastní kontejner pomocí `WEBSITES_PORT` nastavení aplikace. Stránka GitHubu pro [ukázku Pythonu v tomto kurzu](https://github.com/Azure-Samples/docker-django-webapp-linux) ukazuje, že je potřeba nastavit `WEBSITES_PORT` na _8000_. Můžete ho nastavit spuštěním [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) příkazu ve službě Cloud Shell. Příklad:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Vlastní kontejner může použít proměnné prostředí, které musí být poskytnuto externě. Můžete předat je do spuštěním [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) příkazu ve službě Cloud Shell. Příklad:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Tato metoda se dá použít pro jeden kontejner aplikace nebo vícekontejnerové aplikace, kde jsou proměnné prostředí určené v i *docker-compose.yml* souboru.

## <a name="use-persistent-shared-storage"></a>Použití trvalé sdílené úložiště

Můžete použít */home* adresáře v systému souborů vaší aplikace pro uchovávání souborů napříč restartování a sdílet je napříč instancemi. `/home` Ve vaší aplikaci je k dispozici v kontejneru aplikace pro přístup k trvalému ukládání.

Když trvalého úložiště je zakázaný a pak zapíše do `/home` adresáře nejsou trvalé napříč restartování aplikace nebo víc instancí. Jedinou výjimkou je `/home/LogFiles` adresáře, který se používá k ukládání protokolů Dockeru a kontejnerech. Pokud je povolená trvalého úložiště, všechny operace zápisu do `/home` adresáře jsou trvalé a je možný přes všechny instance horizontálním navýšením kapacity aplikace.

Ve výchozím nastavení, je trvalé úložiště *zakázané*. Chcete-li povolit nebo zakázat, nastavte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` nastavení aplikace spuštěním [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) příkazu ve službě Cloud Shell. Příklad:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Můžete také [nakonfigurujte trvalého úložiště](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Povolit SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Aby vlastní kontejner podporovala protokol SSH je třeba přidat ji do souboru Dockerfile, samotného.

> [!TIP]
> Všechny předdefinované kontejnery Linuxu ve svých úložištích image přidali pokyny pro SSH. Můžete procházet, postupujte podle následujících pokynů s [Node.js 10.14 úložiště](https://github.com/Azure-App-Service/node/blob/master/10.14) zobrazíte, jak je povoleno existuje.

- Použití [spustit](https://docs.docker.com/engine/reference/builder/#run) pokyny, jak nainstalovat SSH server a nastavte heslo pro kořenový účet na `"Docker!"`. Například pro image podle [Alpine Linux](https://hub.docker.com/_/alpine), budete potřebovat následující příkazy:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Tato konfigurace nepovoluje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím `https://<app-name>.scm.azurewebsites.net` a ověření pomocí přihlašovacích údajů pro publikování.

- Přidat [tohoto souboru sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) do úložiště imagí a použití [kopírování](https://docs.docker.com/engine/reference/builder/#copy) pokyny, jak zkopírovat soubor, který má */etc/ssh/* adresáře. Další informace o *sshd_config* soubory, naleznete v tématu [OpenBSD dokumentaci](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Soubor *sshd_config* musí obsahovat následující položky:
    > - `Ciphers` musí obsahovat alespoň jednu položku v tomto seznamu: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` musí obsahovat alespoň jednu položku v tomto seznamu: `hmac-sha1,hmac-sha1-96`.

- Použití [vystavit](https://docs.docker.com/engine/reference/builder/#expose) pokyny, jak otevřít port 2222 v kontejneru. I když je známé kořenové heslo, port 2222 není přístupný z Internetu. Je dostupná jenom pomocí kontejnery v rámci síťového mostu privátní virtuální síť.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Ve skriptu spuštění kontejneru spusťte SSH server.

    ```bash
    /usr/sbin/sshd
    ```

    Příklad najdete v tématu jak výchozí [Node.js 10.14 kontejneru](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) spuštění serveru SSH.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurace aplikací pro více kontejnerů

- [Použití trvalé úložiště v Docker Compose](#use-persistent-storage-in-docker-compose)
- [Omezení verze Preview](#preview-limitations)
- [Možnosti docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Použití trvalé úložiště v Docker Compose

Vícekontejnerová aplikace, jako je WordPress potřebujete trvalé úložiště fungovat správně. Ho Pokud chcete povolit, konfiguraci Docker Compose musí odkazovat na umístění úložiště *mimo* vašeho kontejneru. Umístění úložiště uvnitř kontejneru nechcete zachovat změny nad rámec restartování aplikace.

Povolit trvalé úložiště tak, že nastavíte `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplikace nastavení, pomocí [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) příkazu ve službě Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Ve vaší *docker-compose.yml* souborů, mapování `volumes` umožňuje `${WEBAPP_STORAGE_HOME}`. 

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

Více kontejnerů je aktuálně ve verzi preview. Nejsou podporovány následující funkce služby App Service:

- Ověřování / autorizace
- Spravované identity

### <a name="docker-compose-options"></a>Možnosti docker Compose

Následující seznamy shrnují podporované a nepodporované možnosti Docker Compose konfigurace:

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
- jiné porty než 80 a 8080 (ignorováno)

> [!NOTE]
> Jakékoli možnosti, které nejsou výslovně uvádějí, jsou ignorovány ve verzi Public Preview.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Nasazení z kontejneru soukromého úložiště](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Kurz: Wordpressu vícekontejnerové aplikace](tutorial-multi-container-app.md)
