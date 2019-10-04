---
title: Instalace Istio ve službě Azure Kubernetes (AKS)
description: Naučte se instalovat a používat Istio k vytvoření sítě v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9344d2832c37c34d5690dc8f3aae7394ca644276
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827322"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalace a použití Istio ve službě Azure Kubernetes Service (AKS)

[Istio][istio-github] je open source síť, která poskytuje klíčovou sadu funkcí napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří Správa provozu, identita služby a zabezpečení, vynucování zásad a jejich pozorování. Další informace o Istio najdete v dokumentaci oficiální dokumentace k [Istio?][istio-docs-concepts] .

V tomto článku se dozvíte, jak nainstalovat Istio. V klientském počítači je nainstalován binární soubor klienta Istio `istioctl` a součásti Istio jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Tyto pokyny odkazují na verzi Istio `1.1.3`.
>
> Verze Istio `1.1.x` byly testovány týmem Istio proti verzím Kubernetes `1.11` `1.12`, `1.13`. Další verze Istio najdete na webu [GitHub-Istio releases][istio-github-releases] a v informacích o každé z těchto verzí na adrese [Istio News][istio-release-notes].

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Stáhnout Istio
> * Instalace binárního souboru klienta Istio istioctl
> * Instalace Istio CRDs na AKS
> * Instalace součástí Istio v AKS
> * Ověření instalace Istio
> * Přístup k doplňkům
> * Odinstalace Istio z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.11` a vyšší s povoleným RBAC) a navázali jste připojení `kubectl` ke clusteru. Pokud potřebujete s kteroukoli z těchto položek pomáhat, přečtěte si [rychlý Start AKS][aks-quickstart].

K provedení těchto pokynů budete potřebovat [Helm][helm] a nainstalujete Istio. Doporučuje se, abyste měli verzi `2.12.2` nebo novější správně nainstalovanou a nakonfigurovanou v clusteru. Pokud potřebujete pomoc s instalací Helm, přečtěte si [pokyny k instalaci AKS Helm][helm-install]. Všechny lusky Istio musí být také naplánované pro spouštění na uzlech se systémem Linux.

Ujistěte se, že jste si přečetli dokumentaci [výkon a škálovatelnost Istio](https://istio.io/docs/concepts/performance-and-scalability/) , abyste pochopili další požadavky na prostředky pro spuštění Istio v clusteru AKS. Požadavky na základní a paměť se budou lišit v závislosti na konkrétním zatížení. Pro instalaci vyberte příslušný počet uzlů a velikost virtuálního počítače.

Tento článek odděluje pokyny k instalaci Istio do několika diskrétních kroků. Konečný výsledek je stejný ve struktuře jako oficiální [návod][istio-install-helm]k instalaci Istio.

## <a name="download-istio"></a>Stáhnout Istio

Nejdřív Stáhněte a extrahujte nejnovější verzi Istio. Postup je trochu jiný pro prostředí bash v MacOS, Linux nebo v subsystému Windows pro Linux a prostředí PowerShell. Vyberte jeden z následujících kroků instalace, které odpovídají vašemu preferovanému prostředí:

* [Bash v subsystému MacOS, Linux nebo Windows pro Linux](#bash)
* [Prostředí](#powershell)

### <a name="bash"></a>Bash

Na MacOS použijte `curl` ke stažení nejnovější verze Istio a pak se extrahujte pomocí `tar` následujícím způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

V systému Linux nebo subsystém Windows pro Linux použijte `curl` ke stažení nejnovější verze Istio a pak je extrahujte pomocí `tar` následujícím způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Nyní přejděte do oddílu a [nainstalujte binární soubor klienta Istio istioctl](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

V PowerShellu použijte `Invoke-WebRequest` ke stažení nejnovější verze Istio a pak ji extrahujte pomocí `Expand-Archive` následujícím způsobem:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Nyní přejděte do oddílu a [nainstalujte binární soubor klienta Istio istioctl](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Instalace binárního souboru klienta Istio istioctl

> [!IMPORTANT]
> Ujistěte se, že jste spustili kroky v této části, ze složky na nejvyšší úrovni verze Istio, kterou jste stáhli a extrahovali.

Binární soubor klienta `istioctl` se spouští na klientském počítači a umožňuje interakci s sítí služby Istio. Tyto kroky instalace se mezi klientskými operačními systémy trochu liší. Vyberte jeden z následujících kroků instalace, které odpovídají vašemu preferovanému prostředí:

* [MacOS](#macos)
* [Linux nebo subsystém Windows pro Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Pro instalaci binárního souboru klienta Istio `istioctl` do prostředí založeného na bash na MacOS použijte následující příkazy. Tyto příkazy kopírují binární soubor klienta `istioctl` do umístění standardního uživatelského programu v `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Pokud chcete dokončit příkazový řádek pro binární soubor klienta Istio `istioctl`, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nyní přejděte k další části a [nainstalujte Istio CRDs na AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux nebo subsystém Windows pro Linux

Pomocí následujících příkazů nainstalujte binární soubor klienta Istio `istioctl` do prostředí založeného na bash na Linux nebo v subsystému [Windows pro Linux][install-wsl]. Tyto příkazy kopírují binární soubor klienta `istioctl` do umístění standardního uživatelského programu v `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Pokud chcete dokončit příkazový řádek pro binární soubor klienta Istio `istioctl`, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nyní přejděte k další části a [nainstalujte Istio CRDs na AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Pokud chcete nainstalovat binární soubor klienta Istio `istioctl` do prostředí založeného na **prostředí PowerShell**ve Windows, použijte následující příkazy. Tyto příkazy kopírují binární soubor klienta `istioctl` do složky Istio a pak ho hned (v současném prostředí) zpřístupní hned (v aktuálním prostředí) a trvale (v rámci restartování prostředí) prostřednictvím `PATH`. Ke spuštění těchto příkazů nepotřebujete oprávnění vyšší úrovně (správce) a nemusíte restartovat prostředí.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Nyní přejděte k další části a [nainstalujte Istio CRDs na AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Instalace Istio CRDs na AKS

> [!IMPORTANT]
> Ujistěte se, že jste spustili kroky v této části, ze složky na nejvyšší úrovni verze Istio, kterou jste stáhli a extrahovali.

Istio používá ke správě konfigurace modulu runtime [vlastní definice prostředků (CRDs)][kubernetes-crd] . Nejdřív musíme nainstalovat Istio CRDs, protože komponenty Istio na nich závisí. Pomocí Helm a grafu `istio-init` nainstalujte Istio CRDs do oboru názvů `istio-system` v clusteru AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Úlohy][kubernetes-jobs] se nasazují jako součást Helm grafu `istio-init` pro instalaci CRDs. Dokončení těchto úloh by mělo trvat 1 až 2 minuty v závislosti na prostředí clusteru. Úspěšné dokončení úloh můžete ověřit následujícím způsobem:

```azurecli
kubectl get jobs -n istio-system
```

Následující příklad výstupu ukazuje úspěšné dokončené úlohy.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Teď, když jsme potvrdili úspěšné dokončení úloh, ověříme, že máte nainstalovaný správný počet Istio CRDs. Spuštěním příslušného příkazu pro vaše prostředí můžete ověřit, že jste nainstalovali všechny 53 Istio CRDs. Příkaz by měl vracet číslo `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Prostředí

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Pokud jste se dostali k tomuto bodu, znamená to, že jste úspěšně nainstalovali Istio CRDs. Teď přejděte k další části a [nainstalujte komponenty Istio do AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Instalace součástí Istio v AKS

> [!IMPORTANT]
> Ujistěte se, že jste spustili kroky v této části, ze složky na nejvyšší úrovni verze Istio, kterou jste stáhli a extrahovali.

V rámci naší instalace Istio budeme instalovat [Grafana][grafana] a [Kiali][kiali] . Grafana poskytuje řídicí panely pro analýzy a monitorování a Kiali poskytuje řídicí panel pro pozorování v mřížce služby. V naší instalaci každá z těchto komponent vyžaduje přihlašovací údaje, které je třeba zadat jako [tajný kód][kubernetes-secrets].

Než budeme moct nainstalovat komponenty Istio, je potřeba vytvořit tajné klíče pro Grafana i Kiali. Vytvořte tyto tajné klíče spuštěním odpovídajících příkazů pro vaše prostředí.

### <a name="add-grafana-secret"></a>Přidat tajný klíč Grafana

Nahraďte token `REPLACE_WITH_YOUR_SECURE_PASSWORD` vaším heslem a spusťte následující příkazy:

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

### <a name="add-kiali-secret"></a>Přidat tajný klíč Kiali

Nahraďte token `REPLACE_WITH_YOUR_SECURE_PASSWORD` vaším heslem a spusťte následující příkazy:

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

### <a name="install-istio-components"></a>Nainstalovat komponenty Istio

Teď, když jsme úspěšně vytvořili tajné kódy Grafana a Kiali v našem clusteru AKS, je čas nainstalovat komponenty Istio. Pomocí Helm a grafu `istio` nainstalujete součásti Istio do oboru názvů `istio-system` v clusteru AKS. Použijte příslušné příkazy pro vaše prostředí.

> [!NOTE]
> V rámci naší instalace používáme následující možnosti:
> - `global.controlPlaneSecurityEnabled=true` – oboustranný protokol TLS povolený pro rovinu ovládacího prvku
> - `mixer.adapters.useAdapterCRDs=false` – odebere hodinky CRDs adaptéru mixer, protože budou zastaralé a tím se zvýší výkon.
> - `grafana.enabled=true` – povolení nasazení Grafana pro řídicí panely pro analýzu a monitorování
> - `grafana.security.enabled=true` – povolení ověřování pro Grafana
> - `tracing.enabled=true` – povolení Jaeger nasazení pro trasování
> - `kiali.enabled=true` – povolení Kiali nasazení pro řídicí panel pro pozorování pro síť služby

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Prostředí

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

Helm graf s `istio` nasadí velký počet objektů. Seznam můžete zobrazit z výstupu příkazu `helm install` výše. Dokončení nasazení součástí Istio může trvat 4 až 5 minut, a to v závislosti na prostředí clusteru.

> [!NOTE]
> Všechny lusky Istio musí být naplánované na spouštění na uzlech systému Linux. Pokud ve vašem clusteru kromě fondů uzlů pro Linux máte fondy uzlů Windows serveru, ověřte, že všechny Istio lusky mají naplánované spuštění na uzlech se systémem Linux.

V tomto okamžiku jste nasadili Istio do svého clusteru AKS. Abychom zajistili úspěšné nasazení Istio, pojďme přejít k další části a [ověřit instalaci Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Ověření instalace Istio

Nejprve potvrďte, že byly vytvořeny očekávané služby. K zobrazení spuštěných služeb použijte příkaz [kubectl Get svc][kubectl-get] . Zadejte dotaz na obor názvů `istio-system`, kde byly nainstalovány komponenty Istio a Add-Helm pomocí grafu `istio`:

```console
kubectl get svc --namespace istio-system --output wide
```

Následující příklad výstupu ukazuje služby, které by nyní měly být spuštěny:

- @no__t – 0 služeb
- trasovací služby `jaeger-*`, `tracing` a `zipkin`
- `prometheus` – služba metriky pro Doplňky
- Služba řídicího panelu pro analýzu a monitorování `grafana` pro Doplňky
- Služba řídicího panelu mřížky služby doplňku `kiali`

Pokud `istio-ingressgateway` zobrazí externí IP adresu `<pending>`, počkejte několik minut, než se IP adresa přiřadí pomocí sítě Azure.

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

Pak potvrďte, že byly vytvořeny požadované lusky. Použijte příkaz [kubectl Get lusks][kubectl-get] a znovu spusťte dotaz na obor názvů `istio-system`:

```console
kubectl get pods --namespace istio-system
```

Následující příklad výstupu ukazuje, kde jsou spuštěny tyto lusky:

- @no__t – 0 lusky
- metriky `prometheus-*` pod
- řídicí panel pro analýzu a monitorování `grafana-*` pod
- řídicí panel mřížky služby doplňku `kiali` pod

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

Měli byste mít dvě `istio-init-crd-*` lusků se stavem `Completed`. Tyto lusky byly zodpovědné za spouštění úloh, které CRDs vytvořili v předchozím kroku. Všechny ostatní lusky by měly zobrazit stav `Running`. Pokud vaše lusky nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělá. Pokud jakékoli lusky nahlásí problém, zkontrolujte výstup a stav pomocí příkazu [kubectl popsat pod][kubectl-describe] .

## <a name="accessing-the-add-ons"></a>Přístup k doplňkům

V naší instalaci jsme Istioi několik doplňků, které poskytují další funkce. Uživatelská rozhraní pro doplňky nejsou veřejně vystavena prostřednictvím externí IP adresy. Pro přístup k uživatelským rozhraním doplňku použijte příkaz [kubectl-dopředný port][kubectl-port-forward] . Tento příkaz vytvoří zabezpečené připojení mezi klientským počítačem a relevantním pod v clusteru AKS.

Přidali jsme další vrstvu zabezpečení pro Grafana a Kiali, a to zadáním přihlašovacích údajů dříve v tomto článku.

### <a name="grafana"></a>Grafana

Řídicí panely pro analýzu a monitorování pro Istio poskytuje [Grafana][grafana]. Předejte místní port `3000` na klientském počítači na adresu `3000` na počítači pod systémem Grafana v clusteru AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Následující příklad výstupu ukazuje, že je nakonfigurováno přeposílání portů pro Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Na klientském počítači teď můžete Grafana na následující adrese URL – [http://localhost:3000](http://localhost:3000). Nezapomeňte použít přihlašovací údaje, které jste po zobrazení výzvy vytvořili prostřednictvím tajného klíče Grafana.

### <a name="prometheus"></a>Prometheus

Metriky pro Istio poskytuje [Prometheus][prometheus]. Předejte místní port `9090` na klientském počítači na adresu `9090` na počítači pod systémem Prometheus v clusteru AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Následující příklad výstupu ukazuje, že je nakonfigurováno přeposílání portů pro Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Nyní se můžete dostat k prohlížeči výrazů Prometheus na následující adrese URL na klientském počítači – [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Trasování v rámci Istio zajišťuje [Jaeger][jaeger]. Předejte místní port `16686` na klientském počítači na adresu `16686` na počítači pod systémem Jaeger v clusteru AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Následující příklad výstupu ukazuje, že je nakonfigurováno přeposílání portů pro Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Nyní se můžete dostat na uživatelské rozhraní pro trasování Jaeger na následující adrese URL na klientském počítači – [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Řídicí panel pro pozorování sítě služby poskytuje [Kiali][kiali]. Předejte místní port `20001` na klientském počítači na adresu `20001` na počítači pod systémem Kiali v clusteru AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Následující příklad výstupu ukazuje, že je nakonfigurováno přeposílání portů pro Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Nyní se můžete dostat na řídicí panel zobrazení sítě služby Kiali na následující adrese URL na klientském počítači – [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Nezapomeňte použít přihlašovací údaje, které jste po zobrazení výzvy vytvořili prostřednictvím tajného klíče Kiali.

## <a name="uninstall-istio-from-aks"></a>Odinstalace Istio z AKS

> [!WARNING]
> Odstranění Istio ze spuštěného systému může vést k problémům souvisejícím s provozem mezi vašimi službami. Než budete pokračovat, ujistěte se, že jste provedli správné fungování vašeho systému bez Istio.

### <a name="remove-istio-components-and-namespace"></a>Odebrat součásti a obor názvů Istio

K odebrání Istio z clusteru AKS použijte následující příkazy. Příkazy `helm delete` odstraní grafy `istio` a `istio-init` a příkaz `kubectl delete ns` odebere obor názvů `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Odebrat CRDs Istio

Výše uvedené příkazy odstraní všechny komponenty Istio a obor názvů, ale pořád ještě zbývá s CRDsem Istio. Chcete-li odstranit CRDs, můžete použít jeden z následujících přístupů.

Postup #1 – Tento příkaz předpokládá, že spouštíte tento krok ze složky na nejvyšší úrovni stažené a extrahované verze Istio, kterou jste použili k instalaci Istio pomocí.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Přístup #2 – použijte jeden z těchto příkazů, pokud už nebudete mít přístup ke staženým a extrahovaným vydaným verzím Istio, které jste použili k instalaci Istio pomocí nástroje. Tento příkaz bude trvat déle, než bude trvat několik minut, než se dokončí.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Prostředí
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete použít Istio k poskytování inteligentního směrování k zavedení testovací verze:

> [!div class="nextstepaction"]
> [Scénář inteligentního směrování AKS Istio][istio-scenario-routing]

Pokud chcete prozkoumat další možnosti instalace a konfigurace pro Istio, přečtěte si následující oficiální články pro Istio:

- [Průvodce instalací Istio-Helm][istio-install-helm]
- [Istio – možnosti instalace Helm][istio-install-helm-options]

Můžete také postupovat podle dalších scénářů pomocí [příkladu aplikace Istio Bookinfo][istio-bookinfo-example].

Informace o tom, jak monitorovat aplikaci AKS pomocí Application Insights a Istio, najdete v následující dokumentaci k Azure Monitor:
- [Nulová monitorování aplikací instrumentace pro hostované aplikace Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
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

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
