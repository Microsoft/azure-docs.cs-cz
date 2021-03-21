---
title: Spuštění a zastavení služby Azure Kubernetes (AKS)
description: Naučte se zastavit nebo spustit cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 87d51f9c1d084faf79c7ec1cf1255a6fb3c8245d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201005"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Zastavení a spuštění clusteru Azure Kubernetes Service (AKS)

Vaše úlohy AKS nemusí být potřeba spouštět nepřetržitě, například cluster pro vývoj, který se používá jenom během pracovní doby. To vede k časem, kdy by cluster služby Azure Kubernetes (AKS) mohl být nečinný a běžela více než systémové součásti. Můžete omezit nároky na clustery tím, že [změníte měřítko všech `User` fondů uzlů na hodnotu 0](scale-cluster.md#scale-user-node-pools-to-0), ale [ `System` fond](use-system-pools.md) je stále nutný ke spuštění systémových součástí, když je cluster spuštěný. Pokud chcete své náklady dále optimalizovat během těchto období, můžete cluster úplně vypnout (zastavit). Tato akce zastaví všechny uzly řídicí plochy a agentů, což vám umožní ušetřit všechny náklady na výpočetní výkon a přitom zachovat všechny vaše objekty a stav clusteru uložený při jejich opětovném spuštění. Pak můžete vybrat hned tam, kde zbývá po víkendu, nebo nechat cluster spuštěný jenom při spouštění dávkových úloh.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Omezení

Při použití funkce Spustit/zastavit pro cluster platí následující omezení:

- Tato funkce je podporována pouze pro Virtual Machine Scale Sets zálohovaných clusterů.
- Stav clusteru zastaveného clusteru AKS se uchová po dobu až 12 měsíců. Pokud je váš cluster zastavený déle než 12 měsíců, nelze obnovit stav clusteru. Další informace najdete v tématu [zásady podpory AKS](support-policies.md).
- Můžete spustit nebo odstranit zastavený cluster AKS. Chcete-li provést jakoukoli operaci, jako je například škálování nebo upgrade, spusťte nejprve svůj cluster.

## <a name="stop-an-aks-cluster"></a>Zastavení clusteru AKS

Pomocí `az aks stop` příkazu můžete zastavit běžící uzly clusteru AKS a plochu ovládacího prvku. Následující příklad zastaví cluster s názvem *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Můžete ověřit, jestli je cluster zastavený, pomocí příkazu [AZ AKS show][az-aks-show] a potvrzením, `powerState` jak ukazuje `Stopped` následující výstup:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Pokud `provisioningState` to ukazuje `Stopping` , že váš cluster ještě není úplně zastavený.

> [!IMPORTANT]
> Používáte-li [nerušené rozpočty](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) , může operace zastavení trvat delší dobu, než dokončí proces vyprázdnění.

## <a name="start-an-aks-cluster"></a>Spuštění clusteru AKS

Pomocí `az aks start` příkazu můžete spustit zastavené uzly a řídicí plochu clusteru AKS. Cluster se restartuje s předchozím stavem roviny řízení a počtem uzlů agentů.  
Následující příklad spustí cluster s názvem *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Spuštění clusteru můžete ověřit pomocí příkazu [AZ AKS show][az-aks-show] a potvrzením, `powerState` `Running` jak ukazuje následující výstup:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Pokud `provisioningState` to ukazuje `Starting` , že váš cluster ještě není úplně spuštěný.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak škálovat `User` fondy na 0, najdete v tématu [škálování `User` fondů na 0](scale-cluster.md#scale-user-node-pools-to-0).
- Informace o tom, jak ušetřit náklady pomocí instancí s přímým odkazem, najdete v tématu [Přidání fondu bodových uzlů do AKS](spot-node-pool.md).
- Další informace o zásadách podpory AKS najdete v tématu [zásady podpory AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-show]: /cli/azure/aks#az_aks_show
