---
title: Správa vlastních kontejnerů Linux pomocí rozhraní příkazového řádku
description: Naučte se spravovat vlastní kontejnery Linux v Azure App Service z příkazového řádku. Automatizace zřizování a údržby aplikací.
keywords: Azure App Service, Web App, CLI, Linux, OSS
author: ahmedelnably
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 773c8036a345383162013f9f7103164b0f382f12
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689064"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Správa Web App for Containers pomocí rozhraní příkazového řádku Azure

Pomocí příkazů v tomto článku můžete vytvořit a spravovat Web App for Containers pomocí rozhraní příkazového řádku Azure CLI.
Novou verzi rozhraní příkazového řádku můžete začít používat dvěma způsoby:

* Instalace rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na váš počítač.
* Použití [Azure Cloud Shell (Preview)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Vytvoření plánu App Service pro Linux

Pokud chcete vytvořit plán App Service pro Linux, můžete použít následující příkaz:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Vytvoření vlastní webové aplikace kontejneru Docker

Pokud chcete vytvořit webovou aplikaci a nakonfigurovat ji tak, aby spouštěla vlastní kontejner Docker, můžete použít následující příkaz:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivace protokolování kontejneru Docker

K aktivaci protokolování kontejneru Docker můžete použít následující příkaz:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Změna vlastního kontejneru Docker pro existující aplikaci Web App for Containers

Chcete-li změnit dříve vytvořenou aplikaci, z aktuální image Docker na novou bitovou kopii můžete použít následující příkaz:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Použití imagí Docker z privátního registru

Aplikaci můžete nakonfigurovat tak, aby používala image z privátního registru. Je nutné zadat adresu URL pro váš registr, uživatelské jméno a heslo. Toho lze dosáhnout pomocí následujícího příkazu:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Povolit nepřetržitá nasazení pro vlastní image Docker

Pomocí následujícího příkazu můžete povolit funkce CD a získat adresu URL Webhooku. Tato adresa URL se dá použít ke konfiguraci úložišť Dockerhubu nebo Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Vytvoření aplikace Web App for Containers pomocí některé z našich vestavěných prostředí modulu runtime

Pokud chcete vytvořit Web App for Containers aplikaci PHP 5,6, můžete použít následující příkaz.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Změna verze rozhraní pro existující aplikaci Web App for Containers

Chcete-li změnit dříve vytvořenou aplikaci z aktuální verze rozhraní na Node. js 6,11, můžete použít následující příkaz:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Nastavení nasazení Git pro vaši webovou aplikaci

Pro nastavení nasazení Git pro vaši aplikaci můžete použít následující příkaz:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Další kroky

* [Co je Azure App Service v systému Linux?](app-service-linux-intro.md)
* [Instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (Preview)](../../cloud-shell/overview.md)
* [Nastavení přípravných prostředí ve službě Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Průběžné nasazování pomocí Web App for Containers](app-service-linux-ci-cd.md)
