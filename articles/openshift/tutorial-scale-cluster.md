---
title: Kurz – škálování clusteru Azure Red Hat OpenShift
description: Naučte se škálovat Microsoft Azure clusteru Red Hat OpenShift pomocí Azure CLI.
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: bf9172f0c84834c951446520ff0bfcc3ef756c9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278311"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Kurz: škálování clusteru Azure Red Hat OpenShift

Tento kurz je druhá část série. Naučíte se, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí Azure CLI, škálovat ho a pak odstranit a vyčistit prostředky.

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

* Pomocí kurzu [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) vytvořte cluster.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: přihlášení k Azure

Pokud používáte Azure CLI místně, spusťte `az login`, abyste se přihlásili do Azure.

```bash
az login
```

Pokud máte přístup k několika předplatným, spusťte `az account set -s {subscription ID}` nahrazující `{subscription ID}` s předplatným, které chcete použít.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Krok 2: škálování clusteru pomocí dalších uzlů

V terminálu bash nastavte proměnnou CLUSTER_NAME na název vašeho clusteru:

```bash
CLUSTER_NAME=yourclustername
```

Teď budete moct cluster škálovat na pět uzlů pomocí Azure CLI:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Po několika minutách se `az openshift scale` úspěšně dokončí a vrátí dokument JSON obsahující podrobnosti o škálování clusteru.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Škálování clusteru Azure Red Hat OpenShift

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)
