---
title: Kurz – odstranění clusteru Azure Red Hat OpenShift
description: V tomto kurzu se dozvíte, jak odstranit cluster Azure Red Hat OpenShift pomocí příkazového příkazového příkazu Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278775"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Kurz: Odstranění clusteru Azure Red Hat OpenShift

Toto je konec tohoto kurzu. Po dokončení testování ukázkového clusteru, tady je postup, jak ho odstranit a jeho přidružené prostředky, abyste se neúčtovali za to, co nepoužíváte.

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

* Vytvořte cluster podle kurzu [vytvořit azure red hat openshift clusteru.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Přihlášení do Azure

Pokud používáte azure cli místně, `az login` spusťte přihlášení k Azure.

```bash
az login
```

Pokud máte přístup k více `az account set -s {subscription ID}` předplatných, spusťte nahrazení `{subscription ID}` předplatného, které chcete použít.

## <a name="step-2-delete-the-cluster"></a>Krok 2: Odstranění clusteru

Otevřete terminál Bash a nastavte proměnnou CLUSTER_NAME na název clusteru:

```bash
CLUSTER_NAME=yourclustername
```

Nyní odstraňte cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Budete vyzváni, zda chcete cluster odstranit. Po potvrzení `y`pomocí služby bude odstranění clusteru trvat několik minut. Po dokončení příkazu bude odstraněna celá skupina prostředků a všechny prostředky uvnitř, včetně clusteru.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Odstranění clusteru pomocí portálu Azure

Případně můžete odstranit přidružené skupiny prostředků vašeho clusteru prostřednictvím online portálu Azure. Název skupiny prostředků je stejný jako název clusteru.

V současné `Microsoft.ContainerService/openShiftManagedClusters` době prostředek, který se vytvoří při vytváření clusteru je skrytý na portálu Azure. V `Resource group` zobrazení zkontrolujte, zda `Show hidden types` chcete zobrazit skupinu prostředků.

![Snímek obrazovky se zaškrtávacím políčkem Skrytý typ](./media/aro-portal-hidden-type.png)

Odstraněním skupiny prostředků odstraníte všechny související prostředky, které se vytvoří při vytváření clusteru Azure Red Hat OpenShift.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Odstranění clusteru Azure Red Hat OpenShift

Další informace o používání OpenShiftu s oficiální [dokumentací Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)
