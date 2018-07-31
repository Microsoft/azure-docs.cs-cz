---
title: Nastavení rozhraní příkazového řádku služby Azure Service Fabric Mesh | Microsoft Docs
description: Naučte se nastavit rozhraní příkazového řádku služby Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: cf899e236b7fa21a90a0784e1e2b4fce0feae055
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213431"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh
Rozhraní příkazového řádku služby Service Fabric Mesh je potřeba k nasazení a správě prostředků v Service Fabric Mesh. 

Ve verzi Preview je rozhraní příkazového řádku služby Azure Service Fabric Mesh jako rozšíření Azure CLI. Můžete ho nainstalovat do Azure Cloud Shellu nebo do místní instalace Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte nainstalovat verzi Azure CLI 2.0.35 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete rozhraní příkazového řádku nainstalovat nebo upgradovat na nejnovější verzi, přečtěte si téma [Instalace Azure CLI 2.0][azure-cli-install].

Odeberte všechny předchozí instalace modulu Azure Service Fabric Mesh CLI.

```azurecli-interactive
az extension remove --name mesh
```

Nainstalujte modul rozšíření Azure Service Fabric Mesh CLI pomocí následujícího příkazu. 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

Můžete také nastavit [vývojové prostředí ve Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli