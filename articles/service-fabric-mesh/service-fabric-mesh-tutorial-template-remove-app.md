---
title: Kurz – odebrání aplikace běžící v Azure Service Fabric sítě
description: V tomto kurzu se naučíte odebrat aplikaci spuštěnou ve službě Service Fabric Mesh a odstranit prostředky.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: d449343fd00ff958470a71ecb3a37d585d7ff8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626607"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Kurz: Odebrání aplikace a prostředků

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

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
