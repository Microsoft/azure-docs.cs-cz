---
title: Správa clusteru služby Azure Kubernetes pomocí webového řídicího panelu
description: Naučte se používat integrovaný řídicí panel webového uživatelského rozhraní Kubernetes ke správě clusteru Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: a80082ac524a4777b3b5ee32d946e9db8ec6e7f5
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681614"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Přístup k webovému řídicímu panelu Kubernetes ve službě Azure Kubernetes (AKS)

Kubernetes zahrnuje webový řídicí panel, který se dá použít pro základní operace správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvářet a nasazovat služby a upravovat stávající aplikace. V tomto článku se dozvíte, jak získat přístup k řídicímu panelu Kubernetes pomocí rozhraní příkazového řádku Azure, a pak vás provede několik základních operací řídicích panelů.

Další informace o řídicím panelu Kubernetes najdete v tématu [řídicí panel webového uživatelského rozhraní Kubernetes][kubernetes-dashboard]. AKS používá verzi 2,0 a větší z open source řídicího panelu.

> [!WARNING]
> **Doplněk řídicího panelu AKS je nastaven pro vyřazení. Místo toho použijte [zobrazení prostředků Kubernetes v Azure Portal (Preview)][kubernetes-portal] .** 
> * Řídicí panel Kubernetes je ve výchozím nastavení povolen pro clustery se systémem Kubernetes verze nižší než 1,18.
> * Doplněk řídicího panelu bude ve výchozím nastavení zakázán pro všechny nové clustery vytvořené v Kubernetes 1,18 nebo novějším. 
 > * AKS ve verzi Preview od verze Kubernetes 1,19 už nebude podporovat instalaci spravovaného doplňku Kube-Dashboard. 
 > * Existující clustery s povoleným doplňkem nebudou ovlivněny. Uživatelé budou moci ručně nainstalovat open source řídicí panel jako software nainstalovaný uživatelem.

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto dokumentu předpokládají, že jste vytvořili cluster AKS a navázali připojení k `kubectl` tomuto clusteru. Pokud potřebujete vytvořit cluster AKS, přečtěte si téma [rychlý Start: nasazení clusteru služby Azure Kubernetes pomocí rozhraní příkazového řádku Azure][aks-quickstart].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Zakázání řídicího panelu Kubernetes

Doplněk Kube-Dashboard je **ve výchozím nastavení povolen v clusterech starších než K8s 1,18**. Doplněk lze zakázat spuštěním následujícího příkazu.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Spuštění řídicího panelu Kubernetes

Chcete-li spustit řídicí panel Kubernetes v clusteru, použijte příkaz [AZ AKS Browse][az-aks-browse] . Tento příkaz vyžaduje instalaci doplňku Kube-Dashboard na cluster, který je ve výchozím nastavení zahrnutý v clusterech s libovolnou verzí starší než Kubernetes 1,18.

Následující příklad otevře řídicí panel pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Tento příkaz vytvoří proxy server mezi vývojovým systémem a rozhraním API Kubernetes a otevře webový prohlížeč na řídicím panelu Kubernetes. Pokud se webový prohlížeč neotevře na řídicím panelu Kubernetes, zkopírujte a vložte adresu URL, kterou jste si poznamenali v Azure CLI, obvykle `http://127.0.0.1:8001` .

> [!NOTE]
> Pokud řídicí panel nevidíte na `http://127.0.0.1:8001` , můžete ručně směrovat na následující adresy. Clustery na 1,16 nebo více použití HTTPS a vyžadují samostatný koncový bod.
> * K8s 1,16 nebo vyšší: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1,15 a nižší: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Přihlaste se na řídicí panel (Kubernetes 1.16 +).

> [!IMPORTANT]
> Od verze [1.10.1 řídicího panelu Kubernetes](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) nebo Kubernetes v 1.16 + účet služby "Kubernetes-Dashboard" již nelze použít k načtení prostředků z důvodu [opravy zabezpečení v této verzi](https://github.com/kubernetes/dashboard/pull/3400). V důsledku toho požadavky bez ověřovacích údajů vrátí [chybu 401 s neoprávněným přístupem](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). Nosný token načtený z účtu služby se pořád používá jako v tomto [příkladu řídicího panelu Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), ale to má vliv na tok přihlášení doplňku na řídicí panel v porovnání se staršími verzemi.
>
>Pokud pořád pracujete s verzí starší než 1,16, můžete přesto udělit oprávnění k účtu služby Kubernetes-Dashboard, ale **nedoporučuje** se to:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Úvodní zobrazená obrazovka vyžaduje kubeconfig nebo token. Obě možnosti vyžadují oprávnění prostředků pro zobrazení těchto prostředků na řídicím panelu.

![přihlašovací obrazovka](./media/kubernetes-dashboard/login.png)

**Použití kubeconfig**

Pro clustery s podporou Azure AD i mimo Azure AD se dá kubeconfig předat. Zajistěte, aby byly tokeny přístupu platné, pokud vypršela platnost tokenů, můžete tokeny aktualizovat prostřednictvím kubectl.

1. Nastavte kubeconfig správce na `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Výběrem `Kubeconfig` a kliknutím `Choose kubeconfig file` Otevřete selektor souborů.
1. Vyberte soubor kubeconfig (výchozí nastavení $HOME/.Kube/config).
1. Klikněte na `Sign In`.

**Použití tokenu**

1. Pro **cluster, který není povolený pro Azure AD**, spusťte `kubectl config view` a zkopírujte token přidružený k uživatelskému účtu vašeho clusteru.
1. Vložte do možnosti token při přihlášení.    
1. Klikněte na `Sign In`.

U clusterů s podporou Azure AD načtěte token AAD pomocí následujícího příkazu. Ověřte, že jste v příkazu nahradili skupinu prostředků a název clusteru.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Po úspěšném zobrazení se zobrazí stránka podobná této.

![Stránka s přehledem webového řídicího panelu Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Vytvoření aplikace

Následující kroky vyžadují, aby měl uživatel oprávnění pro příslušné prostředky. 

Pokud chcete zjistit, jak může řídicí panel Kubernetes snižovat složitost úloh správy, vytvoříme aplikaci. Aplikaci můžete z řídicího panelu Kubernetes vytvořit zadáním textového zadání, souboru YAML nebo pomocí grafického průvodce.

Chcete-li vytvořit aplikaci, proveďte následující kroky:

1. V pravém horním rohu vyberte tlačítko **vytvořit** .
1. Chcete-li použít Průvodce grafickým rozhraním, vyberte možnost **vytvořit aplikaci**.
1. Zadejte název pro nasazení, například *Nginx* .
1. Zadejte název Image kontejneru, která se má použít, například *Nginx: 1.15.5.*
1. K vystavení portu 80 pro webový provoz vytvoříte službu Kubernetes. V části **Služba** vyberte **externí** a potom zadejte **80** pro port i cílový port.
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
1. Vyhledejte `spec.replica` hodnotu, při přibližně na řádku 20. Chcete-li zvýšit počet replik pro aplikaci, změňte tuto hodnotu z *1* na *3*.
1. Po přípravě vyberte **aktualizovat** .

![Upravte nasazení a aktualizujte počet replik.](./media/kubernetes-dashboard/edit-deployment.png)

Vytvoření nových lusků v sadě replik může chvíli trvat. V nabídce vlevo zvolte **sady replik** a pak zvolte svou sadu replik *Nginx* . Seznam lusků nyní odráží aktualizovaný počet replik, jak je znázorněno v následujícím příkladu výstupu:

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
[kubernetes-portal]: ./kubernetes-portal.md
