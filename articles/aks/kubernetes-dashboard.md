---
title: Správa clusteru s řídicím panelu webové služby Azure Kubernetes
description: Zjistěte, jak použít integrovaný řídicí panel Kubernetes webového uživatelského rozhraní pro správu clusteru služby Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 9d953cdb82412c07fe0ed4bef75dece4a929cad9
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067580"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Přístup k řídicímu panelu Kubernetes web ve službě Azure Kubernetes Service (AKS)

Kubernetes zahrnuje webové řídicí panel, který lze použít pro operací základní správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvoření a nasazení služeb a upravit stávající aplikace. V tomto článku se dozvíte, jak získat přístup k řídicímu panelu Kubernetes pomocí Azure CLI a provede vás těmito možnostmi některých operací základní řídicího panelu.

Další informace na řídicí panel Kubernetes najdete v tématu [řídicí panel Kubernetes webové uživatelské rozhraní][kubernetes-dashboard].

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto dokumentu předpokládají, že jste vytvořili AKS cluster a navázali `kubectl` připojení ke clusteru. Pokud potřebujete k vytvoření clusteru AKS, přečtěte si [AKS quickstart][aks-quickstart].

Také nutné mít Azure CLI verze 2.0.46 nebo později nainstalované a nakonfigurované. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Spustit řídicí panel Kubernetes

Chcete-li spustit řídicí panel Kubernetes, použijte [az aks Procházet] [ az-aks-browse] příkazu. Následující příklad otevře řídicí panel pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Tento příkaz vytvoří proxy mezi váš vývojový systém a rozhraní API Kubernetes a otevře se webový prohlížeč na řídicí panel Kubernetes. Pokud se webový prohlížeč se neotevře na řídicí panel Kubernetes, zkopírujte a vložte adresu URL, které jste si poznamenali v rozhraní příkazového řádku Azure, obvykle *http://127.0.0.1:8001*.

![Stránka s přehledem webové řídicího panelu Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

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

## <a name="create-an-application"></a>Vytvoření aplikace

Pokud chcete zobrazit, jak řídicí panel Kubernetes můžete zjednodušit úlohy správy, vytvoříme aplikaci. Tím, že poskytuje textový vstup, soubor YAML, nebo prostřednictvím průvodce s grafickým rozhraním, můžete vytvořit aplikaci z řídicího panelu Kubernetes.

Vytvoření aplikace, proveďte následující kroky:

1. Vyberte **vytvořit** tlačítko v horním pravém okně.
1. Chcete-li použít průvodce s grafickým rozhraním, **vytvořit aplikaci**.
1. Zadejte název pro nasazení, jako třeba *serveru nginx*
1. Zadejte název pro image kontejneru, který chcete použít, jako například *nginx:1.15.5*
1. Zpřístupňuje porty 80 pro webový provoz, můžete vytvořit služby Kubernetes. V části **služby**vyberte **externí**, zadejte **80** pro port a cílový port.
1. Až to budete mít, vyberte **nasadit** vytvořte aplikaci.

![Nasazení aplikace na řídicím panelu webové Kubernetes](./media/kubernetes-dashboard/create-app.png)

Bude trvat minutu nebo dvě veřejné externí IP adresu a přiřadit do služby Kubernetes. Na levé straně velikosti v části **zjišťování a vyrovnávání zatížení** vyberte **služby**. Uvedené služby vaší aplikace, včetně *externí koncové body*, jak je znázorněno v následujícím příkladu:

![Zobrazit seznam služeb a koncových bodů](./media/kubernetes-dashboard/view-services.png)

Vyberte adresu koncového bodu a otevřete okno webového prohlížeče k výchozí stránka serveru NGINX:

![Zobrazit výchozí stránka serveru NGINX z nasazenou aplikaci.](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Zobrazení informací o pod

Řídicí panel Kubernetes může poskytnout základní metriky monitorování a řešení potíží s informace, jako jsou protokoly.

Pokud chcete zobrazit další informace o vaše pody aplikací, vyberte **Podů** v levé nabídce. Seznam dostupných podů se zobrazí. Zvolte vaši *nginx* pod zobrazíte informace, jako je využití prostředků:

![Zobrazení informací o pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Upravit aplikace

Vedle vytváření a zobrazení aplikací, je možné upravit a aktualizovat nasazení aplikací řídicí panel Kubernetes. Další redundanci pro aplikaci, Řekněme zvýšit počet replik serveru NGINX.

Chcete-li upravit nasazení:

1. Vyberte **nasazení** v nabídce vlevo a pak zvolte vaši *nginx* nasazení.
1. Vyberte **upravit** v horním pravém navigačním panelu.
1. Vyhledejte `spec.replica` hodnota, na kolem řádku 20. Pokud chcete zvýšit počet replik pro aplikaci, změňte tuto hodnotu z *1* k *3*.
1. Vyberte **aktualizace** Jakmile budete připraveni.

![Upravit nasazení aktualizovat počet replik](./media/kubernetes-dashboard/edit-deployment.png)

Trvá ještě chvilku na nových podů, který se má vytvořit uvnitř sady replik. V nabídce vlevo zvolte **sady replik**a klikněte na tlačítko vaše *nginx* sady replik. Seznam podů nyní odráží počet aktualizovaných replik, jak je znázorněno v následujícím příkladu výstupu:

![Zobrazit informace na replikách](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Další postup

Další informace o řídicím panelu Kubernetes najdete v tématu [řídicí panel Kubernetes webové uživatelské rozhraní][kubernetes-dashboard].

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
