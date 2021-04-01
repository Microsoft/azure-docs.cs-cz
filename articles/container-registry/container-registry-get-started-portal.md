---
title: Rychlý Start – vytvoření registru na portálu
description: Rychle se naučíte, jak vytvořit privátní službu Azure Container Registry pomocí Azure Portal.
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: ff74232edf5c96f091082f8486c44fed6125b0b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97825848"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Rychlý Start: vytvoření služby Azure Container Registry pomocí Azure Portal

Registr kontejnerů Azure je privátní registr Docker v Azure, kde můžete ukládat a spravovat soukromé image kontejneru Docker a související artefakty. V tomto rychlém startu vytvoříte registr kontejnerů pomocí webu Azure Portal. Pak pomocí příkazů Docker nahrajte image kontejneru do registru a nakonec si vydejte a spusťte image z registru.

Pokud se chcete přihlásit k registru, abyste mohli pracovat s imagemi kontejnerů, tento rychlý Start vyžaduje, abyste spustili Azure CLI (doporučuje se verze 2.0.55 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-mac] nebo systému [Windows][docker-windows] či [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

Vyberte **vytvořit**  >  **kontejnery** prostředků  >  **Container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Přejít na registr kontejnerů na portálu":::

Na kartě **základy** zadejte hodnoty pro **skupinu prostředků** a **název registru**. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Pro účely tohoto rychlého startu vytvořte novou skupinu prostředků `myResourceGroup` v umístění `West US` a jako **skladovou položku** vyberte Basic.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Vytvoření registru kontejnerů na portálu":::

Přijměte výchozí hodnoty pro zbývající nastavení. Pak vyberte **zkontrolovat + vytvořit**. Po zkontrolování nastavení vyberte **vytvořit**.

V tomto rychlém startu vytvoříte *základní* registr, což je výhodná možnost pro vývojáře, kteří se naučí o Azure Container Registry. Podrobnosti o dostupných úrovních služby (SKU) najdete v tématu [úrovně služby registru kontejnerů][container-registry-skus].

Po zobrazení zprávy o **úspěšném nasazení** vyberte v portálu registr kontejnerů. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Přehled registru kontejnerů na portálu":::

Poznamenejte si název registru a hodnotu **přihlašovacího serveru**. Tyto hodnoty použijete v následujících krocích při doručování a vyžádání imagí pomocí Docker.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před vložením a stažením imagí kontejneru se musíte přihlásit k instanci registru. [Přihlaste se k Azure CLI][get-started-with-azure-cli] na svém místním počítači a spusťte příkaz [AZ ACR Login][az-acr-login] . Při přihlašování pomocí Azure CLI zadejte jenom název registru. Nepoužívejte název přihlašovacího serveru, který zahrnuje příponu domény, například `azurecr.io` .

```azurecli
az acr login --name <registry-name>
```

Příklad:

```azurecli
az acr login --name mycontainerregistry
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné). 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Pokud chcete zobrazit seznam imagí v registru, přejděte na portálu na svůj registr **a vyberte úložiště a pak** vyberte úložiště  **Hello** , které jste vytvořili pomocí `docker push` .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Výpis imagí kontejneru na portálu":::

Výběrem úložiště **Hello-World** se v `v1` části **značky** zobrazí obrázek označený příznakem.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete prostředky vyčistit, přejděte do skupiny prostředků **myResourceGroup** na portálu. Po načtení skupiny prostředků klikněte na **Odstranit skupinu prostředků** a odeberte skupinu prostředků, registr kontejnerů a image kontejnerů, které jsou tam uložené.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Odstranit skupinu prostředků na portálu":::


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Azure Container Registry s Azure Portal, nahráli jste image kontejneru a z registru jste vyžádali a spustili image. Pokračujte Azure Container Registry výukové kurzy, kde najdete hlubší přehled na ACR.

> [!div class="nextstepaction"]
> [Kurzy Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Kurzy k Azure Container Registry úlohám][container-registry-tutorial-quick-task]

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
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
