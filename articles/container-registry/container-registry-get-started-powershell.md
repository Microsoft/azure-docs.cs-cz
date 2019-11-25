---
title: Quickstart - Create registry - Powershell
description: Quickly learn to create a private Docker registry in Azure Container Registry with PowerShell
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 872b2a29444e5278db34ce44741e2ca90d885702
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456377"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Quickstart: Create a private container registry using Azure PowerShell

Azure Container Registry je spravovaná privátní služba registru kontejneru Dockeru, která se používá k vytváření, ukládání a obsluze imagí kontejnerů Dockeru. V tomto rychlém startu se naučíte vytvořit registr kontejneru Azure pomocí PowerShellu. Then, use Docker commands to push a container image into the registry, and finally pull and run the image from your registry.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

This quickstart requires Azure PowerShell module. Svou nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Je také nutné mít Docker nainstalovaný místně. Docker provides packages for [macOS][docker-mac], [Windows][docker-windows], and [Linux][docker-linux] systems.

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Sign in to your Azure subscription with the [Connect-AzAccount][Connect-AzAccount] command, and follow the on-screen directions.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Once you're authenticated with Azure, create a resource group with [New-AzResourceGroup][New-AzResourceGroup]. Skupina prostředků je logický kontejner, ve kterém nasazujete a spravujete prostředky Azure.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Vytvoření registru kontejneru

Next, create a container registry in your new resource group with the [New-AzContainerRegistry][New-AzContainerRegistry] command.

Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Následující příklad vytvoří registr s názvem myContainerRegistry007. Nahraďte v následujícím příkazu název *myContainerRegistry007* a pak spuštěním tohoto příkazu vytvořte registr:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

In this quickstart you create a *Basic* registry, which is a cost-optimized option for developers learning about Azure Container Registry. For details on available service tiers, see [Container registry SKUs][container-registry-skus].

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k registru. In production scenarios you should use an individual identity or service principal for container registry access, but to keep this quickstart brief, enable the admin user on your registry with the [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] command:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Next, run [docker login][docker-login] to log in:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Once you're done working with the resources you created in this quickstart, use the [Remove-AzResourceGroup][Remove-AzResourceGroup] command to remove the resource group, the container registry, and the container images stored there:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

In this quickstart, you created an Azure Container Registry with Azure PowerShell, pushed a container image, and pulled and ran the image from the registry. Continue to the Azure Container Registry tutorials for a deeper look at ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry tutorials][container-registry-tutorial-quick-task]

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
