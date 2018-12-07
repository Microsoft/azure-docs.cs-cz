---
title: (NEPOUŽÍVANÉ) Kurz Azure Container Service – Příprava aplikace
description: Kurz Azure Container Service – Příprava aplikace
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a003248104c45fa2bcc1020e0ad8ea895d759457
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992381"
---
# <a name="deprecated-create-container-images-to-be-used-with-azure-container-service"></a>(NEPOUŽÍVANÉ) Vytváření imagí kontejneru pro použití s Azure Container Service

> [!TIP]
> Aktualizovaná verze tohoto kurzu, který používá Azure Kubernetes Service, najdete v článku [kurz: Příprava aplikace pro Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-prepare-app.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

V tomto kurzu, který je první částí sedmidílné série, se připraví vícekontejnerová aplikace pro použití v Kubernetes. Mezi dokončené kroky patří:  

> [!div class="checklist"]
> * Klonování zdroje aplikace z GitHubu  
> * Vytvoření image kontejneru ze zdroje aplikace
> * Test aplikace v místním prostředí Dockeru

Po dokončení bude ve vašem místním vývojovém prostředí dostupná následující aplikace.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

V následujících kurzech se image kontejneru nahraje do služby Azure Container Registry a pak se spustí v clusteru Kubernetes hostovaném v Azure.

## <a name="before-you-begin"></a>Než začnete

V tomto kurzu se předpokládá základní znalost klíčových konceptů Dockeru, jako jsou kontejnery, image kontejnerů a základní příkazy Dockeru. V případě potřeby najdete základní informace o kontejnerech v článku [Get started with Docker]( https://docs.docker.com/get-started/) (Začínáme s Dockerem). 

K dokončení tohoto kurzu potřebujete vývojové prostředí pro Docker. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli systému [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) nebo [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell neobsahuje součásti Dockeru nutné pro dokončení všech kroků v tomto kurzu. Proto doporučujeme použít úplné vývojové prostředí pro Docker.

## <a name="get-application-code"></a>Získání kódu aplikace

Ukázkovou aplikací používanou v tomto kurzu je základní hlasovací aplikace. Aplikace se skládá z front-end webové součásti a back-end instance Redis. Webová součást je zabalená do vlastní image kontejneru. Instance Redis využívá nezměněnou image z Docker Hubu.  

Pomocí gitu si stáhněte kopii aplikace do vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Změňte adresáře tak, abyste pracovali v naklonovaném adresáři.

```
cd azure-voting-app-redis
```

Tento adresář obsahuje zdrojový kód aplikace, předem vytvořený soubor Docker Compose a soubor manifestu Kubernetes. Tyto soubory se používají v celé této sérii kurzů. 

## <a name="create-container-images"></a>Vytváření imagí kontejneru

[Docker Compose](https://docs.docker.com/compose/) je možné použít k automatizaci sestavení z imagí kontejnerů a nasazení vícekontejnerových aplikací.

Spuštěním souboru `docker-compose.yml` vytvořte image kontejneru, stáhněte image Redis a spusťte aplikaci.

```bash
docker-compose up -d
```

Po dokončení můžete vytvořené image zobrazit pomocí příkazu [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Všimněte si, že se stáhly nebo vytvořily tři image. Image `azure-vote-front` obsahuje aplikaci a jako základ využívá image `nginx-flask`. Image `redis` slouží ke spuštění instance Redis.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Spuštěním příkazu [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) zobrazte spuštěné kontejnery.

```bash
docker ps
```

Výstup:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testování aplikace v místním prostředí

Přejděte na adresu http://localhost:8080 a prohlédněte si spuštěnou aplikaci.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste ověřili funkčnost aplikace, můžete zastavit a odebrat spuštěné kontejnery. Neodstraňujte image kontejnerů. Image `azure-vote-front` se do instance služby Azure Container Registry nahraje v dalším kurzu.

Spuštěním následujícího příkazu zastavte spuštěné kontejnery.

```bash
docker-compose stop
```

Pomocí následujícího příkazu odstraňte zastavené kontejnery a prostředky.

```bash
docker-compose down
```

Po dokončení budete mít image kontejneru obsahující aplikaci Azure Vote.

## <a name="next-steps"></a>Další postup

V tomto kurzu se otestovala aplikace a vytvořily se pro ni image kontejnerů. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Klonování zdroje aplikace z GitHubu  
> * Vytvoření image kontejneru ze zdroje aplikace
> * Test aplikace v místním prostředí Dockeru

Přejděte k dalšímu kurzu, ve kterém se seznámíte s ukládáním imagí kontejnerů ve službě Azure Container Registry.

> [!div class="nextstepaction"]
> [Nahrávání imagí do služby Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
