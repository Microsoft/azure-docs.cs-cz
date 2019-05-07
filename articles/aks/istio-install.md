---
title: Nainstalujte Istio ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a používat Istio k vytvoření sítě služby v clusteru služby Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: fc95ce4aad4e8597b02b9c862be33bfcf6185541
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073805"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Nainstalovat a používat Istio ve službě Azure Kubernetes Service (AKS)

[Istio] [ istio-github] je open source služba sítě, který poskytuje sadu klíčových funkcí, které v rámci mikroslužby v clusteru Kubernetes. Tyto funkce patří řízení provozu, identita služby a zabezpečení, vynucení zásad a observability. Další informace o Istio, najdete v oficiální [novinky Istio?] [ istio-docs-concepts] dokumentaci.

V tomto článku se dozvíte, jak nainstalovat Istio. Istio `istioctl` binární klient se nainstaluje do klientského počítače a jsou nainstalované komponenty Istio do clusteru Kubernetes v AKS.

> [!NOTE]
> Tyto pokyny odkaz Istio verzi `1.1.3`.
>
> Istio `1.1.x` vydání bylo otestováno týmem Istio proti verze Kubernetes `1.11`, `1.12`, `1.13`. Můžete najít další verze Istio na [GitHub - Istio uvolní] [ istio-github-releases] a informace o jednotlivých verzí na [Istio – zpráva k vydání verze] [ istio-release-notes].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stáhněte si Istio
> * Instalace klienta istioctl Istio binární
> * Instalace slovníky Istio CRD v AKS
> * Nainstalujte součásti Istio v AKS
> * Ověření instalace Istio
> * Přístup k doplňky
> * Odinstalujte Istio z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili AKS cluster (Kubernetes `1.11` a vyšších povolena pomocí RBAC) a navázali `kubectl` připojení ke clusteru. Pokud potřebujete pomoc s libovolnou z těchto položek, přejděte na téma [AKS quickstart][aks-quickstart].

Budete potřebovat [Helm] [ helm] postupujte podle těchto pokynů a nainstalujte Istio. Doporučuje se, že máte verzi `2.12.2` nebo později správně nainstalován a nakonfigurován v clusteru. Pokud potřebujete pomoc s instalací Helm, přejděte na téma [pokyny instalaci AKS Helm][helm-install]. Všechny Istio pody musí také naplánovány ke spuštění na uzly s Linuxem.

Tento článek odděluje Istio pokyny instalaci do několika diskrétní kroky. Konečný výsledek je stejný jako oficiální instalační Istio struktury [pokyny][istio-install-helm].

## <a name="download-istio"></a>Stáhněte si Istio

Nejprve stáhnout a extrahovat nejnovější vydaná verze Istio. Postup se mírně liší pro prostředí bash v systému MacOS, Linux nebo subsystému Windows pro Linux a prostředí PowerShell. Vyberte jednu z následujících kroků instalace, které odpovídá preferovaném prostředí:

