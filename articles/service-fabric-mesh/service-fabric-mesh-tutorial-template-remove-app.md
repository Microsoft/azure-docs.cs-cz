---
title: Kurz – odebrání aplikace běžící v Azure Service Fabric sítě
description: V tomto kurzu se naučíte odebrat aplikaci spuštěnou ve službě Service Fabric Mesh a odstranit prostředky.
author: dkkapur
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 4780f81d23f0183837d2aafb9a8e5e2c41faa1cf
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "75351797"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Kurz: Odebrání aplikace a prostředků

Tento kurz je čtvrtou částí série. Dozvíte se, jak odebrat spuštěnou aplikaci, která se [předtím nasadila do služby Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). 

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Odstranění aplikace spuštěné ve službě Service Fabric Mesh
> * Odstranění prostředků aplikace

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Nasadit aplikaci do služby Service Fabric Mesh pomocí šablony](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Škálování aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Upgrade aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Odebrání aplikace

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Otevřete [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) nebo si [nainstalujte Azure CLI a Service Fabric Mesh CLI místně](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Odstranění skupiny prostředků a všech prostředků

Pokud už vytvořené prostředky nepotřebujete, všechny je odstraňte. Dříve jste [vytvořili novou skupinu prostředků](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) pro hostování instance služby Azure Container Registry a prostředků aplikace Service Fabric Mesh.  Tuto skupinu prostředků můžete odstranit a tím se odstraní i všechny přidružené prostředky.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Odstranění jednotlivých prostředků
Instanci služby ACR, aplikaci Service Fabric Mesh a síťové prostředky můžete odstranit také jednotlivě.

Odstranění instance služby ACR:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Odstranění aplikace Service Fabric Mesh:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Odstranění sítě:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Odstranění aplikace spuštěné ve službě Service Fabric Mesh
> * Odstranění prostředků aplikace