---
title: Kurz – škálování clusteru Azure Red Hat OpenShift
description: Zjistěte, jak škálovat cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového příkazu Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c6334aa20b543dfbf87fedcfe45d54bbcf7a219a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477013"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Kurz: Škálování clusteru Azure Red Hat OpenShift

Tento kurz je druhá část série. Dozvíte se, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí rozhraní příkazového příkazu Azure, škálovat ho a pak ho odstranit, abyste vyčistili prostředky.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Změna velikosti clusteru Red Hat OpenShift

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Škálování clusteru Azure Red Hat OpenShift
> * [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Vytvořte cluster podle kurzu [vytvořit azure red hat openshift clusteru.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlášení do Azure

Pokud používáte azure cli místně, `az login` spusťte přihlášení k Azure.

```azurecli
az login
```

Pokud máte přístup k více `az account set -s {subscription ID}` předplatných, spusťte nahrazení `{subscription ID}` předplatného, které chcete použít.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Krok 2: Škálování clusteru s dalšími uzly

Z terminálu Bash nastavte proměnnou CLUSTER_NAME na název clusteru:

```bash
CLUSTER_NAME=yourclustername
```

Nyní pojďme škálovat cluster u pěti uzlů pomocí Azure CLI:

```azurecli
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Po několika minutách `az openshift scale` bude úspěšně dokončena a vrátí dokument JSON obsahující podrobnosti clusteru s měřítkem.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Škálování clusteru Azure Red Hat OpenShift

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Odstranění clusteru Azure Red Hat OpenShift](tutorial-delete-cluster.md)
