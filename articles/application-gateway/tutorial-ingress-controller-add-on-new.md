---
title: 'Kurz: Povolení doplňku příchozího řadiče pro nový cluster AKS s novým Application Gateway Azure'
description: V tomto kurzu se dozvíte, jak povolit doplněk pro nový cluster AKS s novou instancí Application Gateway do řídicího panelu příchozího provozu.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: c37168c5165f5402dd4f57c8557bc2b7b3603533
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720184"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Kurz: Povolení doplňku příchozího řadiče pro nový cluster AKS s novou instancí Application Gateway

Pomocí rozhraní příkazového řádku Azure můžete pro nový cluster [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) povolit doplněk [Application Gateway AGIC (příchozího kontroleru)](ingress-controller-overview.md) .

V tomto kurzu vytvoříte cluster AKS s povoleným doplňkem AGIC. Vytvořením clusteru se automaticky vytvoří instance Azure Application Gateway, kterou chcete použít. Pak nasadíte ukázkovou aplikaci, která použije doplněk k vystavení aplikace prostřednictvím Application Gateway. 

Doplněk poskytuje mnohem rychlejší způsob, jak nasadit AGIC pro cluster AKS než [dřív Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Nabízí také plně spravované prostředí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte skupinu prostředků. 
> * Vytvořte nový cluster AKS s povoleným doplňkem AGIC.
> * Nasaďte ukázkovou aplikaci pomocí AGIC pro příchozí přenosy v clusteru AKS.
> * Ověřte, že je aplikace dosažitelná prostřednictvím Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *canadacentral* (region): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Nasazení clusteru AKS s povoleným doplňkem

Nyní nasadíte nový cluster AKS s povoleným doplňkem AGIC. Pokud neposkytnete existující instanci Application Gateway pro použití v tomto procesu, vytvoříme automaticky novou instanci Application Gateway, která bude obsluhovat provoz do clusteru AKS.  

> [!NOTE]
> Doplněk Application Gatewayho kontroleru příchozího přenosu dat podporuje *pouze* SKU Application Gateway v2 (Standard a WAF), *nikoli* SKU Application Gateway v1. Při nasazení nové instance Application Gateway prostřednictvím doplňku AGIC můžete nasadit pouze SKU Application Gateway Standard_v2. Pokud chcete povolit doplněk pro Application Gateway WAF_v2 skladové položky, použijte některou z těchto metod:
>
> - Povolte WAF na Application Gateway prostřednictvím portálu. 
> - Nejprve vytvořte instanci WAF_v2 Application Gateway a pak postupujte podle pokynů, jak [Povolit doplněk AGIC s existujícím clusterem AKS a existující instancí Application Gateway](tutorial-ingress-controller-add-on-existing.md). 

V následujícím příkladu nasadíte nový cluster AKS s názvem *myCluster* s využitím [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) a [spravovaných identit](../aks/use-managed-identity.md). Doplněk AGIC bude povolen ve skupině prostředků, kterou jste vytvořili, *myResourceGroup*. 

Nasazení nového clusteru AKS s povoleným doplňkem AGIC bez zadání existující instance Application Gateway bude znamenat automatické vytvoření instance Application Gateway SKU Standard_v2. Zadejte také název a adresní prostor podsítě instance Application Gateway. Název instance Application Gateway bude *myApplicationGateway* a adresní prostor podsítě používáme 10.2.0.0/16.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

Chcete-li nakonfigurovat další parametry `az aks create` příkazu, přečtěte si [tyto odkazy](/cli/azure/aks#az-aks-create). 

> [!NOTE]
> Cluster AKS, který jste vytvořili, se zobrazí ve skupině prostředků, kterou jste vytvořili, *myResourceGroup*. Automaticky vytvořená instance Application Gateway však bude ve skupině prostředků uzlu, kde jsou fondy agentů. Skupina prostředků uzlu podle má ve výchozím nastavení název *MC_resource-Group-name_cluster-name_location* , ale dá se upravit. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Nasazení ukázkové aplikace pomocí AGIC

Nyní nasadíte ukázkovou aplikaci do clusteru AKS, který jste vytvořili. Aplikace použije doplněk AGIC pro příchozí připojení a připojí instanci Application Gateway k clusteru AKS. 

Nejdřív Získejte přihlašovací údaje ke clusteru AKS spuštěním `az aks get-credentials` příkazu: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Teď, když máte přihlašovací údaje, spusťte následující příkaz, který nastaví ukázkovou aplikaci, která pro příchozí provoz do clusteru používá AGIC. AGIC aktualizuje instanci Application Gateway, kterou jste nastavili dříve, pomocí odpovídajících pravidel směrování na nově nasazenou ukázkovou aplikaci.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Ověřte, že je aplikace dostupná.

Teď, když je instance Application Gateway nastavená tak, aby sloužila provozu do clusteru AKS, pojďme ověřit, jestli je vaše aplikace dostupná. Nejdřív Získejte IP adresu příchozího přenosu dat: 

```azurecli-interactive
kubectl get ingress
```

Ověřte, že ukázková aplikace, kterou jste vytvořili, je spuštěná buď pomocí:

- Návštěvy IP adresy instance Application Gateway, kterou jste získali ze spuštění předchozího příkazu.
- Pomocí `curl` . 

Aktualizace může trvat Application Gateway minut. Pokud je Application Gateway stále ve stavu **aktualizace** na portálu, nechte ho dokončit, než se pokusíte připojit k IP adrese. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, odeberte skupinu prostředků, instanci Application Gateway a všechny související prostředky:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o zakázání doplňku AGIC](./ingress-controller-disable-addon.md)
