---
title: Inteligentní směrování a Kanárské verze pomocí Istio ve službě Azure Kubernetes Service (AKS)
description: Naučte se používat Istio k poskytování inteligentního směrování a nasazování Kanárských verzí do clusteru Azure Kubernetes Service (AKS).
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4c29658473aaa50168175c76234dfca34fcdad83
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594104"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Použití inteligentního směrování a Kanárských verzí s Istio ve službě Azure Kubernetes Service (AKS)

[Istio][istio-github] je open source síť, která poskytuje klíčovou sadu funkcí napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří Správa provozu, identita služby a zabezpečení, vynucování zásad a jejich pozorování. Další informace o Istio najdete v dokumentaci oficiální dokumentace k [Istio?][istio-docs-concepts] .

V tomto článku se dozvíte, jak používat funkci správy provozu Istio. Ukázková hlasovací aplikace AKS slouží k prozkoumávání inteligentního směrování a zkušební verze.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasazení aplikace
> * Aktualizace aplikace
> * Zavedení Kanárské verze aplikace
> * Finalizace zavedení

## <a name="before-you-begin"></a>Než začnete

> [!NOTE]
> Tento scénář byl testován proti Istio verze `1.3.2`.

V krocích popsaných v tomto článku se předpokládá, že jste vytvořili cluster AKS (Kubernetes `1.13` a novější s povoleným RBAC) a navázali `kubectl` spojení s clusterem. Ve svém clusteru budete taky muset nainstalovat Istio.

Pokud potřebujete pomoc s kteroukoli z těchto položek, přečtěte si [rychlý Start AKS][aks-quickstart] a [nainstalujte Istio v][istio-install] tématu pokyny pro AKS.

## <a name="about-this-application-scenario"></a>O tomto scénáři aplikace

Ukázková hlasovací aplikace AKS poskytuje uživatelům dvě možnosti hlasování (**kočky** nebo **psi**). Existuje součást úložiště, která uchovává počet hlasů pro jednotlivé možnosti. Kromě toho existuje analytická komponenta, která poskytuje podrobné informace o přetypování hlasů pro jednotlivé možnosti.

V tomto scénáři aplikace začnete nasazením verze `1.0` hlasovací aplikace a verze `1.0` součásti Analytics. Komponenta Analytics poskytuje pro počet hlasů jednoduché počty. Komponenta pro hlasovací aplikace a analýzy spolupracuje s `1.0` verzí komponenty úložiště, která je zajištěná pomocí Redis.

Provedete upgrade součásti Analytics na verzi `1.1`, která poskytuje počty a nyní celkové a procentuální hodnoty.

Podmnožina zkušební verze aplikace `2.0` aplikaci v rámci Kanárských vydání. Tato nová verze používá komponentu úložiště, která je zálohovaná databází MySQL.

Jakmile si nejste jistí, že verze `2.0` v podmnožině uživatelů funguje podle očekávání, navedete `2.0` verze na všechny vaše uživatele.

## <a name="deploy-the-application"></a>Nasazení aplikace

Pojďme začít nasazovat aplikaci do clusteru Azure Kubernetes Service (AKS). Následující diagram ukazuje, co se spouští na konci této části – verze `1.0` všech komponent, které mají příchozí požadavky prostřednictvím brány Istio příchozí připojení k provozu:

![Komponenty hlasovacích aplikací AKS a směrování.](media/servicemesh/istio/scenario-routing-components-01.png)

Artefakty, které je třeba provést spolu s tímto článkem, jsou k dispozici v úložišti GitHub [Azure-Samples/AKS-hlasování-App][github-azure-sample] . Můžete buď stáhnout artefakty, nebo klonovat úložiště následujícím způsobem:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Přejděte do následující složky ve staženém/klonovaném úložišti a spusťte všechny následné kroky z této složky:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Nejprve v clusteru AKS vytvořte obor názvů pro ukázkovou aplikaci AKS s názvem `voting` následujícím způsobem:

```azurecli
kubectl create namespace voting
```

