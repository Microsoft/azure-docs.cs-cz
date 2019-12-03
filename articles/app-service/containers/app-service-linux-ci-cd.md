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
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687633"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Průběžné nasazování pomocí Web App for Containers

V tomto kurzu nakonfigurujete průběžné nasazování pro vlastní image kontejneru ze spravovaných [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) úložišť nebo [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Povolit průběžné nasazování pomocí ACR

![Snímek Webhooku ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Na levé straně stránky vyberte možnost **App Service** .
3. Vyberte název aplikace, pro kterou chcete nakonfigurovat průběžné nasazování.
4. Na stránce **nastavení kontejneru** vyberte **jeden kontejner** .
5. Vyberte **Azure Container Registry**
6. Vybrat **průběžné nasazování > na**
7. Pokud chcete povolit průběžné nasazování, vyberte **Uložit** .

## <a name="use-the-acr-webhook"></a>Použití Webhooku ACR

Po povolení průběžného nasazování můžete na stránce Azure Container Registry Webhooky zobrazit nově vytvořený Webhook.

![Snímek Webhooku ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

V Container Registry kliknutím na Webhooky zobrazíte aktuální Webhooky.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Povolit průběžné nasazování pomocí Docker Hub (volitelné)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Na levé straně stránky vyberte možnost **App Service** .
3. Vyberte název aplikace, pro kterou chcete nakonfigurovat průběžné nasazování.
4. Na stránce **nastavení kontejneru** vyberte **jeden kontejner** .
5. Výběr **centra Docker**
6. Vybrat **průběžné nasazování > na**
7. Pokud chcete povolit průběžné nasazování, vyberte **Uložit** .

![Snímek obrazovky s nastavením aplikace](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Zkopírujte adresu URL Webhooku. Pokud chcete přidat Webhook pro Docker Hub, Sledujte <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhooky pro Docker Hub</a>.

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure App Service v systému Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md)
* [Vytvoření webové aplikace v Ruby v App Service v systému Linux](quickstart-ruby.md)
* [Nasazení webové aplikace Docker/jít do Web App for Containers](quickstart-docker-go.md)
* [Nejčastější dotazy k Azure App Service v Linuxu](./app-service-linux-faq.md)
* [Správa služby Web App for Containers pomocí Azure CLI](./app-service-linux-cli.md)
