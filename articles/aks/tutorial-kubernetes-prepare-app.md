---
title: Kurz Kubernetes v Azure – Příprava aplikace
description: Kurz AKS – Příprava aplikace
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8a2c2e53ed04cf00cc02135c5e5f82ded18fc2bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-prepare-application-for-azure-container-service-aks"></a>Kurz: Příprava aplikace pro službu Azure Container Service (AKS)

V tomto kurzu, který je první částí osmidílné série, se připraví vícekontejnerová aplikace pro použití v Kubernetes. Mezi dokončené kroky patří:  

> [!div class="checklist"]
> * Klonování zdroje aplikace z GitHubu  
> * Vytvoření image kontejneru ze zdroje aplikace
> * Test aplikace v místním prostředí Dockeru

Po dokončení bude ve vašem místním vývojovém prostředí dostupná následující aplikace.

![Obrázek clusteru Kubernetes v Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

V následujících kurzech se image kontejneru nahraje do služby Azure Container Registry a pak se spustí v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

V tomto kurzu se předpokládá základní znalost klíčových konceptů Dockeru, jako jsou kontejnery, image kontejnerů a základní příkazy Dockeru. V případě potřeby najdete základní informace o kontejnerech v článku [Get started with Docker][docker-get-started] (Začínáme s Dockerem). 

K dokončení tohoto kurzu potřebujete vývojové prostředí pro Docker. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-for-mac] nebo systému [Windows][docker-for-windows] či [Linux][docker-for-linux].

Azure Cloud Shell neobsahuje součásti Dockeru nutné pro dokončení všech kroků v tomto kurzu. Proto doporučujeme použít úplné vývojové prostředí pro Docker.

## <a name="get-application-code"></a>Získání kódu aplikace

Ukázkovou aplikací používanou v tomto kurzu je základní hlasovací aplikace. Aplikace se skládá z front-end webové součásti a back-end instance Redis. Webová součást je zabalená do vlastní image kontejneru. Instance Redis využívá nezměněnou image z Docker Hubu.  

Pomocí gitu si stáhněte kopii aplikace do vývojového prostředí.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Změňte adresáře tak, abyste pracovali v naklonovaném adresáři.

```console
cd azure-voting-app-redis
```

Tento adresář obsahuje zdrojový kód aplikace, předem vytvořený soubor Docker Compose a soubor manifestu Kubernetes. Tyto soubory se používají v celé této sérii kurzů. 

## <a name="create-container-images"></a>Vytváření imagí kontejneru

[Docker Compose][docker-compose] je možné použít k automatizaci sestavení z imagí kontejnerů a nasazení vícekontejnerových aplikací.

Spuštěním souboru `docker-compose.yaml` vytvořte image kontejneru, stáhněte image Redis a spusťte aplikaci.

```console
docker-compose up -d
```

Po dokončení můžete vytvořené image zobrazit pomocí příkazu [docker images][docker-images].

```console
docker images
```

Všimněte si, že se stáhly nebo vytvořily tři image. Image `azure-vote-front` obsahuje aplikaci a jako základ využívá image `nginx-flask`. Image `redis` slouží ke spuštění instance Redis.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Spuštěním příkazu [docker ps][docker-ps] zobrazte spuštěné kontejnery.

```console
docker ps
```

Výstup:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testování aplikace v místním prostředí

Přejděte na http://localhost:8080 a prohlédněte si spuštěnou aplikaci.

![Obrázek clusteru Kubernetes v Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste ověřili funkčnost aplikace, můžete zastavit a odebrat spuštěné kontejnery. Neodstraňujte image kontejnerů. Image `azure-vote-front` se do instance služby Azure Container Registry nahraje v dalším kurzu.

Spuštěním následujícího příkazu zastavte spuštěné kontejnery.

```console
docker-compose stop
```

Pomocí následujícího příkazu odstraňte zastavené kontejnery a prostředky.

```console
docker-compose down
```

Po dokončení budete mít image kontejneru obsahující aplikaci Azure Vote.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se otestovala aplikace a vytvořily se pro ni image kontejnerů. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Klonování zdroje aplikace z GitHubu  
> * Vytvoření image kontejneru ze zdroje aplikace
> * Test aplikace v místním prostředí Dockeru

Přejděte k dalšímu kurzu, ve kterém se seznámíte s ukládáním imagí kontejnerů ve službě Azure Container Registry.

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

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md