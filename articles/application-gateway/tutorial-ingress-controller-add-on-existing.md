---
title: 'Kurz: povolení Add-On adaptéru příchozího přenosu dat pro existující cluster AKS se stávajícím Application Gatewaym Azure'
description: Tento kurz vám umožní povolit Add-On pro existující cluster AKS s existujícím Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 1daf5fef1383272f728ff3dac7557e55398f7d50
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720218"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Kurz: Povolení doplňku Application Gateway příchozího adaptéru pro existující cluster AKS s existujícím Application Gateway

Pomocí Azure CLI nebo portálu můžete pro existující cluster [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) povolit doplněk [Application Gateway AGIC (příchozího kontroleru)](ingress-controller-overview.md) . V tomto kurzu se naučíte používat doplněk AGIC k zpřístupnění vaší aplikace Kubernetes v existujícím clusteru AKS prostřednictvím stávajícího Application Gateway nasazeného v samostatných virtuálních sítích. Začnete vytvořením clusteru AKS v jedné virtuální síti a Application Gateway v samostatné virtuální síti pro simulaci stávajících prostředků. Pak povolíte doplněk AGIC, partnerský vztah obou virtuálních sítí a nasadíte ukázkovou aplikaci, která bude zpřístupněna prostřednictvím Application Gateway pomocí doplňku AGIC. Pokud povolíte doplněk AGIC pro existující Application Gateway a stávající cluster AKS ve stejné virtuální síti, můžete přeskočit níže uvedený krok partnerského vztahu. Doplněk poskytuje mnohem rychlejší způsob, jak nasadit AGIC pro cluster AKS než [dřív Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) a nabízí také plně spravované prostředí.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků 
> * Vytvoření nového clusteru AKS 
> * Vytvořit nový Application Gateway 
> * Povolení doplňku AGIC v existujícím clusteru AKS prostřednictvím Azure CLI 
> * Povolení doplňku AGIC v existujícím clusteru AKS prostřednictvím portálu 
> * Navázání partnerského vztahu Application Gateway virtuální sítě s virtuální sítí clusteru AKS
> * Nasazení ukázkové aplikace pomocí AGIC pro příchozí přenosy v clusteru AKS
> * Ověřte, že je aplikace dosažitelná prostřednictvím Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *canadacentral* (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Nasazení nového clusteru AKS

Nyní nasadíte nový cluster AKS, abyste simulovali existující cluster AKS, pro který chcete povolit doplněk AGIC pro.  

V následujícím příkladu budete nasazovat nový cluster AKS s názvem *myCluster* pomocí [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) a [spravované identity](../aks/use-managed-identity.md) ve vámi vytvořené skupině prostředků *myResourceGroup*.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Chcete-li nakonfigurovat další parametry pro `az aks create` příkaz, navštivte odkazy [zde](/cli/azure/aks#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Nasadit nový Application Gateway 

Nyní nasadíte novou Application Gateway pro simulaci existující Application Gateway, kterou chcete použít k vyrovnávání zatížení provozu do clusteru AKS *myCluster*. Název Application Gateway bude *myApplicationGateway*, ale budete muset nejdřív vytvořit prostředek veřejné IP adresy s názvem *myPublicIp* a novou virtuální síť s názvem *myVnet* s adresním prostorem 11.0.0.0/8 a podsíť s adresním prostorem 11.1.0.0/16 nazvanou *MySubnet* a nasadit Application Gateway v *mySubnet* pomocí *myPublicIp*. 

Pokud používáte cluster AKS a Application Gateway v samostatných virtuálních sítích, nesmí se adresní prostory těchto dvou virtuálních sítí překrývat. Výchozím adresním prostorem, ve kterém se nasazuje cluster AKS, je 10.0.0.0/8, takže nastavíme předponu adresy Application Gateway virtuální sítě na 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Doplněk Application Gateway AGIC (inWAF Controller) podporuje **pouze** SKU Application Gateway v2 (Standard a), **nikoli** SKU Application Gateway v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Povolení doplňku AGIC v existujícím clusteru AKS prostřednictvím Azure CLI 

Pokud chcete i nadále používat rozhraní příkazového řádku Azure CLI, můžete pokračovat ve povolování doplňku AGIC v clusteru AKS, který jste vytvořili, *myCluster* a zadáním doplňku AGIC pro použití existujícího Application Gateway, který jste vytvořili, *myApplicationGateway*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Povolení doplňku AGIC v existujícím clusteru AKS prostřednictvím portálu 

Pokud chcete použít Azure Portal k povolení doplňku AGIC, přejděte na adresu [( https://aka.ms/azure/portal/aks/agic) ](https://aka.ms/azure/portal/aks/agic) a přejděte do svého clusteru AKS prostřednictvím odkazu na portál. V takovém případě přejít na kartu sítě v rámci clusteru AKS. Zobrazí se část Application Gateway řadič příchozího přenosu dat, která umožňuje povolit nebo zakázat doplněk pro řadič příchozího přenosu dat pomocí uživatelského rozhraní portálu. Zaškrtněte políčko u možnosti Povolit řadič příchozího přenosu dat a v rozevírací nabídce vyberte Application Gateway, které jste vytvořili, *myApplicationGateway* . 

![Portál pro Application Gateway adaptéru příchozího přenosu dat](./media/tutorial-ingress-controller-add-on-existing/portal_ingress_controller_addon.png)

## <a name="peer-the-two-virtual-networks-together"></a>Partnerský vztah mezi dvěma virtuálními sítěmi

Vzhledem k tomu, že jsme cluster AKS nasadili ve vlastní virtuální síti a Application Gateway v jiné virtuální síti, budete muset vytvořit partnerský vztah mezi dvěma virtuálními sítěmi, aby přenos dat z Application Gateway do lusků v clusteru. Partnerský vztah dvou virtuálních sítí vyžaduje, aby se příkaz Azure CLI používal dvakrát, aby se zajistilo obousměrné připojení. První příkaz vytvoří připojení partnerského vztahu z Application Gateway virtuální sítě k virtuální síti AKS; Druhý příkaz vytvoří připojení partnerského vztahu v druhém směru.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Nasazení ukázkové aplikace pomocí AGIC 

Nyní nasadíte ukázkovou aplikaci do vytvořeného clusteru AKS, který bude používat doplněk AGIC pro příchozí připojení a připojí Application Gateway k clusteru AKS. Nejdřív získáte přihlašovací údaje ke clusteru AKS, který jste nasadili, spuštěním `az aks get-credentials` příkazu. 

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

> [!div class="nextstepaction"]
> [Další informace o zakázání doplňku AGIC](./ingress-controller-disable-addon.md)
