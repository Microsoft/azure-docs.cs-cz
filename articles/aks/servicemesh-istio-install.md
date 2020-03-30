---
title: Instalace Istia ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a používat Istio k vytvoření sítě služeb v clusteru Služby Azure Kubernetes (AKS).
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f0fe4ab46bfe5c0c0c2ea67aa2e2694321628be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136359"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalace a použití Istio ve službě Azure Kubernetes Service (AKS)

[Istio][istio-github] je síť služeb s otevřeným zdrojovým kódem, která poskytuje sadu funkcí klíče napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří správa provozu, identita a zabezpečení služby, vynucení zásad a pozorovatelnost. Další informace o Istiu naleznete v oficiální dokumentaci [Co je Istio?][istio-docs-concepts]

Tento článek ukazuje, jak nainstalovat Istio. Binární binární `istioctl` soubor klienta Istio je nainstalován do klientského počítače a součásti Istio jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Následující pokyny odkazují na `1.4.0`verzi Istio .
>
> Verze `1.4.x` Istio byly testovány týmem Istio proti verzím `1.13`Kubernetes , `1.14`. . `1.15` Další verze Istio najdete na [GitHubu - Istio Releases][istio-github-releases], informace o každé verzi na [Istio News][istio-release-notes] a podporované verze Kubernetes na [Istio General FAQ][istio-faq].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stáhněte a nainstalujte binární soubor klienta Istio istioctl
> * Instalace Istio na AKS
> * Ověření instalace Istio
> * Přístup k doplňkům
> * Odinstalovat Istio z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.13` a `kubectl` výše, s povoleným RBAC) a navázali připojení ke clusteru. Pokud potřebujete pomoc s některou z těchto položek, naleznete [v aks rychlý start][aks-quickstart].

Ujistěte se, že jste si přečetli dokumentaci [istio výkonu a škálovatelnosti](https://istio.io/docs/concepts/performance-and-scalability/) pochopit další požadavky na prostředky pro spuštění Istio v clusteru AKS. Základní a požadavky na paměť se budou lišit v závislosti na konkrétním pracovním vytížení. Zvolte vhodný počet uzlů a velikost virtuálních her, abyste se postarali o vaše nastavení.

Tento článek odděluje pokyny k instalaci Istio do několika samostatných kroků. Konečný výsledek má stejnou strukturu jako oficiální [pokyny k][istio-install-istioctl]instalaci Istio .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Instalace součástí Istio na AKS

Budeme instalovat [Grafana][grafana] a [Kiali][kiali] jako součást naší instalace Istio. Grafana poskytuje analytické a monitorovací řídicí panely a Kiali poskytuje řídicí panel viditelnosti sítě služeb. V našem nastavení každá z těchto součástí vyžaduje pověření, která musí být poskytnuta jako [tajný klíč][kubernetes-secrets].

Než budeme moci nainstalovat istio komponenty, musíme vytvořit tajemství jak pro Grafana a Kiali. Tyto tajné klíče je `istio-system` třeba nainstalovat do oboru názvů, který bude používán Istio, takže budeme muset vytvořit obor názvů příliš. Musíme použít `--save-config` možnost při vytváření oboru `kubectl create` názvů přes tak, aby `kubectl apply` istio instalátor může běžet na tento objekt v budoucnu.

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

### <a name="install-istio-components"></a>Instalace součástí Istio

Nyní, když jsme úspěšně vytvořili tajemství Grafany a Kiali v našem clusteru AKS, je čas nainstalovat součásti Istio. 

Instalační přístup [helmu][helm] pro Istio bude v budoucnu zastaral. Nový přístup k instalaci pro Istio využívá `istioctl` binární klient, [istio konfigurační profily][istio-configuration-profiles]a nové [istio řízení rovina spec a api][istio-control-plane]. Tento nový přístup je to, co budeme používat k instalaci Istio.

> [!NOTE]
> Istio musí být aktuálně naplánováno na spuštění na linuxových uzlech. Pokud máte v clusteru uzly Windows Server, musíte zajistit, aby pody Istio byly naplánované pouze na linuxových uzlech. Budeme používat [voliče uzlů,][kubernetes-node-selectors] abychom se ujistili, že pody jsou naplánovány na správné uzly.

> [!CAUTION]
> [SDS (tajná discovery service)][istio-feature-sds] a [Istio CNI][istio-feature-cni] Istio funkce jsou v současné době v [Alfa][istio-feature-stages], tak si myslel, že by měla být uvedena před povolením těchto. Kromě toho funkce Kubernetes [tokenu tokenu účtu služby][kubernetes-feature-sa-projected-volume] (požadavek na SDS) není povolena v aktuálních verzích AKS.
Vytvořte soubor `istio.aks.yaml` s následujícím obsahem. Tento soubor bude obsahovat [istio ovládací roviny spec][istio-control-plane] podrobnosti pro konfiguraci Istio.

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

Nainstalujte istio `istioctl apply` pomocí příkazu a výše uvedeného `istio.aks.yaml` istio ovládací roviny spec soubor takto:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Instalační program nasadí několik [crd ů][kubernetes-crd] a poté bude spravovat závislosti za účelem instalace všech příslušných objektů definovaných pro tuto konfiguraci istio. Měli byste vidět něco jako následující výstřižek výstupu.

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

V tomto okamžiku jste istio nasadili do clusteru AKS. Abychom zajistili úspěšné nasazení Istia, přejdeme k další části [k ověření instalace Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Ověření instalace Istio

Nejprve potvrďte, že byly vytvořeny očekávané služby. Pomocí příkazu [kubectl get svc][kubectl-get] zobrazte spuštěné služby. Dotaz `istio-system` na obor názvů, kde byly istio a doplňkové `istio` součásti nainstalovány grafem Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

Následující příklad výstupu ukazuje služby, které by nyní měly být spuštěny:

- `istio-*`Služby
- `jaeger-*`, `tracing`a `zipkin` doplňkové služby trasování
- `prometheus`služba doplňkových metrik
- `grafana`doplňková analýza a monitorovací služba řídicího panelu
- `kiali`služba řídicího panelu sítě služby pro doplňkovou službu

Pokud `istio-ingressgateway` se zobrazí externí `<pending>`IP adresa , počkejte několik minut, dokud síť Azure nepřiřadí IP adresu.

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

Dále potvrďte, že byly vytvořeny požadované pody. Použijte příkaz [kubectl get pods][kubectl-get] a `istio-system` znovu se dotazte na obor názvů:

```console
kubectl get pods --namespace istio-system
```

Následující příklad výstup udává pody, které jsou spuštěny:

- lusky `istio-*`
- pod `prometheus-*` metrik y doplňků
- analýza `grafana-*` a monitorování panelového panelu pro doplňky
- panel `kiali` řídicího panelu mřížky pro přispoty služby

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

Všechny lusky by měly `Running`zobrazovat stav . Pokud vaše moduly nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělají. Pokud některé pody hlásí problém, použijte [příkaz kubectl describe pod][kubectl-describe] ke kontrole jejich výstupu a stavu.

## <a name="accessing-the-add-ons"></a>Přístup k doplňkům

Společnost Istio nainstalovala do výše uvedeného nastavení několik doplňků, které poskytují další funkce. Webové aplikace pro doplňky **nejsou** veřejně vystaveny prostřednictvím externí IP adresy. 

Chcete-li získat přístup k uživatelským `istioctl dashboard` rozhraním doplňků, použijte příkaz. Tento příkaz využívá [kubectl port-forward][kubectl-port-forward] a náhodný port k vytvoření zabezpečeného připojení mezi klientským počítačem a příslušným modulem v clusteru AKS. Poté automaticky otevře doplňkovou webovou aplikaci ve výchozím prohlížeči.

Přidali jsme další vrstvu zabezpečení pro Grafana a Kiali zadáním pověření pro ně dříve v tomto článku.

### <a name="grafana"></a>Grafana

Analytické a monitorovací řídicí panely pro Istio poskytuje [Grafana][grafana]. Nezapomeňte použít přihlašovací údaje, které jste vytvořili prostřednictvím tajného klíče Grafana dříve, když se zobrazí výzva. Otevřete řídicí panel Grafana bezpečně takto:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metriky pro Istio poskytuje [Prometheus][prometheus]. Otevřete přístrojovou desku Prometheus bezpečně takto:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Trasování v Istiu zajišťuje [Jaeger][jaeger]. Otevřete jaegerovou řídicí panel bezpečně takto:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali (Kiali)

Ovládací panel pro pozorovatelnost servisní sítě je poskytován společností [Kiali][kiali]. Nezapomeňte použít přihlašovací údaje, které jste vytvořili prostřednictvím tajného klíče Kiali dříve, když se zobrazí výzva. Otevřete řídicí panel Kiali bezpečně takto:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

K dispozici je jednoduché rozhraní pro proxy [vyslanců.][envoy] Poskytuje informace o konfiguraci a metriky pro proxy server pro vyslance spuštěné v zadaném podu. Bezpečně otevřete rozhraní vyslance takto:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Odinstalovat Istio z AKS

> [!WARNING]
> Odstranění Istio ze spuštěného systému může mít za následek problémy související s provozem mezi vašimi službami. Ujistěte se, že jste před pokračováním zajistili, že váš systém bude fungovat správně bez istio.

### <a name="remove-istio-components-and-namespace"></a>Odebrání součástí istio a oboru názvů

Chcete-li odebrat Istio z clusteru AKS, použijte `istioctl manifest generate` příkaz se souborem specifikace řídicí roviny `istio.aks.yaml` Istio. Tím se vygeneruje nasazený manifest, `kubectl delete` do kterého se budeme potácet, abychom odstranili všechny nainstalované součásti a obor `istio-system` názvů.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Odebrat crd istio a tajné klíče

Výše uvedené příkazy odstranit všechny součásti Istio a obor názvů, ale stále jsme vlevo s generované Istio tajných kódů. 

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

Následující dokumentace popisuje, jak můžete pomocí istio poskytnout inteligentní směrování k zavedení kanárské verze:

> [!div class="nextstepaction"]
> [Inteligentní scénář směrování AKS Istio][istio-scenario-routing]

Chcete-li prozkoumat další možnosti instalace a konfigurace pro Istio, podívejte se na následující oficiální pokyny Istio:

- [Istio - instalační průvodce][istio-installation-guides]

Můžete také sledovat další scénáře pomocí:

- [Příklad aplikace Istio Bookinfo][istio-bookinfo-example]

Informace o tom, jak sledovat aplikaci AKS pomocí aplikací Insights a Istio, najdete v následující dokumentaci k Azure Monitoru:

- [Monitorování aplikací nulové instrumentace pro hostované aplikace Kubernetes][app-insights]

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
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
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
