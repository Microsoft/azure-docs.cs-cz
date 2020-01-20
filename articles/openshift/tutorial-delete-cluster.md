---
title: Kurz – odstranění clusteru Azure Red Hat OpenShift
description: V tomto kurzu se dozvíte, jak odstranit cluster Azure Red Hat OpenShift pomocí Azure CLI.
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278775"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Kurz: odstranění clusteru Azure Red Hat OpenShift

Toto je konec tohoto kurzu. Až dokončíte testování ukázkového clusteru, tady je postup, jak ho odstranit a k němu přidružené prostředky, abyste se vám neúčtovali za to, co nepoužíváte.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Odstranění clusteru Azure Red Hat OpenShift

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * Odstranění clusteru Azure Red Hat OpenShift

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pomocí kurzu [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) vytvořte cluster.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: přihlášení k Azure

Pokud používáte Azure CLI místně, spusťte `az login`, abyste se přihlásili do Azure.

```bash
az login
```

Pokud máte přístup k několika předplatným, spusťte `az account set -s {subscription ID}` nahrazující `{subscription ID}` s předplatným, které chcete použít.

## <a name="step-2-delete-the-cluster"></a>Krok 2: odstranění clusteru

Otevřete terminál bash a nastavte proměnnou CLUSTER_NAME na název vašeho clusteru:

```bash
CLUSTER_NAME=yourclustername
```

Nyní odstraňte cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Zobrazí se dotaz, zda chcete cluster odstranit. Po potvrzení u `y`bude trvat několik minut, než se cluster odstraní. Po dokončení příkazu se odstraní celá skupina prostředků a všechny prostředky v ní obsažené, včetně clusteru.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Odstranění clusteru pomocí Azure Portal

Případně můžete odstranit přidruženou skupinu prostředků clusteru prostřednictvím online Azure Portal. Název skupiny prostředků je stejný jako název clusteru.

V současné době je `Microsoft.ContainerService/openShiftManagedClusters` prostředek, který se vytvoří při vytváření clusteru, skrytý v Azure Portal. V zobrazení `Resource group` zkontrolujte `Show hidden types` a zobrazte skupinu prostředků.

![Snímek obrazovky se zaškrtávacím políčkem skrytého typu](./media/aro-portal-hidden-type.png)

Odstraněním skupiny prostředků se odstraní všechny související prostředky, které se vytvoří při vytváření clusteru Azure Red Hat OpenShift.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Odstranění clusteru Azure Red Hat OpenShift

Další informace o používání OpenShift s oficiální [dokumentací Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)
