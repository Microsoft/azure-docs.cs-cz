---
title: Spuštění a zastavení služby Azure Kubernetes (AKS)
description: Naučte se zastavit nebo spustit cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 4a814d575e0879daec64ebfdabc1539219bea250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91368936"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>Zastavení a spuštění clusteru Azure Kubernetes Service (AKS) (Preview)

Vaše úlohy AKS nemusí být potřeba spouštět nepřetržitě, například cluster pro vývoj, který se používá jenom během pracovní doby. To vede k časem, kdy by cluster služby Azure Kubernetes (AKS) mohl být nečinný a běžela více než systémové součásti. Můžete omezit nároky na clustery tím, že [změníte měřítko všech `User` fondů uzlů na hodnotu 0](scale-cluster.md#scale-user-node-pools-to-0), ale [ `System` fond](use-system-pools.md) je stále nutný ke spuštění systémových součástí, když je cluster spuštěný. Pokud chcete své náklady dále optimalizovat během těchto období, můžete cluster úplně vypnout (zastavit). Tato akce zastaví všechny uzly řídicí plochy a agentů, což vám umožní ušetřit všechny náklady na výpočetní výkon a přitom zachovat všechny vaše objekty a stav clusteru uložený při jejich opětovném spuštění. Pak můžete vybrat hned tam, kde zbývá po víkendu, nebo nechat cluster spuštěný jenom při spouštění dávkových úloh.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].


### <a name="limitations"></a>Omezení

Při použití funkce Spustit/zastavit pro cluster platí následující omezení:

- Tato funkce je podporována pouze pro Virtual Machine Scale Sets zálohovaných clusterů.
- Ve verzi Preview není tato funkce pro privátní clustery podporovaná.
- Stav clusteru zastaveného clusteru AKS se uchová po dobu až 12 měsíců. Pokud je váš cluster zastavený déle než 12 měsíců, nelze obnovit stav clusteru. Další informace najdete v tématu [zásady podpory AKS](support-policies.md).
- Můžete spustit nebo odstranit zastavený cluster AKS. Chcete-li provést jakoukoli operaci, jako je například škálování nebo upgrade, spusťte nejprve svůj cluster.

### <a name="install-the-aks-preview-azure-cli"></a>Instalace rozhraní příkazového `aks-preview` řádku Azure 

Budete také potřebovat rozšíření Azure CLI AKS ve verzi *Preview* 0.4.64 nebo novější. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] . Nebo nainstalujte jakékoli dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="register-the-startstoppreview-preview-feature"></a>Registrace `StartStopPreview` funkce Preview

Pokud chcete používat funkci spustit/zastavit cluster, musíte `StartStopPreview` u svého předplatného povolit příznak funkce.

Příznak funkce Zaregistrujte `StartStopPreview` pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

Zobrazení stavu v *registraci*trvá několik minut. Pomocí příkazu [AZ Feature list][az-feature-list] ověřte stav registrace:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

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
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az_aks_show
