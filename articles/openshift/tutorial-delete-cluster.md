---
title: Kurz – odstranění clusteru Azure Red Hat OpenShift
description: V tomto kurzu se dozvíte, jak odstranit cluster Azure Red Hat OpenShift pomocí Azure CLI.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232134"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Kurz: odstranění clusteru Azure Red Hat OpenShift 4

V tomto kurzu, který je třetí částí než tři, se odstraní cluster Azure Red Hat OpenShift se systémem OpenShift 4. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Odstranění clusteru Azure Red Hat OpenShift


## <a name="before-you-begin"></a>Před zahájením

V předchozích kurzech se vytvořil cluster Azure Red Hat OpenShift a připojil se k používání webové konzole OpenShift. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte s [kurzem 1 – Vytvoření clusteru Azure Red Hat OpenShift 4.](tutorial-create-cluster.md)

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.75 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud používáte Azure CLI místně, spusťte příkaz `az login` pro přihlášení k Azure.

```bash
az login
```

Pokud máte přístup k několika předplatným, `az account set -s {subscription ID}` spusťte `{subscription ID}` nahrazení pomocí předplatného, které chcete použít.

## <a name="delete-the-cluster"></a>Odstranění clusteru

V předchozích kurzech byly nastaveny následující proměnné. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

Pomocí těchto hodnot odstraňte cluster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Pak se zobrazí výzva k potvrzení, jestli chcete cluster odstranit. Po potvrzení v nástroji `y`bude trvat několik minut, než se cluster odstraní. Po dokončení příkazu se odstraní celá skupina prostředků a všechny prostředky uvnitř ní, včetně clusteru –.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Odstranění clusteru Azure Red Hat OpenShift 4

Další informace o používání OpenShift s oficiální [dokumentací Red Hat OpenShift](https://www.openshift.com/try)
