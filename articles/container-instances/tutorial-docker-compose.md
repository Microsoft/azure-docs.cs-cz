---
title: Kurz – použití Docker Compose k nasazení skupiny více kontejnerů
description: Pomocí Docker Compose Sestavte a spusťte aplikaci s více kontejnery a pak aplikaci zaveďte do Azure Container Instances
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: ''
ms.openlocfilehash: 1e8a5cd856358a0dc3e9c356cb3a55f75db29c86
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709655"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Kurz: nasazení skupiny s více kontejnery pomocí Docker Compose 

V tomto kurzu použijete [Docker Compose](https://docs.docker.com/compose/) k definování a spuštění aplikace s více kontejnery místně a pak ji nasadíte jako [skupinu kontejnerů](container-instances-container-groups.md) v Azure Container Instances. 

Při vývoji nativních aplikací cloudu s Docker můžete spouštět kontejnery v Azure Container Instances na vyžádání a chcete bez problémů přepnout z místního vývoje na nasazení v cloudu. Tato funkce je povolená [integrací mezi Docker a Azure](https://docs.docker.com/engine/context/aci-integration/). Pomocí nativních příkazů Docker můžete v Azure spustit [jednu instanci kontejneru](quickstart-docker-cli.md) nebo skupinu více kontejnerů.

> [!IMPORTANT]
> Ne všechny funkce Azure Container Instances jsou podporovány. Poskytněte zpětnou vazbu o integraci Docker – Azure vytvořením problému v úložišti GitHub [ACI Integration Docker](https://github.com/docker/aci-integration-beta) .

> [!TIP]
> [Rozšíření Docker pro Visual Studio Code](https://aka.ms/VSCodeDocker) můžete použít pro integrované prostředí pro vývoj, spouštění a správu kontejnerů, obrázků a kontextů.

V tomto článku:

> [!div class="checklist"]
> * Vytvoření registru kontejneru Azure
> * Klonování zdrojového kódu aplikace z GitHubu
> * Použití Docker Compose k sestavení image a spuštění aplikace s více kontejnery místně
> * Vložení image aplikace do registru kontejneru
> * Vytvoření kontextu Azure pro Docker
> * Uvedení aplikace v Azure Container Instances

## <a name="prerequisites"></a>Požadavky

* **Azure CLI** – v místním počítači musíte mít nainstalované rozhraní příkazového řádku Azure. Doporučuje se verze 2.10.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

* **Docker Desktop** – musíte použít Docker Desktop verze 2.3.0.5 nebo novější, který je k dispozici pro [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) nebo [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg). Nebo nainstalujte rozhraní příkazového [řádku Docker ACI Integration CLI pro Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Získání kódu aplikace

Ukázkovou aplikací používanou v tomto kurzu je základní hlasovací aplikace. Aplikace se skládá z front-end webové součásti a back-end instance Redis. Webová součást je zabalená do vlastní image kontejneru. Instance Redis využívá nezměněnou image z Docker Hubu.

Pomocí příkazu [git](https://git-scm.com/downloads) naklonujte ukázkovou aplikaci do svého vývojového prostředí:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Přejděte do klonovaného adresáře.

```console
cd azure-voting-app-redis
```

V adresáři je zdrojový kód aplikace a předem vytvořený soubor s příznakem Docker, Docker-skládání. yaml.

## <a name="modify-docker-compose-file"></a>Upravit soubor pro sestavení Docker

Otevřete Docker-YAML. v textovém editoru. Soubor konfiguruje `azure-vote-back` `azure-vote-front` služby a.

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

V `azure-vote-front` konfiguraci proveďte následující dvě změny:

1. Aktualizuje `image` vlastnost ve `azure-vote-front` službě. Jako předponu názvu obrázku použijte název přihlašovacího serveru služby Azure Container Registry, \<acrName\> . azurecr.IO. Pokud je například registr nazvaný *myregistry*, název přihlašovacího serveru je *myregistry.azurecr.IO* (všechny malé písmeno) a vlastnost Image je pak `myregistry.azurecr.io/azure-vote-front` .
1. Změňte `ports` mapování na `80:80` . Uložte soubor.

Aktualizovaný soubor by měl vypadat nějak takto:

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Když tyto náhrady provedete, `azure-vote-front` obrázek, který sestavíte v dalším kroku, se označí pro váš registr kontejneru Azure a image se dá spustit v Azure Container Instances.

> [!TIP]
> Pro tento scénář nemusíte používat službu Azure Container Registry. Můžete například vybrat privátní úložiště v Docker Hub a hostovat tak image vaší aplikace. Pokud zvolíte jiný registr, aktualizujte odpovídající vlastnosti bitové kopie.

## <a name="run-multi-container-application-locally"></a>Spustit aplikaci s více kontejnery místně

Spustit [Docker –](https://docs.docker.com/compose/reference/up/)Sestavte, které používá ukázkový `docker-compose.yaml` soubor k sestavení image kontejneru, Stáhněte si image Redis a spusťte aplikaci:

```console
docker-compose up --build -d
```

Po dokončení můžete vytvořené image zobrazit pomocí příkazu [docker images](https://docs.docker.com/engine/reference/commandline/images/). Stáhly se nebo se vytvořily tři image. `azure-vote-front`Obrázek obsahuje front-end aplikaci, která používá `uwsgi-nginx-flask` Image jako základní. Image `redis` slouží ke spuštění instance Redis.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
redis                                     latest     a1b99da73d05        7 days ago          104MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Spuštěním příkazu [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) zobrazte spuštěné kontejnery:

```
$ docker ps

CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front  "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b68fed4b66b6        redis                                   "docker-entrypoint.s…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Pokud chcete zobrazit spuštěnou aplikaci, zadejte v místním webovém prohlížeči `http://localhost:80`. Načte se ukázková aplikace, jak je znázorněno v následujícím příkladu:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Obrázek hlasovací aplikace":::

Po pokusu o použití místní aplikace spusťte [Docker – vytvořit](https://docs.docker.com/compose/reference/down/) , aby se aplikace zastavila a odstranila kontejnery.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Vložení image do registru kontejneru

Chcete-li nasadit aplikaci, aby Azure Container Instances, je nutné odeslat `azure-vote-front` image do registru kontejneru. Spustit [Docker – vytvořením nabízeného oznámení](https://docs.docker.com/compose/reference/push) pro nahrání Image:

```console
docker-compose push
```

Vložení do registru může trvat několik minut.

Chcete-li ověřit, zda je bitová kopie uložena v registru, spusťte příkaz [AZ ACR úložiště show](/cli/azure/acr/repository#az-acr-repository-show) :

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Nasazení aplikace do služby Azure Container Instances

Dále přejděte do kontextu ACI. Následující příkazy Docker se spouštějí v tomto kontextu.

```console
docker context use myacicontext
```

Spuštěním `docker compose up` aplikace spustíte aplikaci v Azure Container Instances. `azure-vote-front`Bitová kopie je načítána z registru kontejneru a skupina kontejnerů je vytvořena v Azure Container Instances.

```console
docker compose up
```

> [!NOTE]
> Příkazy Docker Compose aktuálně dostupné v kontextu ACI jsou `docker compose up` a `docker compose down` . `docker` `compose` V těchto příkazech není žádné spojovníky.

V krátké době je nasazena skupina kontejnerů. Ukázkový výstup:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Spusťte `docker ps` , chcete-li zobrazit spuštěné kontejnery a IP adresu přiřazenou ke skupině kontejnerů.

```console
docker ps
```

Ukázkový výstup:

```
CONTAINER ID                           IMAGE                                    COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    redis                                                        Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                       Running             52.179.23.131:80->80/tcp
```

Pokud chcete zobrazit spuštěnou aplikaci v cloudu, zadejte zobrazenou IP adresu v místním webovém prohlížeči. V tomto příkladu zadejte `52.179.23.131` . Načte se ukázková aplikace, jak je znázorněno v následujícím příkladu:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Obrázek hlasovací aplikace v ACI":::

Pokud chcete zobrazit protokoly front-endu kontejneru, spusťte příkaz [Docker logs](https://docs.docker.com/engine/reference/commandline/logs) . Například:

```console
docker logs azurevotingappredis_azure-vote-front
```

Můžete také použít Azure Portal nebo jiné nástroje Azure a zobrazit vlastnosti a stav skupiny kontejnerů, které jste nasadili.

Až skončíte s vyzkoušením aplikace, zastavte aplikaci a kontejnery pomocí `docker compose down` :

```console
docker compose down
```

Tento příkaz odstraní skupinu kontejnerů v Azure Container Instances.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili Docker Compose k přepnutí ze spouštění aplikace s více kontejnery místně na spouštění v Azure Container Instances. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření registru kontejneru Azure
> * Klonování zdrojového kódu aplikace z GitHubu
> * Použití Docker Compose k sestavení image a spuštění aplikace s více kontejnery místně
> * Vložení image aplikace do registru kontejneru
> * Vytvoření kontextu Azure pro Docker
> * Uvedení aplikace v Azure Container Instances

Můžete také použít [rozšíření Docker pro Visual Studio Code](https://aka.ms/VSCodeDocker) pro integrované prostředí pro vývoj, spouštění a správu kontejnerů, obrázků a kontextů.

Pokud chcete využít výhod více funkcí v Azure Container Instances, pomocí nástrojů Azure určete skupinu s více kontejnery. Podívejte se například na kurzy k nasazení skupiny kontejnerů pomocí Azure CLI se [souborem YAML](container-instances-multi-container-yaml.md)nebo nasazení pomocí [šablony Azure Resource Manager](container-instances-multi-container-group.md). 