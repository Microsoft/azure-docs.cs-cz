---
title: 'Kurz: Sestavení a spuštění vlastní bitové kopie'
description: Zjistěte, jak vytvořit vlastní bitovou kopii Linuxu, která se dá spouštět ve službě Azure App Service, nasazovat ji do registrů kontejnerů Azure a spouštět ji ve službě App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: a6c9eb354bce09a5f652895f4af34df1f6750bec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80045743"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Kurz: Vytvoření vlastní bitové kopie a spuštění v aplikaci App Service ze soukromého registru

[Služba App Service](app-service-linux-intro.md) poskytuje integrované imitace Dockeru na Linuxu s podporou konkrétních verzí, jako je PHP 7.3 a Node.js 10.14. Služba App Service používá technologii kontejneru Dockeru k hostování integrovaných i vlastních ihných bitových kopií jako platformy jako služby. V tomto kurzu se dozvíte, jak vytvořit vlastní bitovou kopii a spustit ji ve službě App Service. Tento přístup je užitečný v případě, že integrované image nezahrnují váš vybraný jazyk, nebo když vaše aplikace vyžaduje specifickou konfiguraci, kterou neposkytují integrované image.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasazení vlastní bitové kopie do registru privátního kontejneru
> * Spuštění vlastní image ve službě App Service
> * Konfigurace proměnných prostředí
> * Aktualizace a opětovné nasazení bitové kopie
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