* [Bash v systému MacOS, Linux nebo subsystému Windows pro Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

V systému MacOS pomocí `curl` stáhnout nejnovější verzi Istio a extrahujte s `tar` následujícím způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

V systému Linux nebo subsystému Windows pro Linux, použijte `curl` stáhnout nejnovější verzi Istio a extrahujte s `tar` následujícím způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Nyní se přesunout do oddílu, který má [instalace klienta istioctl Istio binární](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

V prostředí PowerShell, použijte `Invoke-WebRequest` stáhnout nejnovější verzi Istio a extrahujte s `Expand-Archive` následujícím způsobem:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Nyní se přesunout do oddílu, který má [instalace klienta istioctl Istio binární](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Instalace klienta istioctl Istio binární

> [!IMPORTANT]
> Nezapomeňte spustit kroky v této části, ze složky nejvyšší úrovně verzi Istio, že jste stažené a rozbalené.

`istioctl` Binární klienta běží na klientském počítači a umožňuje pracovat s Istio síť služby. Postup instalace se mírně liší mezi klientskými operačními systémy. Vyberte jednu z následujících kroků instalace, které odpovídá preferovaném prostředí:

* [MacOS](#macos)
* [Linux nebo subsystému Windows pro Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Chcete-li nainstalovat Istio `istioctl` binární klienta v prostředí založené na prostředí bash v systému MacOS, použijte následující příkazy. Zkopírujte tyto příkazy `istioctl` klienta binární umístění programu standardního uživatele ve vaší `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Pokud byste o ni příkazového řádku dokončování pro Istio `istioctl` klienta binární, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nyní přejít k další části a [instalace slovníky Istio CRD v AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux nebo subsystému Windows pro Linux

Použijte následující příkazy pro instalaci Istio `istioctl` klienta binární v prostředí bash založené na Linuxu nebo [subsystém Windows pro Linux][install-wsl]. Zkopírujte tyto příkazy `istioctl` klienta binární umístění programu standardního uživatele ve vaší `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Pokud byste o ni příkazového řádku dokončování pro Istio `istioctl` klienta binární, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nyní přejít k další části a [instalace slovníky Istio CRD v AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

K instalaci Istio `istioctl` klienta v binární **Powershellu**– na základě prostředí ve Windows, použijte následující příkazy. Zkopírujte tyto příkazy `istioctl` binární do složky Istio klienta a nastavte ji trvale k dispozici prostřednictvím vaší `PATH`. Není nutné zvýšená oprávnění (správce) pro spuštění těchto příkazů.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Nyní přejít k další části a [instalace slovníky Istio CRD v AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Instalace slovníky Istio CRD v AKS

> [!IMPORTANT]
> Nezapomeňte spustit kroky v této části, ze složky nejvyšší úrovně verzi Istio, že jste stažené a rozbalené.

Používá Istio [vlastní definice prostředků (Slovníky CRD)] [ kubernetes-crd] spravovat jeho konfiguraci modulu runtime. Musíme nainstalovat Slovníky CRD Istio, protože Istio komponent jsou závislé na nich. Pomocí helmu a `istio-init` grafu tak, aby instalace slovníky Istio CRD do `istio-system` obor názvů v clusteru AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Úlohy] [ kubernetes-jobs] jsou nasazeny jako součást `istio-init` grafu helmu instalace Slovníky CRD. Tyto úlohy mělo trvat přibližně 1 až 2 minut v závislosti na prostředí vašeho clusteru. Můžete ověřit, že úlohy, které byly úspěšně dokončeny následujícím způsobem:

```azurecli
kubectl get jobs -n istio-system
```

Následující příklad výstupu ukazuje úspěšně dokončené úlohy.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Teď, když jsme ověření, že úspěšné dokončení úlohy, Pojďme ověřit, že máme správný počet slovníky Istio CRD nainstalované. Můžete ověřit, zda byly nainstalovány všechny 53 Slovníky CRD Istio spuštěním příkazu vhodné pro vaše prostředí. Příkaz by měl vrátit číslo `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Pokud máte k tomuto bodu, pak to znamená, že jste úspěšně nainstalovali slovníky Istio CRD. Nyní přejít k další části a [nainstalovat komponenty Istio v AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Nainstalujte součásti Istio v AKS

> [!IMPORTANT]
> Nezapomeňte spustit kroky v této části, ze složky nejvyšší úrovně verzi Istio, že jste stažené a rozbalené.

Jsme budete instalovat [Grafana] [ grafana] a [Kiali] [ kiali] jako součást instalace naše Istio. Grafana poskytuje řídicí panely pro monitorování a analýzu a Kiali poskytuje observability řídicího panelu služby sítě. V našem nastavení, každá z těchto komponent vyžaduje přihlašovací údaje, které musí být ve formě [tajný kód][kubernetes-secrets].

Předtím, než nainstalujeme Istio komponenty, vytvoříme musí tajné klíče pro Grafana a Kiali. Vytvoření těchto tajných kódů pomocí příkazů vhodné pro vaše prostředí.

### <a name="add-grafana-secret"></a>Add Grafana Secret

Nahradit `REPLACE_WITH_YOUR_SECURE_PASSWORD` token pomocí hesla a spusťte následující příkazy:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Přidání tajného klíče Kiali

Nahradit `REPLACE_WITH_YOUR_SECURE_PASSWORD` token pomocí hesla a spusťte následující příkazy:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Instalace součásti Istio

Teď, když jsme vytvořili úspěšně Grafana a Kiali tajných kódů v našem clusteru AKS, je čas Istio součásti nainstalovat. Pomocí helmu a `istio` grafu pro instalaci součástí Istio do `istio-system` obor názvů v clusteru AKS. Pomocí příkazu vhodné pro vaše prostředí.

> [!NOTE]
> Jako součást naše instalace používáme následující možnosti:
> - `global.controlPlaneSecurityEnabled=true` -povoleno pro rovinu řízení vzájemného TLS
> - `mixer.adapters.useAdapterCRDs=false` -odebrat hodinky na adaptéru mixer Slovníky CRD, protože jsou zastaralé a tím dojde ke zvýšení výkonu
> - `grafana.enabled=true` -Povolit Grafana nasazení pro analýzy a řídicí panely monitorování
> - `grafana.security.enabled=true` – Povolení ověřování pro Grafana
> - `tracing.enabled=true` -Povolit Jaeger nasazení pro trasování
> - `kiali.enabled=true` -Povolit Kiali nasazení pro řídicí panel služby sítě observability

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

`istio` Grafu helmu nasazuje velký počet objektů. Zobrazí se seznam z výstupu vaše `helm install` příkazu výše. Nasazení součásti Istio může trvat 4 až 5 minut v závislosti na prostředí vašeho clusteru.

> [!NOTE]
> Všechny Istio pody musí být naplánovány ke spuštění na uzly s Linuxem. Pokud máte fondy uzlů Windows Server kromě fondy Linux uzlů v clusteru, ověřte, že všechny podů Istio bylo naplánováno ke spuštění na uzlech systému Linux.

V tomto okamžiku jste nasadili Istio ke svému clusteru AKS. Aby bylo zajištěno, že máme úspěšné nasazení Istio, přejdeme k další části a [ověření instalace Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Ověření instalace Istio

Zkontrolujte, že nebyly vytvořeny očekávané služby. Použití [kubectl get svc] [ kubectl-get] příkazu zobrazte spuštěné služby. Dotaz `istio-system` obor názvů, ve kterém byly nainstalovány komponenty Istio a doplněk podle `istio` grafu helmu:

```console
kubectl get svc --namespace istio-system --output wide
```

Následující příklad výstupu ukazuje služby, které by teď měla být spuštěná:

- `istio-*` Služby
- `jaeger-*`, `tracing`, a `zipkin` doplňkových služeb trasování
- `prometheus` metriky doplňkových služeb.
- `grafana` doplněk analýzy a monitorování řídicího panelu služby
- `kiali` Doplněk služby sítě řídicího panelu služby

Pokud `istio-ingressgateway` zobrazí externí IP adresu z `<pending>`, počkejte několik minut, než byla přiřazena IP adresu pomocí sítě Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

V dalším kroku ověřte, zda byly vytvořeny požadovaných podů. Použití [kubectl get pods] [ kubectl-get] příkazů a znovu dotazů `istio-system` obor názvů:

```console
kubectl get pods --namespace istio-system
```

Následující příklad výstupu ukazuje, na kterých běží tyto pody:

- `istio-*` podů
- `prometheus-*` pod doplněk metriky
- `grafana-*` doplněk analýzy a monitorování podu řídicího panelu
- `kiali` podu řídicího panelu mřížky doplňkové služby

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Měla by existovat dva `istio-init-crd-*` podů s `Completed` stav. Tyto pody tým byl zodpovědný za spouštění úloh, které vytvořili Slovníky CRD v dřívějším kroku. Všechny další pody musí zobrazit stav `Running`. Pokud se vaše pody nemáte k dispozici tyto stavy, Počkejte minutu nebo dvě dělají. Pokud žádné podů nahlásit problém, použijte [kubectl popisují pod] [ kubectl-describe] příkazu ke kontrole jejich výstup a stav.

## <a name="accessing-the-add-ons"></a>Přístup k doplňky

Počet doplňků byly nainstalovány Istio v našich výše uvedené nastavení, které poskytují další funkce. Uživatelská rozhraní pro doplňky nejsou veřejně dostupné prostřednictvím externí ip adresu. Chcete-li získat přístup k uživatelským rozhraním doplňku, použijte [kubectl dopředné port] [ kubectl-port-forward] příkazu. Tento příkaz vytvoří zabezpečené připojení mezi klientského počítače a relevantní pod ve vašem clusteru AKS.

Přidali jsme další úroveň zabezpečení pro Grafana a Kiali zadáním přihlašovacích údajů pro ně dříve v tomto článku.

### <a name="grafana"></a>Grafana

Analýzy a řídicí panely monitorování pro Istio poskytuje společnost [Grafana][grafana]. Vpřed na místní port `3000` v klientském počítači na port `3000` na pod, na kterém běží Grafana ve vašem clusteru AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Následující příklad výstupu ukazuje konfigurovaných na Grafana dalšího portu:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Teď můžete oslovit Grafana na následující adrese URL v klientském počítači - [ http://localhost:3000 ](http://localhost:3000). Nezapomeňte použít přihlašovací údaje, které jste vytvořili prostřednictvím Grafana tajných kódů při zobrazení výzvy.

### <a name="prometheus"></a>Prometheus

Poskytuje metriky pro Istio [Prometheus][prometheus]. Vpřed na místní port `9090` v klientském počítači na port `9090` na pod, na kterém běží Prometheus ve vašem clusteru AKS:

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

Trasování v rámci Istio poskytuje [Jaeger][jaeger]. Vpřed na místní port `16686` v klientském počítači na port `16686` na pod, na kterém běží Jaeger ve vašem clusteru AKS:

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

Řídicí panel služby sítě observability poskytuje [Kiali][kiali]. Vpřed na místní port `20001` v klientském počítači na port `20001` na pod, na kterém běží Kiali ve vašem clusteru AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Následující příklad výstupu ukazuje konfigurovaných na Kiali dalšího portu:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Teď můžete oslovit Kiali řídicím panelu služby sítě observability na následující adrese URL v klientském počítači - [ http://localhost:20001/kiali/console/ ](http://localhost:20001/kiali/console/). Nezapomeňte použít přihlašovací údaje, které jste vytvořili prostřednictvím Kiali tajných kódů při zobrazení výzvy.

## <a name="uninstall-istio-from-aks"></a>Odinstalujte Istio z AKS

> [!WARNING]
> Odstranění Istio spuštěné systému může vést k provozu v otázkách mezi službami. Ujistěte se, že jste provedli ustanovení pro váš systém i nadále fungovat správně bez Istio než budete pokračovat.

### <a name="remove-istio-components-and-namespace"></a>Odebrat Istio součásti a obor názvů

K odebrání Istio z clusteru AKS, použijte následující příkazy. `helm delete` Příkazy odeberou `istio` a `istio-init` grafy a `kubectl delete ns` příkaz odebere `istio-system` oboru názvů.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Odebrat slovníky Istio CRD

Výše uvedené příkazy pro odstranění všech součástí Istio a obor názvů, ale jsou stále ponechali jsme s slovníky Istio CRD. Pokud chcete odstranit Slovníky CRD, můžete jeden následujících dvou přístupů.

Přístup #1 - Tento příkaz předpokládá, že používáte tento krok ze složky nejvyšší úrovně verzi stažený a extrahované Istio, který jste použili k instalaci Istio s.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Způsob 2 # – použijte některou z těchto příkazů, pokud již máte přístup k stažený a extrahované verzi Istio, který jste použili k instalaci Istio s. Tento příkaz bude trvat o něco déle - očekávají, že bude trvat několik minut.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Další postup

Následující dokumentace popisuje, jak vám pomůže Istio poskytují inteligentního směrování zavedení testovací verze:

> [!div class="nextstepaction"]
> [Scénář směrování inteligentní AKS Istio][istio-scenario-routing]

Prozkoumat další možnosti instalace a konfigurace pro Istio, naleznete v následujících článcích Istio oficiální:

- [Istio – Průvodce instalací Helm][istio-install-helm]
- [Istio – možnosti instalace Helm][istio-install-helm-options]

Můžete také postupovat podle dalších scénářů použití [Istio Bookinfo aplikace – příklad][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md