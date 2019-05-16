---
title: Jak vytvořit cluster Kubernetes povolené prostory vývoj Azure pomocí Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Zjistěte, jak rychle vytvořit cluster Kubernetes povolené prostory vývoj Azure přímo z prohlížeče bez nutnosti instalace cokoli.
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s
ms.openlocfilehash: cf518fb0062a44619894059a1b7369fc92ba4f5d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597184"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes pomocí Azure Cloud Shellu

Můžete použít [Azure Cloud Shell](/azure/cloud-shell) k vytvoření clusteru prostory vývoj Azure s použitím **vyzkoušet** tlačítko z této stránky. Pokud nejste přihlášení, postupujte podle pokynů se přihlásit pomocí účtu Azure a pak zadejte příkazy příkazového řádku Azure Cloud Shell, když se objeví.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nejprve vytvořte skupinu prostředků [oblast, která podporuje Azure Dev prostory](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Vytvoření clusteru bude trvat několik minut.  Po dokončení se zobrazí výstup ve formátu JSON. Vyhledejte `provisioningState` a ověřte ho má `Succeeded`.

## <a name="next-steps"></a>Další postup

Naleznete v tématu [Azure Dev prostory](/azure/dev-spaces/) odkazy na úplné kurzy.
