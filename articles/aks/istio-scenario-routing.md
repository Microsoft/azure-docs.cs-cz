---
title: Inteligentní směrování a testovací verze s Istio ve službě Azure Kubernetes Service (AKS)
description: Další informace o použití Istio k zajištění inteligentního směrování a nasadit testovací verze v clusteru služby Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702213"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Použijte inteligentní směrování a testovací verze s Istio ve službě Azure Kubernetes Service (AKS)

[Istio] [ istio-github] je open source služba sítě, který poskytuje sadu klíčových funkcí, které v rámci mikroslužby v clusteru Kubernetes. Tyto funkce patří řízení provozu, identita služby a zabezpečení, vynucení zásad a observability. Další informace o Istio, najdete v oficiální [novinky Istio?] [ istio-docs-concepts] dokumentaci.

V tomto článku se dozvíte, jak používat funkce správy provozu Istio. Ukázková hlasovací aplikace AKS slouží k prozkoumání inteligentního směrování a testovací verze.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasazení aplikace
> * Aktualizace aplikace
> * Zavedení testovací verze aplikace
> * Dokončení tohoto uvedení

## <a name="before-you-begin"></a>Než začnete

> [!NOTE]
> Tento scénář je testovaná s verzí Istio `1.1.3`.

Kroky popsané v tomto článku se předpokládá jste vytvořili AKS cluster (Kubernetes `1.11` a vyšších povolena pomocí RBAC) a navázali `kubectl` připojení ke clusteru. Budete také potřebovat Istio nainstalovaný ve vašem clusteru.

Pokud potřebujete pomoc s libovolnou z těchto položek, přejděte na téma [AKS quickstart] [ aks-quickstart] a [nainstalovat Istio ve službě AKS] [ istio-install] pokyny.

## <a name="about-this-application-scenario"></a>O tomto scénáři aplikace

Ukázková hlasovací aplikace AKS poskytuje dvě možnosti hlasování (**kočky** nebo **PSI**) pro uživatele. Je součást úložiště, která udržuje počet hlasů pro jednotlivé možnosti. Kromě toho je komponentu analytics, která poskytuje podrobnosti o hlasování pro jednotlivé možnosti.

V tomto scénáři aplikace začít nasazením verze `1.0` hlasovací aplikace a verze `1.0` součásti analytics. Komponenta analytics poskytuje jednoduché počty pro počet hlasů. Hlasovací aplikace a komponenty analytics komunikovat s verzí `1.0` součásti úložiště, která je založená na Redis.

Upgrade komponenty analytics na verzi `1.1`, které poskytuje počty a nyní je celkový počet zpracovaných položek a procenta.

Podmnožinu uživatelů testovací verze `2.0` aplikace přes testovací verze. Tato nová verze používá úložiště komponenty, která je založená na databázi MySQL.

Jakmile budete jistí tuto verzi `2.0` funguje dle očekávání v podmnožinou vašich uživatelů, bude možné zavést verze `2.0` všem svým uživatelům.

## <a name="deploy-the-application"></a>Nasazení aplikace

Začněme tím, že nasazení aplikace do clusteru Azure Kubernetes Service (AKS). Následující diagram znázorňuje, co běží na konci této části – verze `1.0` všech součástí příchozí požadavky opraveno prostřednictvím brány Istio příchozího přenosu dat:

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-01.png)

Artefakty, které budete muset postupovat podle tohoto článku jsou dostupné v [-Samples/aks – hlasování – aplikace Azure] [ github-azure-sample] úložiště GitHub. Chcete stáhnout artefakty nebo naklonujte úložiště následujícím způsobem:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Přejděte do následující složky v úložišti stažené / klonovaný a spusťte všechny následné kroky z této složky:

```console
cd scenarios/intelligent-routing-with-istio
```

Nejprve vytvořte obor názvů v clusteru AKS pro ukázkovou AKS hlasovací aplikaci s názvem `voting` následujícím způsobem:

```azurecli
kubectl create namespace voting
```

