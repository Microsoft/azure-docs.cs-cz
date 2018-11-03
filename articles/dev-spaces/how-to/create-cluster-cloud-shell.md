---
title: Jak vytvořit cluster Kubernetes povolené prostory vývoj Azure pomocí Azure Cloud Shell | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 10/04/2018
ms.topic: article
description: Zjistěte, jak rychle vytvořit cluster Kubernetes povolené prostory vývoj Azure přímo z prohlížeče bez nutnosti instalace cokoli.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 47c467e020a7a9253daa636352352d9a57dddf28
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978143"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes pomocí Azure Cloud Shell

Můžete použít [Azure Cloud Shell](/azure/cloud-shell) k vytvoření clusteru prostory vývoj Azure s použitím **vyzkoušet** tlačítko z této stránky. Pokud nejste přihlášení, postupujte podle pokynů se přihlásit pomocí účtu Azure a pak zadejte příkazy příkazového řádku Azure Cloud Shell, když se objeví.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nejprve vytvořte skupinu prostředků. Použijte jeden z aktuálně podporované oblasti (EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral nebo CanadaEast).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.3 --enable-addons http_application_routing
```

Vytvoření clusteru bude trvat několik minut.  Po dokončení se zobrazí výstup ve formátu JSON. Vyhledejte `provisioningState` a ověřte ho má `Succeeded`.

## <a name="next-steps"></a>Další postup

Naleznete v tématu [Azure Dev prostory](/azure/dev-spaces/) odkazy na úplné kurzy.
