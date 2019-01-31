---
title: Rychlý start – vytvoření privátního registru Dockeru v Azure – Azure portal
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí webu Azure Portal.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 93c22475a4043d1cbf5cb0ad7f9b134e8ac717cc
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298411"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Rychlý start: Vytvořit privátní registr pomocí webu Azure portal

Registr kontejnerů Azure je privátním registrem Dockeru v Azure, kde můžete ukládat a spravovat privátní image kontejnerů Dockeru. V tomto rychlém startu vytvoříte registr kontejnerů pomocí webu Azure Portal. Potom použijte příkazy Dockeru pro nasdílení image kontejneru do registru a nakonec o přijetí změn a spusťte image z registru.

Pro přihlášení k registru pro práci s imagí kontejnerů, tento rychlý start vyžaduje použití rozhraní příkazového řádku Azure (verze 2.0.55 nebo později doporučené). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-mac] nebo systému [Windows][docker-windows] či [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-01]

Zadejte odpovídající hodnoty pro **Název registru** a **Skupina prostředků**. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Pro účely tohoto rychlého startu vytvořte novou skupinu prostředků `myResourceGroup` v umístění `West US` a jako **skladovou položku** vyberte Basic. Vyberte **Vytvořit** a nasaďte instanci služby ACR.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-03]

V tomto rychlém startu vytvoříte *základní* registru, který je možnost náklady optimalizovaná pro vývojáře, další informace o službě Azure Container Registry. Podrobnosti o úrovních služeb najdete v tématu [skladové položky služby Container registry][container-registry-skus].

Když **nasazení bylo úspěšné** se zobrazí zpráva, vyberte na portálu registr kontejnerů. 

![Registr kontejnerů přehled na webu Azure Portal][qs-portal-05]

Poznamenejte si hodnotu **přihlašovací server**. Tuto hodnotu použijete v následujících krocích při práci s registrem pomocí rozhraní příkazového řádku Azure a Dockeru.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k instanci služby ACR. Otevřete příkazové okno v operačním systému a použít [az acr login] [ az-acr-login] příkaz v rozhraní příkazového řádku Azure.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné). 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Výpis všech imagí v registru, přejděte do svého registru v portálu a vyberte **úložišť**, pak vyberte úložiště, který jste vytvořili pomocí `docker push`.

V tomto příkladu vybereme **busybox** úložiště a jsme viděli `v1`-tagged image v rámci **značky**.

![Výpis imagí kontejnerů na webu Azure Portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky, přejděte na **myResourceGroup** skupinu prostředků na portálu. Po načtení skupiny prostředků klikněte na **odstranit skupinu prostředků** došlo k odebrání skupiny prostředků, do registru kontejneru a Image kontejneru uložena.

![Odstranění skupiny prostředků na webu Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili službu Azure Container Registry pomocí webu Azure portal, nahráli image kontejneru a dali a spustili image z registru. Pokračujte na Azure Container Registry kurzy pro najdete podrobnější přehled služby ACR.

> [!div class="nextstepaction"]
> [Kurzy služby Azure Container Registry][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
