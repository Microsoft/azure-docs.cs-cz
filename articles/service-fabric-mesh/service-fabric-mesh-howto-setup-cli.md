---
title: Nastavení rozhraní příkazového řádku služby Azure Service Fabric Mesh | Microsoft Docs
description: Naučte se nastavit rozhraní příkazového řádku služby Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c30f4b9de279f8c02b7f6bc7fa7d9765972899b1
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977427"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh
Rozhraní příkazového řádku služby Service Fabric Mesh je potřeba k nasazení a správě prostředků ve službě Service Fabric Mesh. 

Ve verzi Preview je rozhraní příkazového řádku služby Azure Service Fabric Mesh jako rozšíření Azure CLI. Můžete ho nainstalovat do Azure Cloud Shellu nebo do místní instalace Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Nainstalujte si místně Service Fabric Mesh CLI
Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte nainstalovat verzi Azure CLI 2.0.43 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete rozhraní příkazového řádku nainstalovat nebo upgradovat na nejnovější verzi, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

Nainstalujte modul rozšíření Azure Service Fabric Mesh CLI pomocí následujícího příkazu. 

```azurecli-interactive
az extension add --name mesh
```

Pokud chcete aktualizovat stávající modul Azure Service Fabric Mesh CLI, spusťte následující příkaz.

```azurecli-interactive
az extension update --name mesh
```
## <a name="next-steps"></a>Další kroky

Můžete také nastavit [vývojové prostředí ve Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Najděte odpovědi na [běžné dotazy a problémy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
