---
title: Kurz – odstranění clusteru služby Azure Red Hat OpenShift | Dokumentace Microsoftu
description: V tomto kurzu přečtěte si postup odstranění clusteru služby Azure Red Hat OpenShift pomocí Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 627acbfc1f3a460cbb94e322c43445a55fce1ffa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306171"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Kurz: Odstranění clusteru služby Azure Red Hat OpenShift

Toto je konec tohoto kurzu. Po dokončení testování ukázkové clusteru, tady je postup odstranění ji a její přidružené prostředky, proto není účtovat co nepoužíváte.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Odstranění clusteru služby Azure Red Hat OpenShift

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * [Škálování clusteru Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * Odstranění clusteru služby Azure Red Hat OpenShift

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Vytvoření clusteru pomocí následujícího [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlásit se k Azure

Pokud používáte Azure CLI místně, spusťte `az login` pro přihlášení k Azure.

```bash
az login
```

Pokud máte přístup k několika předplatným, spusťte `az account set -s {subscription ID}` nahrazení `{subscription ID}` s předplatným, které chcete použít.

## <a name="step-2-delete-the-cluster"></a>Krok 2: Odstranění clusteru

Otevřete terminál Bash a nastavte proměnnou název_clusteru na název vašeho clusteru:

```bash
CLUSTER_NAME=yourclustername
```

Teď odstraňte cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Zobrazí se výzva, jestli chcete odstranit cluster. Jakmile potvrdíte s `y`, bude trvat několik minut, než se cluster odstranit. Po dokončení příkazu celou skupinu prostředků a všechny prostředky uvnitř, včetně clusterů, se odstraní.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Odstranění clusteru pomocí webu Azure portal

Alternativně můžete odstranit v přidružené skupině prostředků clusteru prostřednictvím online webu Azure portal. Název skupiny prostředků je stejný jako název clusteru.

V současné době `Microsoft.ContainerService/openShiftManagedClusters` prostředek, který se vytvoří při vytvoření clusteru je skrytý na webu Azure Portal. V `Resource group` zobrazení, zkontrolujte `Show hidden types` zobrazíte skupinu prostředků.

![Snímek obrazovky skryté typu zaškrtávací políčko](./media/aro-portal-hidden-type.png)

Odstraněním skupiny prostředků odstraní všechny související prostředky, které se generují při vytváření clusteru služby Azure Red Hat OpenShift.

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Odstranění clusteru služby Azure Red Hat OpenShift

Další informace o Openshiftu pomocí oficiální [Red Hat OpenShift dokumentace](https://docs.openshift.com/aro/welcome/index.html)