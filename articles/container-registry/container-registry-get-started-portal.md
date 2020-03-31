---
title: Úvodní příručka – vytvoření registru na portálu
description: Rychle se naučte vytvářet privátní registr Dockeru v registru kontejnerů Azure pomocí portálu Azure.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 6fe6358655f50ab783b4017efa8ee1db351cd018
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409234"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Úvodní příručka: Vytvoření registru privátního kontejneru pomocí portálu Azure

Registr kontejnerů Azure je soukromý registr Dockeru v Azure, kde můžete ukládat a spravovat privátní ibi kontejnerů Dockeru a související artefakty. V tomto rychlém startu vytvoříte registr kontejnerů pomocí webu Azure Portal. Potom pomocí příkazů Dockeru vysuňte bitovou kopii kontejneru do registru a nakonec ji vyprovokejte a spusťte z registru.

Chcete-li se přihlásit do registru pro práci s imagí kontejneru, tento rychlý start vyžaduje, abyste spustili azure CLI (verze 2.0.55 nebo novější doporučeno). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-mac] nebo systému [Windows][docker-windows] či [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Vyberte **možnost Vytvořit** > **registr kontejnerů****prostředků** > .

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-01]

Na kartě **Základy** zadejte hodnoty pro **skupinu prostředků** a **název registru**. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Pro účely tohoto rychlého startu vytvořte novou skupinu prostředků `myResourceGroup` v umístění `West US` a jako **skladovou položku** vyberte Basic. 

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-03]

Přijměte výchozí hodnoty pro zbývající nastavení. Pak vyberte **Zkontrolovat + vytvořit**. Po kontrole nastavení vyberte **Vytvořit**.

V tomto rychlém startu vytvoříte *základní* registr, což je nákladově optimalizovaná možnost pro vývojáře, kteří se učí o registru kontejnerů Azure. Podrobnosti o dostupných úrovních služeb naleznete [v tématu Kontejner registru SKU][container-registry-skus].

Po **oznámení o úspěšném nasazení** vyberte registr kontejnerů na portálu. 

![Přehled registru kontejnerů na webu Azure Portal][qs-portal-05]

Poznamenejte si hodnotu **přihlašovacího serveru**. Tuto hodnotu použijete v následujících krocích při nabízení a vytahovat image s Docker.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k instanci služby ACR. Otevřete příkazové prostředí v operačním systému a použijte příkaz [az acr přihlášení][az-acr-login] v azure CLI. (Při přihlašování zadejte pouze název registru. Nezahrnejte příponu azurecr.io.)

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné). 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Chcete-li zobrazit seznam bitových kopií v registru, přejděte na portál do registru `docker push`a vyberte **možnost Repozitáře**a vyberte úložiště, které jste vytvořili.

V tomto příkladu vybereme úložiště **hello-world** a `v1`vidíme tagovaný obrázek v části **Tagy**.

![Seznam ibi kontejnerů na webu Azure Portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li vyčistit prostředky, přejděte do skupiny prostředků **myResourceGroup** na portálu. Po načtení skupiny prostředků klikněte na **Odstranit skupinu prostředků** odebrat skupinu prostředků, registr kontejneru a image kontejneru uložené v něm.

![Odstranění skupiny prostředků na webu Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili registr kontejnerů Azure s portálem Azure, posunuli image kontejneru a stáhli a spustili image z registru. Pokračujte do kurzů Azure Container Registry pro hlubší pohled na ACR.

> [!div class="nextstepaction"]
> [Kurzy registru kontejnerů Azure][container-registry-tutorial-quick-task]

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
