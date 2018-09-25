---
title: Spravovat Web App for Containers pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Spravujte Web App for Containers pomocí Azure CLI.
keywords: služby Azure app service, webové aplikace, rozhraní příkazového řádku, linux a oss
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
ms.openlocfilehash: 024ca5bcf9c1f82f07656691d6ef5358ca3d5111
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998487"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Spravovat Web App for Containers pomocí Azure CLI

Pomocí příkazů v tomto článku, který budete moct vytvářet a spravovat služby Web App for Containers pomocí Azure CLI.
Můžete začít používat novou verzi rozhraní příkazového řádku dvěma způsoby:

* [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na svém počítači.
* Pomocí [Azure Cloud Shell (Preview)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Vytvoření plánu služby App Service pro Linux

Vytvořte systému Linux plán služby App Service, můžete použít následující příkaz:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Vytvoření vlastního kontejneru Dockeru webové aplikace

Vytvoření webové aplikace a nakonfigurovat jej pro spuštění vlastního kontejneru Dockeru, můžete použít následující příkaz:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivovat protokolování kontejneru Dockeru

K aktivaci protokolování kontejneru Dockeru, můžete použít následující příkaz:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Změna vlastního kontejneru Dockeru pro webovou aplikaci existující kontejnery aplikace

Chcete-li změnit dřív vytvořenou aplikaci, z aktuální image Dockeru pro novou bitovou kopii, slouží následující příkaz:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Použití Image Dockeru z privátního registru

Můžete nakonfigurovat aplikaci, abyste mohli používat Image z privátního registru. Budete muset zadat adresu url vašeho registru, uživatelské jméno a heslo. Toho lze dosáhnout pomocí následujícího příkazu:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Povolit nepřetržité nasazení pro vlastní Image Dockeru

Pomocí následujícího příkazu můžete povolit funkci CD a získat adresu url webhooku. Tato adresa url je možné nakonfigurujete úložiště Dockerhubu nebo ve službě Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Vytvoření webové aplikace pro kontejnery aplikace pomocí jedné z našich integrovaných runtime architektury

Vytvoření webové aplikace PHP 5.6 pro kontejnery aplikace, která, slouží následující příkaz.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Změnit verzi rozhraní framework pro existující Web App for Containers aplikace

Pokud chcete změnit dřív vytvořenou aplikaci, z aktuální verze rozhraní framework Node.js 6.11, můžete použít následující příkaz:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Nastavení nasazení Git pro vaši webovou aplikaci

Nastavení nasazení Git pro vaši aplikaci, můžete použít následující příkaz:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Další postup

* [Co je Azure App Service v Linuxu?](app-service-linux-intro.md)
* [Instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (Preview)](../../cloud-shell/overview.md)
* [Nastavení přípravných prostředí ve službě Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Průběžné nasazování pomocí služby Web App for Containers](app-service-linux-ci-cd.md)
