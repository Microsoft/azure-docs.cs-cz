---
title: Jak vytvořit cluster Kubernetes povolené prostory vývoj Azure pomocí Azure Cloud Shell | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 10/04/2018
ms.topic: article
description: Zjistěte, jak rychle vytvořit cluster Kubernetes povolené prostory vývoj Azure přímo z prohlížeče bez nutnosti instalace cokoli.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: f10a84a602ce152d5c428525aa50f678b50c8b41
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48871503"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes pomocí Azure Cloud Shell

Můžete použít [Azure Cloud Shell](/azure/cloud-shell) k vytvoření clusteru prostory vývoj Azure s použitím **vyzkoušet** tlačítko z této stránky. Pokud nejste přihlášení, postupujte podle pokynů se přihlásit pomocí účtu Azure a pak zadejte příkazy příkazového řádku Azure Cloud Shell, když se objeví.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nejprve vytvořte skupinu prostředků. Použijte jeden z aktuálně podporovaných oblastí (EastUS, CentralUS, WestUS2, WestEurope, CanadaCentral nebo CanadaEast).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Vytvoření clusteru Kubernetes pomocí následujícího příkazu:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.2 --enable-addons http_application_routing
```

Trvá několik minut pro vytvoření clusteru.  Po dokončení se zobrazí výstup ve formátu JSON. Vyhledejte `provisioningState` a ověřte ho má `Succeeded`.

## <a name="next-steps"></a>Další postup

Naleznete v tématu [Azure Dev prostory](/azure/dev-spaces/) odkazy na úplné kurzy.