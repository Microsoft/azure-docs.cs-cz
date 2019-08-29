---
title: Správa clusteru služby Azure Kubernetes pomocí webového řídicího panelu
description: Naučte se používat integrovaný řídicí panel webového uživatelského rozhraní Kubernetes ke správě clusteru Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126888"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Přístup k webovému řídicímu panelu Kubernetes ve službě Azure Kubernetes (AKS)

Kubernetes zahrnuje webový řídicí panel, který se dá použít pro základní operace správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvářet a nasazovat služby a upravovat stávající aplikace. V tomto článku se dozvíte, jak získat přístup k řídicímu panelu Kubernetes pomocí rozhraní příkazového řádku Azure, a pak vás provede několik základních operací řídicích panelů.

Další informace o řídicím panelu Kubernetes najdete v tématu [řídicí panel webového uživatelského rozhraní Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Před zahájením

Kroky popsané v tomto dokumentu předpokládají, že jste vytvořili cluster AKS a navázali `kubectl` připojení k tomuto clusteru. Pokud potřebujete vytvořit cluster AKS, přečtěte si téma [rychlý Start AKS][aks-quickstart].

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu  `az --version` . Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Spuštění řídicího panelu Kubernetes

Řídicí panel Kubernetes spustíte pomocí příkazu [AZ AKS Browse][az-aks-browse] . Následující příklad otevře řídicí panel pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Tento příkaz vytvoří proxy server mezi vývojovým systémem a rozhraním API Kubernetes a otevře webový prohlížeč na řídicím panelu Kubernetes. Pokud se webový prohlížeč neotevře na řídicím panelu Kubernetes, zkopírujte a vložte adresu URL, kterou jste si poznamenali `http://127.0.0.1:8001`v Azure CLI, obvykle.

![Přihlašovací stránka webového řídicího panelu Kubernetes](./media/kubernetes-dashboard/dashboard-login.png)

K přihlášení na řídicí panel clusteru máte následující možnosti:

* [Kubeconfig soubor][kubeconfig-file]. Soubor kubeconfig můžete vygenerovat pomocí [AZ AKS Get-Credentials][az-aks-get-credentials].
* Token, například [token účtu služby][aks-service-accounts] nebo token uživatele. U [clusterů s podporou AAD][aad-cluster]by tento token byl token AAD. K vypsání tokenů v souboru kubeconfig můžete použít `kubectl config view` . Další podrobnosti o vytvoření tokenu AAD pro použití s clusterem AKS najdete v tématu věnovaném [integraci Azure Active Directory se službou Azure Kubernetes pomocí rozhraní příkazového řádku Azure][aad-cluster].
* Výchozí účet služby řídicího panelu, který se použije, když kliknete na *Přeskočit*.

> [!WARNING]
> Nikdy veřejně zveřejňujte řídicí panel Kubernetes bez ohledu na použitou metodu ověřování.
> 
> Při nastavování ověřování pro řídicí panel Kubernetes se doporučuje použít token pro výchozí účet služby řídicího panelu. Token umožňuje každému uživateli používat vlastní oprávnění. Použití výchozího účtu služby řídicího panelu může uživateli dovolit obejít vlastní oprávnění a místo toho použít účet služby.
> 
> Pokud se rozhodnete použít výchozí účet služby řídicího panelu a váš cluster AKS používá RBAC, musí se vytvořit *ClusterRoleBinding* , aby bylo možné správně přistupovat k řídicímu panelu. Ve výchozím nastavení je řídicí panel Kubernetes nasazený s minimálním přístupem pro čtení a zobrazuje chyby přístupu RBAC. Správce clusteru se může rozhodnout pro udělení dalšího přístupu k účtu služby *Kubernetes-Dashboard* , ale může se jednat o vektor pro eskalaci oprávnění. Můžete také integrovat Azure Active Directory ověřování a zajistit tak podrobnější úroveň přístupu.
>
> Chcete-li vytvořit vazbu, použijte příkaz [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] , jak je znázorněno v následujícím příkladu. **Tato ukázková vazba nepoužívá žádné další ověřovací součásti a může vést k nezabezpečenému použití.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Teď máte přístup k řídicímu panelu Kubernetes v clusteru s podporou RBAC. Řídicí panel Kubernetes spustíte tak, že použijete příkaz [AZ AKS Browse][az-aks-browse] , který je podrobně popsaný v předchozím kroku.
>
> Pokud váš cluster nepoužívá RBAC, nedoporučuje se vytvořit *ClusterRoleBinding*.
> 
> Další informace o používání různých metod ověřování najdete v článku wiki řídicího panelu Kubernetes na [ovládacím prvku Access Controls][dashboard-authentication].

Po výběru metody pro přihlášení se zobrazí řídicí panel Kubernetes. Pokud se rozhodnete použít *token* nebo *Přeskočit*, bude řídicí panel Kubernetes používat pro přístup ke clusteru oprávnění aktuálně přihlášeného uživatele.

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

Pokud chcete zobrazit další informace o luskech aplikací, vyberte v nabídce vlevo možnost lusky. Zobrazí se seznam dostupných lusků. Pokud si chcete zobrazit informace, jako je například spotřeba prostředků, vyberte *Nginx* pod.

![Zobrazit informace pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Upravit aplikaci

Kromě vytváření a zobrazování aplikací je možné pomocí řídicího panelu Kubernetes upravit a aktualizovat nasazení aplikací. Abychom zajistili další redundanci pro aplikaci, zvýšili jsme počet NGINX replik.

Postup úpravy nasazení:

1. V nabídce vlevo vyberte **nasazení** a pak zvolte nasazení *Nginx* .
1. V pravém horním navigačním panelu vyberte **Upravit** .
1. `spec.replica` Vyhledejte hodnotu, při přibližně na řádku 20. Chcete-li zvýšit počet replik pro aplikaci, změňte tuto hodnotu z *1* na *3*.
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