V úložišti Git si prohlédněte soubor _Dockerfile_. Tento soubor popisuje prostředí Pythonu potřebné pro spuštění vaší aplikace. Kromě toho image nastaví server [SSH](https://www.ssh.com/ssh/protocol/) pro zabezpečení komunikace mezi kontejnerem a hostitelem. Poslední řádek v _Souboru Dockerfile_, `ENTRYPOINT ["init.sh"]`vyvolá spuštění služby `init.sh` SSH a serveru Pythonu.

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

#service SSH start
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Vytvořte image Dockeru pomocí příkazu. `docker build`

```bash
docker build --tag mydockerimage .
```

Spuštěním kontejneru Dockeru otestujte, že sestavení funguje. Vyjevěte [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) příkaz a předajmu mu název a značku obrázku. Nezapomeňte zadat port pomocí argumentu `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Přejděte na adresu `http://localhost:8000` a ověřte, že webová aplikace i kontejner fungují správně.

![Místní test webové aplikace](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

Chcete-li vytvořit aplikaci, která používá image, kterou jste právě vytvořili, spusťte příkazy Azure CLI, které vytvářejí skupinu prostředků, tlačí image a pak vytvoří webovou aplikaci plán služby App Service pro jeho spuštění.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

V prostředí Cloud shellu použijte [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) příkaz k vytvoření registru kontejnerů Azure.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Přihlášení k registru kontejnerů Azure

Chcete-li vysunout bitovou kopii do registru, je třeba ověřit pomocí soukromého registru. V prostředí cloudu [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) použijte příkaz k načtení pověření z registru, který jste vytvořili.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Výstup odhaluje dvě hesla spolu s uživatelským jménem.

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

Z okna místního terminálu se přihlaste `docker login` k registru kontejnerů Azure pomocí příkazu, jak je znázorněno v následujícím příkladu. Nahraďte * \<>* a uživatelské * \<ho názvu registru azure-container-registry>* hodnotami pro váš registr. Po zobrazení výzvy zadejte jedno z hesel z předchozího kroku.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Potvrďte, že přihlášení proběhne úspěšně.

### <a name="push-image-to-azure-container-registry"></a>Nahrání image do služby Azure Container Registry

Označte místní bitovou kopii pro registr kontejnerů Azure. Například:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Odešlete image pomocí příkazu `docker push`. Přidejte k imagi značku s názvem registru následovaným názvem a značkou vaší image.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Zpět v prostředí cloudu ověřte, zda je nabízená zpráva úspěšná.

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

V prostředí Cloud Shell vytvořte `myAppServicePlan` [webovou aplikaci](app-service-linux-intro.md) v plánu služby App Service pomocí příkazu. [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) Nahraďte _ \<>názvu aplikace_ jedinečným názvem aplikace a _ \<>název registru azure-container-registry_ názvem.

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

Pro App Service vytáhnout soukromou bitovou kopii, potřebuje informace o registru a image. V prostředí Cloud Shell jim [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) poskytněte příkaz. Nahraďte * \<>názvů aplikací *, * \<>název azure-container-registry , * _ \<>uživatelského jména registru _a _ \<>hesla _.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Při použití jiného registru `--docker-registry-server-url` než Docker Hub, musí být formátován `https://` následovaný plně kvalifikovaný název domény registru.
>

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Většina imitací Dockeru používá vlastní proměnné prostředí, například port jiný než 80. Sdělte službě App Service o portu, který obrázek používá, pomocí nastavení `WEBSITES_PORT` aplikace. Stránka GitHubu pro [ukázku Pythonu v tomto kurzu](https://github.com/Azure-Samples/docker-django-webapp-linux) ukazuje, že je potřeba nastavit `WEBSITES_PORT` na _8000_.

Chcete-li nastavit nastavení [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) aplikace, použijte příkaz v prostředí Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Test webové aplikace

Ověřte fungování webové aplikace tím, že do ní přejdete (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Při prvním přístupu k aplikaci může nějakou dobu trvat, protože služba App Service potřebuje vytáhnout celý obrázek. Pokud prohlížeč vynese časový plán, stačí stránku aktualizovat.

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

Jakmile upravíte a uložíte soubor Pythonu, musíte znovu sestavit a odeslat novou image Dockeru. Pak restartujte webovou aplikaci, aby se změny projevily. Použijte stejné příkazy jako v dřívějších částech tohoto kurzu. Můžete odkazovat na [Sestavení bitové kopie ze souboru Dockeru](#build-the-image-from-the-docker-file) a [Push image do registru kontejnerů Azure](#push-image-to-azure-container-registry). Otestujte webovou aplikaci podle pokynů v části [Otestování webové aplikace](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Povolení připojení SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Chcete-li povolit připojení SSH k kontejneru, musí být pro něj nakonfigurována vlastní bitová kopie. Podívejme se na ukázkové úložiště, které již má potřebnou konfiguraci.

* V [souboru Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)nainstaluje následující kód server SSH a také nastaví přihlašovací pověření.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Tato konfigurace nepovoluje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím webu Kudu/SCM. Web Kudu/SCM je ověřen pomocí vašeho účtu Azure.

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) zkopíruje [soubor sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) v úložišti do adresáře */etc/ssh/.*

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) zpřístupňuje port 2222 v kontejneru. Je to interní port přístupný pouze pro kontejnery v rámci síťového mostu privátní virtuální sítě. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Vstupní skript](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) spustí server SSH.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Otevření připojení SSH ke kontejneru

Připojení SSH je k dispozici pouze prostřednictvím webu `https://<app-name>.scm.azurewebsites.net`Kudu, který je přístupný na adrese .

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

Blahopřejeme! Ve službě App Service jste nakonfigurovali vlastní linuxový kontejner.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Nasazení vlastní bitové kopie do registru privátního kontejneru
> * Spuštění vlastní image ve službě App Service
> * Konfigurace proměnných prostředí
> * Aktualizace a opětovné nasazení bitové kopie
> * Přístup k diagnostickým protokolům
> * Připojit se ke kontejneru pomocí SSH

Přejdete k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS do aplikace.

> [!div class="nextstepaction"]
> [Kurz: Mapování vlastního názvu DNS do aplikace](../app-service-web-tutorial-custom-domain.md)

Nebo se podívejte na další zdroje:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Kurz: Aplikace WordPress s více kontejnery](tutorial-multi-container-app.md)
