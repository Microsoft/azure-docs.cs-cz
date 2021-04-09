---
title: Rychlý Start – vytvoření registru – PowerShell
description: Rychle se naučíte, jak vytvořit privátní registr Docker v Azure Container Registry pomocí prostředí PowerShell.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc, devx-track-azurepowershell
ms.openlocfilehash: 91d4209ccf558bf7c8038d8a753ec038428bc484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019994"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure PowerShell

Azure Container Registry je spravovaná privátní služba registru kontejneru Dockeru, která se používá k vytváření, ukládání a obsluze imagí kontejnerů Dockeru. V tomto rychlém startu se naučíte vytvořit registr kontejneru Azure pomocí PowerShellu. Pak pomocí příkazů Docker nahrajte image kontejneru do registru a nakonec si vydejte a spusťte image z registru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento rychlý Start vyžaduje modul Azure PowerShell. Svou nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Je také nutné mít Docker nainstalovaný místně. Docker poskytuje balíčky pro systémy [macOS][docker-mac], [Windows][docker-windows] a [Linux][docker-linux].

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount][Connect-AzAccount] a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Až budete s Azure ověřeni, vytvořte skupinu prostředků pomocí [New-AzResourceGroup][New-AzResourceGroup]. Skupina prostředků je logický kontejner, ve kterém nasazujete a spravujete prostředky Azure.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Vytvoření registru kontejneru

V dalším kroku vytvořte v nové skupině prostředků registr kontejneru pomocí příkazu [New-AzContainerRegistry][New-AzContainerRegistry] .

Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Následující příklad vytvoří registr s názvem myContainerRegistry007. Nahraďte v následujícím příkazu název *myContainerRegistry007* a pak spuštěním tohoto příkazu vytvořte registr:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

V tomto rychlém startu vytvoříte *základní* registr, což je výhodná možnost pro vývojáře, kteří se naučí o Azure Container Registry. Podrobnosti o dostupných úrovních služby najdete v tématu [úrovně služby registru kontejnerů][container-registry-skus].

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k registru. V produkčních scénářích byste měli použít jednotlivou identitu nebo instanční objekt pro přístup k registru kontejnerů, ale pokud chcete, aby byl tento rychlý Start krátký, povolte v registru uživatele správce pomocí příkazu [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] :

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Potom k přihlášení spusťte [docker login][docker-login]:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete pracovat s prostředky, které jste vytvořili v tomto rychlém startu, odeberte pomocí příkazu [Remove-AzResourceGroup][Remove-AzResourceGroup] skupinu prostředků, registr kontejnerů a image kontejnerů, které jsou tam uložené:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Azure Container Registry s Azure PowerShell, nahráli jste image kontejneru a z registru jste vyžádali a spustili image. Pokračujte Azure Container Registry výukové kurzy, kde najdete hlubší přehled na ACR.

> [!div class="nextstepaction"]
> [Kurzy Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Kurzy k Azure Container Registry úlohám][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
