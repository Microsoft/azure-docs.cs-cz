---
title: Správa clusteru služby Azure Kubernetes pomocí webového řídicího panelu
description: Naučte se používat integrovaný řídicí panel webového uživatelského rozhraní Kubernetes ke správě clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595344"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Přístup k webovému řídicímu panelu Kubernetes ve službě Azure Kubernetes (AKS)

Kubernetes zahrnuje webový řídicí panel, který se dá použít pro základní operace správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvářet a nasazovat služby a upravovat stávající aplikace. V tomto článku se dozvíte, jak získat přístup k řídicímu panelu Kubernetes pomocí rozhraní příkazového řádku Azure, a pak vás provede několik základních operací řídicích panelů.

Další informace o řídicím panelu Kubernetes najdete v tématu [řídicí panel webového uživatelského rozhraní Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto dokumentu předpokládají, že jste vytvořili cluster AKS a navázali jste `kubectl` připojení k tomuto clusteru. Pokud potřebujete vytvořit cluster AKS, přečtěte si téma [rychlý Start AKS][aks-quickstart].

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu  `az --version` . Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Spuštění řídicího panelu Kubernetes

Řídicí panel Kubernetes spustíte pomocí příkazu [AZ AKS Browse][az-aks-browse] . Následující příklad otevře řídicí panel pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Tento příkaz vytvoří proxy server mezi vývojovým systémem a rozhraním API Kubernetes a otevře webový prohlížeč na řídicím panelu Kubernetes. Pokud se webový prohlížeč neotevře na řídicím panelu Kubernetes, zkopírujte a vložte adresu URL, kterou jste si poznamenali v rozhraní příkazového řádku Azure CLI, obvykle `http://127.0.0.1:8001`.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Pokud cluster AKS používá RBAC, musí být vytvořen *ClusterRoleBinding* , aby bylo možné správně přistupovat k řídicímu panelu. Ve výchozím nastavení je řídicí panel Kubernetes nasazený s minimálním přístupem pro čtení a zobrazuje chyby přístupu RBAC. Řídicí panel Kubernetes v současné době nepodporuje uživatelsky zadané přihlašovací údaje k určení úrovně přístupu, místo toho používá role udělené účtu služby. Správce clusteru se může rozhodnout pro udělení dalšího přístupu k účtu služby *Kubernetes-Dashboard* , ale může se jednat o vektor pro eskalaci oprávnění. Můžete také integrovat Azure Active Directory ověřování a zajistit tak podrobnější úroveň přístupu.
> 
> K vytvoření vazby použijte příkaz [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] . Následující příklad ukazuje, jak vytvořit ukázkovou vazbu, ale Tato vzorová vazba nepoužívá žádné další ověřovací komponenty a může vést k nezabezpečenému použití. Řídicí panel Kubernetes je otevřený pro kohokoli, kdo má přístup k adrese URL. Nezveřejňujte řídicí panel Kubernetes veřejně.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Další informace o používání různých metod ověřování najdete v článku wiki řídicího panelu Kubernetes na [ovládacím prvku Access Controls][dashboard-authentication].

![Stránka s přehledem webového řídicího panelu Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Vytvoření aplikace

Pokud chcete zjistit, jak může řídicí panel Kubernetes snižovat složitost úloh správy, vytvoříme aplikaci. Aplikaci můžete z řídicího panelu Kubernetes vytvořit zadáním textového zadání, souboru YAML nebo pomocí grafického průvodce.

Chcete-li vytvořit aplikaci, proveďte následující kroky:

1. V pravém horním rohu vyberte tlačítko **vytvořit** .
1. Chcete-li použít Průvodce grafickým rozhraním, vyberte možnost **vytvořit aplikaci**.
1. Zadejte název pro nasazení, například *Nginx* .
1. Zadejte název Image kontejneru, která se má použít, například *Nginx: 1.15.5.*
1. K vystavení portu 80 pro webový provoz vytvoříte službu Kubernetes. V části **Služba**vyberte **externí**a potom zadejte **80** pro port i cílový port.
1. Až budete připraveni, vyberte **nasadit** a vytvořte aplikaci.

![Nasazení aplikace na webový řídicí panel Kubernetes](./media/kubernetes-dashboard/create-app.png)

K přiřazení veřejné externí IP adresy službě Kubernetes může trvat minutu nebo dvě. Na levé straně v části **zjišťování a vyrovnávání zatížení** vyberte **služby**. Služba vaší aplikace je uvedena, včetně *externích koncových bodů*, jak je znázorněno v následujícím příkladu:

![Zobrazení seznamu služeb a koncových bodů](./media/kubernetes-dashboard/view-services.png)

Vyberte adresu koncového bodu a otevřete tak okno webového prohlížeče na výchozí stránce NGINX:

![Zobrazit výchozí stránku NGINX nasazené aplikace](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Zobrazit informace pod

Řídicí panel Kubernetes může poskytovat základní metriky monitorování a informace o řešení problémů, jako jsou protokoly.

Pokud chcete zobrazit další informace o luskech aplikací, vyberte v nabídce vlevo možnost **lusky** . Zobrazí se seznam dostupných lusků. Pokud si chcete zobrazit informace, jako je například spotřeba prostředků, vyberte *Nginx* pod.

![Zobrazit informace pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Upravit aplikaci

Kromě vytváření a zobrazování aplikací je možné pomocí řídicího panelu Kubernetes upravit a aktualizovat nasazení aplikací. Abychom zajistili další redundanci pro aplikaci, zvýšili jsme počet NGINX replik.

Postup úpravy nasazení:

1. V nabídce vlevo vyberte **nasazení** a pak zvolte nasazení *Nginx* .
1. V pravém horním navigačním panelu vyberte **Upravit** .
1. Najděte `spec.replica`ovou hodnotu, a to přibližně na řádku 20. Chcete-li zvýšit počet replik pro aplikaci, změňte tuto hodnotu z *1* na *3*.
1. Po přípravě vyberte **aktualizovat** .

![Upravte nasazení a aktualizujte počet replik.](./media/kubernetes-dashboard/edit-deployment.png)

Vytvoření nových lusků v sadě replik může chvíli trvat. V nabídce vlevo zvolte **sady replik**a pak zvolte svou sadu replik *Nginx* . Seznam lusků nyní odráží aktualizovaný počet replik, jak je znázorněno v následujícím příkladu výstupu:

![Zobrazit informace o sadě replik](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Další kroky

Další informace o řídicím panelu Kubernetes najdete na [řídicím panelu webového uživatelského rozhraní Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
