---
title: Průběžné nasazování z registru kontejnerů Dockeru pomocí služby Web App for Containers – Azure | Dokumentace Microsoftu
description: Jak nastavit průběžné nasazování z registru kontejnerů Dockeru ve službě Web App for Containers.
keywords: služby Azure app service, od linuxu, docker, acr, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu;yili
ms.openlocfilehash: b26366edddc223b842cc5d38473bda42422f1840
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298524"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Průběžné nasazování se službou Web App for Containers

V tomto kurzu nakonfigurujete průběžné nasazování pro vlastní image kontejneru ze spravované [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) úložišť nebo [Docker Hubu](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Povolit průběžné nasazování s ACR

![Snímek obrazovky s ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **služby App Service** možnost na levé straně stránky.
3. Vyberte název aplikace, pro kterou chcete nakonfigurovat průběžné nasazování.
4. Na **nastavení kontejneru** stránce **jedním kontejnerem**
5. Vyberte **Azure Container Registry**
6. Vyberte **průběžné nasazování > na**
7. Vyberte **Uložit** umožnit průběžné nasazování.

## <a name="use-the-acr-webhook"></a>Použití webhooků ACR

Po povolení průběžného nasazování můžete zobrazit nově vytvořené webhookem na stránce webhooky registru kontejneru Azure.

![Snímek obrazovky s ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

V registru kontejneru klikněte na Webhooky k zobrazení webhooků, které aktuální.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Povolit průběžné nasazování s Docker Hubu (volitelné)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **služby App Service** možnost na levé straně stránky.
3. Vyberte název aplikace, pro kterou chcete nakonfigurovat průběžné nasazování.
4. Na **nastavení kontejneru** stránce **jedním kontejnerem**
5. Vyberte **Docker Hubu**
6. Vyberte **průběžné nasazování > na**
7. Vyberte **Uložit** umožnit průběžné nasazování.

![Snímek obrazovky nastavení aplikace](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Zkopírujte adresu URL Webhooku. Přidání webhooku pro Docker Hub, postupujte podle <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooky pro Docker Hubu</a>.

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure App Service v Linuxu](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md)
* [Vytvoření webové aplikace Ruby ve službě App Service v Linuxu](quickstart-ruby.md)
* [Nasazení webové aplikace v Dockeru/Go ve službě Web App for Containers](quickstart-docker-go.md)
* [Nejčastější dotazy k Azure App Service v Linuxu](./app-service-linux-faq.md)
* [Spravovat Web App for Containers pomocí Azure CLI](./app-service-linux-cli.md)
