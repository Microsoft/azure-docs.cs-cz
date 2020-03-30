---
title: CI/CD na vlastní linuxové kontejnery
description: Zjistěte, jak nastavit průběžné nasazování do vlastního kontejneru Linuxu ve službě Azure App Service. Pro Docker Hub a ACR je podporováno průběžné nasazení.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687633"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Průběžné nasazování pomocí služby Web App for Containers

V tomto kurzu nakonfigurujete průběžné nasazení pro vlastní image kontejneru ze spravovaných úložišť [azure registru kontejnerů](https://azure.microsoft.com/services/container-registry/) nebo [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Povolení průběžného nasazování pomocí ACR

![Snímek obrazovky s webhookem ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Na levé straně stránky vyberte možnost **Služba aplikace.**
3. Vyberte název aplikace, pro kterou chcete konfigurovat průběžné nasazení.
4. Na stránce **Nastavení kontejneru** vyberte **Jeden kontejner.**
5. Výběr **registru kontejnerů Azure**
6. Vyberte **možnost Nepřetržité nasazení > zapnuto.**
7. Chcete-li povolit průběžné nasazení, vyberte **možnost Uložit.**

## <a name="use-the-acr-webhook"></a>Použití webového háčku ACR

Po povolení nepřetržitého nasazení můžete zobrazit nově vytvořený webhook na stránce webhooků registru kontejnerů Azure.

![Snímek obrazovky s webhookem ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

V registru kontejnerů klikněte na Webhooks pro zobrazení aktuální webhooky.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Povolení nepřetržitého nasazení pomocí Centra Docker Hub (volitelné)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Na levé straně stránky vyberte možnost **Služba aplikace.**
3. Vyberte název aplikace, pro kterou chcete konfigurovat průběžné nasazení.
4. Na stránce **Nastavení kontejneru** vyberte **Jeden kontejner.**
5. Vybrat **centrum Dockeru**
6. Vyberte **možnost Nepřetržité nasazení > zapnuto.**
7. Chcete-li povolit průběžné nasazení, vyberte **možnost Uložit.**

![Snímek obrazovky s nastavením aplikace](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Zkopírujte adresu URL webhooku. Pokud chcete přidat webhookpro Docker Hub, postupujte podle <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooků pro Docker Hub</a>.

## <a name="next-steps"></a>Další kroky

* [Seznámení se službou Azure App Service v Linuxu](./app-service-linux-intro.md)
* [Registr kontejnerů Azure](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md)
* [Vytvoření webové aplikace Ruby ve službě App Service na Linuxu](quickstart-ruby.md)
* [Nasazení webové aplikace v Dockeru/Go ve službě Web App for Containers](quickstart-docker-go.md)
* [Nejčastější dotazy k Azure App Service v Linuxu](./app-service-linux-faq.md)
* [Správa služby Web App for Containers pomocí Azure CLI](./app-service-linux-cli.md)
