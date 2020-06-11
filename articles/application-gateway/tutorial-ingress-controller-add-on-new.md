---
title: Povolit doplněk řadiče příchozího přenosu pro nový AKS cluster s novým Azure Application Gateway
description: Pomocí tohoto kurzu můžete pro svůj nový cluster AKS povolit doplněk příchozího řadiče s novým Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 8595b7fc37a46dbb27dec4d1388e4b0251606411
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670933"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-through-azure-cli-preview"></a>Kurz: Povolení doplňku Application Gateway příchozího adaptéru pro nový cluster AKS s novým Application Gateway prostřednictvím rozhraní příkazového řádku Azure (Preview)

Pomocí Azure CLI můžete pro cluster [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) povolit doplněk [Application Gateway AGIC ()](ingress-controller-overview.md) , který je aktuálně ve verzi Preview. V tomto kurzu vytvoříte cluster AKS s povoleným doplňkem AGIC, který automaticky vytvoří Application Gateway, který se bude používat. Pak nasadíte ukázkovou aplikaci, která bude používat doplněk AGIC k vystavení aplikace prostřednictvím Application Gateway. Doplněk poskytuje mnohem rychlejší způsob, jak nasadit AGIC pro cluster AKS než [dřív Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) a nabízí také plně spravované prostředí.    

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků 
> * Vytvoření nového clusteru AKS s povoleným doplňkem AGIC 
> * Nasazení ukázkové aplikace pomocí AGIC pro příchozí přenosy v clusteru AKS
> * Ověřte, že je aplikace dosažitelná prostřednictvím Application Gateway

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat CLI místně, potřebujete k tomuto kurzu verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Pomocí příkazu [AZ Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) Zaregistrujte příznak funkce *AKS-IngressApplicationGatewayAddon* , jak je znázorněno v následujícím příkladu. To je potřeba udělat jenom jednou pro každé předplatné, zatímco doplněk je stále ve verzi Preview:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Může to trvat několik minut, než se stav zobrazí jako zaregistrované. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) :
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků Microsoft. ContainerService pomocí příkazu [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Nezapomeňte nainstalovat/aktualizovat rozšíření AKS-Preview pro tento kurz; použijte následující příkazy rozhraní příkazového řádku Azure
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *canadacentral* (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster-with-agic-add-on-enabled"></a>Nasazení nového clusteru AKS s povoleným doplňkem AGIC

Teď budete nasazovat nový cluster AKS s povoleným doplňkem AGIC. Když nasadíte nový cluster AKS s povoleným doplňkem AGIC a neposkytnete existující Application Gateway k použití, automaticky vytvoříme a nastavíme nový Application Gateway, který bude obsluhovat provoz do clusteru AKS.  

> [!NOTE]
> Doplněk Application Gateway AGIC (inWAF Controller) podporuje **pouze** SKU Application Gateway v2 (Standard a), **nikoli** SKU Application Gateway v1. Při nasazení nového Application Gateway prostřednictvím doplňku AGIC můžete nasadit pouze Application Gateway Standard_v2 SKU. Pokud chcete povolit doplněk AGIC pro Application Gateway WAF_v2 SKLADOVOU položku, buď povolte WAF na Application Gateway prostřednictvím portálu, nebo nejprve vytvořte WAF_v2 Application Gateway a pak postupujte podle pokynů, jak [Povolit AGIC doplněk s existujícím clusterem AKS a stávajícími Application Gateway](tutorial-ingress-controller-add-on-existing.md). 

V následujícím příkladu budete nasazovat nový cluster AKS s názvem *myCluster* pomocí [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) a [spravované identity](https://docs.microsoft.com/azure/aks/use-managed-identity) pomocí doplňku AGIC povoleného ve skupině prostředků, kterou jste vytvořili, *myResourceGroup*. Vzhledem k tomu, že nasazení nového clusteru AKS s povoleným doplňkem AGIC bez zadání existující Application Gateway bude znamenat automatické vytvoření Application Gateway skladové položky Standard_v2, bude také zadáno jméno a adresní prostor podsítě Application Gateway. Název Application Gateway bude *myApplicationGateway* a adresní prostor podsítě je 10.2.0.0/16. Ujistěte se, že jste na začátku tohoto kurzu přidali nebo aktualizovali rozšíření AKS-Preview. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Chcete-li nakonfigurovat další parametry pro `az aks create` příkaz, navštivte odkazy [zde](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Cluster AKS, který jste vytvořili, se zobrazí ve skupině prostředků, kterou jste vytvořili, *myResourceGroup*. Automaticky vytvořený Application Gateway ale bude živý v rámci skupiny prostředků uzlu, kde jsou fondy agentů. Skupina prostředků uzlu ve výchozím nastavení bude pojmenována *MC_resource-Group-name_cluster-name_location*, ale lze ji upravit. 

## <a name="deploy-a-sample-application-using-agic"></a>Nasazení ukázkové aplikace pomocí AGIC

Teď budete nasazovat ukázkovou aplikaci do vytvořeného clusteru AKS, který bude používat doplněk AGIC pro příchozí připojení a připojit Application Gateway k clusteru AKS. Nejdřív získáte přihlašovací údaje ke clusteru AKS, který jste nasadili, spuštěním `az aks get-credentials` příkazu. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Jakmile budete mít přihlašovací údaje ke clusteru, který jste vytvořili, spusťte následující příkaz, který nastaví ukázkovou aplikaci, která používá AGIC pro příchozí přenos dat do clusteru. AGIC aktualizuje Application Gateway, kterou jste nastavili dříve, a odpovídající pravidla směrování na novou ukázkovou aplikaci, kterou jste nasadili.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Ověřte, že je aplikace dostupná.

Teď, když je Application Gateway nastavené na obsluhu provozu do clusteru AKS, ověříme, že je vaše aplikace dostupná. Nejdřív získáte IP adresu příchozího přenosu dat. 

```azurecli-interactive
kubectl get ingress
```

Ověřte, že ukázková aplikace, kterou jste vytvořili, je v provozu, buď na základě IP adresy Application Gateway, kterou jste získali ze spuštění výše uvedeného příkazu, nebo se podívejte na `curl` . Aktualizace může trvat Application Gateway minutu, takže pokud se Application Gateway na portálu stále nachází ve stavu "aktualizace", pak ho nechte dokončit, než se pokusíte připojit k IP adrese. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete skupinu prostředků, aplikační bránu a další související prostředky potřebovat, odeberte je.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky
* [Další informace o zakázání doplňku AGIC](./ingress-controller-disable-addon.md)
* [Další informace o tom, které poznámky jsou podporované pomocí AGIC](./ingress-controller-annotations.md)
* [Řešení potíží s AGIC](./ingress-controller-troubleshoot.md)

