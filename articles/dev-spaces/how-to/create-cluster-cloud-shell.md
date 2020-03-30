---
title: Vytvoření clusteru Kubernetes s povoleným Azure Dev Spaces – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Zjistěte, jak rychle vytvořit cluster Kubernetes, který je povolen pro Azure Dev Spaces přímo z vašeho prohlížeče, aniž byste museli cokoli instalovat.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605300"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes s povoleným Azure Dev Spaces s Azure Cloud Shell

[Azure Cloud Shell](/azure/cloud-shell) můžete použít k vytvoření clusteru služby Azure Kubernetes pomocí tlačítka Try **It** z této stránky. Pokud nejste přihlášení, postupujte podle pokynů pro přihlášení pomocí účtu Azure a po zobrazení zadejte příkazy na výzvu Azure Cloud Shell.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nejprve vytvořte skupinu prostředků v [oblasti, která podporuje Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Vytvoření clusteru bude trvat několik minut.  Po dokončení se výstup zobrazí ve formátu JSON. Vyhledejte `provisioningState` a ověřte, zda je . `Succeeded`

## <a name="next-steps"></a>Další kroky

Odkazy na úplné kurzy najdete [v tématu Azure Dev Spaces.](/azure/dev-spaces/)

> [!IMPORTANT]
> Mnoho rychlých startů a kurzů Azure Dev Spaces používá k provádění operací příkazcli Azure Dev Spaces. V prostředí Azure Cloud Shell nelze nainstalovat příkaz příkazpříkaz ový příkaz Azure Dev Spaces.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service