---
title: Kurz Kubernetes v Azure – Příprava aplikace
description: V tomto kurzu Azure Kubernetes Service (AKS) se dozvíte, jak pomocí Docker Compose připravit a sestavit vícekontejnerovou aplikaci, kterou pak můžete nasadit do AKS.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: 349bf90ea0b344d5232c885358814f39fba4c19f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251946"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Kurz: Příprava aplikace pro službu Azure Kubernetes Service (AKS)

V tomto kurzu, který je první částí sedmidílné série, se připraví vícekontejnerová aplikace pro použití v Kubernetes. Pomocí existujících vývojových nástrojů, jako je Docker Compose, se aplikace místně sestaví a otestuje. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Klonování zdroje ukázkové aplikace z GitHubu
> * Vytvoření image kontejneru ze zdroje ukázkové aplikace
> * Test vícekontejnerové aplikace v místním prostředí Dockeru

Po dokončení bude ve vašem místním vývojovém prostředí spuštěná následující aplikace:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Snímek obrazovky s obrázkem kontejneru hlasovací aplikace Azure, která je místně otevřená v místním webovém prohlížeči" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

V novějších kurzech se image kontejneru nahraje do Azure Container Registry a pak se nasadí do clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

V tomto kurzu se předpokládá základní znalost klíčových konceptů Dockeru, jako jsou kontejnery, image kontejnerů a příkazy `docker`. Základní informace o kontejnerech najdete v článku [Get started with Docker][docker-get-started] (Začínáme s Dockerem).

K dokončení tohoto kurzu potřebujete místní vývojové prostředí pro Docker se spuštěnými kontejnery Linuxu. Docker nabízí balíčky pro konfiguraci Dockeru v systému [Mac][docker-for-mac], [Windows][docker-for-windows] nebo [Linux][docker-for-linux].

> [!NOTE]
> Azure Cloud Shell neobsahuje součásti Dockeru nutné pro dokončení všech kroků v těchto kurzech. Proto doporučujeme použít úplné vývojové prostředí pro Docker.

## <a name="get-application-code"></a>Získání kódu aplikace

[Ukázková aplikace][sample-application] použitá v tomto kurzu je základní hlasovací aplikace, která se skládá z front-endové webové komponenty a back-endové instance Redis. Webová součást je zabalená do vlastní image kontejneru. Instance Redis využívá nezměněnou image z Docker Hubu.

Pomocí příkazu [git][] naklonujte ukázkovou aplikaci do svého vývojového prostředí:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Přejděte do klonovaného adresáře.

```console
cd azure-voting-app-redis
```

Tento adresář obsahuje zdrojový kód aplikace, předem vytvořený soubor Docker Compose a soubor manifestu Kubernetes. Tyto soubory se používají v celé této sérii kurzů. Obsah a struktura adresáře jsou následující:

```output
azure-voting-app-redis
│   azure-vote-all-in-one-redis.yaml
│   docker-compose.yaml
│   LICENSE
│   README.md
│
├───azure-vote
│   │   app_init.supervisord.conf
│   │   Dockerfile
│   │   Dockerfile-for-app-service
│   │   sshd_config
│   │
│   └───azure-vote
│       │   config_file.cfg
│       │   main.py
│       │
│       ├───static
│       │       default.css
│       │
│       └───templates
│               index.html
│
└───jenkins-tutorial
        config-jenkins.sh
        deploy-jenkins-vm.sh
```

## <a name="create-container-images"></a>Vytváření imagí kontejneru

[Docker Compose][docker-compose] je možné použít k automatizaci sestavování imagí kontejnerů a nasazování vícekontejnerových aplikací.

Pomocí ukázkového souboru `docker-compose.yaml` vytvořte image kontejneru, stáhněte image Redis a spusťte aplikaci:

```console
docker-compose up -d
```

Po dokončení můžete vytvořené image zobrazit pomocí příkazu [docker images][docker-images]. Stáhly se nebo se vytvořily tři image. Image pro *hlasování v Azure* obsahuje front-end aplikaci a používá Image *Nginx* jako základ. Image *Redis* se používá ke spuštění instance Redis.

```
$ docker images

REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        9 seconds ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Spuštěním příkazu [docker ps][docker-ps] zobrazte spuštěné kontejnery:

```
$ docker ps

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS                           NAMES
d10e5244f237        mcr.microsoft.com/azuredocs/azure-vote-front:v1   "/entrypoint.sh /sta…"   3 minutes ago       Up 3 minutes        443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
21574cb38c1f        mcr.microsoft.com/oss/bitnami/redis:6.0.8         "/opt/bitnami/script…"   3 minutes ago       Up 3 minutes        0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testování aplikace v místním prostředí

Pokud chcete zobrazit spuštěnou aplikaci, zadejte v místním webovém prohlížeči `http://localhost:8080`. Načte se ukázková aplikace, jak je znázorněno v následujícím příkladu:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Snímek obrazovky s obrázkem kontejneru hlasovací aplikace Azure, která je místně otevřená v místním webovém prohlížeči" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste ověřili funkčnost aplikace, můžete zastavit a odebrat spuštěné kontejnery. ***Neodstraňujte image kontejnerů** _ – v dalším kurzu se nahraje obrázek _azure-hlasování-dopředu * do instance Azure Container Registry.

Zastavte a odeberte instance kontejnerů a související prostředky pomocí příkazu [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

Po odebrání místní aplikace máte k dispozici image Docker, která obsahuje hlasovací aplikaci Azure, *hlasování pro Azure* pro použití s dalším kurzem.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se otestovala aplikace a vytvořily se pro ni image kontejnerů. Naučili jste se:

> [!div class="checklist"]
> * Klonování zdroje ukázkové aplikace z GitHubu
> * Vytvoření image kontejneru ze zdroje ukázkové aplikace
> * Test vícekontejnerové aplikace v místním prostředí Dockeru

V dalším kurzu se dozvíte, jak ukládat image do služby Azure Container Registry.

> [!div class="nextstepaction"]
> [Nahrávání imagí do služby Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads
[sample-application]: https://github.com/Azure-Samples/azure-voting-app-redis

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
