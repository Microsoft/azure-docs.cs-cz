---
title: Úvodní příručka – Vytvoření registru – Powershell
description: Rychle se naučíte vytvářet privátní registr Dockeru v registru kontejnerů Azure pomocí PowerShellu
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 872b2a29444e5278db34ce44741e2ca90d885702
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74456377"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Úvodní příručka: Vytvoření registru privátního kontejneru pomocí Azure PowerShellu

Azure Container Registry je spravovaná privátní služba registru kontejneru Dockeru, která se používá k vytváření, ukládání a obsluze imagí kontejnerů Dockeru. V tomto rychlém startu se naučíte vytvořit registr kontejneru Azure pomocí PowerShellu. Potom pomocí příkazů Dockeru vysuňte bitovou kopii kontejneru do registru a nakonec ji vyprovokejte a spusťte z registru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento rychlý start vyžaduje modul Azure PowerShell. Svou nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Je také nutné mít Docker nainstalovaný místně. Docker poskytuje balíčky pro systémy [macOS][docker-mac], [Windows][docker-windows] a [Linux][docker-linux].

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount][Connect-AzAccount] a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Po ověření pomocí Azure vytvořte skupinu prostředků s [New-AzResourceGroup][New-AzResourceGroup]. Skupina prostředků je logický kontejner, ve kterém nasazujete a spravujete prostředky Azure.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Vytvoření registru kontejneru

Dále vytvořte registr kontejnerů v nové skupině prostředků pomocí příkazu [New-AzContainerRegistry.][New-AzContainerRegistry]

Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Následující příklad vytvoří registr s názvem myContainerRegistry007. Nahraďte v následujícím příkazu název *myContainerRegistry007* a pak spuštěním tohoto příkazu vytvořte registr:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

V tomto rychlém startu vytvoříte *základní* registr, což je nákladově optimalizovaná možnost pro vývojáře, kteří se učí o registru kontejnerů Azure. Podrobnosti o dostupných úrovních služeb naleznete [v tématu Kontejner registru SKU][container-registry-skus].

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k registru. V produkčních scénářích byste měli použít individuální identitu nebo instanční objekt pro přístup k registru kontejneru, ale chcete-li zachovat tento rychlý start stručný, povolte uživatele správce v registru pomocí příkazu [Get-AzContainerRegistryCredential:][Get-AzContainerRegistryCredential]

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

Po dokončení práce s prostředky, které jste vytvořili v tomto rychlém startu, použijte příkaz [Odebrat AzResourceGroup][Remove-AzResourceGroup] k odebrání skupiny prostředků, registru kontejneru a bitových kopií kontejnerů, které jsou tam uloženy:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili registr kontejnerů Azure s Azure PowerShell, posunuli image kontejneru a stáhli a spustili image z registru. Pokračujte do kurzů Azure Container Registry pro hlubší pohled na ACR.

> [!div class="nextstepaction"]
> [Kurzy registru kontejnerů Azure][container-registry-tutorial-quick-task]

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
