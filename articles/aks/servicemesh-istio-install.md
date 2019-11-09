---
title: Instalace Istio ve službě Azure Kubernetes (AKS)
description: Naučte se instalovat a používat Istio k vytvoření sítě v clusteru Azure Kubernetes Service (AKS).
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885385"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalace a použití Istio ve službě Azure Kubernetes Service (AKS)

[Istio][istio-github] je open source síť, která poskytuje klíčovou sadu funkcí napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří Správa provozu, identita služby a zabezpečení, vynucování zásad a jejich pozorování. Další informace o Istio najdete v dokumentaci oficiální dokumentace k [Istio?][istio-docs-concepts] .

V tomto článku se dozvíte, jak nainstalovat Istio. Binární soubor klienta Istio `istioctl` je nainstalován do klientského počítače a součásti Istio jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Tyto pokyny odkazují na verzi Istio `1.3.2`a používají minimálně Helm verzi `2.14.2`.
>
> `1.3.x` Istio vydaná vydání byla testována týmem Istio proti verzím Kubernetes `1.13``1.14``1.15`. Další verze Istio najdete na webu [GitHub-Istio releases][istio-github-releases], informace o každé vydané verzi v [Istio News][istio-release-notes] a podporovaných verzích Kubernetes na [Istio obecné Nejčastější dotazy][istio-faq].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stažení a instalace binárního souboru klienta Istio istioctl
> * Instalace Istio na AKS
> * Ověření instalace Istio
> * Přístup k doplňkům
> * Odinstalace Istio z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.13` a novější s povoleným RBAC) a navázali jste `kubectl` spojení s clusterem. Pokud potřebujete s kteroukoli z těchto položek pomáhat, přečtěte si [rychlý Start AKS][aks-quickstart].

K provedení těchto pokynů budete potřebovat [Helm][helm] a nainstalujete Istio. Doporučuje se správně nainstalovat a nakonfigurovat nejnovější stabilní verze v clusteru. Pokud potřebujete pomoc s instalací Helm, přečtěte si [pokyny k instalaci AKS Helm][helm-install]. Všechny lusky Istio musí být také naplánované pro spouštění na uzlech se systémem Linux.

Ujistěte se, že jste si přečetli dokumentaci [výkon a škálovatelnost Istio](https://istio.io/docs/concepts/performance-and-scalability/) , abyste pochopili další požadavky na prostředky pro spuštění Istio v clusteru AKS. Požadavky na základní a paměť se budou lišit v závislosti na konkrétním zatížení. Pro instalaci vyberte příslušný počet uzlů a velikost virtuálního počítače.

Tento článek odděluje pokyny k instalaci Istio do několika diskrétních kroků. Konečný výsledek je stejný ve struktuře jako oficiální [návod][istio-install-helm]k instalaci Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Přidat úložiště grafu Istio Helm

Přidejte úložiště grafu Istio Helm pro vydání Istio. Ujistěte se, že spustíte `helm repo update` pro aktualizaci místních informací pro úložiště grafu.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Instalace Istio CRDs na AKS

Istio používá ke správě konfigurace modulu runtime [vlastní definice prostředků (CRDs)][kubernetes-crd] . Nejdřív musíme nainstalovat Istio CRDs, protože komponenty Istio na nich závisí. Pomocí Helm a `istio-init`ového grafu nainstalujte Istio CRDs do oboru názvů `istio-system` v clusteru AKS:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[Úlohy][kubernetes-jobs] se nasazují jako součást grafu `istio-init` Helm k instalaci CRDs. V závislosti na prostředí clusteru by tyto úlohy měly trvat méně než 20s. Úspěšné dokončení úloh můžete ověřit následujícím způsobem:

```azurecli
kubectl get jobs -n istio-system
```

Následující příklad výstupu ukazuje úspěšné dokončené úlohy.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Teď, když jsme potvrdili úspěšné dokončení úloh, ověříme, že máte nainstalovaný správný počet Istio CRDs. Spuštěním následujícího příkazu můžete ověřit, že jste nainstalovali všechny 23 Istio CRDs. Příkaz by měl vrátit číslo `23`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Pokud jste se dostali k tomuto bodu, znamená to, že jste úspěšně nainstalovali Istio CRDs. Teď přejděte k další části a [nainstalujte komponenty Istio do AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Instalace součástí Istio v AKS

V rámci naší instalace Istio budeme instalovat [Grafana][grafana] a [Kiali][kiali] . Grafana poskytuje řídicí panely pro analýzy a monitorování a Kiali poskytuje řídicí panel pro pozorování v mřížce služby. V naší instalaci každá z těchto komponent vyžaduje přihlašovací údaje, které je třeba zadat jako [tajný kód][kubernetes-secrets].

Než budeme moct nainstalovat komponenty Istio, je potřeba vytvořit tajné klíče pro Grafana i Kiali. 

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

Teď, když jsme úspěšně vytvořili tajné kódy Grafana a Kiali v našem clusteru AKS, je čas nainstalovat komponenty Istio. Pomocí Helm a `istio`ového grafu nainstalujte komponenty Istio do oboru názvů `istio-system` v clusteru AKS. 

> [!NOTE]
> **Možnosti instalace**
> 
> V rámci naší instalace používáme následující možnosti:
> - `global.controlPlaneSecurityEnabled=true` – pro plochu ovládacího prvku je povolen oboustranný protokol TLS.
> - `global.mtls.enabled=true` – vyžaduje, aby komunikace všech služeb měla MTLS
> - `grafana.enabled=true` – povolení nasazení Grafana pro řídicí panely pro analýzu a monitorování
> - `grafana.security.enabled=true` – povolení ověřování pro Grafana
> - `tracing.enabled=true` – povolení Jaeger nasazení pro trasování
> - `kiali.enabled=true` – povolení nasazení Kiali pro řídicí panel pro pozorování pro síť služby
>
> **Selektory uzlů**
>
> Istio je v současné době nutné naplánovat na spuštění v uzlech se systémem Linux. Pokud máte v clusteru uzly Windows serveru, musíte zajistit, aby se Istio lusky spouštěly jenom na uzlech se systémem Linux. Použijeme [Selektory uzlů][kubernetes-node-selectors] k ujištění, že lusky jsou naplánované na správných uzlech.

> [!CAUTION]
> [SDS (tajná služba pro zjišťování tajného klíče)][istio-feature-sds] a [Istio CNI][istio-feature-cni] Istio jsou momentálně v [alfa][istio-feature-stages], takže by se mělo před povolením této možnosti udělit. Kromě toho není v aktuálních verzích AKS povolená funkce projekce Kubernetes (požadavek pro SDS) [tokenu účtu služby][kubernetes-feature-sa-projected-volume] .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

Graf `istio` Helm nasadí velký počet objektů. Seznam můžete zobrazit z výstupu příkazu `helm install` výše. Dokončení nasazení součástí Istio by mělo trvat déle než 2 minuty, a to v závislosti na prostředí clusteru.

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
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
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
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Mělo by se jednat o tři `istio-init-crd-*` lusky se stavem `Completed`. Tyto lusky byly zodpovědné za spouštění úloh, které CRDs vytvořili v předchozím kroku. Všechny ostatní lusky by měly zobrazit stav `Running`. Pokud vaše lusky nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělá. Pokud jakékoli lusky nahlásí problém, zkontrolujte výstup a stav pomocí příkazu [kubectl popsat pod][kubectl-describe] .

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

K odebrání Istio z clusteru AKS použijte následující příkazy. Příkazy `helm delete` odstraní grafy `istio` a `istio-init` a příkaz `kubectl delete namespace` odstraní obor názvů `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Odebrat Istio CRDs a tajné klíče

Výše uvedené příkazy odstraní všechny součásti a obor názvů Istio, ale pořád ještě zbývá s Istio CRDs a tajné klíče. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete použít Istio k poskytování inteligentního směrování k zavedení testovací verze:

> [!div class="nextstepaction"]
> [Scénář inteligentního směrování AKS Istio][istio-scenario-routing]

Pokud chcete prozkoumat další možnosti instalace a konfigurace pro Istio, přečtěte si následující oficiální články pro Istio:

- [Průvodce instalací Istio-Helm][istio-install-helm]
- [Istio – možnosti instalace Helm][istio-install-helm-options]

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
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
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
[helm-install]: ./kubernetes-helm.md