Obor názvů se popisek `istio-injection=enabled`. Tento popisek se dá pokyn Istio automaticky vkládat proxy servery istio jako sajdkáry do všech pody v tomto oboru názvů.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Teď Pojďme vytvářet komponenty pro AKS hlasovací aplikace. Vytvoření těchto součástí `voting` obor názvů vytvořený v předchozím kroku.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Následující příklad výstupu ukazuje vytvářené prostředky:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio má některé specifické požadavky týkající se podů a služeb. Další informace najdete v tématu [Istio požadavky Podů a služby dokumentaci][istio-requirements-pods-and-services].

Pokud chcete zobrazit podů, které byly vytvořeny, použijte [kubectl get pods] [ kubectl-get] takto:

```azurecli
kubectl get pods -n voting
```

Následující příklad výstupu ukazuje existují tři instance `voting-app` pod a jedna instance obou `voting-analytics` a `voting-storage` pody. Každý podů má dva kontejnery. Jeden z těchto kontejnerů je komponenta, a druhý je `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Chcete-li zobrazit informace o pod, použijte [kubectl popisují pod][kubectl-describe]. Nahraďte název pod ve vlastní cluster AKS z výstupu předchozího názvu podu:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy` Kontejneru se automaticky byly vloženy Istio ke správě síťového provozu do a z komponent, jak je znázorněno v následujícím příkladu výstupu:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Nelze připojit k hlasovací aplikace, dokud nevytvoříte Istio [brány] [ istio-reference-gateway] a [virtuální služby][istio-reference-virtualservice]. Tyto prostředky Istio směrovat provoz z brány výchozí Istio příchozího přenosu dat pro naši aplikaci.

> [!NOTE]
> A **brány** je komponenta na hraničních zařízeních síť služby, který přijímá příchozí nebo odchozí provoz protokolu HTTP a TCP.
> 
> A **virtuální služby** definuje sadu pravidel směrování pro jeden nebo více cílových služeb.

Použití `kubectl apply` příkaz pro nasazení brány a virtuální služby yaml. Nezapomeňte zadat obor názvů, který tyto prostředky se nasadí do.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Následující příklad výstupu ukazuje novou bránu a vytváří virtuální služby:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Získejte IP adresu brány Istio příchozího přenosu dat pomocí následujícího příkazu:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Následující příklad výstupu ukazuje na IP adresu brány příchozího přenosu dat:

```
20.188.211.19
```

Otevřete prohlížeč a vložte adresu IP. Zobrazí se ukázková hlasovací aplikace pro AKS.

![AKS hlasovací aplikace běžící v našich Istio povolené clusteru AKS.](media/istio/deploy-app-01.png)

Informace v dolní části obrazovky ukazuje, že aplikace použije verzi `1.0` z `voting-app` a verze `1.0` z `voting-storage` (Redis).

## <a name="update-the-application"></a>Aktualizace aplikace

Jsme můžeme nasadit novou verzi součásti analytics. Tato nová verze `1.1` zobrazuje celkový počet položek a procenta kromě počty v jednotlivých kategoriích.

Následující diagram znázorňuje, co bude spuštěn na konci této části – jen verzi `1.1` z našich `voting-analytics` komponenta má provoz směrovat z `voting-app` komponenty. I když verze `1.0` z našich `voting-analytics` komponenty běží dál a odkazuje `voting-analytics` služby, proxy servery Istio zakazují provoz do a z něj.

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-02.png)

Nasadíme verze `1.1` z `voting-analytics` komponenty. Vytvoření této komponenty v `voting` obor názvů:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Následující příklad výstupu ukazuje vytvářené prostředky:

```console
deployment.apps/voting-analytics-1-1 created
```

Otevřete ukázku, kterou AKS hlasovací aplikace v prohlížeči, pomocí IP adresy brány Istio příchozího přenosu dat získaných v předchozím kroku.

