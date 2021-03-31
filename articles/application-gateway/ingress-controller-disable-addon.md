---
title: Zakázat a znovu povolit doplněk Application Gatewayho kontroleru příchozího přenosu pro cluster služby Azure Kubernetes
description: Tento článek poskytuje informace o tom, jak zakázat a znovu povolit doplněk AGIC pro cluster AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84807944"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>Zakázání a opětovné povolení doplňku AGIC pro cluster AKS
Application Gateway AGIC (příchozí adaptér) nasazený jako doplněk AKS umožňuje povolit nebo zakázat doplněk s jedním řádkem v Azure CLI. Životní cyklus Application Gateway se bude lišit v závislosti na tom, zda byla Application Gateway vytvořena doplňkem AGIC nebo pokud byla nasazena samostatně z doplňku AGIC. Můžete spustit stejný příkaz pro opětovné povolení doplňku AGIC, pokud ho někdy zakážete, nebo pokud chcete povolit doplněk AGIC pomocí existujícího clusteru AKS a Application Gateway.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>Zakazování doplňku AGIC s přidruženými Application Gateway 
Pokud doplněk AGIC automaticky nasadil Application Gateway při první instalaci všech položek, pak se vypnutím doplňku AGIC standardně odstraní Application Gateway na základě několika kritérií. Existují dvě kritéria, která AGIC doplněk vyhledá a určí, jestli má při zakázání odstranit přidruženou Application Gateway:
- Je Application Gateway, že je doplněk AGIC spojený s nasazeným ve skupině prostředků MC_ * uzlu? 
- Má Application Gateway, ke kterému je přidružen doplněk AGIC, přiřazená značka "vytvořil-by: příchozí-appgw"? Značku používá AGIC k určení, zda Application Gateway byla nasazena doplňkem nebo nikoli. 

Pokud jsou splněna obě kritéria, doplněk AGIC odstraní Application Gateway, který vytvořil, když je doplněk zakázaný. neodstraní ale veřejnou IP adresu nebo podsíť, ve které je Application Gateway nasazená pomocí/v. Pokud není první kritérium splněné, nezáleží na tom, jestli Application Gateway obsahuje značku "vytvořil-by: příchozí-appgw" – zakázání doplňku neodstraní Application Gateway. Podobně platí, že pokud nejsou splněna druhá kritéria, tj. Application Gateway postrádá tuto značku, pak doplněk neodstraní Application Gateway ve skupině prostředků uzlu MC_ *. 

> [!TIP] 
> Pokud nechcete, aby se Application Gateway při zakázání doplňku odstranila, ale splňuje obě kritéria, odeberte značku "vytvořeno pomocí: příchozí-appgw", abyste zabránili tomu, že doplněk odstraní vaše Application Gateway. 

Pokud chcete zakázat doplněk AGIC, spusťte následující příkaz: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>Povolit doplněk AGIC pro existující cluster Application Gateway a AKS
Pokud jste někdy zakázali doplněk AGIC a potřebujete znovu povolit doplněk nebo chcete povolit doplněk pomocí stávajícího Application Gateway a clusteru AKS, spusťte následující příkaz:

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak povolit doplněk AGIC pomocí existujícího clusteru Application Gateway a AKS, najdete v tématu věnovaném [nasazení brownfield AGIC Add-on](tutorial-ingress-controller-add-on-existing.md).