---
title: CI/CD do vlastních kontejnerů Linux
description: Naučte se, jak nastavit průběžné nasazování do vlastního kontejneru Linux v Azure App Service. Průběžné nasazování se podporuje pro Docker Hub a ACR.
keywords: Azure App Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8a51fbcb7b7504b9a16e8d0025856c2b007070a9
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928004"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Průběžné nasazování pomocí služby Web App for Containers

V tomto kurzu nakonfigurujete průběžné nasazování pro vlastní image kontejneru ze spravovaných [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) úložišť nebo [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Povolit průběžné nasazování pomocí ACR

![Snímek Webhooku ACR](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na levé straně stránky vyberte možnost **App Service** .
3. Vyberte název aplikace, pro kterou chcete nakonfigurovat průběžné nasazování.
4. Na stránce **nastavení kontejneru** vyberte **jeden kontejner** .
5. Vyberte **Azure Container Registry**
6. Vybrat **průběžné nasazování > na**
7. Pokud chcete povolit průběžné nasazování, vyberte **Uložit** .

## <a name="use-the-acr-webhook"></a>Použití Webhooku ACR

Po povolení průběžného nasazování můžete na stránce Azure Container Registry Webhooky zobrazit nově vytvořený Webhook.

![Snímek obrazovky, který ukazuje, kde můžete na stránce Azure Container Registry Webhooky zobrazit nově vytvořený Webhook.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

V Container Registry kliknutím na Webhooky zobrazíte aktuální Webhooky.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Povolit průběžné nasazování pomocí Docker Hub (volitelné)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na levé straně stránky vyberte možnost **App Service** .
3. Vyberte název aplikace, pro kterou chcete nakonfigurovat průběžné nasazování.
4. Na stránce **nastavení kontejneru** vyberte **jeden kontejner** .
5. Výběr **centra Docker**
6. Vybrat **průběžné nasazování > na**
7. Pokud chcete povolit průběžné nasazování, vyberte **Uložit** .

![Snímek obrazovky s nastavením aplikace](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Zkopírujte adresu URL Webhooku. Pokud chcete přidat Webhook pro Docker Hub, Sledujte <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhooky pro Docker Hub</a>.

## <a name="automate-with-cli"></a>Automatizace pomocí rozhraní příkazového řádku

Pokud chcete konfigurovat CI/CD pomocí Azure CLI, spusťte příkaz [AZ WebApp Deployment Container config](/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) , který vygeneruje adresu URL Webhooku. Adresu URL lze použít ke konfiguraci Dockerhubu nebo Azure Container Registry.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Další kroky

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md?pivots=platform-linux)
* [Vytvoření webové aplikace v Ruby v App Service v systému Linux](quickstart-ruby.md)
* [Rychlý Start: spuštění vlastního kontejneru v App Service](quickstart-custom-container.md?pivots=container-linux)
* [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)
* [Konfigurace vlastních kontejnerů pro Linux](configure-custom-container.md)