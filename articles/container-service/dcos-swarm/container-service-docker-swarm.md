---
title: (NEPOUŽÍVANÉ) Správa clusteru Swarm v Azure pomocí rozhraní API Dockeru
description: Nasazení kontejnerů do clusteru Docker Swarm ve službě Azure Container Service
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 04cc9048271d653bd77fd7f2707c8f510ea8c29f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997951"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>(NEPOUŽÍVANÉ) Správa kontejnerů pomocí nástroje Docker Swarm

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Docker Swarm poskytuje prostředí pro nasazování kontejnerizovaných úloh v celé sadě hostitelů Docker uspořádaných do fondu. Docker Swarm využívá nativní rozhraní Docker API. Workflow správy kontejnerů v nástroji Docker Swarm je téměř identický s workflow pro hostitele s jedním kontejnerem. Tento dokument poskytuje jednoduché příklady, jak nasadit kontejnerizované úlohy do instance Docker Swarm v Azure Container Service. Podrobnější dokumentaci k nástroji Docker Swarm najdete v tématu [Docker Swarm na Docker.com](https://docs.docker.com/swarm/).

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Předpoklady pro praktická cvičení v tomto dokumentu:

[Vytvoření clusteru Swarm v Azure Container Service](container-service-deployment.md)

[Propojení s clusterem Swarm ve službě Azure Container Service](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Nasazení nového kontejneru
Chcete-li vytvořit nový kontejner v Docker Swarm, použijte příkaz `docker run` (zajistí, že jste otevřeli tunelové propojení SSH k předlohám podle výše uvedených požadavků). Tento příklad vytvoří kontejner z image `yeasy/simple-web`:

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Až se kontejner vytvoří, zobrazte si informace o kontejneru příkazem `docker ps`. Můžete si zde všimnout, že je uveden agent Swarm, který je hostitelem kontejneru:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Nyní můžete k aplikaci, která běží v tomto kontejneru, přistoupit přes veřejný název DNS nástroje pro vyrovnávání zatížení agenta Swarm. Na Portálu Azure lze najít tyto informace:  

![Výsledky skutečných návštěv](./media/container-service-docker-swarm/real-visit.jpg)  

Ve výchozím nastavení má nástroj pro vyrovnávání zatížení otevřené porty 80, 8080 a 443. Pokud se chcete připojit k jinému portu, musíte otevřít tento port v nástroji Azure Load Balancer pro fond agenta.

## <a name="deploy-multiple-containers"></a>Nasazení několika kontejnerů
Když je spuštěno více kontejnerů, je možné vícenásobným spuštěním příkazu „spuštění docker“ použít příkaz `docker ps` k zobrazení, na kterých hostitelích kontejnery běží. V tomto příkladu níže jsou tři kontejnery rovnoměrně rozloženy na tři agenty Swarm:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Nasazení kontejnerů pomocí Docker Compose
Pomocí Docker Compose je možné automatizovat nasazení a konfiguraci několika kontejnerů. Abyste toho mohli využít, ujistěte se, že je vytvořen tunel Secure Shell (SSH) a že je nastavena proměnná DOCKER_HOST (viz předpoklady výše).

Vytvořte v lokálním systému soubor docker-compose.yml. Použijte k tomu tuto [ukázku](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Příkazem `docker-compose up -d` spusťte nasazení kontejnerů:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Nakonec se vrátí seznam spuštěných kontejnerů. Tento seznam reflektuje kontejnery nasazené pomocí Docker Compose:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Přirozeně, můžete použít `docker-compose ps` k prozkoumání samotných kontejnerů definovaných ve vašem souboru `compose.yml`.

## <a name="next-steps"></a>Další postup
[Další informace o Docker Swarmu](https://docs.docker.com/swarm/)

