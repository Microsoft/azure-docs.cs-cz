---
title: Sestavit vlastní image a pro službu Web App for Containers – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak použít vlastní image Dockeru pro službu Web App for Containers.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b48ec72a1f0a4178dad66ed31c544399e90c5293
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484502"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Kurz: Sestavit vlastní image a spustit ve službě App Service z privátního registru

[App Service](app-service-linux-intro.md) poskytuje integrované Image Dockeru v Linuxu s podporou konkrétních verzí, jako například PHP 7.3 a Node.js 10.14. App Service využívá technologii kontejnerů Dockeru k hostování integrovaných imagí i vlastních imagí jako platforma jako služba. V tomto kurzu se dozvíte, jak sestavit vlastní image a spusťte ho ve službě App Service. Tento přístup je užitečný v případě, že integrované image nezahrnují váš vybraný jazyk, nebo když vaše aplikace vyžaduje specifickou konfiguraci, kterou neposkytují integrované image.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasadit vlastní image k privátním registru kontejneru
> * Spuštění vlastní image ve službě App Service
> * Konfigurace proměnných prostředí
> * Aktualizace a opětovné nasazení image
> * Přístup k diagnostickým protokolům
> * Připojit se ke kontejneru pomocí SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače a pak přejděte do adresáře, který obsahuje vzorový kód.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Sestavení image ze souboru Dockeru

