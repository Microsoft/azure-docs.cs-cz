---
title: Instalace linkeru ve službě Azure Kubernetes Service (AKS)
description: Naučte se instalovat a používat linker pro vytvoření sítě v clusteru Azure Kubernetes Service (AKS).
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593707"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Instalace linkeru ve službě Azure Kubernetes Service (AKS)

[Linker][linkerd-github] je open source síť a CNCF se na [inkubaci projektu][linkerd-cncf]. Linker je síť Ultralight, která poskytuje funkce, které zahrnují správu provozu, identitu služby a zabezpečení, spolehlivost a možnosti pozorovatele. Další informace o linkeru naleznete v oficiálních [nejčastějších dotazech][linkerd-faq] a v dokumentaci k [linkerům][linkerd-architecture] .

V tomto článku se dozvíte, jak nainstalovat linker. Linkerový `linkerd` binární soubor klienta je nainstalován do klientského počítače a linkerované komponenty jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Tyto pokyny odkazují na verzi linkeru `stable-2.6.0` .
>
> Linker se `stable-2.6.x` dá spustit na Kubernetes verzích `1.13+` . Další stabilní a hraniční linkery jsou k dispozici ve [verzích linkeru na GitHubu][linkerd-github-releases].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stažení a instalace binárního linkeru linkeru
> * Instalace linkeru v AKS
> * Ověřit Linkerskou instalaci
> * Přístup k řídicímu panelu
> * Odinstalace linkeru z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.13` a vyšší s povoleným RBAC) a navázali jste `kubectl` připojení ke clusteru. Pokud potřebujete s kteroukoli z těchto položek pomáhat, přečtěte si [rychlý Start AKS][aks-quickstart].

Všechna Linkerová Luska musí být naplánována na spuštění v uzlech se systémem Linux – Tato instalace je výchozím nastavením v níže popsané metodě instalace a nevyžaduje žádnou další konfiguraci.

Tento článek odděluje linkery s pokyny k instalaci do několika diskrétních kroků. Výsledek je stejný ve struktuře jako oficiální úvodní [Průvodce][linkerd-getting-started]Začínáme.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Instalace linkeru v AKS

Před instalací linkeru spustíme kontroly před instalací, abyste zjistili, jestli se na náš cluster AKS dá nainstalovat rovina ovládacího prvku:

```console
linkerd check --pre
```

Měl by se zobrazit něco podobného jako u následujícího, což znamená, že váš cluster AKS je platným cílem instalace linkeru:

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

Nyní je čas nainstalovat Linkerované komponenty. Pomocí `linkerd` `kubectl` binárních souborů a nainstalujte linkerované komponenty do clusteru AKS. `linkerd`Automaticky se vytvoří obor názvů a součásti se nainstalují do tohoto oboru názvů.

```console
linkerd install | kubectl apply -f -
```

Linker nasadí určitý počet objektů. Zobrazí se seznam z výstupu `linkerd install` příkazu výše. Nasazení Linkerních komponent by mělo trvat přibližně jednu minutu, v závislosti na prostředí clusteru.

V tomto okamžiku jste nasadili linker s clusterem AKS. Abychom zajistili úspěšné nasazení linkeru, pojďme přejít k další části a [ověřit linkerovou instalaci](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Ověřit Linkerskou instalaci

Potvrďte, že se prostředky úspěšně vytvořily. Použijte příkazy [kubectl Get svc][kubectl-get] a [kubectl Get pod][kubectl-get] k dotazování `linkerd` oboru názvů, ve kterém se linkerové komponenty nainstalovaly `linkerd install` příkazem:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Následující příklad výstupu ukazuje služby a lusky (naplánované na uzlech se systémem Linux), které by nyní měly být spuštěny:

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

Linkerd poskytuje příkaz prostřednictvím `linkerd` binárního souboru klienta pro ověření, že se úspěšně nainstalovala a nakonfigurovala Řídicí rovina ovládacího prvku.

```console
linkerd check
```

Mělo by se zobrazit něco podobného jako v následujícím tématu, které indikuje, že instalace proběhla úspěšně:

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

Linker se dodává s řídicím panelem, který poskytuje přehled o síti a úlohách služby. Pro přístup k řídicímu panelu použijte `linkerd dashboard` příkaz. Tento příkaz využívá [kubectl portů –][kubectl-port-forward] k vytvoření zabezpečeného připojení mezi klientským počítačem a příslušnými lusky v clusteru AKS. Řídicí panel pak bude automaticky otevřen ve výchozím prohlížeči.

```console
linkerd dashboard
```

Příkaz také vytvoří dopředný port a vrátí odkaz na řídicí panely Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Odinstalace linkeru z AKS

> [!WARNING]
> Odstranění linkeru ze spuštěného systému může vést k problémům souvisejícím s provozem mezi vašimi službami. Než budete pokračovat, ujistěte se, že jste provedli správné fungování vašeho systému bez linkeru.

Nejprve bude nutné odebrat proxy roviny dat. Odstraňte jakékoli automatické [Anotace][linkerd-automatic-proxy-injection] injektáže proxy z oboru názvů úlohy a zaveďte nasazení úloh. Vaše úlohy by již neměly mít žádné přidružené součásti roviny dat.

Nakonec odstraňte rovinu ovládacího prvku následujícím způsobem:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Další kroky

Chcete-li prozkoumat další možnosti instalace a konfigurace linkeru, přečtěte si následující oficiální pokyny k linkeru:

- [Linkerem-Helm instalace][linkerd-install-with-helm]
- [Linkerem vytvořená instalace s více fázemi pro oprávnění role][linkerd-multi-stage-installation]

Můžete také postupovat podle dalších scénářů pomocí:

- [Ukázka linkeru emojivoto][linkerd-demo-emojivoto]
- [Ukázka linkerních knih][linkerd-demo-books]

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
