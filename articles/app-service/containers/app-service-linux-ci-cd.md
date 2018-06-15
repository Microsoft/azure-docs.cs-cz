---
title: Průběžné nasazování registr, kontejner Docker s webovou aplikaci pro kontejnery - Azure | Microsoft Docs
description: Jak nastavit průběžné nasazování registr, kontejner Docker ve webové aplikaci pro kontejnery.
keywords: služby Azure app service, linux, docker, acr, operačních systémů
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30168322"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Průběžné nasazování s webovou aplikaci pro kontejnery

V tomto kurzu nakonfigurujete průběžné nasazování pro bitovou kopii vlastní kontejner ze spravovaných [registru kontejner Azure](https://azure.microsoft.com/services/container-registry/) úložiště nebo [úložiště Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Povolit funkci průběžné nasazování.

Povolit funkci průběžné nasazování pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a spuštěním následujícího příkazu:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

V [portál Azure](https://portal.azure.com/), vyberte **služby App Service** možnost na levé straně stránky.

Vyberte název aplikace, pro kterou chcete provést konfiguraci úložiště Docker Hub průběžné nasazování.

Na **kontejner Docker** vyberte **na**a potom vyberte **Uložit** povolit průběžné nasazování.

![Snímek obrazovky nastavení aplikace](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Příprava adresa URL webhooku

Získat adresu URL webhooku pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a spuštěním následujícího příkazu:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

URL webhooku se nenačetla, potřebujete následující koncový bod: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Můžete získat vaše `publishingusername` a `publishingpwd` stažením webové aplikace profil publikování se pomocí portálu Azure.

![Snímek obrazovky přidání webhooku 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Přidat webhook, jehož

### <a name="azure-container-registry"></a>Azure Container Registry

1. Na stránce portálu registru, vyberte **Webhooky**.
2. Chcete-li vytvořit nové webhooku, vyberte **přidat**. 
3. V **vytvoření webhooku** podokně, zadejte název vaší webhooku. Webhooku identifikátor URI zadejte adresu URL získaných v předchozím oddílu.

Zajistěte, aby že definujte rozsah jako úložiště obsahující bitové kopie kontejneru.

![Snímek obrazovky webhooku](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Při aktualizaci bitovou kopii, webové aplikace se automaticky aktualizuje s novou bitovou kopii.

### <a name="docker-hub"></a>Docker Hub

Na stránce úložiště Docker Hub, vyberte **Webhooky**a potom **vytvoření WEBHOOKU A**.

![Snímek obrazovky přidání webhooku 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

URL webhooku se nenačetla zadejte adresu URL, kterou jste získali dříve.

![Snímek obrazovky přidání webhooku 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Při aktualizaci bitovou kopii, webové aplikace se automaticky aktualizuje s novou bitovou kopii.

## <a name="next-steps"></a>Další postup

* [Úvod do Azure App Service v systému Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md)
* [Vytvoření Ruby webové aplikace ve službě App Service v systému Linux](quickstart-ruby.md)
* [Nasazení webové aplikace Docker nebo přejděte ve webové aplikaci pro kontejnery](quickstart-docker-go.md)
* [Nejčastější dotazy k Azure App Service v Linuxu](./app-service-linux-faq.md)
* [Správa webové aplikace pro kontejnery pomocí rozhraní příkazového řádku Azure](./app-service-linux-cli.md)
