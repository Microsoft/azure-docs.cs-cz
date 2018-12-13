---
title: Inteligentní směrování a testovací verze s Istio ve službě Azure Kubernetes Service (AKS)
description: Další informace o použití Istio k zajištění inteligentního směrování a nasadit testovací verze v clusteru služby Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892161"
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

Kroky popsané v tomto článku se předpokládá jste vytvořili AKS cluster (Kubernetes 1.10 a vyšších povolena pomocí RBAC) a navázali `kubectl` připojení ke clusteru. Budete také potřebovat Istio nainstalovaný ve vašem clusteru.

Pokud potřebujete pomoc s libovolnou z těchto položek, přejděte na téma [AKS quickstart] [ aks-quickstart] a [nainstalovat Istio ve službě AKS][istio-install].

## <a name="about-this-application-scenario"></a>O tomto scénáři aplikace

Ukázková hlasovací aplikace AKS poskytuje dvě možnosti hlasování (kočky nebo psy) pro uživatele. Je součást úložiště, která udržuje počet hlasů pro jednotlivé možnosti. Kromě toho je komponentu analytics, která poskytuje podrobnosti o hlasování pro jednotlivé možnosti.

V tomto článku začnete tím, že nasazení verze *1.0* hlasovací aplikace a verze *1.0* součásti analytics. Komponenta analytics poskytuje jednoduché počty pro počet hlasů. Hlasovací aplikace a komponenty analytics komunikovat s verzí *1.0* součásti úložiště, která je založená na Redis.

Upgrade komponenty analytics na verzi *1.1*, které poskytuje počty a nyní je celkový počet zpracovaných položek a procenta.

Podmnožinu uživatelů testovací verze *2.0* aplikace přes testovací verze. Tato nová verze používá úložiště komponenty, která je založená na databázi MySQL.

Jakmile budete jistí tuto verzi *2.0* funguje dle očekávání v podmnožinou vašich uživatelů, bude možné zavést verze *2.0* všem svým uživatelům.

## <a name="deploy-the-application"></a>Nasazení aplikace

Začněme tím, že nasazení aplikace do clusteru Azure Kubernetes Service (AKS). Následující diagram znázorňuje, co běží na konci této části – verze *1.0* všech součástí příchozí požadavky opraveno prostřednictvím brány Istio příchozího přenosu dat:

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-01.png)

Artefakty, které budete muset postupovat podle tohoto článku jsou dostupné v [-Samples/aks – hlasování – aplikace Azure] [ github-azure-sample] úložiště GitHub. Chcete stáhnout artefakty nebo naklonujte úložiště následujícím způsobem:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Přejděte do následující složky v úložišti stažené / klonovaný a spusťte všechny následné kroky z této složky:

```console
cd scenarios/intelligent-routing-with-istio
```

Nejprve vytvořte obor názvů v clusteru AKS pro ukázkovou AKS hlasovací aplikaci s názvem *hlasování* následujícím způsobem:

```console
kubectl create namespace voting
```

Obor názvů se popisek `istio-injection=enabled`. Tento popisek se dá pokyn Istio automaticky vkládat proxy servery istio jako sajdkáry do všech pody v tomto oboru názvů.

```console
kubectl label namespace voting istio-injection=enabled
```

Teď Pojďme vytvářet komponenty pro AKS hlasovací aplikace. Vytvoření těchto součástí *hlasování* obor názvů vytvořený v předchozím kroku.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Následující příklad výstupu ukazuje, že prostředky byly úspěšně vytvořeny:

