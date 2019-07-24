---
title: Rychlý Start – vytvoření privátního registru Docker v Azure – Azure Portal
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí webu Azure Portal.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c9e8c7fe4d32a44e8c0831154f02eda1f82aaff3
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309474"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Rychlý start: Vytvoření soukromého registru kontejnerů pomocí Azure Portal

Registr kontejnerů Azure je privátním registrem Dockeru v Azure, kde můžete ukládat a spravovat privátní image kontejnerů Dockeru. V tomto rychlém startu vytvoříte registr kontejnerů pomocí webu Azure Portal. Pak pomocí příkazů Docker nahrajte image kontejneru do registru a nakonec si vydejte a spusťte image z registru.

Pokud se chcete přihlásit k registru, abyste mohli pracovat s imagemi kontejnerů, tento rychlý Start vyžaduje, abyste spustili Azure CLI (doporučuje se verze 2.0.55 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker poskytuje balíčky, které snadno konfigurují Docker na jakémkoli [Macu][docker-mac], [Windows][docker-windows]nebo systému [Linux][Docker-Linux] .

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-01]

Zadejte odpovídající hodnoty pro **Název registru** a **Skupina prostředků**. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Pro účely tohoto rychlého startu vytvořte novou skupinu prostředků `myResourceGroup` v umístění `West US` a jako **skladovou položku** vyberte Basic. Vyberte **Vytvořit** a nasaďte instanci služby ACR.

![Vytvoření registru kontejneru v Azure Portal][qs-portal-03]

V tomto rychlém startu vytvoříte *základní* registr, což je výhodná možnost pro vývojáře, kteří se naučí o Azure Container Registry. Podrobnosti k dostupným úrovním služeb najdete v tématu [SKU služby Container Registry][container-registry-skus].

Po zobrazení zprávy o **úspěšném nasazení** vyberte v portálu registr kontejnerů. 

![Přehled registru kontejnerů v Azure Portal][qs-portal-05]

Poznamenejte si hodnotu **přihlašovacího serveru**. Tuto hodnotu použijete v následujících krocích při práci s registrem pomocí rozhraní příkazového řádku Azure CLI a Docker.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k instanci služby ACR. Otevřete příkazové prostředí v operačním systému a použijte příkaz [AZ ACR Login][az-acr-login] v rozhraní příkazového řádku Azure CLI.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné). 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Pokud chcete zobrazit seznam imagí v registru, přejděte na portálu na svůj registr a vyberte úložiště a pak vyberte úložiště, pomocí `docker push`kterého jste vytvořili.

V tomto příkladu vybereme úložiště **Hello-World** a v části `v1` **značky**se zobrazí obrázek označený příznakem.

![Výpis imagí kontejneru v Azure Portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete prostředky vyčistit, přejděte do skupiny prostředků **myResourceGroup** na portálu. Po načtení skupiny prostředků klikněte na **Odstranit skupinu prostředků** a odstraňte skupinu prostředků, registr kontejnerů a image kontejnerů, které jsou tam uložené.

![Odstranění skupiny prostředků na webu Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Azure Container Registry s Azure Portal, nahráli jste image kontejneru a z registru jste vyžádali a spustili image. Pokračujte Azure Container Registry výukové kurzy, kde najdete hlubší přehled na ACR.

> [!div class="nextstepaction"]
> [Kurzy Azure Container Registry][container-registry-tutorial-quick-task]

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
