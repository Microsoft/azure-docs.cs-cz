---
title: Použití spravovaných identit ve službě Azure Kubernetes Service
description: Zjistěte, jak používat spravované identity ve službě Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369974"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Použití spravovaných identit ve službě Azure Kubernetes Service

V současné době cluster služby Azure Kubernetes (AKS) (konkrétně zprostředkovatel cloudu Kubernetes) vyžaduje *instanční objekt* pro vytváření dalších prostředků, jako jsou nástroje pro vyrovnávání zatížení a spravované disky v Azure. Buď musíte poskytnout instanční objekt nebo AKS vytvoří jeden vaším jménem. Instanční objekty mají obvykle datum vypršení platnosti. Clustery nakonec dosáhnou stavu, ve kterém musí být instanční objekt obnoven, aby cluster fungoval. Správa instančních objektů zvyšuje složitost.

*Spravované identity* jsou v podstatě obálka kolem instančních objektů a zjednodušit jejich správu. Další informace najdete v informacích o [spravovaných identitách pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS vytvoří dvě spravované identity:

- **Spravovaná identita přiřazená systémem**: Identita, kterou poskytovatel cloudu Kubernetes používá k vytvoření prostředků Azure jménem uživatele. Životní cyklus identity přiřazené k systému je vázán na klastr. Identita je odstraněna při odstranění clusteru.
- **Spravovaná identita přiřazená uživatelem**: Identita, která se používá pro autorizaci v clusteru. Například uživatelem přiřazená identita se používá k autorizaci AKS k používání registrů kontejnerů Azure (ACRs) nebo k autorizaci kubelet k získání metadat z Azure.

Doplňky se také ověřují pomocí spravované identity. Pro každý doplněk je spravovaná identita vytvořena AKS a trvá po dobu životnosti doplňku. Pro vytváření a používání vlastní virtuální sítě, statické IP adresy nebo připojeného disku Azure, kde jsou prostředky mimo skupinu prostředků MC_*, použijte k přiřazení role PrincipalID clusteru. Další informace o přiřazení rolí najdete v [tématu Delegát přístup k jiným prostředkům Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

## <a name="before-you-begin"></a>Než začnete

Musíte mít nainstalovaný následující prostředek:

- Nastavení příkazového příkazu Azure verze 2.2.0 nebo novější

## <a name="create-an-aks-cluster-with-managed-identities"></a>Vytvoření clusteru AKS se spravovanými identitami

Nyní můžete vytvořit cluster AKS se spravovanými identitami pomocí následujících příkazů příkazového příkazu příkazového příkazu.

Nejprve vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Potom vytvořte cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Úspěšné vytvoření clusteru pomocí spravovaných identit obsahuje informace o profilu instančního objektu:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Nakonec získat pověření pro přístup ke clusteru:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Cluster bude vytvořen během několika minut. Pak můžete nasadit úlohy aplikace do nového clusteru a pracovat s ním stejně jako jste to udělali s clustery AKS založené na službě.

> [!IMPORTANT]
>
> - Clustery AKS se spravovanými identitami lze povolit pouze při vytváření clusteru.
> - Existující clustery AKS nelze aktualizovat ani upgradovat, aby bylo možné povolit spravované identity.
