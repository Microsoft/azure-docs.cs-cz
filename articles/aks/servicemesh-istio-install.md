---
title: Instalace Istio ve službě Azure Kubernetes (AKS)
description: Naučte se instalovat a používat Istio k vytvoření sítě v clusteru Azure Kubernetes Service (AKS).
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 15b73380269c568977b524a63ca709e352485433
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485212"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalace a použití Istio ve službě Azure Kubernetes Service (AKS)

[Istio][istio-github] je open source síť, která poskytuje klíčovou sadu funkcí napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří Správa provozu, identita služby a zabezpečení, vynucování zásad a jejich pozorování. Další informace o Istio najdete v dokumentaci oficiální dokumentace k [Istio?][istio-docs-concepts] .

V tomto článku se dozvíte, jak nainstalovat Istio. Binární soubor klienta Istio `istioctl` je nainstalován do klientského počítače a součásti Istio jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Následující pokyny odkazují na Istio verze `1.4.0`.
>
> `1.4.x` Istio vydaná vydání byla testována týmem Istio proti verzím Kubernetes `1.13``1.14``1.15`. Další verze Istio najdete na webu [GitHub-Istio releases][istio-github-releases], informace o každé vydané verzi v [Istio News][istio-release-notes] a podporovaných verzích Kubernetes na [Istio obecné Nejčastější dotazy][istio-faq].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stažení a instalace binárního souboru klienta Istio istioctl
> * Instalace Istio na AKS
> * Ověření instalace Istio
> * Přístup k doplňkům
> * Odinstalace Istio z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.13` a novější s povoleným RBAC) a navázali jste `kubectl` spojení s clusterem. Pokud potřebujete s kteroukoli z těchto položek pomáhat, přečtěte si [rychlý Start AKS][aks-quickstart].

Ujistěte se, že jste si přečetli dokumentaci [výkon a škálovatelnost Istio](https://istio.io/docs/concepts/performance-and-scalability/) , abyste pochopili další požadavky na prostředky pro spouštění Istio v clusteru AKS. Požadavky na základní a paměť se budou lišit v závislosti na konkrétním zatížení. Pro instalaci vyberte příslušný počet uzlů a velikost virtuálního počítače.

Tento článek odděluje pokyny k instalaci Istio do několika diskrétních kroků. Konečný výsledek je stejný ve struktuře jako oficiální [návod][istio-install-istioctl]k instalaci Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Instalace součástí Istio v AKS

V rámci naší instalace Istio budeme instalovat [Grafana][grafana] a [Kiali][kiali] . Grafana poskytuje řídicí panely pro analýzy a monitorování a Kiali poskytuje řídicí panel pro pozorování v mřížce služby. V naší instalaci každá z těchto komponent vyžaduje přihlašovací údaje, které je třeba zadat jako [tajný kód][kubernetes-secrets].

Než budeme moct nainstalovat komponenty Istio, je potřeba vytvořit tajné klíče pro Grafana i Kiali. Tyto tajné klíče je potřeba nainstalovat do oboru názvů `istio-system`, který budou používat Istio, takže je potřeba vytvořit taky tento obor názvů. Při vytváření oboru názvů prostřednictvím `kubectl create` musíme použít možnost `--save-config`, aby instalační program Istio mohl v budoucnu spustit `kubectl apply` na tomto objektu.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Nainstalovat komponenty Istio

Teď, když jsme úspěšně vytvořili tajné kódy Grafana a Kiali v našem clusteru AKS, je čas nainstalovat komponenty Istio. 

Přístup k instalaci [Helm][helm] pro Istio se v budoucnu už nepoužívá. Nový přístup k instalaci pro Istio využívá binární soubor klienta `istioctl`, [konfigurační profily Istio][istio-configuration-profiles]a novou [specifikaci roviny ovládacího prvku Istio a rozhraní API][istio-control-plane]. Tento nový přístup se používá k instalaci Istio.

> [!NOTE]
> Istio je v současné době nutné naplánovat na spuštění v uzlech se systémem Linux. Pokud máte v clusteru uzly Windows serveru, musíte zajistit, aby se Istio lusky spouštěly jenom na uzlech se systémem Linux. Použijeme [Selektory uzlů][kubernetes-node-selectors] k ujištění, že lusky jsou naplánované na správných uzlech.

> [!CAUTION]
> [SDS (tajná služba pro zjišťování tajného klíče)][istio-feature-sds] a [Istio CNI][istio-feature-cni] Istio jsou momentálně v [alfa][istio-feature-stages], takže by se mělo před povolením této možnosti udělit. Kromě toho není v aktuálních verzích AKS povolená funkce projekce Kubernetes (požadavek pro SDS) [tokenu účtu služby][kubernetes-feature-sa-projected-volume] .
Vytvořte soubor s názvem `istio.aks.yaml` s následujícím obsahem. Tento soubor bude obsahovat podrobnosti o [specifikaci roviny ovládacího prvku Istio][istio-control-plane] pro konfiguraci Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Nainstalujte istio pomocí příkazu `istioctl apply` a výše `istio.aks.yaml` soubor specifikace roviny ovládacího prvku Istio následujícím způsobem:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Instalační program nasadí celou řadu [CRDs][kubernetes-crd] a pak spravuje závislosti pro instalaci všech relevantních objektů definovaných pro tuto konfiguraci Istio. Měl by se zobrazit něco podobného jako následující výstupní fragment.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

V tomto okamžiku jste nasadili Istio do svého clusteru AKS. Abychom zajistili úspěšné nasazení Istio, pojďme přejít k další části a [ověřit instalaci Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Ověření instalace Istio

Nejprve potvrďte, že byly vytvořeny očekávané služby. K zobrazení spuštěných služeb použijte příkaz [kubectl Get svc][kubectl-get] . Zadejte dotaz na obor názvů `istio-system`, kde byly nainstalovány komponenty Istio a Add-on `istio` graf Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

Následující příklad výstupu ukazuje služby, které by nyní měly být spuštěny:

- služby `istio-*`
- trasovací služby `jaeger-*`, `tracing`a `zipkin`
- `prometheus` služby metriky doplňků
- `grafana` služba řídicího panelu pro analýzu a monitorování doplňků
- Služba `kiali` mřížky služby doplňku služby

Pokud `istio-ingressgateway` zobrazuje externí IP adresu `<pending>`, počkejte několik minut, než se IP adresa přiřadí pomocí sítě Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Pak potvrďte, že byly vytvořeny požadované lusky. Použijte příkaz [kubectl Get lusks][kubectl-get] a znovu proveďte dotaz na `istio-system` obor názvů:

```console
kubectl get pods --namespace istio-system
```

Následující příklad výstupu ukazuje, kde jsou spuštěny tyto lusky:

- `istio-*` lusky
- `prometheus-*` metriky doplňku pod
- řídicí panel `grafana-*` pro analýzu a monitorování níže pod
- řídicí panel mřížky služby `kiali` doplňků pod

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Všechny lusky by měly zobrazovat stav `Running`. Pokud vaše lusky nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělá. Pokud jakékoli lusky nahlásí problém, zkontrolujte výstup a stav pomocí příkazu [kubectl popsat pod][kubectl-describe] .

## <a name="accessing-the-add-ons"></a>Přístup k doplňkům

V naší instalaci jsme nainstalovali několik doplňků, které poskytují další funkce. Webové aplikace pro **doplňky nejsou veřejně vystavené** prostřednictvím externí IP adresy. 

Pro přístup k uživatelským rozhraním doplňku použijte příkaz `istioctl dashboard`. Tento příkaz využívá [kubectl předávaného portu][kubectl-port-forward] a náhodný port k vytvoření zabezpečeného připojení mezi klientským počítačem a relevantním pod v clusteru AKS. Následně se automaticky otevře webová aplikace doplňku ve výchozím prohlížeči.

Přidali jsme další vrstvu zabezpečení pro Grafana a Kiali, a to zadáním přihlašovacích údajů dříve v tomto článku.

### <a name="grafana"></a>Grafana

Řídicí panely pro analýzu a monitorování pro Istio poskytuje [Grafana][grafana]. Nezapomeňte použít přihlašovací údaje, které jste po zobrazení výzvy vytvořili prostřednictvím tajného klíče Grafana. Otevřete řídicí panel Grafana bezpečně následujícím způsobem:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metriky pro Istio poskytuje [Prometheus][prometheus]. Otevřete řídicí panel Prometheus bezpečně následujícím způsobem:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Trasování v rámci Istio zajišťuje [Jaeger][jaeger]. Otevřete řídicí panel Jaeger bezpečně následujícím způsobem:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Řídicí panel pro pozorování sítě služby poskytuje [Kiali][kiali]. Nezapomeňte použít přihlašovací údaje, které jste po zobrazení výzvy vytvořili prostřednictvím tajného klíče Kiali. Otevřete řídicí panel Kiali bezpečně následujícím způsobem:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

K dispozici je jednoduché rozhraní pro proxy [zástupné][envoy] . Poskytuje informace o konfiguraci a metriky pro proxy zástupné spuštěné v zadaném pod. Rozhraní zástupné otevřete bezpečně následujícím způsobem:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Odinstalace Istio z AKS

> [!WARNING]
> Odstranění Istio ze spuštěného systému může vést k problémům souvisejícím s provozem mezi vašimi službami. Než budete pokračovat, ujistěte se, že jste provedli správné fungování vašeho systému bez Istio.

### <a name="remove-istio-components-and-namespace"></a>Odebrat součásti a obor názvů Istio

Pokud chcete odebrat Istio z clusteru AKS, použijte příkaz `istioctl manifest generate` se souborem specifikace roviny ovládacího prvku `istio.aks.yaml` Istio. Tím se vygeneruje nasazený manifest, který se pošle do `kubectl delete`, aby se odebraly všechny nainstalované komponenty a obor názvů `istio-system`.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Odebrat Istio CRDs a tajné klíče

Výše uvedené příkazy odstraní všechny součásti a obor názvů Istio, ale pořád ještě zbývá s generovanými tajnými klíči Istio. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete použít Istio k poskytování inteligentního směrování k zavedení testovací verze:

> [!div class="nextstepaction"]
> [Scénář inteligentního směrování AKS Istio][istio-scenario-routing]

Pokud chcete prozkoumat další možnosti instalace a konfigurace pro Istio, přečtěte si následující oficiální pokyny pro Istio:

- [Istio – instalační příručky][istio-installation-guides]

Můžete také postupovat podle dalších scénářů pomocí:

- [Příklad aplikace Istio Bookinfo][istio-bookinfo-example]

Informace o tom, jak monitorovat aplikaci AKS pomocí Application Insights a Istio, najdete v následující dokumentaci k Azure Monitor:

- [Nulová monitorování aplikací instrumentace pro hostované aplikace Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