Váš prohlížeč střídá dvě zobrazení je uvedeno níže. Vzhledem k tomu, že používáte Kubernetes [služby] [ kubernetes-service] pro `voting-analytics` komponentu s názvem bez přípony selektor (`app: voting-analytics`), Kubernetes používá výchozí chování mezi kruhové dotazování podů, které odpovídají této selektor. V takovém případě se obě verze `1.0` a `1.1` z vaší `voting-analytics` pody.

![Verze 1.0 analytics součást spuštěná v naší aplikaci AKS Voting.](media/istio/deploy-app-01.png)

![Verze 1.1 analytics součást spuštěná v naší aplikaci AKS Voting.](media/istio/update-app-01.png)

Můžete vizualizovat, přepínání mezi dvěma verzemi `voting-analytics` komponenty následujícím způsobem. Nezapomeňte použít IP adresu vaší vlastní Istio příchozího přenosu dat brány.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Následující příklad výstupu ukazuje příslušné části vrácený webovou stránku jako lokality Přepne mezi verzí:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Zamknout provoz na verzi 1.1 aplikace

Nyní Pojďme uzamknout provoz pouze verzi `1.1` z `voting-analytics` komponenty a verze `1.0` z `voting-storage` komponenty. Pak definujte pravidla směrování pro všechny ostatní součásti.

> * A **virtuální služby** definuje sadu pravidel směrování pro jeden nebo více cílových služeb.
> * A **cílové pravidlo** definuje zásady provozu a zásady pro konkrétní verzi.
> * A **zásady** definuje, jaké metody ověřování mohou být přijaty na workload(s).

Použití `kubectl apply` příkaz pro nahrazení definice virtuální služby na vaše `voting-app` a přidejte [cíl pravidla] [ istio-reference-destinationrule] a [virtuální služby] [ istio-reference-virtualservice] pro jiné komponenty. Přidáte [zásady] [ istio-reference-policy] k `voting` obor názvů a ujistěte se, že všechny komunikaci mezi službami je zabezpečený pomocí vzájemné TLS a klientských certifikátů.

* Zásada má `peers.mtls.mode` nastavena na `STRICT` zajistit, že se vzájemné TLS vynucuje mezi službami v rámci `voting` oboru názvů.
* Také nastavíme `trafficPolicy.tls.mode` k `ISTIO_MUTUAL` v našich cíl pravidla. Istio poskytne silné identity služby a zabezpečuje komunikaci mezi službami pomocí vzájemné TLS a klientské certifikáty, které Istio transparentně spravuje.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Následující příklad výstupu ukazuje nové zásady, určení pravidel a virtuální služby se vytvoří nebo aktualizuje:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Pokud otevřete AKS hlasovací aplikace v prohlížeči znovu, pouze novou verzi `1.1` z `voting-analytics` používá komponenty `voting-app` komponenty.

![Verze 1.1 analytics součást spuštěná v naší aplikaci AKS Voting.](media/istio/update-app-01.png)

Můžete vizualizovat, můžete nyní pouze směrují na verzi `1.1` z vaší `voting-analytics` komponenty následujícím způsobem. Nezapomeňte použít IP adresu své vlastní brány Istio příchozího přenosu dat:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Následující příklad výstupu ukazuje příslušné části vrácené webové stránky:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Pojďme teď potvrďte, že Istio používá vzájemné TLS pro zabezpečení komunikace mezi každou z našich služeb. K tomu použijeme [tls kontrola ověřovacích] [ istioctl-authn-tls-check] příkaz `istioctl` klienta binární, které mají následující podobu.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Tuto sadu příkazů obsahují informace o přístup do určeným službám ze všech podů, které jsou v oboru názvů a splňují sadu popisků:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

Tato následující příklad výstupu ukazuje, že se vzájemné TLS vynucuje pro každou z našich výše uvedené dotazy. Výstup ukazuje také zásad a určení pravidla, která vynucuje vzájemné TLS:

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Zavedení testovací verze aplikace

Teď můžeme nasadit novou verzi `2.0` z `voting-app`, `voting-analytics`, a `voting-storage` komponenty. Nové `voting-storage` komponenty nahrazujícím Redis, MySQL a `voting-app` a `voting-analytics` komponenty se aktualizují, aby mohly využít nové `voting-storage` komponenty.

