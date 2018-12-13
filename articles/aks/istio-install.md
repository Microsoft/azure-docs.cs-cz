---
title: Nainstalujte Istio ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a ise mřížky Istio k vytvoření služby v clusteru služby Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 33a72b6e8fdd4a66425405ff15d7cc31461c0bf3
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892150"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Nainstalovat a používat Istio ve službě Azure Kubernetes Service (AKS)

[Istio] [ istio-github] je open source služba sítě, který poskytuje sadu klíčových funkcí, které v rámci mikroslužby v clusteru Kubernetes. Tyto funkce patří řízení provozu, identita služby a zabezpečení, vynucení zásad a observability. Další informace o Istio, najdete v oficiální [novinky Istio?] [ istio-docs-concepts] dokumentaci.

V tomto článku se dozvíte, jak nainstalovat Istio. Istio `istioctl` binární klient se nainstaluje do klientského počítače, pak jsou nainstalované komponenty Istio do clusteru Kubernetes v AKS. Tyto pokyny odkaz Istio verzi *1.0.4*. Můžete najít další verze Istio na [GitHub - Istio verze][istio-github-releases].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stáhněte si Istio
> * Instalovat binární Istio klienta
> * Nainstalujte součásti Istio Kubernetes
> * Ověření instalace

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku se předpokládá jste vytvořili AKS cluster (Kubernetes 1.10 a vyšších povolena pomocí RBAC) a navázali `kubectl` připojení ke clusteru. Pokud potřebujete pomoc s libovolnou z těchto položek, přejděte na téma [AKS quickstart][aks-quickstart].

K instalaci Istio, budete potřebovat [Helm] [ helm] verze *2.10.0* nebo později správně nainstalován a nakonfigurován v clusteru. Pokud potřebujete pomoc s instalací Helm, najdete v článku [pokyny instalaci AKS Helm][helm-install]. Pokud nemáte alespoň verzi *2.10.0* Helm nainstalovaná, proveďte upgrade nebo viz [Istio – instalace pomocí Helm příručky] [ istio-install-helm] další možnosti instalace.

Tento článek odděluje Istio pokyny instalaci do několika diskrétní kroky. Každý z těchto kroků je popsán tak informace o instalaci Istio a seznámení s fungováním Istio s využitím Kubernetes. Konečný výsledek je stejný jako oficiální instalační Istio struktury [pokyny][istio-install-k8s-quickstart].

## <a name="download-istio"></a>Stáhněte si Istio

Nejprve stáhnout a extrahovat nejnovější vydaná verze Istio. Kroky pro prostředí bash v systému MacOS, Linux nebo subsystému Windows pro Linux a prostředí PowerShell mírně liší. Zvolte jeden z následujících instalace kroků pro vaše preferované prostředí:

