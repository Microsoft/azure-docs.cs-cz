---
title: Instalace linkerdu ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a používat Linkerd k vytvoření sítě služeb v clusteru Služby Azure Kubernetes (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593707"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Instalace linkerdu ve službě Azure Kubernetes Service (AKS)

[Linkerd][linkerd-github] je open-source service mesh a [CNCF inkubační projekt][linkerd-cncf]. Linkerd je síť ultralehkých služeb, která poskytuje funkce, které zahrnují správu provozu, identitu služby a zabezpečení, spolehlivost a pozorovatelnost. Další informace o linkerd, naleznete v [oficiálnílinkerd FAQ][linkerd-faq] a [Linkerd architektura][linkerd-architecture] dokumentace.

Tento článek ukazuje, jak nainstalovat Linkerd. Binární soubor `linkerd` klienta Linkerd je nainstalován do klientského počítače a součásti Linkerd jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Tyto pokyny odkazují na `stable-2.6.0`verzi Linkerd .
>
> Linkerd `stable-2.6.x` lze spustit proti verzím Kubernetes `1.13+`. Další stabilní a hraniční verze Linkerd najdete na [GitHubu - Linkerd Releases][linkerd-github-releases].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stažení a instalace binárního souboru klienta Linkerd Linkerd
> * Instalace linkerdu na AKS
> * Ověření instalace linkerd
> * Přístup k řídicímu panelu
> * Odinstalace linkerdu z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.13` a `kubectl` výše, s povoleným RBAC) a navázali připojení ke clusteru. Pokud potřebujete pomoc s některou z těchto položek, naleznete [v aks rychlý start][aks-quickstart].

Všechny linkerdové pody musí být naplánovány tak, aby běžely na linuxových uzlech - toto nastavení je výchozí v níže uvedené metodě instalace a nevyžaduje žádnou další konfiguraci.

Tento článek odděluje linkerd instalace pokyny do několika samostatných kroků. Výsledek je ve struktuře stejný jako oficiální [linkerd][linkerd-getting-started]začínáte pokyny .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Instalace linkerdu na AKS

Než nainstalujeme Linkerd, spustíme kontroly před instalací, abychom zjistili, zda lze řídicí rovinu nainstalovat do našeho clusteru AKS:

```console
linkerd check --pre
```

Mělo by se zobrazit něco jako následující, které označuje, že cluster AKS je platný cíl instalace pro Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Nyní je čas nainstalovat součásti Linkerd. Pomocí `linkerd` binárních souborů a `kubectl` nainstalujte součásti Linkerd do clusteru AKS. Bude `linkerd` automaticky vytvořen obor názvů a součásti budou nainstalovány do tohoto oboru názvů.

```console
linkerd install | kubectl apply -f -
```

Linkerd nasazuje počet objektů. Zobrazí se seznam z výstupu příkazu `linkerd install` výše. Nasazení součástí Linkerd by mělo trvat přibližně 1 minutu, v závislosti na prostředí clusteru.

V tomto okamžiku jste nasadili Linkerd do clusteru AKS. Chcete-li zajistit úspěšné nasazení linkerd, přejdeme k další části [k ověření instalace Linkerd](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Ověření instalace linkerd

Zkontrolujte, zda byly prostředky úspěšně vytvořeny. Pomocí příkazů [get svc][kubectl-get] a [kubectl get pod][kubectl-get] použijte `linkerd` příkazy get pod pro dotaz na `linkerd install` obor názvů, kde byly komponenty Linkerd nainstalovány příkazem:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Následující příklad výstupu ukazuje služby a pody (naplánované na linuxových uzlech), které by nyní měly být spuštěny:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd poskytuje příkaz `linkerd` prostřednictvím binárního klienta k ověření, že rovina řízení Linkerd byla úspěšně nainstalována a nakonfigurována.

```console
linkerd check
```

Měli byste vidět něco jako následující, které označuje, že vaše instalace byla úspěšná:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Přístup k řídicímu panelu

Linkerd je dodáván s řídicím panelem, který poskytuje přehled o síti služeb a úlohách. Chcete-li získat přístup `linkerd dashboard` k řídicímu panelu, použijte příkaz. Tento příkaz využívá [port ubectl vpřed][kubectl-port-forward] k vytvoření zabezpečeného připojení mezi klientským počítačem a příslušnými pody v clusteru AKS. Poté automaticky otevře řídicí panel ve výchozím prohlížeči.

```console
linkerd dashboard
```

Příkaz také vytvoří port-forward a vrátí odkaz pro řídicí panely Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Odinstalace linkerdu z AKS

> [!WARNING]
> Odstranění linkerdu ze spuštěného systému může mít za následek problémy související s provozem mezi vašimi službami. Ujistěte se, že jste před pokračováním zajistili, že váš systém bude stále fungovat správně bez linkerdu.

Nejprve budete muset odebrat proxy datové roviny. Odeberte všechny [poznámky][linkerd-automatic-proxy-injection] automatického vkládání proxy prostředků z oborů názvů úloh a zaveďte nasazení úloh. Vaše úlohy by již neměly mít žádné přidružené součásti roviny dat.

Nakonec odstraňte rovinu ovládacího prvku následujícím způsobem:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Další kroky

Další možnosti instalace a konfigurace linkerdu naleznete v následujících oficiálních pokynech pro linkerd:

- [Linkerd - Instalace helmu][linkerd-install-with-helm]
- [Linkerd - Vícestupňová instalace pro zajištění oprávnění role][linkerd-multi-stage-installation]

Můžete také sledovat další scénáře pomocí:

- [Linkerd emojivoto demo][linkerd-demo-emojivoto]
- [Linkerd knihy demo][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
