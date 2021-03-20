---
title: Vytvoření clusteru Kubernetes s povoleným Azure Dev Spaces – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Zjistěte, jak rychle vytvořit cluster Kubernetes s povoleným Azure Dev Spaces přímo z prohlížeče, aniž byste museli instalovat cokoli.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.openlocfilehash: ce73f46a2451dfa87751d90f9cd6b31d5c80683f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91963546"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes s povoleným Azure Dev Spaces s Azure Cloud Shell

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Pomocí [Azure Cloud Shell](/azure/cloud-shell) můžete vytvořit cluster služby Azure Kubernetes pomocí tlačítka **vyzkoušet** na této stránce. Pokud nejste přihlášení, postupujte podle pokynů pro přihlášení pomocí účtu Azure a pak zadejte příkazy na Azure Cloud Shell výzvy, když se objeví.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nejdřív vytvořte skupinu prostředků v [oblasti, která podporuje Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Vytvoření clusteru bude trvat několik minut.  Po dokončení se výstup zobrazí ve formátu JSON. Vyhledejte `provisioningState` a ověřte, zda je `Succeeded` .

## <a name="next-steps"></a>Další kroky

Odkazy na úplné kurzy najdete v tématu [Azure dev Spaces](../index.yml) .

> [!IMPORTANT]
> Mnohé z Azure Dev Spaces rychlých startů a kurzů používají k provádění operací Azure Dev Spaces CLI. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
