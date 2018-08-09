---
title: Použití vlastní image Dockeru pro službu Web App for Containers – Azure | Microsoft Docs
description: Zjistěte, jak použít vlastní image Dockeru pro službu Web App for Containers.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: SyntaxC4
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 161207b96deb2f7bd605d845a9207393f9f59c23
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444738"
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Použití vlastní image Dockeru pro službu Web App for Containers

[Web App for Containers](app-service-linux-intro.md) poskytuje integrované image Dockeru v Linuxu s podporou konkrétních verzí, jako například PHP 7.0 a Node.js 4.5. Web App for Containers využívá technologii kontejnerů Dockeru k hostování integrovaných imagí i vlastních imagí v podobě platformy jako služby. V tomto kurzu se dozvíte, jak sestavit vlastní image Dockeru a nasadit ji do služby Web App for Containers. Tento přístup je užitečný v případě, že integrované image nezahrnují váš vybraný jazyk, nebo když vaše aplikace vyžaduje specifickou konfiguraci, kterou neposkytují integrované image.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasadit vlastní image Dockeru do Azure
> * Nakonfigurovat proměnné prostředí pro spuštění kontejneru
> * Aktualizovat image Dockeru a znovu ji nasadit
> * Připojit se ke kontejneru pomocí SSH
> * Nasadit privátní image Dockeru do Azure

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Účet Docker Hubu](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače a pak přejděte do adresáře, který obsahuje vzorový kód.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Sestavení image ze souboru Dockeru

V úložišti Git si prohlédněte soubor _Dockerfile_. Tento soubor popisuje prostředí Pythonu potřebné pro spuštění vaší aplikace. Kromě toho image nastaví server [SSH](https://www.ssh.com/ssh/protocol/) pro zabezpečení komunikace mezi kontejnerem a hostitelem.

```docker
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

Pokud chcete sestavit image Dockeru, spusťte příkaz `docker build` a zadejte název _mydockerimage_ a značku _v1.0.0_. Nahraďte _\<docker-id>_ za ID vašeho účtu Docker Hubu.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Příkaz vygeneruje výstup podobný následujícímu:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Spuštěním kontejneru Dockeru otestujte, že sestavení funguje. Zadejte příkaz [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) a předejte mu název a značku image. Nezapomeňte zadat port pomocí argumentu `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Přejděte na adresu `http://localhost:2222` a ověřte, že webová aplikace i kontejner fungují správně.

![Místní test webové aplikace](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> Ke kontejneru aplikace se můžete připojit také přímo z místního vývojového počítače pomocí SSH, SFTP nebo Visual Studio Code (pro živé ladění aplikací Node.js). Další informace najdete v tématu [Vzdálené ladění a SSH ve službě App Service v Linuxu](https://aka.ms/linux-debug).
>

## <a name="push-the-docker-image-to-docker-hub"></a>Odeslání image Dockeru do Docker Hubu

Registr je aplikace, která hostuje image a poskytuje image služeb a služby kontejneru. Pokud chcete sdílet svou image, musíte ji odeslat do registru. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Odesíláte do privátního registru Dockeru? Přečtěte si volitelné pokyny k [Použití image Dockeru z jakéhokoli privátního registru](#use-a-docker-image-from-any-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub je registr pro image Dockeru, který umožňuje hostovat vlastní veřejná nebo privátní úložiště. Pokud chcete odeslat vlastní image Dockeru do veřejného Docker Hubu, použijte příkaz [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) a zadejte úplný název a značku image. Úplný název a značka image vypadají jako v následující ukázce:

```
<docker-id>/image-name:tag
```

Než budete moci odeslat image, musíte se přihlásit k Docker Hubu pomocí příkazu [`docker login`](https://docs.docker.com/engine/reference/commandline/login/). Nahraďte _\<docker-id>_ názvem vašeho účtu a po zobrazení výzvy zadejte do konzoly své heslo.

```bash
docker login --username <docker-id>
```

Zpráva „login succeeded“ (Přihlášení proběhlo úspěšně) potvrzuje, že jste přihlášeni. Po přihlášení můžete odeslat image do Docker Hubu pomocí příkazu [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Prozkoumejte výstup příkazu a ověřte si, jestli odeslání proběhlo úspěšně.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

Pomocí Azure Web Apps můžete v cloudu hostovat nativní aplikace pro Linux. Pokud chcete vytvořit službu Web App for Containers, musíte spustit příkazy Azure CLI, které vytvoří skupinu, pak plán služby a nakonec samotnou webovou aplikaci. 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Vytvoření plánu služby App Service pro Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Ve službě Cloud Shell pomocí příkazu [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte v plánu služby App Service `myAppServicePlan` [webovou aplikaci](app-service-linux-intro.md). Nezapomeňte nahradit _<appname>_ jedinečným názvem aplikace a _\<docker-ID>_ vaším ID Dockeru.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
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
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Konfigurace proměnných prostředí

Většina imagí Dockeru obsahuje proměnné prostředí, které je potřeba nakonfigurovat. Pokud používáte existující image Dockeru sestavenou někým jiným, tato image může používat jiný port než 80. Azure můžete sdělit port, který vaše image používá, pomocí nastavení aplikace `WEBSITES_PORT`. Stránka GitHubu pro [ukázku Pythonu v tomto kurzu](https://github.com/Azure-Samples/docker-django-webapp-linux) ukazuje, že je potřeba nastavit `WEBSITES_PORT` na _8000_.

Nastavit nastavení aplikace můžete pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. Nastavení aplikace rozlišují velká a malá písmena a jsou oddělená mezerami.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Nasazujete z privátního registru Dockeru? Přečtěte si volitelné pokyny k [Použití image Dockeru z jakéhokoli privátního registru](#use-a-docker-image-from-any-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Test webové aplikace

Ověřte fungování webové aplikace tím, že do ní přejdete (`http://<app_name>azurewebsites.net`). 

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

Jakmile upravíte a uložíte soubor Pythonu, musíte znovu sestavit a odeslat novou image Dockeru. Pak restartujte webovou aplikaci, aby se změny projevily. Použijte stejné příkazy jako v dřívějších částech tohoto kurzu. Jako reference můžete použít části [Sestavení image ze souboru Dockeru](#build-the-image-from-the-docker-file) a [Odeslání image Dockeru do Docker Hubu](#push-the-docker-image-to-docker-hub). Otestujte webovou aplikaci podle pokynů v části [Otestování webové aplikace](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Připojení ke službě Web App for Containers pomocí SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Aby vlastní image Dockeru podporovala protokol SSH, musíte ho integrovat do souboru Dockerfile. SSH povolíte v samotném souboru Dockeru. Ukázkový soubor Dockerfile již obsahuje pokyny pro SSH, takže podle nich můžete postupovat i u vlastní image:

* Pokyn [RUN](https://docs.docker.com/engine/reference/builder/#run), který zavolá `apt-get` a pak nastaví heslo pro kořenový účet na `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Tato konfigurace nepovoluje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím webu Kudu/SCM. Web Kudu/SCM se ověřuje pomocí přihlašovacích údajů pro publikování.

* Pokyn [COPY](https://docs.docker.com/engine/reference/builder/#copy), který dává modulu Dockeru pokyn ke zkopírování souboru [sshd_config](http://man.openbsd.org/sshd_config) do adresáře */etc/ssh/*. Váš konfigurační soubor by měl být založený na [tomto souboru sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Soubor *sshd_config* musí obsahovat následující položky: 
    > * `Ciphers` musí obsahovat alespoň jednu položku v tomto seznamu: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` musí obsahovat alespoň jednu položku v tomto seznamu: `hmac-sha1,hmac-sha1-96`.

* Pokyn [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose), který v kontejneru zveřejní port 2222. I když je známé kořenové heslo, port 2222 není přístupný z internetu. Je to interní port přístupný pouze pro kontejnery v rámci síťového mostu privátní virtuální sítě. Potom příkazy zkopírují podrobnosti o konfiguraci SSH a spustí službu `ssh`.

    ```docker
    EXPOSE 8000 2222
    ```

* Nezapomeňte [spustit službu ssh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) pomocí skriptu prostředí v adresáři /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Otevření připojení SSH ke kontejneru

Web App for Containers nepovoluje externí připojení ke kontejneru. SSH je k dispozici pouze prostřednictvím webu Kudu, který je dostupný na adrese `https://<app_name>.scm.azurewebsites.net`.

Pokud se chcete připojit, přejděte na adresu `https://<app_name>.scm.azurewebsites.net/webssh/host` a přihlaste se pomocí svého účtu Azure.

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

Blahopřejeme! Nakonfigurovali jste vlastní image Dockeru pro službu Web App for Containers.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Použití privátní image z Docker Hubu (volitelné)

V části [Vytvoření webové aplikace](#create-a-web-app) jste zadali image v Docker Hubu v příkazu `az webapp create`. To je dostatečné pro veřejnou image. Pokud chcete použít privátní image, je potřeba nakonfigurovat ID a heslo vašeho účtu Dockeru ve webové aplikaci Azure.

Ve službě Cloud Shell za příkaz `az webapp create` zadejte [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Nahraďte *\<app_name>*, názvem vaší aplikace a _\<docker-id>_ a _\<password>_ za ID a heslo vašeho účtu Dockeru.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

Příkaz zobrazí výstup podobný následujícímu řetězci JSON, který značí úspěšnou změnu konfigurace:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Použití image Dockeru z privátního registru (volitelné)

V této části se na příkladu služby Azure Container Registry dozvíte, jak ve službě Web App for Containers použít image Dockeru z privátního registru. Postup při použití jiných privátních registrů je podobný. 

Azure Container Registry je spravovaná služba Dockeru od Azure pro hostování privátních imagí. Nasazení můžou být jakéhokoli typu, včetně [Dockeru Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) a Web App for Containers. 

### <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Ve službě Cloud Shell pomocí příkazu [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) vytvořte službu Azure Container Registry. Předejte název, skupinu prostředků a skladovou položku `Basic`. Dostupné skladové položky jsou `Classic`, `Basic`, `Standard` a `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Vytváření kontejneru vygeneruje následující výstup:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Přihlášení ke službě Azure Container Registry

Abyste mohli do registru odeslat image, musíte zadat přihlašovací údaje, aby registr odeslání přijal. Tyto přihlašovací údaje můžete načíst pomocí příkazu [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) ve službě Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Příkaz odhalí dvě hesla, která se dají použít s příslušným uživatelským jménem.

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

Z místního okna terminálu se pomocí příkazu `docker login` přihlaste ke službě Azure Container Registry. K přihlášení se vyžaduje název serveru. Použijte formát `{azure-container-registry-name>.azurecr.io`. Po zobrazení výzvy do konzoly zadejte své heslo.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Potvrďte, že přihlášení proběhlo úspěšně. 

### <a name="push-an-image-to-azure-container-registry"></a>Odeslání image do služby Azure Container Registry

> [!NOTE]
> Pokud používáte vlastní image, označte ji následujícím způsobem:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Odešlete image pomocí příkazu `docker push`. Přidejte k imagi značku s názvem registru následovaným názvem a značkou vaší image.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Výpisem seznamu úložišť ACR ověřte, že se odesláním úspěšně přidal kontejner do registru. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Výpis imagí v registru potvrdí, že je v registru image `mydockerimage`.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Konfigurace webové aplikace pro použití image ze služby Azure Container Registry (nebo jakéhokoli privátního registru)

Službu Web App for Containers můžete nakonfigurovat tak, aby spouštěla kontejner uložený ve službě Azure Container Registry. Použití služby Azure Container Registry je stejné jako použití jakéhokoli privátního registru, takže pokud potřebujete použít vlastní privátní registr, postup pro dokončení tohoto úkolu bude podobný.

Ve službě Cloud Shell spuštěním příkazu [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show) zobrazte uživatelské jméno a heslo pro službu Azure Container Registry. Zkopírujte uživatelské jméno a jedno z hesel, abyste je mohli použít ke konfiguraci webové aplikace v dalším kroku.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

Ve službě Cloud Shell spuštěním příkazu [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) přiřaďte vlastní image Dockeru k webové aplikaci. Nahraďte položky *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ a _\<password>_. Pro službu Azure Container Registry má *\<docker-registry-server-url>* (adresa URL serveru registru Dockeru) formát `https://<azure-container-registry-name>.azurecr.io`. Pokud používáte jakýkoli registr mimo Docker Hub, název image musí začínat plně kvalifikovaným názvem domény vašeho registru. Pro službu Azure Container Registry to bude vypadat přibližně takto: `<azure-container-registry>.azurecr.io/mydockerimage`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> V hodnotě *\<docker-registry-server-url>* (adresa URL serveru registru Dockeru) se vyžaduje `https://`.
>

Příkaz zobrazí výstup podobný následujícímu řetězci JSON, který značí úspěšnou změnu konfigurace:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace Docker Python využívající databázi PostgreSQL v Azure](tutorial-docker-python-postgresql-app.md)
