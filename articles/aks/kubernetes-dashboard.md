---
title: Správa clusteru služby Azure Kubernetes pomocí webového řídicího panelu
description: Přečtěte si, jak pomocí integrovaného webového řídicího panelu uživatelského panelu Kubernetes spravovat cluster služby Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595344"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Přístup k webovému řídicímu panelu Kubernetes ve službě Azure Kubernetes Service (AKS)

Kubernetes obsahuje webový řídicí panel, který lze použít pro základní operace správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvářet a nasazovat služby a upravovat stávající aplikace. Tento článek ukazuje, jak získat přístup k řídicímu panelu Kubernetes pomocí azure CLI, pak vás provede některé základní operace řídicího panelu.

Další informace o řídicím panelu Kubernetes najdete v [tématu Kubernetes Web UI Dashboard][kubernetes-dashboard].

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto dokumentu předpokládají, že jste vytvořili `kubectl` cluster AKS a navázali připojení ke clusteru. Pokud potřebujete vytvořit cluster AKS, přečtěte si [úvodní příručku AKS][aks-quickstart].

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu  `az --version` . Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Spuštění řídicího panelu Kubernetes

Chcete-li spustit řídicí panel Kubernetes, použijte příkaz [az aks browse.][az-aks-browse] Následující příklad otevře řídicí panel pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Tento příkaz vytvoří proxy mezi vaším vývojovým systémem a rozhraním API Kubernetes a otevře webový prohlížeč na řídicím panelu Kubernetes. Pokud se webový prohlížeč neotevře na řídicím panelu Kubernetes, zkopírujte a vložte adresu URL uvedenou v nastavení příkazového řádku Azure, obvykle `http://127.0.0.1:8001`.

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
> Pokud váš cluster AKS používá RBAC, *clusterRoleBinding* musí být vytvořen před správně přístup k řídicímu panelu. Ve výchozím nastavení je řídicí panel Kubernetes nasazen s minimálním přístupem pro čtení a zobrazuje chyby přístupu RBAC. Řídicí panel Kubernetes aktuálně nepodporuje pověření zajišťovaná uživatelem k určení úrovně přístupu, ale používá role udělené účtu služby. Správce clusteru se může rozhodnout udělit další přístup k účtu *služby kubernetes-dashboard,* ale může to být vektor pro eskalaci oprávnění. Můžete také integrovat ověřování služby Azure Active Directory a poskytnout tak podrobnější úroveň přístupu.
> 
> Chcete-li vytvořit vazbu, použijte [příkaz create clusterrolebinding kubectl.][kubectl-create-clusterrolebinding] Následující příklad ukazuje, jak vytvořit vzorovou vazbu, ale tato ukázková vazba nepoužije žádné další součásti ověřování a může vést k nezabezpečenému použití. Řídicí panel Kubernetes je otevřen všem, kdo mají přístup k adrese URL. Nezveřejňujte řídicí panel Kubernetes veřejně.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Další informace o používání různých metod ověřování najdete na wiki řídicího panelu Kubernetes o [ovládacích prvcích přístupu][dashboard-authentication].

![Přehledná stránka webového panelu Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Vytvoření aplikace

Chcete-li zjistit, jak řídicí panel Kubernetes může snížit složitost úloh správy, vytvořte aplikaci. Aplikaci můžete vytvořit z řídicího panelu Kubernetes poskytnutím textového vstupu, souboru YAML nebo pomocí grafického průvodce.

Chcete-li vytvořit aplikaci, proveďte následující kroky:

1. V pravém horním okně vyberte tlačítko **Vytvořit.**
1. Chcete-li použít grafického průvodce, zvolte **vytvořit aplikaci**.
1. Zadejte název pro nasazení, například *nginx*
1. Zadejte název obrázku kontejneru, který chcete použít, například *nginx:1.15.5*
1. Chcete-li vystavit port 80 pro webový provoz, vytvořte službu Kubernetes. V části **Služba**vyberte **Externí**a zadejte **80** pro port i cílový port.
1. Až budete připraveni, vyberte **Nasazení** a vytvořte aplikaci.

![Nasazení aplikace na webovém řídicím panelu Kubernetes](./media/kubernetes-dashboard/create-app.png)

Trvá minutu nebo dvě, než bude veřejná externí IP adresa přiřazena službě Kubernetes. Na levé straně velikosti vyberte v části **Zjišťování a vyrovnávání zatížení** **služby**. Služba aplikace je uvedena, včetně *externích koncových bodů*, jak je znázorněno v následujícím příkladu:

![Zobrazit seznam služeb a koncových bodů](./media/kubernetes-dashboard/view-services.png)

Výběrem adresy koncového bodu otevřete okno webového prohlížeče na výchozí stránku NGINX:

![Zobrazení výchozí stránky NGINX nasazené aplikace](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Zobrazit informace o podu

Řídicí panel Kubernetes může poskytovat základní metriky monitorování a informace o řešení potíží, jako jsou protokoly.

Chcete-li zobrazit další informace o podech aplikací, vyberte **pody** v levé nabídce. Zobrazí se seznam dostupných podů. Zvolte *nginx* pod pro zobrazení informací, jako je například spotřeba zdrojů:

![Zobrazit informace o podu](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Úprava aplikace

Kromě vytváření a prohlížení aplikací lze řídicí panel Kubernetes použít k úpravám a aktualizaci nasazení aplikací. Chcete-li poskytnout další redundance pro aplikaci, pojďme zvýšit počet replik NGINX.

Úprava nasazení:

1. V levé nabídce vyberte **Nasazení** a pak zvolte nasazení *nginx.*
1. V pravém horním navigačním panelu vyberte **Upravit.**
1. Vyhledejte `spec.replica` hodnotu na řádku 20. Chcete-li zvýšit počet replik pro aplikaci, změňte tuto hodnotu z *1* na *3*.
1. Vyberte **Aktualizovat,** až bude připraven.

![Úprava nasazení za účelem aktualizace počtu replik](./media/kubernetes-dashboard/edit-deployment.png)

Trvá několik okamžiků pro nové pody, které mají být vytvořeny uvnitř sady replik. V nabídce na levé straně zvolte **Sady replik**a pak zvolte sadu replik *nginx.* Seznam podů nyní odráží aktualizovaný počet replik, jak je znázorněno v následujícím příkladu výstupu:

![Zobrazit informace o sadě replik](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Další kroky

Další informace o řídicím panelu Kubernetes najdete v [ovládacím panelu kubernetes web ui .][kubernetes-dashboard]

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