* [Bash v systému MacOS, Linux nebo subsystému Windows pro Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

V systému MacOS pomocí `curl` stáhnout nejnovější verzi Istio a extrahujte s `tar` následujícím způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

V systému Linux nebo subsystému Windows pro Linux, použijte `curl` stáhnout nejnovější verzi Istio a extrahujte s `tar` následujícím způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

V prostředí PowerShell, použijte [Invoke-WebRequest] [ Invoke-WebRequest] stáhnout nejnovější verzi Istio a extrahujte s [rozbalení archivu] [ Expand-Archive] jako následující:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Instalovat binární Istio klienta

`istioctl` Klienta binární běží na klientském počítači a umožňuje vám spravovat zásady a Istio pravidla směrování. Postup instalace znovu, se mírně liší mezi klientskými operačními systémy. Zvolte jeden z následujících instalace kroků pro vaše preferované prostředí.

> [!IMPORTANT]
> Spusťte všechny zbývající kroky ze složky nejvyšší úrovně z verze Istio stažené a rozbalené v předchozí části.

### <a name="macos"></a>MacOS

Chcete-li nainstalovat Istio `istioctl` binární klienta v prostředí založené na prostředí bash v systému MacOS, použijte následující příkazy. Zkopírujte tyto příkazy `istioctl` klienta binární umístění programu standardního uživatele ve vaší `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Pokud byste o ni příkazového řádku dokončování pro Istio `istioctl` klienta binární, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nyní se přesunout do oddílu, který má [nainstalovat komponenty Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux nebo subsystému Windows pro Linux

Použijte následující příkazy pro instalaci Istio `istioctl` klienta binární v prostředí bash založené na Linuxu nebo [subsystém Windows pro Linux][install-wsl]. Zkopírujte tyto příkazy `istioctl` klienta binární umístění programu standardního uživatele ve vaší `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Pokud byste o ni příkazového řádku dokončování pro Istio `istioctl` klienta binární, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nyní se přesunout do oddílu, který má [nainstalovat komponenty Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="windows"></a>Windows

Chcete-li nainstalovat Istio `istioctl` binární klienta v prostředí pomocí prostředí Powershell na Windows, použijte následující příkazy. Zkopírujte tyto příkazy `istioctl` binární soubor klienta pro nového uživatele programu umístění a zpřístupňují ho prostřednictvím vaší `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:/Program Files/Istio/", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Nainstalujte součásti Istio Kubernetes

Instalace součásti Istio do clusteru AKS, použijte příkaz Helm. Nainstalujte Istio prostředků do `istio-system` obor názvů a povolit další možnosti zabezpečení a monitorování následujícím způsobem:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Grafu helmu nasazuje velký počet objektů. Nasazení může trvat 2 až 3 minut v závislosti na prostředí clusteru.

## <a name="validate-the-installation"></a>Ověření instalace

Pokud chcete mít jistotu, že máte úspěšné nasazení Istio, ověříme instalace.

Zkontrolujte, že nebyly vytvořeny očekávané služby. Použití [kubectl get svc] [ kubectl-get] příkazu zobrazte spuštěné služby. Dotaz *istio systému* obor názvů, kde byly nainstalovány komponenty Istio a doplněk pomocí grafu helmu:

```console
kubectl get svc --namespace istio-system --output wide
```

Následující příklad výstupu ukazuje služby, které by teď měla být spuštěná:

- *istio -** služby
- * jaeger-**, *trasování*, a *zipkin* doplňkových služeb trasování
- *prometheus* metriky doplňkových služeb.
- *grafana* doplněk analýzy a monitorování řídicího panelu služby
- *kiali* doplněk služby sítě řídicího panelu služby

Pokud `istio-ingressgateway` zobrazí externí IP adresu z `<pending>`, počkejte několik minut, než byla přiřazena IP adresu pomocí sítě Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

V dalším kroku ověřte, zda byly vytvořeny požadovaných podů. Použití [kubectl get pods] [ kubectl-get] příkazů a znovu dotazů *istio systému* obor názvů:

```console
kubectl get pods --namespace istio-system
```

Následující příklad výstupu ukazuje, na kterých běží tyto pody:

- *istio -** podů
- *prometheus -** pod doplněk metriky
- *grafana -** doplněk analýzy a monitorování podu řídicího panelu
- *kiali* podu řídicího panelu mřížky doplňkové služby

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Všechny tyto pody zobrazovat stav `Running`. Pokud se vaše pody nemáte k dispozici tyto stavy, Počkejte minutu nebo dvě dělají. Pokud žádné podů nahlásit problém, použijte [kubectl popisují pod] [ kubectl-describe] příkazu ke kontrole jejich výstup a stav.

## <a name="accessing-the-add-ons"></a>Přístup k doplňky

Počet doplňků byly nainstalovány Istio v našich výše uvedené nastavení, které poskytují další funkce. Uživatelská rozhraní pro doplňky nejsou veřejně dostupné prostřednictvím externí ip adresu. Chcete-li získat přístup k uživatelským rozhraním doplňku, použijte [kubectl dopředné port] [ kubectl-port-forward] příkazu. Tento příkaz vytvoří zabezpečené připojení mezi místní port v klientském počítači a relevantní pod ve vašem clusteru AKS.

### <a name="grafana"></a>Grafana

Analýzy a řídicí panely monitorování pro Istio poskytuje [Grafana][grafana]. Vpřed na místní port *3000* v klientském počítači na port *3000* na pod, na kterém běží Grafana ve vašem clusteru AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Následující příklad výstupu ukazuje konfigurovaných na Grafana dalšího portu:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Teď můžete oslovit Grafana na následující adrese URL v klientském počítači - [ http://localhost:3000 ](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

Výraz prohlížeče pro metriky poskytuje [Prometheus][prometheus]. Vpřed na místní port *9090* v klientském počítači na port *9090* na pod, na kterém běží Prometheus ve vašem clusteru AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Následující příklad výstupu ukazuje konfigurovaných na Prometheus dalšího portu:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Teď můžete oslovit Prometheus výraz prohlížeči na následující adrese URL v klientském počítači - [ http://localhost:9090 ](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Trasování uživatelské rozhraní poskytuje [Jaeger][jaeger]. Vpřed na místní port *16686* v klientském počítači na port *16686* na pod, na kterém běží Jaeger ve vašem clusteru AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Následující příklad výstupu ukazuje konfigurovaných na Jaeger dalšího portu:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Teď můžete oslovit Jaeger trasování uživatelského rozhraní na následující adrese URL v klientském počítači - [ http://localhost:16686 ](http://localhost:16686).

### <a name="kiali"></a>Kiali

Řídicí panel služby sítě observability poskytuje [Kiali][kiali]. Vpřed na místní port *20001* v klientském počítači na port *20001* na pod, na kterém běží Kiali ve vašem clusteru AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Následující příklad výstupu ukazuje konfigurovaných na Kiali dalšího portu:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Teď můžete oslovit Kiali řídicím panelu služby sítě observability na následující adrese URL v klientském počítači - [ http://localhost:20001 ](http://localhost:20001).

Je výchozí uživatelské jméno a heslo pro řídicí panel Kiali *username:admin / heslo: admin*. Tyto přihlašovací údaje můžete nakonfigurovat přes *kiali.dashboard.username* a *kiali.dashboard.passphrase* Helm hodnoty.

## <a name="next-steps"></a>Další postup

Pokud chcete zobrazit, jak můžete Istio, aby bylo zajištěno inteligentního směrování mezi více verzí aplikace a zavedení testovací verze, viz následující dokumentace:

> [!div class="nextstepaction"]
> [Scénář směrování inteligentní AKS Istio][istio-scenario-routing]

Prozkoumat další možnosti instalace a konfigurace pro Istio, naleznete v následujících článcích Istio oficiální:

- [Istio – rychlý start s Kubernetes instalace][istio-install-k8s-quickstart]
- [Istio – Průvodce instalací Helm][istio-install-helm]
- [Istio – možnosti instalace Helm][istio-install-helm-options]

Můžete také postupovat podle dalších scénářů použití [Istio Bookinfo aplikace – příklad][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