`voting-app` Komponenta teď podporuje funkci příznak funkce. Tento příznak funkce umožňuje testovat testovací verze funkce Istio pro určitou podskupinu uživatelů.

Následující diagram znázorňuje, co budete mít spuštěn na konci této části.

* Verze `1.0` z `voting-app` komponenty, verze `1.1` z `voting-analytics` komponenty a verze `1.0` z `voting-storage` komponenty jsou schopny komunikovat mezi sebou.
* Verze `2.0` z `voting-app` komponenty, verze `2.0` z `voting-analytics` komponenty a verze `2.0` z `voting-storage` komponenty jsou schopny komunikovat mezi sebou.
* Verze `2.0` z `voting-app` komponenty jsou pouze dostupné pro uživatele, kteří nastaven příznak konkrétní funkce. Tato změna se spravuje pomocí příznak funkce prostřednictvím souboru cookie.

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-03.png)

Nejprve aktualizujte Istio cíl pravidla a virtuální služby, aby vyhovovaly pro tyto nové komponenty. Tyto aktualizace ověřte, že nechcete směrovat provoz nesprávně na nových komponent a uživatelé Nezískávat neočekávaný přístup:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Následující příklad výstupu ukazuje cíl pravidla a aktualizuje virtuální služby:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

V dalším kroku přidáme objekty Kubernetes pro novou verzi `2.0` komponenty. Je také aktualizovat `voting-storage` služby, které chcete zahrnout `3306` port pro MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Následující příklad výstupu ukazuje objekty Kubernetes jsou úspěšně aktualizovány nebo vytvořili:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Počkejte, dokud všechny verze `2.0` pody spuštěné. Použití [kubectl get pods] [ kubectl-get] příkazu zobrazíte všechna podů v `voting` obor názvů:

```azurecli
kubectl get pods --namespace voting
```

Nyní byste měli být schopni přepínat mezi verzí `1.0` a verze `2.0` (testovací) hlasovací aplikace. Přepnout příznak funkce v dolní části obrazovky nastaví soubor cookie. Tento soubor cookie používá `voting-app` virtuální služby ke směrování uživatelů na novou verzi `2.0`.

![Verze 1.0 AKS hlasovací aplikace - příznak funkce, které není nastavena.](media/istio/canary-release-01.png)

![Verze 2.0 AKS hlasovací aplikace – sada je příznak funkce.](media/istio/canary-release-02.png)

Hlas počty se liší mezi verzemi aplikace. Tento rozdíl upozorňuje, že používáte dva back-EndY jiného úložiště.

## <a name="finalize-the-rollout"></a>Dokončení tohoto uvedení

Jakmile úspěšně otestujete testovací verze, aktualizujte `voting-app` virtuální služby směrování veškerého provozu na verzi `2.0` z `voting-app` komponenty. Všichni uživatelé pak uvidí verze `2.0` aplikace bez ohledu na to, zda je nastaven příznak funkce nebo ne:

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-04.png)

Aktualizujte všechny cílové pravidla k odebrání verze komponent už má aktivní. Potom aktualizujte všechny virtuální služby zastavit odkazující na těchto verzí.

Vzhledem k tomu, že již není k dispozici veškerý provoz některou ze starší verze komponent, nyní můžete bezpečně odstranit všechna nasazení pro tyto součásti.

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-05.png)

Budete mít teď úspěšně nasazeny novou verzi AKS hlasovací aplikace.

## <a name="clean-up"></a>Vyčištění 

Můžete odebrat AKS hlasovací aplikace jsme použili v tomto scénáři z clusteru AKS tak, že odstraníte `voting` oboru názvů následujícím způsobem:

```azurecli
kubectl delete namespace voting
```

Následující příklad výstupu ukazuje, že všechny komponenty AKS hlasovací aplikace byly odebrány z clusteru AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Další postup

Můžete prozkoumat další scénáře s využitím [Istio Bookinfo aplikace – příklad][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
