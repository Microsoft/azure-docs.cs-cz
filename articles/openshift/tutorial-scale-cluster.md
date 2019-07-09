---
title: Kurz – škálování clusteru Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Zjistěte, jak škálovat cluster Microsoft Azure Red Hat OpenShift pomocí Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: deb136a70c24cb7bd1b6c60505cc6ab0376a7b02
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672435"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Kurz: Škálování clusteru Azure Red Hat OpenShift

Tento kurz je druhá část série. Budete se dozvíte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového řádku Azure, škálovat ji a pak jej odstranit a vyčištění prostředků.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Škálování clusteru Red Hat OpenShift

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Škálování clusteru Azure Red Hat OpenShift
> * [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Vytvoření clusteru pomocí následujícího [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlásit se k Azure

Pokud používáte Azure CLI místně, spusťte `az login` pro přihlášení k Azure.

```bash
az login
```

Pokud máte přístup k několika předplatným, spusťte `az account set -s {subscription ID}` nahrazení `{subscription ID}` s předplatným, které chcete použít.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Krok 2: Škálování clusteru se další uzly

Z Bash terminálu nastavte na název vašeho clusteru název_clusteru proměnné:

```bash
CLUSTER_NAME=yourclustername
```

Teď můžeme škálovat cluster tak, aby pět uzlů pomocí Azure CLI:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Po několika minutách `az openshift scale` se úspěšně dokončit a vrátit dokument JSON obsahující podrobnosti o přizpůsobený cluster.

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Škálování clusteru Azure Red Hat OpenShift

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)