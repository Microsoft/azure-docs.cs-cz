---
title: 'Kurz: sestavení a spuštění vlastní image'
description: Naučte se vytvářet vlastní image Linux, která může běžet na Azure App Service, nasazovat ji do služby Azure Container registry a spouštět ji v App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 965897afc8e23c123575de0c497d4071ff4ca85a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767105"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Kurz: Vytvoření vlastní image a spuštění v App Service z privátního registru

[App Service](app-service-linux-intro.md) poskytuje integrované image Docker v systému Linux s podporou pro konkrétní verze, například php 7,3 a Node. js 10,14. App Service používá technologii kontejneru Docker k hostování vestavěných imagí a vlastních imagí jako platformy jako služby. V tomto kurzu se naučíte vytvořit vlastní image a spustit ji v App Service. Tento přístup je užitečný v případě, že integrované image nezahrnují váš vybraný jazyk, nebo když vaše aplikace vyžaduje specifickou konfiguraci, kterou neposkytují integrované image.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasazení vlastní image do privátního registru kontejnerů
> * Spuštění vlastní image v App Service
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

Sestavte image Docker pomocí příkazu `docker build`.

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

Pokud chcete vytvořit aplikaci, která používá Image, kterou jste právě vytvořili, spustíte příkazy rozhraní příkazového řádku Azure, které vytvoří skupinu prostředků, nahraje image a pak vytvoří službu App Service Plan Web App, která ji spustí.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Ve službě Cloud Shell pomocí příkazu [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) vytvořte službu Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Přihlášení k Azure Container Registry

Chcete-li odeslat image do registru, je nutné provést ověření pomocí privátního registru. V Cloud Shell pomocí příkazu [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) načtěte přihlašovací údaje z registru, který jste vytvořili.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Výstup odhalí dvě hesla spolu s uživatelským jménem.

```json
{
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

V místním okně terminálu se přihlaste k Azure Container Registry pomocí příkazu `docker login`, jak je znázorněno v následujícím příkladu. Nahraďte *\<Azure-Container-Registry-name >* a *\<registru-username >* hodnotami pro váš registr. Po zobrazení výzvy zadejte jedno z hesel z předchozího kroku.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Potvrďte, že přihlášení bylo úspěšné.

### <a name="push-image-to-azure-container-registry"></a>Nahrání image do služby Azure Container Registry

Označte místní obrázek pro Azure Container Registry. Příklad:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Odešlete image pomocí příkazu `docker push`. Přidejte k imagi značku s názvem registru následovaným názvem a značkou vaší image.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Zpět v Cloud Shell ověřte, zda byla nabízená oznámení úspěšná.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Měli byste získat následující výstup.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Vytvoření webové aplikace

Ve službě Cloud Shell pomocí příkazu [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte v `myAppServicePlan`plánu služby App Service [webovou aplikaci](app-service-linux-intro.md). Nahraďte _\<název aplikace >_ jedinečným názvem aplikace a _\<Azure-Container-registry-Name >_ s vaším názvem registru.

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

Pokud App Service chcete načíst privátní image, potřebuje informace o vašem registru a imagi. V Cloud Shell je zadejte pomocí příkazu [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) . Nahraďte *\<App-name >* , *\<Azure-Container-registry-name >* , _\<registry-username >_ a _\<Password >_ .

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Pokud používáte jiný registr než Docker Hub, `--docker-registry-server-url` musí být naformátovaná jako `https://` následovaná plně kvalifikovaným názvem domény registru.
>

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Většina imagí Docker používá vlastní proměnné prostředí, jako je například port jiný než 80. App Service o portu, který používá vaše image, můžete zjistit pomocí nastavení aplikace `WEBSITES_PORT`. Stránka GitHubu pro [ukázku Pythonu v tomto kurzu](https://github.com/Azure-Samples/docker-django-webapp-linux) ukazuje, že je potřeba nastavit `WEBSITES_PORT` na _8000_.

Nastavit nastavení aplikace můžete pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Test webové aplikace

Ověřte fungování webové aplikace tím, že do ní přejdete (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Při prvním přístupu k aplikaci může trvat nějakou dobu, protože App Service musí načíst celou image. Pokud vyprší časový limit prohlížeče, stačí stránku aktualizovat.

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

Jakmile upravíte a uložíte soubor Pythonu, musíte znovu sestavit a odeslat novou image Dockeru. Pak restartujte webovou aplikaci, aby se změny projevily. Použijte stejné příkazy jako v dřívějších částech tohoto kurzu. Můžete se podívat, jak [vytvořit image ze souboru Docker](#build-the-image-from-the-docker-file) a [vložit obrázek do Azure Container Registry](#push-image-to-azure-container-registry). Otestujte webovou aplikaci podle pokynů v části [Otestování webové aplikace](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Povolit připojení SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Pokud chcete povolit připojení SSH ke kontejneru, musíte pro něj nakonfigurovat vlastní image. Pojďme se podívat na ukázkové úložiště, které už má nějakou potřebnou konfiguraci.

* V [souboru Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)následující kód nainstaluje server SSH a také nastaví přihlašovací údaje pro přihlášení.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Tato konfigurace nepovoluje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím webu Kudu/SCM. Web Kudu/SCM se ověřuje pomocí vašeho účtu Azure.

* [Souboru Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) zkopíruje soubor [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) do úložiště do adresáře */etc/ssh/* .

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Souboru Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) vystaví port 2222 v kontejneru. Je to interní port přístupný pouze pro kontejnery v rámci síťového mostu privátní virtuální sítě. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Skript pro zadávání](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) SPUSTÍ Server SSH.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Otevření připojení SSH ke kontejneru

Připojení SSH je k dispozici pouze prostřednictvím webu Kudu, který je přístupný na `https://<app-name>.scm.azurewebsites.net`.

Pokud se chcete připojit, přejděte na adresu `https://<app-name>.scm.azurewebsites.net/webssh/host` a přihlaste se pomocí svého účtu Azure.

Pak budete přesměrováni na stránku s interaktivní konzolou.

Možná budete chtít ověřit, že se v kontejneru spouštějí určité aplikace. Pokud chcete zkontrolovat kontejner a ověřit spuštěné procesy, zadejte na příkazovém řádku příkaz `top`.

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

Blahopřejeme! Nakonfigurovali jste vlastní kontejner pro Linux v App Service.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Nasazení vlastní image do privátního registru kontejnerů
> * Spuštění vlastní image v App Service
> * Konfigurace proměnných prostředí
> * Aktualizace a opětovné nasazení image
> * Přístup k diagnostickým protokolům
> * Připojit se ke kontejneru pomocí SSH

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](../app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Kurz: aplikace pro více kontejnerů WordPress](tutorial-multi-container-app.md)
