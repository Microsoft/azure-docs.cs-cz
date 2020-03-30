---
title: Správa vlastních linuxových kontejnerů pomocí cli
description: Zjistěte, jak spravovat vlastní linuxové kontejnery ve službě Azure App Service z příkazového řádku. Automatizujte zřizování nebo údržbu aplikací.
keywords: azure app service, webová aplikace, cli, linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255930"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Správa služby Web App for Containers pomocí Azure CLI

Pomocí příkazů v tomto článku můžete vytvořit a spravovat webovou aplikaci pro kontejnery pomocí azure CLI.
Novou verzi vytelatého zoufukovatce můžete začít dvěma způsoby:

* [Instalace azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) na vašem počítači.
* Použití [prostředí Azure Cloud Shell (preview)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Vytvoření plánu služby Linux App Service

Chcete-li vytvořit plán služby Linux App Service Plan, můžete použít následující příkaz:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Vytvoření vlastního webového appu kontejneru Dockeru

Chcete-li vytvořit webovou aplikaci a nakonfigurovat ji tak, aby spouštěla vlastní kontejner Dockeru, můžete použít následující příkaz:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivace protokolování kontejnerů Dockeru

Chcete-li aktivovat protokolování kontejnerů Dockeru, můžete použít následující příkaz:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Změna vlastního kontejneru Dockeru pro existující webovou aplikaci pro kontejnery

Chcete-li změnit dříve vytvořenou aplikaci z aktuální image Dockeru na novou, můžete použít následující příkaz:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Použití imitací Dockeru ze soukromého registru

Aplikaci můžete nakonfigurovat tak, aby používala obrázky ze soukromého registru. Je třeba zadat adresu URL registru, uživatelské jméno a heslo. Toho lze dosáhnout pomocí následujícího příkazu:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Povolení nepřetržitého nasazení pro vlastní imitace Dockeru

Pomocí následujícího příkazu můžete povolit funkci CD a získat adresu URL webhooku. Tuto adresu URL můžete použít ke konfiguraci úložiště DockerHub nebo Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Vytvoření webové aplikace pro kontejnery pomocí jednoho z našich integrovaných runtime architektur

Chcete-li vytvořit PHP 5.6 Web App pro kontejnery App, který, můžete použít následující příkaz.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Změna verze architektury pro existující webovou aplikaci pro kontejnery App

Chcete-li změnit dříve vytvořenou aplikaci z aktuální verze architektury na Node.js 6.11, můžete použít následující příkaz:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Nastavení nasazení Gitu pro webovou aplikaci

Pokud chcete nastavit nasazení Gitu pro vaši aplikaci, můžete použít následující příkaz:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Další kroky

* [Co je služba Azure App Service na Linuxu?](app-service-linux-intro.md)
* [Instalace azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (náhled)](../../cloud-shell/overview.md)
* [Nastavení přípravných prostředí ve službě Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Průběžné nasazování pomocí webové aplikace pro kontejnery](app-service-linux-ci-cd.md)