Označte obor názvů pomocí `istio-injection=enabled`. Tento popisek instruuje Istio, aby automaticky vložil Istio-proxy jako sajdkáry do všech lusků v tomto oboru názvů.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Nyní vytvoříme komponenty pro hlasovací aplikaci AKS. Vytvořte tyto komponenty v oboru názvů `voting` vytvořeném v předchozím kroku.

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
> Istio má několik specifických požadavků kolem lusků a služeb. Další informace najdete v [dokumentaci Istio požadavky na lusky a služby][istio-requirements-pods-and-services].

V případě, že byly vytvořeny lusky, použijte příkaz [kubectl Get lusks][kubectl-get] následujícím způsobem:

```azurecli
kubectl get pods -n voting --show-labels
```

Následující příklad výstupu ukazuje, že existují tři instance `voting-app` pod a jedna instance `voting-analytics` a `voting-storage` lusky. Každé lusky má dva kontejnery. Jeden z těchto kontejnerů je komponenta a druhá je `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Pokud chcete zobrazit informace o pod, použijeme příkaz [kubectl popsat pod][kubectl-describe] s selektory popisků a vyberete `voting-analytics` pod. Výstup vyfiltrujeme, aby se zobrazily podrobnosti o dvou kontejnerech v poli pod:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

K hlasovací aplikaci se nemůžete připojit, dokud nevytvoříte [bránu][istio-reference-gateway] Istio a [virtuální službu][istio-reference-virtualservice]. Tyto prostředky Istio směrují provoz z výchozí brány Istio příchozí komunikace do naší aplikace.

> [!NOTE]
> **Brána** je součást na hranici sítě, která přijímá příchozí nebo odchozí přenosy HTTP a TCP.
> 
> **Virtuální služba** definuje sadu pravidel směrování pro jednu nebo více cílových služeb.

Pomocí příkazu `kubectl apply` nasaďte bránu a YAML virtuální služby. Nezapomeňte zadat obor názvů, do kterého jsou tyto prostředky nasazeny.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Následující příklad výstupu ukazuje nově vytvořenou bránu a virtuální službu:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

IP adresu Istio příchozí brány získáte pomocí následujícího příkazu:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

V následujícím příkladu výstupu se zobrazuje IP adresa brány příchozího přenosu dat:

```
20.188.211.19
```

Otevřete prohlížeč a vložte IP adresu. Zobrazí se ukázková hlasovací aplikace AKS.

![Hlasovací aplikace AKS spuštěná v našem Istio clusteru AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Informace v dolní části obrazovky ukazují, že aplikace používá `1.0` verze `voting-app` a verze `1.0` `voting-storage` (Redis).

## <a name="update-the-application"></a>Aktualizace aplikace

Pojďme nasadit novou verzi komponenty Analytics. Tato nová verze `1.1` zobrazuje součty a procentní podíly kromě počtu jednotlivých kategorií.

Následující diagram ukazuje, co bude běžet na konci této části – pouze `1.1` verze `voting-analytics` součásti má provoz směrovaný ze `voting-app` komponenty. I když verze `1.0` naší `voting-analytics` komponenty nadále běží a na ni odkazuje služba `voting-analytics`, proxy servery Istio zakazují provoz do a z něj.

![Komponenty hlasovacích aplikací AKS a směrování.](media/servicemesh/istio/scenario-routing-components-02.png)

Pojďme nasazovat verzi `1.1` komponenty `voting-analytics`. Vytvořte tuto součást v oboru názvů `voting`:

```azurecli
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Následující příklad výstupu ukazuje vytvářené prostředky:

```console
deployment.apps/voting-analytics-1-1 created
```

Otevřete znovu ukázkovou hlasovací aplikaci AKS v prohlížeči a použijte IP adresu Istio brány příchozího přenosu dat získaná v předchozím kroku.

Mezi dvěma zobrazeními, které jsou uvedeny níže, se Váš prohlížeč liší. Vzhledem k tomu, že používáte [službu][kubernetes-service] Kubernetes pro komponentu `voting-analytics` jenom s jedním selektorm popisku (`app: voting-analytics`), Kubernetes použije výchozí chování kruhového dotazování mezi lusky, které odpovídají tomuto selektoru. V tomto případě je to jak verze `1.0`, tak `1.1` vaší `voting-analytics` lusky.

