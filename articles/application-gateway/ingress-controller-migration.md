---
title: Postup migrace z Azure Application Gateway Helm adaptéru příchozího přenosu dat na AGIC doplněk
description: Tento článek poskytuje pokyny, jak migrovat z AGIC nasazeného prostřednictvím Helm na AGIC nasazené jako doplněk AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050830"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migrace z AGIC Helm na AGIC doplněk 

Pokud už máte AGIC nasazené prostřednictvím Helm, ale chcete migrovat na AGIC nasazenou jako doplněk AKS, následující kroky vám pomůžou s procesem migrace. 

## <a name="prerequisites"></a>Předpoklady 
Než zahájíte proces migrace, je třeba kontrolovat několik věcí. 
  - Používáte nějaké funkce s AGIC Helm, které [aktuálně nejsou podporované pomocí doplňku AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)?
  - Používáte pro každý cluster AKS více než jedno nasazení AGIC Helm? 
  - Používáte více nasazení AGIC Helm pro cílení na jednu Application Gateway? 

Pokud odpovíte Ano na kteroukoli z výše uvedených otázek, AGIC doplněk zatím nepodporuje váš případ použití, takže bude nejlepší dál používat AGIC Helm. V opačném případě pokračujte v procesu migrace níže i mimo pracovní dobu. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Vyhledá ID prostředku Application Gateway, který aktuálně cílí na Helm AGIC. 
Přejděte na Application Gateway, na kterou cílí vaše nasazení AGIC Helm. Zkopírujte a uložte ID prostředku tohoto Application Gateway. V pozdějším kroku budete potřebovat ID prostředku. ID prostředku najdete na portálu na kartě vlastnosti Application Gateway nebo prostřednictvím rozhraní příkazového řádku Azure. V následujícím příkladu se uloží ID prostředku Application Gateway, které *appgwId* pro bránu s názvem *myApplicationGateway* ve skupině prostředků *myResourceGroup*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>Odstranění AGIC Helm z vašeho clusteru AKS
Pomocí Azure CLI odstraňte nasazení služby AGIC Helm z vašeho clusteru. Aby bylo možné povolit doplněk AGIC AKS, je nutné nejprve odstranit nasazení AGIC Helm. Pamatujte na to, že všechny změny, ke kterým dojde v rámci AKS clusteru mezi časem odstranění nasazení AGIC Helm, a časem, kdy jste povolili doplněk AGIC, se neprojeví na vašem Application Gateway, a proto by se tento proces migrace měl provést mimo pracovní dobu, aby se minimalizoval dopad. Application Gateway bude i nadále mít poslední konfiguraci, kterou používá AGIC, takže stávající pravidla směrování nebudou ovlivněna. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>Povolit doplněk AGIC pomocí stávajícího Application Gateway 
Nyní můžete povolit doplněk AGIC v clusteru AKS a cílit stávající Application Gateway prostřednictvím rozhraní příkazového řádku Azure CLI nebo portálu. Spuštěním následujícího příkazu rozhraní příkazového řádku Azure povolte doplněk AGIC v clusteru AKS. Tento příklad umožňuje doplněk v clusteru s názvem *myCluster* ve skupině prostředků s názvem *myResourceGroup* s použitím Application Gateway prostředku s ID *appgwId* jsme uložili výše v předchozím kroku. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Případně můžete přejít na svůj cluster AKS na portálu pomocí tohoto [odkazu](https://portal.azure.com/?feature.aksagic=true) a povolit doplněk AGIC na kartě síť v clusteru. Vyberte existující Application Gateway z rozevírací nabídky, když zvolíte, na které Application Gateway se má doplněk zaměřit. 

![Portál pro Application Gateway adaptéru příchozího přenosu dat](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Další kroky
- [**Řešení potíží s Application Gatewaym řadičem**](ingress-controller-troubleshoot.md)příchozího přenosu: Průvodce odstraňováním potíží pro AGIC 
- [**Poznámky k kontroleru Application Gateway**](ingress-controller-annotations.md)příchozího přenosu: seznam poznámek na AGIC 