```
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

```console
kubectl get pods -n voting
```

Následující příklad výstupu ukazuje existují tři instance *hlasovací aplikace* pod a jedna instance obou *hlasování analytics* a *hlasování úložiště* pody. Každý podů má dva kontejnery. Jeden z těchto kontejnerů je komponenta, a druhý je *istio proxy*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Chcete-li zobrazit informace o pod, použijte [kubectl popisují pod][kubectl-describe]. Nahraďte název pod ve vlastní cluster AKS z výstupu předchozího názvu podu:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

*Istio proxy* kontejneru se automaticky byly vloženy Istio ke správě síťového provozu do a z komponent, jak je znázorněno v následujícím příkladu výstupu:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

Nelze připojit k hlasovací aplikace, dokud nevytvoříte Istio [brány] [ istio-reference-gateway] a [virtuální služby][istio-reference-virtualservice]. Tyto prostředky Istio směrovat provoz z brány výchozí Istio příchozího přenosu dat pro naši aplikaci.

> [!NOTE]
> A *brány* je komponenta na hraničních zařízeních síť služby, který přijímá příchozí nebo odchozí provoz protokolu HTTP a TCP.
>
> A *virtuální služby* definuje sadu pravidel směrování pro jeden nebo více cílových služeb.

Použití `istioctl` binární klienta k nasazení brány a virtuální služby yaml. Stejně jako u `kubectl apply` příkazu, nezapomeňte zadat obor názvů, který tyto prostředky se nasadí do.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Získejte IP adresu brány Istio příchozího přenosu dat pomocí následujícího příkazu:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Následující příklad výstupu ukazuje na IP adresu brány příchozího přenosu dat:

```
52.187.250.239
```

Otevřete prohlížeč a vložte adresu IP. Zobrazí se ukázková hlasovací aplikace pro AKS.

![AKS hlasovací aplikace běžící v našich Istio povolené clusteru AKS.](media/istio/deploy-app-01.png)

Informace v dolní části obrazovky ukazuje, že aplikace použije verzi *1.0* z *hlasovací aplikace* a verze *1.0* (Redis) jako možnost úložiště.

## <a name="update-the-application"></a>Aktualizace aplikace

Jsme můžeme nasadit novou verzi součásti analytics. Tato nová verze *1.1* zobrazuje celkový počet položek a procenta kromě počty v jednotlivých kategoriích.

Následující diagram znázorňuje, co běží na konci této části – jen verzi *1.1* z našich *hlasování analytics* komponenta má provoz směrovat z *hlasovací aplikace* komponenta. I když verze *1.0* z naše *hlasování analytics* komponenty běží dál a odkazuje *hlasování analytics* služby, proxy servery Istio zakazují provoz do a z něj.

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-02.png)

Nasadíme verze *1.1* z *hlasování analytics* komponenty. Vytvoření této komponenty v *hlasování* obor názvů:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Otevřete ukázku, kterou AKS hlasovací aplikace v prohlížeči, pomocí IP adresy brány Istio příchozího přenosu dat získaných v předchozím kroku.

Váš prohlížeč střídá dvě zobrazení je uvedeno níže. Vzhledem k tomu, že používáte Kubernetes [služby] [ kubernetes-service] pro *hlasování analytics* komponentu s názvem bez přípony selektor (`app: voting-analytics`), Kubernetes používá výchozí chování kruhové dotazování mezi pody, které odpovídají této selektor. V takovém případě se obě verze *1.0* a *1.1* z vaší *hlasování analytics* pody.

![Verze 1.0 analytics součást spuštěná v naší aplikaci AKS Voting.](media/istio/deploy-app-01.png)

![Verze 1.1 analytics součást spuštěná v naší aplikaci AKS Voting.](media/istio/update-app-01.png)

Můžete vizualizovat, přepínání mezi dvěma verzemi *hlasování analytics* komponenty následujícím způsobem. Nezapomeňte použít IP adresu vaší vlastní Istio příchozího přenosu dat brány.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
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

Nyní Pojďme uzamknout provoz pouze verzi *1.1* z *hlasování analytics* komponenty a verze *1.0* z *hlasování úložiště* komponenta. Pak definujte pravidla směrování pro všechny ostatní součásti.

> * A *virtuální služby* definuje sadu pravidel směrování pro jeden nebo více cílových služeb.
> * A *cílové pravidlo* definuje zásady provozu a zásady pro konkrétní verzi.
> * A *zásady* definuje, jaké metody ověřování mohou být přijaty na workload(s).

Můžete použít `istioctl` klienta binární pro nahrazení definice virtuální služby na vaše *hlasovací aplikace* a přidejte [cíl pravidla] [ istio-reference-destinationrule] a [ Služby virtuálních] [ istio-reference-virtualservice] pro jiné komponenty.

Můžete také přidat [zásad] [ istio-reference-policy] k *hlasovací* obor názvů a ujistěte se, že všechny komunikaci mezi službami je zabezpečený pomocí vzájemné TLS a klientských certifikátů.

Jsou existující definice virtuální služby pro *hlasovací aplikace* nahradit, použijte `istioctl replace` takto:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

Následující příklad výstupu ukazuje, jsou úspěšně aktualizovány Istio virtuální služby:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Pak pomocí `istioctl create` příkaz pro přidání nové zásady a také nová pravidla cíl a virtuální služby pro všechny ostatní součásti.

* Zásada má `peers.mtls.mode` nastavena na `STRICT` zajistit, že se vzájemné TLS vynucuje mezi službami v rámci *hlasování* oboru názvů.
* Můžete také nastavit `trafficPolicy.tls.mode` k `ISTIO_MUTUAL` v našich cíl pravidla. Istio poskytne silné identity služby a zabezpečuje komunikaci mezi službami pomocí vzájemné TLS a klientské certifikáty, které Istio transparentně spravuje.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

Následující příklad výstupu ukazuje novou zásadu, určení pravidel a virtuální služby byly úspěšně vytvořeny:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Pokud otevřete AKS hlasovací aplikace v prohlížeči znovu, pouze novou verzi *1.1* z *hlasování analytics* používá komponenty *hlasovací aplikace* komponenty.

![Verze 1.1 analytics součást spuštěná v naší aplikaci AKS Voting.](media/istio/update-app-01.png)

Můžete snadněji vizualizovat, že jsme teď jenom směrují na verzi *1.1* z vaší *hlasování analytics* komponenty následujícím způsobem. Nezapomeňte použít IP adresu brány příchozího přenosu dat Istio.

Můžete vizualizovat, můžete nyní pouze směrují na verzi *1.1* z vaší *hlasování analytics* komponenty následujícím způsobem. Nezapomeňte použít IP adresu své vlastní brány Istio příchozího přenosu dat:

```azurecli-interactive
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Následující příklad výstupu ukazuje příslušné části vrácené webové stránky:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Potvrďte, že Istio používá vzájemné TLS k zabezpečení komunikace mezi každou z našich služeb. Následující příkazy zkontrolujte nastavení protokolu TLS pro každou *hlasovací aplikace* služby:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

