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
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 20ca63b7126a6800538129115ff339308c11d8c5
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867016"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Průběžné nasazování se službou Web App for Containers

V tomto kurzu nakonfigurujete průběžné nasazování pro vlastní image kontejneru ze spravované [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) úložišť nebo [Docker Hubu](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Povolit funkci průběžné nasazování

Povolit funkci průběžné nasazování s použitím [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a spuštěním následujícího příkazu:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

V [webu Azure portal](https://portal.azure.com/), vyberte **služby App Service** možnost na levé straně stránky.

Vyberte název aplikace, pro kterou chcete nakonfigurovat průběžné nasazování Docker Hubu.

Na **nastavení kontejneru** stránce **na**a pak vyberte **Uložit** umožnit průběžné nasazování.

![Snímek obrazovky nastavení aplikace](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Příprava adresu URL webhooku

Získat adresu URL webhooku pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a spuštěním následujícího příkazu:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Poznamenejte si adresu URL webhooku. Budete je potřebovat v další části.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Můžete získat vaše `publishingusername` a `publishingpwd` stažením webové aplikace publikovat profilu pomocí webu Azure portal.

![Snímek obrazovky přidání webhooku 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Přidání webhooku

Chcete-li přidat webhook, postupujte podle těchto návodů:

- [Služba Azure Container Registry](../../container-registry/container-registry-webhook.md) využívající adresu URL webhooku
- [Webhooky pro Docker Hubu](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure App Service v Linuxu](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md)
* [Vytvoření webové aplikace Ruby ve službě App Service v Linuxu](quickstart-ruby.md)
* [Nasazení webové aplikace v Dockeru/Go ve službě Web App for Containers](quickstart-docker-go.md)
* [Nejčastější dotazy k Azure App Service v Linuxu](./app-service-linux-faq.md)
* [Spravovat Web App for Containers pomocí Azure CLI](./app-service-linux-cli.md)