![Verze 1,0 analytické komponenty běžící v naší hlasovací aplikaci AKS](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Verze 1,1 analytické komponenty běžící v naší hlasovací aplikaci AKS](media/servicemesh/istio/scenario-routing-update-app-01.png)

Můžete vizualizovat přepínání mezi dvěma verzemi `voting-analytics` komponenty následujícím způsobem. Nezapomeňte použít IP adresu vlastní brány Istio příchozího přenosu dat.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Následující příklad výstupu ukazuje relevantní část vráceného webu jako přepínače lokality mezi verzemi:

```console
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Uzamčení provozu do verze 1,1 aplikace

Teď tento provoz zamkneme jenom na `1.1` verze `voting-analytics` komponenty a na verzi `1.0` součásti `voting-storage`. Pak definujete pravidla směrování pro všechny ostatní komponenty.

> * **Virtuální služba** definuje sadu pravidel směrování pro jednu nebo více cílových služeb.
> * **Cílové pravidlo** definuje zásady provozu a zásady pro konkrétní verzi.
> * **Zásady** definují, jaké metody ověřování se dají přijmout pro úlohy.

Pomocí příkazu `kubectl apply` Nahraďte definici virtuální služby v `voting-app` a přidejte [cílová pravidla][istio-reference-destinationrule] a [virtuální služby][istio-reference-virtualservice] pro ostatní součásti. Přidáte [zásadu][istio-reference-policy] do oboru názvů `voting`, abyste zajistili, že bude zabezpečená komunikace mezi službami pomocí vzájemného protokolu TLS a klientských certifikátů.

* Zásada má `peers.mtls.mode` nastavená na `STRICT`, aby se zajistilo, že se mezi vašimi službami v rámci `voting` oboru názvů vzájemně vynutily vzájemné protokoly TLS.
* Nastavili jsme také `trafficPolicy.tls.mode` pro `ISTIO_MUTUAL` ve všech našich cílových pravidlech. Istio poskytuje služby se silnými identitami a zabezpečuje komunikaci mezi službami pomocí vzájemného protokolu TLS a klientských certifikátů, které Istio transparentně spravuje.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Následující příklad výstupu ukazuje nové zásady, cílová pravidla a virtuální služby, které se aktualizují/vytváří:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Pokud znovu otevřete hlasovací aplikaci AKS v prohlížeči, bude součást `voting-app` používat pouze novou verzi `1.1` součásti `voting-analytics`.

![Verze 1,1 analytické komponenty běžící v naší hlasovací aplikaci AKS](media/servicemesh/istio/scenario-routing-update-app-01.png)

Můžete vizualizovat, že jste nyní směrováni pouze na `1.1` verze `voting-analytics` komponenty, a to následujícím způsobem. Nezapomeňte použít IP adresu vlastní brány Istio příchozího přenosu dat:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Následující příklad výstupu ukazuje relevantní část vráceného webu:

```console
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Pojďme teď potvrdit, že Istio používá oboustranný protokol TLS k zabezpečení komunikace mezi našimi službami. Pro tuto adresu použijeme příkaz [Authn TLS-check][istioctl-authn-tls-check] v binárním souboru klienta `istioctl`, který má následující formu.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Tato sada příkazů poskytuje informace o přístupu k určeným službám, ze všech lusků, které jsou v oboru názvů a odpovídají sadě jmenovek:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Následující příklad výstupu ukazuje, že je pro každý z výše uvedených dotazů vynutil oboustranný protokol TLS. Ve výstupu se zobrazí také pravidla zásad a cíle, která vynutila vzájemné šifrování TLS:

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

## <a name="roll-out-a-canary-release-of-the-application"></a>Zavedení Kanárské verze aplikace

Nyní nasadíme novou verzi `2.0` komponent `voting-app`, `voting-analytics`a `voting-storage`. Nová součást `voting-storage` používá MySQL místo Redis a aktualizují `voting-app` a `voting-analytics` komponenty tak, aby mohly používat tuto novou `voting-storage` komponentu.

Komponenta `voting-app` nyní podporuje funkce příznaku funkcí. Tento příznak funkce umožňuje otestovat možnost Istio vydání pro skupinu uživatelů.

