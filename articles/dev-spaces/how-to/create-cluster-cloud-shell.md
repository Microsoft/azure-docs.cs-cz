---
title: Postup vytvoření clusteru Kubernetes s povoleným Azure Dev Spaces pomocí Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Zjistěte, jak rychle vytvořit cluster Kubernetes s povoleným Azure Dev Spaces přímo z prohlížeče, aniž byste museli instalovat cokoli.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.openlocfilehash: dbdc9226e417b3142284386ae3586819cda802d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280101"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes pomocí Azure Cloud Shellu

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