V úložišti Git si prohlédněte soubor _Dockerfile_. Tento soubor popisuje prostředí Pythonu potřebné pro spuštění vaší aplikace. Kromě toho image nastaví server [SSH](https://www.ssh.com/ssh/protocol/) pro zabezpečení komunikace mezi kontejnerem a hostitelem.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Sestavíte image Dockeru s `docker build` příkazu.

```bash
docker build --tag mydockerimage .
```

Spuštěním kontejneru Dockeru otestujte, že sestavení funguje. Zadejte příkaz [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) a předejte mu název a značku image. Nezapomeňte zadat port pomocí argumentu `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Přejděte na adresu `http://localhost:8000` a ověřte, že webová aplikace i kontejner fungují správně.

![Místní test webové aplikace](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

Pokud chcete vytvořit aplikaci, která využívá image, kterou jste právě vytvořili, spustit příkazy rozhraní příkazového řádku Azure, vytvořte skupinu prostředků, nabízených oznámení na obrázku a potom vytvoří webovou aplikaci plán služby App Service k jeho spuštění.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Ve službě Cloud Shell pomocí příkazu [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) vytvořte službu Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Přihlaste se do služby Azure Container Registry

Pokud chcete nasdílet image do registru, musíte ověření pomocí privátního registru. Ve službě Cloud Shell, použijte [ `az acr show` ](/cli/azure/acr?view=azure-cli-latest#az-acr-show) příkaz Načíst přihlašovací údaje z registru, které jste vytvořili.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Výstup odhalí dvě hesla spolu s uživatelským jménem.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Z místního okna terminálu, přihlaste se k Azure Container Registry pomocí `docker login` příkaz, jak je znázorněno v následujícím příkladu. Nahraďte  *\<azure-container-registry-name >* a  *\<registry-username >* s hodnotami pro váš registr. Po zobrazení výzvy zadejte jedno z hesel z předchozího kroku.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Potvrďte, že přihlášení úspěšné.

### <a name="push-image-to-azure-container-registry"></a>Nahrání image do služby Azure Container Registry

Označte místní image pro službu Azure Container Registry. Příklad:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Odešlete image pomocí příkazu `docker push`. Přidejte k imagi značku s názvem registru následovaným názvem a značkou vaší image.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Zpět v Cloud Shellu ověřte, že nasdílení změn byla úspěšně dokončena.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Měli byste obdržet následující výstup.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Vytvoření webové aplikace

Ve službě Cloud Shell pomocí příkazu [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte v plánu služby App Service `myAppServicePlan` [webovou aplikaci](app-service-linux-intro.md). Nahraďte  _\<název aplikace >_ s jedinečným názvem aplikace, a  _\<azure-container-registry-name >_ názvem vašeho registru.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Konfigurace přihlašovacích údajů registru ve webové aplikaci

App Service a stáhněte si privátní image potřebuje informace o registru a image. Ve službě Cloud Shell, poskytnout jim [ `az webapp config container set` ](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) příkazu. Nahraďte  *\<název aplikace >* ,  *\<azure-container-registry-name >* ,  _\<registry-username >_ a  _\<heslo >_ .

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Při použití registru Docker Hub, než je `--docker-registry-server-url` musí být ve formátu `https://` a plně kvalifikovaný název registru.
>

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Většina imagí Dockeru použijte vlastní proměnné prostředí, jako je například jiný port než 80. Informace o portu, který vaše image používá, pomocí říkáte službě App Service `WEBSITES_PORT` nastavení aplikace. Stránka GitHubu pro [ukázku Pythonu v tomto kurzu](https://github.com/Azure-Samples/docker-django-webapp-linux) ukazuje, že je potřeba nastavit `WEBSITES_PORT` na _8000_.

Nastavit nastavení aplikace můžete pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Test webové aplikace

Ověřte fungování webové aplikace tím, že do ní přejdete (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Při prvním přístupu k aplikaci, může trvat nějakou dobu protože služby App Service je potřeba o přijetí změn celého obrázku. Pokud v prohlížeči vyprší časový limit, jednoduše obnovte stránku.

![Test konfigurace portů webové aplikace](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Změna a opětovné nasazení webové aplikace

V místním úložišti Git otevřete soubor app/templates/app/index.html. Vyhledejte první prvek HTML a změňte ho na:

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Jakmile upravíte a uložíte soubor Pythonu, musíte znovu sestavit a odeslat novou image Dockeru. Pak restartujte webovou aplikaci, aby se změny projevily. Použijte stejné příkazy jako v dřívějších částech tohoto kurzu. Můžete se podívat do [sestavení image ze souboru Dockeru](#build-the-image-from-the-docker-file) a [nabízených image do služby Azure Container Registry](#push-image-to-azure-container-registry). Otestujte webovou aplikaci podle pokynů v části [Otestování webové aplikace](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Povolit připojení SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Umožňující připojení SSH ke kontejneru musí být pro něj nakonfigurovali vlastní image. Pojďme se podívat na ukázkové úložiště, který už má nezbytné konfigurace.

* V [soubor Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile), následující kód, nainstaluje SSH server a také nastaví přihlašovací údaje.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Tato konfigurace nepovoluje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím webu Kudu/SCM. Web Kudu/SCM se ověřuje pomocí svého účtu Azure.

* [Soubor Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopie [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) souboru v úložišti, abyste */etc/ssh/* adresáře.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Soubor Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) zveřejní port 2222 v kontejneru. Je to interní port přístupný pouze pro kontejnery v rámci síťového mostu privátní virtuální sítě. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Skript vstupního](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) spuštění serveru SSH.

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

```bash
top
```

Příkaz `top` zveřejní všechny spuštěné procesy v kontejneru.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Blahopřejeme! Nakonfigurujete vlastní kontejner Linuxu ve službě App Service.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další postup

Naučili jste se:

> [!div class="checklist"]
> * Nasadit vlastní image k privátním registru kontejneru
> * Spuštění vlastní image ve službě App Service
> * Konfigurace proměnných prostředí
> * Aktualizace a opětovné nasazení image
> * Přístup k diagnostickým protokolům
> * Připojit se ke kontejneru pomocí SSH

Přejděte k dalšímu kurzu, kde se naučíte, jak namapovat vlastní název DNS do vaší aplikace.

> [!div class="nextstepaction"]
> [Kurz: Mapování vlastního názvu DNS do vaší aplikace](../app-service-web-tutorial-custom-domain.md)

Nebo, podívejte se na další prostředky:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Kurz: Wordpressu vícekontejnerové aplikace](tutorial-multi-container-app.md)
