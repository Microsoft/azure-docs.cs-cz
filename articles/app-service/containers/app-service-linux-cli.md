---
title: Správa webové aplikace pro kontejnery pomocí Azure CLI 2.0 | Microsoft Docs
description: Správa webové aplikace pro kontejnery pomocí rozhraní příkazového řádku Azure.
keywords: služby Azure app service, webové aplikace, rozhraní příkazového řádku, linux, operačních systémů
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.openlocfilehash: 54c979313a6ffa43008aa9870332b92d2b2f182a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
ms.locfileid: "24105385"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Správa webové aplikace pro kontejnery pomocí rozhraní příkazového řádku Azure

Pomocí příkazů v tomto článku budete moci vytvářet a spravovat webové aplikace pro kontejnery pomocí Azure CLI 2.0.
Můžete začít používat nové verze rozhraní příkazového řádku dvěma způsoby:

* [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) na váš počítač.
* Pomocí [prostředí cloudu Azure (Preview)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Vytvoření plánu služby App Service pro Linux

Pokud chcete vytvořit, Linux plán služby App Service, můžete použít následující příkaz:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Vytvořit vlastní kontejner Docker webové aplikace

K vytvoření webové aplikace a nastavit ho na spuštění vlastní kontejner Docker, můžete použít následující příkaz:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivovat protokolování kontejner Docker

K aktivaci protokolování kontejner Docker, můžete použít následující příkaz:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Změna vlastní kontejner Docker pro existující webovou aplikaci pro aplikaci kontejnery

Chcete-li změnit dříve vytvořenou aplikaci, z aktuální image Docker na novou bitovou kopii, můžete tyto příkazy:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Pomocí Docker obrázky z privátní registru

Můžete nakonfigurovat pomocí bitových kopií z privátní registru v aplikaci. Je třeba zadat adresu url pro váš registru, uživatelské jméno a heslo. Toho lze dosáhnout pomocí následujícího příkazu:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Povolit průběžnou nasazení pro vlastní Image Docker

Pomocí následujícího příkazu můžete povolit funkci CD a získat adresu url webhooku. Tuto adresu url slouží ke konfiguraci můžete DockerHub nebo registru Azure kontejner úložiště.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Vytvoření webové aplikace pro kontejnery aplikace pomocí jedné z našich předdefinované runtime rozhraní

K vytvoření webové aplikace PHP 5.6 pro aplikaci kontejnery, můžete použít následující příkaz.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Změnit verzi framework pro stávající webovou aplikaci pro aplikaci kontejnery

Chcete-li změnit dříve vytvořenou aplikaci, z aktuální verze framework na Node.js 6.11, můžete tyto příkazy:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Nastavení nasazení Git pro vaši webovou aplikaci

Nastavit nasazení Git pro vaši aplikaci, můžete použít následující příkaz:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Další kroky

* [Co je Azure App Service v systému Linux?](app-service-linux-intro.md)
* [Instalace rozhraní příkazového řádku Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Prostředí cloudu Azure (Preview)](../../cloud-shell/overview.md)
* [Nastavení přípravných prostředí v Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Průběžné nasazování pomocí webové aplikace pro kontejnery](app-service-linux-ci-cd.md)