Následující diagram znázorňuje, co budete spouštět na konci této části.

* Verze `1.0` komponenty `voting-app`, verze `1.1` součásti `voting-analytics` a verze `1.0` součásti `voting-storage` mohou vzájemně komunikovat.
* Verze `2.0` komponenty `voting-app`, verze `2.0` součásti `voting-analytics` a verze `2.0` součásti `voting-storage` mohou vzájemně komunikovat.
* Verze `2.0` komponenty `voting-app` je dostupná jenom pro uživatele, kteří mají nastavenou konkrétní příznak funkce. Tato změna se spravuje pomocí příznaku funkce prostřednictvím souboru cookie.

![Komponenty hlasovacích aplikací AKS a směrování.](media/servicemesh/istio/scenario-routing-components-03.png)

Nejdřív pro tyto nové součásti aktualizujte cílová pravidla Istio a virtuální služby na službu stravování. Tyto aktualizace zajišťují, aby nedošlo k nesprávnému směrování provozu na nové komponenty a uživatelé nezískali neočekávaný přístup:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Následující příklad výstupu ukazuje cílová pravidla a aktualizované virtuální služby:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Nyní přidáme objekty Kubernetes pro nové verze `2.0` komponenty. Službu `voting-storage` taky aktualizujete tak, aby obsahovala `3306` port pro MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Následující příklad výstupu ukazuje, že objekty Kubernetes byly úspěšně aktualizovány nebo vytvořeny:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Počkejte, dokud nebudou spuštěné všechny verze `2.0` lusky. Pomocí příkazu [kubectl Get lusky][kubectl-get] s přepínačem `-w` Watch Sledujte změny všech lusků v oboru názvů `voting`:

```azurecli
kubectl get pods --namespace voting -w
```

Nyní byste měli být schopni přepínat mezi `1.0` verzí a verzí `2.0` (Kanárské) hlasovací aplikace. Přepínač příznak funkce v dolní části obrazovky nastaví soubor cookie. Tento soubor cookie používá virtuální služba `voting-app` ke směrování uživatelů na novou verzi `2.0`.

![Verze 1,0 AKS hlasovací aplikace – příznak funkce není nastaven.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![JE nastavená verze 2,0 AKS hlasovací aplikace – příznak funkce.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Počty hlasů se v různých verzích aplikace liší. Tento rozdíl zvýrazní, že používáte dvě různá back-endy úložiště.

## <a name="finalize-the-rollout"></a>Finalizace zavedení

Po úspěšném otestování zkušební verze aktualizujte `voting-app` virtuální službu tak, aby směrovala všechny přenosy na verzi `2.0` součásti `voting-app`. Všichni uživatelé pak uvidí `2.0` verze aplikace bez ohledu na to, jestli je nastavený příznak funkce, nebo ne:

![Komponenty hlasovacích aplikací AKS a směrování.](media/servicemesh/istio/scenario-routing-components-04.png)

Aktualizujte všechna cílová pravidla tak, aby se odebraly verze komponent, které už nechcete aktivovat. Pak aktualizujte všechny virtuální služby tak, aby přestaly odkazy na tyto verze.

Vzhledem k tomu, že už neexistují žádné přenosy do žádné ze starších verzí součástí, můžete teď všechna nasazení pro tyto součásti bezpečně odstranit.

![Komponenty hlasovacích aplikací AKS a směrování.](media/servicemesh/istio/scenario-routing-components-05.png)

Nyní jste úspěšně vyvolali novou verzi hlasovací aplikace AKS.

## <a name="clean-up"></a>Vyčištění 

Hlasovací aplikaci AKS, kterou jsme použili v tomto scénáři, můžete odebrat z clusteru AKS odstraněním oboru názvů `voting` následujícím způsobem:

```azurecli
kubectl delete namespace voting
```

Následující příklad výstupu ukazuje, že všechny součásti hlasovací aplikace AKS byly odebrány z vašeho clusteru AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Další kroky

Další scénáře můžete prozkoumat pomocí [příkladu aplikace Istio Bookinfo][istio-bookinfo-example].

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
[istio-install]: ./servicemesh-istio-install.md
