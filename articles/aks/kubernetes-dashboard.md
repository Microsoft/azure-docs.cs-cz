---
title: Správa clusteru Kubernetes v Azure pomocí webového uživatelského rozhraní
description: Další informace o použití integrované řídicí panel webového uživatelského rozhraní Kubernetes pomocí služby Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af48af596e86e0eb09fe45deabe13beedef57cd2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307921"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Přístup k řídicímu panelu Kubernetes pomocí služby Azure Kubernetes Service (AKS)

Kubernetes zahrnuje webové řídicí panel, který lze použít pro operací základní správy. V tomto článku se dozvíte, jak získat přístup k řídicímu panelu Kubernetes pomocí Azure CLI a provede vás těmito možnostmi některých operací základní řídicího panelu. Další informace na řídicí panel Kubernetes najdete v tématu [řídicí panel Kubernetes webové uživatelské rozhraní][kubernetes-dashboard].

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto dokumentu předpokládají, že jste vytvořili AKS cluster a navázali `kubectl` připojení ke clusteru. Pokud potřebujete k vytvoření clusteru AKS, přečtěte si [AKS quickstart][aks-quickstart].

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.27 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Spustit řídicí panel Kubernetes

Chcete-li spustit řídicí panel Kubernetes, použijte [az aks Procházet] [ az-aks-browse] příkazu. Následující příklad otevře řídicí panel pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Tento příkaz vytvoří proxy mezi váš vývojový systém a rozhraní API Kubernetes a otevře se webový prohlížeč na řídicí panel Kubernetes.

### <a name="for-rbac-enabled-clusters"></a>Pro clustery s podporou RBAC

Pokud váš cluster AKS používá RBAC, *ClusterRoleBinding* , musíte vytvořit řídicí panel můžete přistupovat správně. Ve výchozím nastavení řídicí panel Kubernetes je nasazen s minimálním přístupem pro čtení a zobrazí chyby přístup RBAC. Řídicí panel Kubernetes aktuálně nepodporuje uživatelem zadané přihlašovací údaje k určení úrovně přístupu, místo toho použije rolí udělit účtu služby. Správce clusteru může rozhodnout udělit další přístup k *řídicí panel kubernetes* účtu, služby, ale může to být vektor pro zvýšení úrovně oprávnění. Můžete také integrovat ověřování Azure Active Directory poskytují podrobnější úroveň přístupu.

Chcete-li vytvořit vazbu, použijte [kubectl vytvořit clusterrolebinding] [ kubectl-create-clusterrolebinding] příkaz, jak je znázorněno v následujícím příkladu. 

> [!WARNING]
> Tato ukázka vazba se nevztahují žádné další ověření součásti a může vést k nezabezpečeného použití. Řídicí panel Kubernetes je otevřené všem uživatelům přístup k adrese URL. Veřejně nezveřejňujte řídicí panel Kubernetes.
>
> Další informace o použití různé metody ověřování, najdete v článku wikiwebu řídicí panel Kubernetes na [ovládací prvky přístupu][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Můžete teď přístup k řídicímu panelu Kubernetes v clusteru s podporou RBAC. Chcete-li spustit řídicí panel Kubernetes, použijte [az aks Procházet] [ az-aks-browse] příkaz podle popisu v předchozím kroku.

## <a name="run-an-application"></a>Spuštění aplikace

Na řídicím panelu Kubernetes, klikněte na tlačítko **vytvořit** tlačítko v horním pravém okně. Zadejte název nasazení `nginx` a zadejte `nginx:latest` názvu image kontejneru. V části **služby**vyberte **externí** a zadejte `80` pro port a cílový port.

Až to budete mít, klikněte na tlačítko **nasadit** vytvořte nasazení.

![Dialogové okno Vytvoření služby Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Zobrazení aplikace

Informace o aplikaci stavu můžete zobrazit na řídicí panel Kubernetes. Jakmile je aplikace spuštěna, každá komponenta má zelené zaškrtávací políčko vedle něj.

![Podů Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Chcete-li zobrazit další informace o pody aplikací, klikněte na **Podů** v panelu nabídky na levé straně a pak vyberte **NGINX** pod. Zde se zobrazí pod konkrétní informace, jako je využití prostředků.

![Prostředky Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Pokud chcete zjistit IP adresu aplikace, klikněte na **služby** v panelu nabídky na levé straně a pak vyberte **NGINX** služby.

![zobrazení serveru nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Upravit aplikace

Vedle vytváření a zobrazení aplikací, je možné upravit a aktualizovat nasazení aplikací řídicí panel Kubernetes.

Chcete-li upravit nasazení, klikněte na tlačítko **nasazení** v panelu nabídky na levé straně a pak vyberte **NGINX** nasazení. Nakonec vyberte **upravit** v horním pravém navigačním panelu.

![Upravit Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Vyhledejte `spec.replica` hodnotu, která by měla být 1, změňte tuto hodnotu na 3. Počet replik nasazení serveru NGINX je přitom, zvýšil z 1 na 3.

Vyberte **aktualizace** Jakmile budete připraveni.

![Upravit Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Další postup

Další informace o řídicím panelu Kubernetes najdete v dokumentaci ke Kubernetes.

> [!div class="nextstepaction"]
> [Řídicí panel Kubernetes webového uživatelského rozhraní][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