Tato následující příklad výstupu ukazuje, že se vzájemné TLS vynucuje pro každou ze služeb prostřednictvím zásad a určení pravidel:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Zavedení testovací verze aplikace

Teď můžeme nasadit novou verzi *2.0* z *hlasovací aplikace*, *hlasování analytics*, a *hlasování úložiště* komponenty. Nové *hlasování úložiště* komponenty nahrazujícím Redis, MySQL a *hlasovací aplikace* a *hlasování analytics* komponenty se aktualizují, aby mohly používat tento nový *hlasování úložiště* komponenty.

*Hlasovací aplikace* komponenta teď podporuje funkci příznak funkce. Tento příznak funkce umožňuje testovat testovací verze funkce Istio pro určitou podskupinu uživatelů.

Následující diagram znázorňuje, co běží na konci této části.

* Verze *1.0* z *hlasovací aplikace* komponenty, verze *1.1* z *hlasování analytics* komponenty a verze  *1.0* z *hlasování úložiště* komponenty jsou schopny komunikovat mezi sebou.
* Verze *2.0* z *hlasovací aplikace* komponenty, verze *2.0* z *hlasování analytics* komponenty a verze  *2.0* z *hlasování úložiště* komponenty jsou schopny komunikovat mezi sebou.
* Verze *2.0* z *hlasovací aplikace* komponenty jsou pouze dostupné pro uživatele, kteří nastaven příznak konkrétní funkce. Tato změna se spravuje pomocí příznak funkce prostřednictvím souboru cookie.

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-03.png)

Nejprve aktualizujte Istio cíl pravidla a virtuální služby, aby vyhovovaly pro tyto nové komponenty. Tyto aktualizace ověřte, že nechcete směrovat provoz nesprávně na nových komponent a uživatelé Nezískávat neočekávaný přístup:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Následující příklad výstupu ukazuje, že jsou úspěšně aktualizovány určení pravidel a virtuální služby:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

V dalším kroku přidáme objekty Kubernetes pro novou verzi *2.0* komponenty. Je také aktualizovat *hlasování úložiště* služby, které chcete zahrnout *3306* port pro MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Následující příklad výstupu ukazuje objekty Kubernetes jsou úspěšně aktualizovány nebo vytvořili:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Počkejte, dokud všechny verze *2.0* pody spuštěné. Použití [kubectl get pods] [ kubectl-get] příkazu zobrazíte všechna podů v *hlasování* obor názvů:

```azurecli-interactive
kubectl get pods --namespace voting
```

Nyní byste měli být schopni přepínat mezi verzí *1.0* a verze *2.0* (testovací) hlasovací aplikace. Přepnout příznak funkce v dolní části obrazovky nastaví soubor cookie. Tento soubor cookie používá *hlasovací aplikace* virtuální služby ke směrování uživatelů na novou verzi *2.0*.

![Verze 1.0 AKS hlasovací aplikace - příznak funkce, které není nastavena.](media/istio/canary-release-01.png)

![Verze 2.0 AKS hlasovací aplikace – sada je příznak funkce.](media/istio/canary-release-02.png)

Hlas počty se liší mezi verzemi aplikace. Tento rozdíl upozorňuje, že používáte dva back-EndY jiného úložiště.

## <a name="finalize-the-rollout"></a>Dokončení tohoto uvedení

Jakmile úspěšně otestujete testovací verze, aktualizujte *hlasovací aplikace* virtuální služby směrování veškerého provozu na verzi *2.0* z *hlasovací aplikace* komponenty. Všichni uživatelé pak uvidí verze *2.0* aplikace bez ohledu na to, zda je nastaven příznak funkce nebo ne:

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-04.png)

Aktualizujte všechny cílové pravidla k odebrání verze komponent už má aktivní. Potom aktualizujte všechny virtuální služby zastavit odkazující na těchto verzí.

Vzhledem k tomu, že již není k dispozici veškerý provoz některou ze starší verze komponent, nyní můžete bezpečně odstranit všechna nasazení pro tyto součásti.

![AKS hlasování komponent aplikace a směrování.](media/istio/components-and-routing-05.png)

Budete mít teď úspěšně nasazeny novou verzi AKS hlasovací aplikace.

## <a name="next-steps"></a>Další postup

Můžete prozkoumat další scénáře s využitím [Istio Bookinfo aplikace – příklad][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
