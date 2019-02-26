---
title: Rychlý start – vytvoření privátního registru Dockeru v Azure – PowerShell
description: Rychle se naučíte, jak vytvořit privátní registr kontejneru Dockeru v Azure pomocí PowerShellu.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 23a62a9d8d24bbf2223fb5295190ad9acb39faf0
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806567"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Rychlý start: Vytvoření kontejneru soukromého registru pomocí Azure Powershellu

Azure Container Registry je spravovaná privátní služba registru kontejneru Dockeru, která se používá k vytváření, ukládání a obsluze imagí kontejnerů Dockeru. V tomto rychlém startu se naučíte vytvořit registr kontejneru Azure pomocí PowerShellu. Potom použijte příkazy Dockeru pro nasdílení image kontejneru do registru a nakonec o přijetí změn a spusťte image z registru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento rychlý start vyžaduje modul Azure PowerShell. Svou nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Je také nutné mít Docker nainstalovaný místně. Docker poskytuje balíčky pro systémy [macOS][docker-mac], [Windows][docker-windows] a [Linux][docker-linux].

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k předplatnému Azure pomocí [připojit AzAccount] [ Connect-AzAccount] příkaz a postupujte podle na obrazovce pokynů.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Po ověření v Azure vytvořit skupinu prostředků pomocí [New-AzResourceGroup][New-AzResourceGroup]. Skupina prostředků je logický kontejner, ve kterém nasazujete a spravujete prostředky Azure.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Vytvoření registru kontejneru

Dále vytvořte registr kontejnerů v nové skupině prostředků s [New-AzContainerRegistry] [ New-AzContainerRegistry] příkazu.

Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Následující příklad vytvoří registr s názvem myContainerRegistry007. Nahraďte v následujícím příkazu název *myContainerRegistry007* a pak spuštěním tohoto příkazu vytvořte registr:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

V tomto rychlém startu vytvoříte *základní* registru, který je možnost náklady optimalizovaná pro vývojáře, další informace o službě Azure Container Registry. Podrobnosti o úrovních služeb najdete v tématu [skladové položky služby Container registry][container-registry-skus].

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k registru. V produkčních scénářích byste použít jednotlivé identity nebo instančního objektu pro přístup k registru kontejneru ale pro zkrácení tohoto rychlého startu povolte ve svém registru uživatele s rolí správce [Get-AzContainerRegistryCredential] [ Get-AzContainerRegistryCredential] příkaz:

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

Jakmile budete mít práci s prostředky vytvořené v rámci tohoto rychlého startu, použijte [odebrat AzResourceGroup] [ Remove-AzResourceGroup] příkazu odeberte skupinu prostředků, do registru kontejneru a imagí kontejneru ukládají se zde:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili službu Azure Container Registry pomocí Azure Powershellu, nahráli image kontejneru a vyžádá a spustili image z registru. Pokračujte na Azure Container Registry kurzy pro najdete podrobnější přehled služby ACR.

> [!div class="nextstepaction"]
> [Kurzy služby Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.profile/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
