---
title: Vytvoření clusteru Kubernetes s povoleným Azure Dev Spaces – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Zjistěte, jak rychle vytvořit cluster Kubernetes s povoleným Azure Dev Spaces přímo z prohlížeče, aniž byste museli instalovat cokoli.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325755"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes s povoleným Azure Dev Spaces s Azure Cloud Shell

Pomocí [Azure Cloud Shell](/azure/cloud-shell) můžete vytvořit cluster služby Azure Kubernetes pomocí tlačítka **vyzkoušet** na této stránce. Pokud nejste přihlášení, postupujte podle pokynů pro přihlášení pomocí účtu Azure a pak zadejte příkazy na Azure Cloud Shell výzvy, když se objeví.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nejdřív vytvořte skupinu prostředků v [oblasti, která podporuje Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Vytvoření clusteru bude trvat několik minut.  Po dokončení se výstup zobrazí ve formátu JSON. Vyhledejte `provisioningState` a ověřte `Succeeded`.

## <a name="next-steps"></a>Další kroky

Odkazy na úplné kurzy najdete v tématu [Azure dev Spaces](/azure/dev-spaces/) .

> [!IMPORTANT]
> Mnohé z Azure Dev Spaces rychlých startů a kurzů používají k provádění operací Azure Dev Spaces CLI. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations