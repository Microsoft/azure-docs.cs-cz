---
title: Instalace Istio ve službě Azure Kubernetes (AKS)
description: Naučte se instalovat a používat Istio k vytvoření sítě v clusteru Azure Kubernetes Service (AKS).
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 285fa34db3886cf405a3682438a27a17c75d81ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666693"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalace a použití Istio ve službě Azure Kubernetes Service (AKS)

[Istio][istio-github] je open source síť, která poskytuje klíčovou sadu funkcí napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří Správa provozu, identita služby a zabezpečení, vynucování zásad a jejich pozorování. Další informace o Istio najdete v dokumentaci oficiální dokumentace k [Istio?][istio-docs-concepts] .

V tomto článku se dozvíte, jak nainstalovat Istio. `istioctl`Binární soubor klienta Istio se nainstaluje do klientského počítače a součásti Istio se nainstalují do clusteru Kubernetes v AKS.

> [!NOTE]
> Následující pokyny odkazují na verzi Istio `1.7.3` .
>
> Istio vydané verze byly `1.7.x` testovány týmem Istio proti verzi Kubernetes `1.16+` . Další verze Istio najdete na webu [GitHub-Istio releases][istio-github-releases], informace o každé vydané verzi v [Istio News][istio-release-notes] a podporovaných verzích Kubernetes na [Istio obecné Nejčastější dotazy][istio-faq].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stažení a instalace binárního souboru klienta Istio istioctl
> * Instalace Istio na AKS
> * Ověření instalace Istio
> * Přístup k doplňkům
> * Odinstalace Istio z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.16` a vyšší s povoleným RBAC) a navázali jste `kubectl` připojení ke clusteru. Pokud potřebujete s kteroukoli z těchto položek pomáhat, přečtěte si [rychlý Start AKS][aks-quickstart].

Ujistěte se, že jste si přečetli dokumentaci [výkon a škálovatelnost Istio](https://istio.io/docs/concepts/performance-and-scalability/) , abyste pochopili další požadavky na prostředky pro spouštění Istio v clusteru AKS. Požadavky na základní a paměť se budou lišit v závislosti na konkrétním zatížení. Pro instalaci vyberte příslušný počet uzlů a velikost virtuálního počítače.

Tento článek odděluje pokyny k instalaci Istio do několika diskrétních kroků. Konečný výsledek je stejný ve struktuře jako oficiální [návod][istio-install-istioctl]k instalaci Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Instalace operátoru Istio na AKS

Istio poskytuje [operátor][istio-install-operator] ke správě instalace a aktualizací komponent Istio v rámci vašeho clusteru AKS. Pomocí `istioctl` binárního souboru klienta nainstalujeme operátor Istio.

```bash
istioctl operator init
```

Měli byste vidět něco jako v následujícím výstupu a ověřit, zda byl nainstalován operátor Istio.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Operátor Istio je nainstalován do `istio-operator` oboru názvů. Dotaz na obor názvů.

```bash
kubectl get all -n istio-operator
```

Měli byste vidět následující komponenty nasazené.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Můžete získat další informace o vzoru operátoru a o tom, jak může pomoci automatizovat složité úlohy prostřednictvím [Kubernetes.IO][kubernetes-operator].


### <a name="install-istio-components"></a>Nainstalovat komponenty Istio

Teď, když jsme úspěšně nainstalovali operátor Istio do našeho clusteru AKS, je čas nainstalovat komponenty Istio. 

Použijeme `default` [profil konfigurace Istio][istio-configuration-profiles] k vytvoření [specifikace operátoru Istio][istio-control-plane].

Spuštěním následujícího `istioctl` příkazu můžete zobrazit konfiguraci pro `default` konfigurační profil Istio.

```bash
istioctl profile dump default
```

> [!NOTE]
> Istio je v současné době nutné naplánovat na spuštění v uzlech se systémem Linux. Pokud máte v clusteru uzly Windows serveru, musíte zajistit, aby se Istio lusky spouštěly jenom na uzlech se systémem Linux. Použijeme [Selektory uzlů][kubernetes-node-selectors] k ujištění, že lusky jsou naplánované na správných uzlech.

> [!CAUTION]
> Funkce [ISTIO CNI][istio-feature-cni] Istio jsou momentálně v [alfa][istio-feature-stages], proto by se měla před povolením předávat. 

Vytvořte soubor s názvem `istio.aks.yaml` s následujícím obsahem. Tento soubor bude obsahovat [specifikace operátoru Istio][istio-control-plane] pro konfiguraci Istio.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Vytvořte `istio-system` obor názvů a nasaďte do tohoto oboru názvů specifikaci operátoru Istio. Istio operátor bude sledovat pro specifikaci operátoru Istio a použije ho k instalaci a konfiguraci Istio v clusteru AKS.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

V tomto okamžiku jste nasadili Istio do svého clusteru AKS. Abychom zajistili úspěšné nasazení Istio, pojďme přejít k další části a [ověřit instalaci Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Ověření instalace Istio

Dotaz na `istio-system` obor názvů, ve kterém byly součásti Istio a Add-on nainstalovány pomocí operátoru Istio:

```bash
kubectl get all -n istio-system
```

Měli byste vidět následující komponenty:

- `istio*` – součásti Istio
- `jaeger-*``tracing`doplněk, a `zipkin` -trasování
- `prometheus` – doplněk metriky
- `grafana` – doplněk pro analýzu a monitorování řídicího panelu
- `kiali` – doplněk na řídicím panelu mřížky služby

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Další vhled k instalaci můžete získat také tak, že si vyhledáte v protokolech operátor Istio.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Pokud se `istio-ingressgateway` zobrazí externí IP adresa `<pending>` , počkejte několik minut, než se IP adresa přiřadí pomocí sítě Azure.

Všechny lusky by měly zobrazit stav `Running` . Pokud vaše lusky nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělá. Pokud jakékoli lusky nahlásí problém, zkontrolujte výstup a stav pomocí příkazu [kubectl popsat pod][kubectl-describe] .

## <a name="accessing-the-add-ons"></a>Přístup k doplňkům

Istio operátor nainstaloval mnoho doplňků, které poskytují další funkce. Webové aplikace pro **doplňky nejsou veřejně vystavené** prostřednictvím externí IP adresy. 

Pro přístup k uživatelským rozhraním doplňku použijte `istioctl dashboard` příkaz. Tento příkaz používá [kubectl portů][kubectl-port-forward] a náhodný port k vytvoření zabezpečeného připojení mezi klientským počítačem a relevantním pod v clusteru AKS. Následně se automaticky otevře webová aplikace doplňku ve výchozím prohlížeči.

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

### <a name="remove-istio"></a>Odebrat Istio

Pokud chcete odebrat Istio z clusteru AKS, odstraňte `IstioOperator` prostředek s názvem `istio-control-plane` , který jsme přidali dříve. Operátor Istio rozpozná, že byla odstraněna specifikace operátoru Istio, a poté odstraní všechny přidružené komponenty Istio.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

Chcete-li ověřit, zda byly všechny součásti Istio odstraněny, můžete spustit následující příkaz.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Odebrat operátor Istio

Po úspěšné odinstalaci Istio můžete také odebrat operátor Istio.

```bash
istioctl operator remove
```

A nakonec odeberte `istio-` obory názvů.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Další kroky

Pokud chcete prozkoumat další možnosti instalace a konfigurace pro Istio, přečtěte si následující oficiální pokyny pro Istio:

- [Istio – instalační příručky][istio-installation-guides]

Můžete také postupovat podle dalších scénářů pomocí:

- [Příklad aplikace Istio Bookinfo][istio-bookinfo-example]

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
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
