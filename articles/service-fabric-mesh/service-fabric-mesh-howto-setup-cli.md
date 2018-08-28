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
ms.openlocfilehash: c0e2aefe1222263b169e21490da079b165a57321
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42108474"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh
Rozhraní příkazového řádku služby Service Fabric Mesh je potřeba k nasazení a správě prostředků v Service Fabric Mesh. 

Ve verzi Preview je rozhraní příkazového řádku služby Azure Service Fabric Mesh jako rozšíření Azure CLI. Můžete ho nainstalovat do Azure Cloud Shellu nebo do místní instalace Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte nainstalovat verzi Azure CLI 2.0.43 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete rozhraní příkazového řádku nainstalovat nebo upgradovat na nejnovější verzi, přečtěte si téma [Instalace Azure CLI 2.0][azure-cli-install].

Nainstalujte modul rozšíření Azure Service Fabric Mesh CLI pomocí následujícího příkazu. 

```azurecli-interactive
az extension add --name mesh
```

Pokud chcete aktualizovat stávající modul Azure Service Fabric Mesh CLI, spusťte následující příkaz.

```azurecli-interactive
az extension update --name mesh
```

Můžete také nastavit [vývojové prostředí ve Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli