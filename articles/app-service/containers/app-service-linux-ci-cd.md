---
title: Průběžné nasazování registr, kontejner Docker s webovou aplikaci pro kontejnery - Azure | Microsoft Docs
description: Jak nastavit průběžné nasazování registr, kontejner Docker ve webové aplikaci pro kontejnery.
keywords: služby Azure app service, linux, docker, acr, operačních systémů
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
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130957"
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

Poznamenejte si adresu URL webhooku. Budete ho potřebovat v další části.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Můžete získat vaše `publishingusername` a `publishingpwd` stažením webové aplikace profil publikování se pomocí portálu Azure.

![Snímek obrazovky přidání webhooku 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Přidat webhook, jehož

Pokud chcete přidat webhook, jehož, postupujte podle kroků v těchto průvodcích se dozvíte:

- [Azure kontejneru registru](../../container-registry/container-registry-webhook.md) pomocí adresy URL webhooku
- [Webhooky pro úložiště Docker Hub](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Další postup

* [Úvod do Azure App Service v systému Linux](./app-service-linux-intro.md)
* [Kontejner Azure registru](https://azure.microsoft.com/services/container-registry/)
* [Vytvoření webové aplikace .NET Core ve službě App Service v Linuxu](quickstart-dotnetcore.md)
* [Vytvoření Ruby webové aplikace ve službě App Service v systému Linux](quickstart-ruby.md)
* [Nasazení webové aplikace Docker nebo přejděte ve webové aplikaci pro kontejnery](quickstart-docker-go.md)
* [Nejčastější dotazy k Azure App Service v Linuxu](./app-service-linux-faq.md)
* [Správa webové aplikace pro kontejnery pomocí rozhraní příkazového řádku Azure](./app-service-linux-cli.md)
