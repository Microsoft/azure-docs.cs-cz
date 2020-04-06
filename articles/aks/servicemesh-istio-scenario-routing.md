---
title: Použití Istio pro inteligentní směrování
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak používat Istio k inteligentnímu směrování a nasazování kanárských verzí v clusteru Služby Azure Kubernetes (AKS).
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 01a7764eb0a353e6842441093f70ad29c9316bbd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668270"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Používejte inteligentní směrování a kanárské verze s Istio ve službě Azure Kubernetes Service (AKS)

[Istio][istio-github] je síť služeb s otevřeným zdrojovým kódem, která poskytuje sadu funkcí klíče napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří správa provozu, identita a zabezpečení služby, vynucení zásad a pozorovatelnost. Další informace o Istiu naleznete v oficiální dokumentaci [Co je Istio?][istio-docs-concepts]

Tento článek ukazuje, jak používat funkce řízení provozu Istio. Ukázková hlasovací aplikace AKS se používá k prozkoumání inteligentního směrování a kanárských verzí.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasazení aplikace
> * Aktualizace aplikace
> * Zavedení kanárského uvolnění aplikace
> * Dokončení zavedení

## <a name="before-you-begin"></a>Než začnete

> [!NOTE]
> Tento scénář byl testován proti verzi `1.3.2`Istio .

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster `1.13` AKS (Kubernetes a `kubectl` výše, s povoleným RBAC) a navázali připojení ke clusteru. Budete také potřebovat Istio nainstalován ve vašem clusteru.

Pokud potřebujete pomoc s některou z těchto položek, pak se podívejte na [AKS rychlý start][aks-quickstart] a [nainstalovat Istio v AKS][istio-install] pokyny.

## <a name="about-this-application-scenario"></a>O tomto scénáři aplikace

Ukázková hlasovací aplikace AKS poskytuje uživatelům dvě možnosti hlasování **(Kočky** nebo **psi).** Existuje součást úložiště, která zachová počet hlasů pro každou možnost. Kromě toho je analytická komponenta, která poskytuje podrobnosti o odevzdaných hlasů pro každou možnost.

V tomto scénáři aplikace můžete `1.0` začít nasazením verze `1.0` hlasovací aplikace a verze součásti analytics. Analytická komponenta poskytuje jednoduché počty pro počet hlasů. Hlasovací aplikace a analytická komponenta `1.0` interagují s verzí komponenty úložiště, která je podporována společností Redis.

Upgradovat komponentu analytics `1.1`na verzi , která poskytuje počty a nyní součty a procenta.

Podmnožina uživatelů `2.0` testuje verzi aplikace prostřednictvím kanárské verze. Tato nová verze používá komponentu úložiště, která je zálohována databází MySQL.

Jakmile jste si jisti, že verze `2.0` funguje podle očekávání na `2.0` podmnožinu uživatelů, zavedete verzi pro všechny uživatele.

## <a name="deploy-the-application"></a>Nasazení aplikace

Začněme nasazením aplikace do clusteru Služby Azure Kubernetes (AKS). Následující diagram znázorňuje, co se spustí `1.0` na konci této části - verze všech součástí s příchozí požadavky obsluhované prostřednictvím brány příchozího přenosu dat Istio:

![Součásti a směrování aplikace AKS Hlasování.](media/servicemesh/istio/scenario-routing-components-01.png)

Artefakty, které je třeba sledovat spolu s tímto článkem jsou k dispozici v [Azure-Samples/aks-hlasování aplikace][github-azure-sample] GitHub úložiště. Artefakty můžete stáhnout nebo naklonovat úložiště následujícím způsobem:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Změňte na následující složku ve staženém / klonovaném repo a spusťte všechny následující kroky z této složky:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Nejprve vytvořte obor názvů v clusteru AKS pro ukázkovou hlasovací aplikaci AKS s názvem: `voting`

```console
kubectl create namespace voting
```

Označte obor `istio-injection=enabled`názvů písmenem . Tento štítek instruuje Istio automaticky vstříknout istio-proxy jako sidecars do všech lusků v tomto oboru názvů.

```console
kubectl label namespace voting istio-injection=enabled
```

Nyní vytvoříme součásti pro hlasovací aplikaci AKS. Vytvořte tyto součásti v oboru `voting` názvů vytvořeném v předchozím kroku.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Následující příklad výstupu ukazuje vytvářené prostředky:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio má některé specifické požadavky kolem lusky a služby. Další informace naleznete v [dokumentaci k požadavkům istio pro pody a služby][istio-requirements-pods-and-services].

Chcete-li zobrazit pody, které byly vytvořeny, použijte [příkaz kubectl get pods][kubectl-get] takto:

```console
kubectl get pods -n voting --show-labels
```

Následující příklad výstup ukazuje, že existují `voting-app` tři instance pod a `voting-analytics` `voting-storage` jednu instanci obou podů a podů. Každý z lusků má dva kontejnery. Jeden z těchto kontejnerů je součástí `istio-proxy`a druhý je :

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Chcete-li zobrazit informace o pod, budeme používat [kubectl popsat pod][kubectl-describe] příkaz `voting-analytics` s lektorek pro výběr pod. Výstup zfiltrujeme tak, aby zobrazovaly podrobnosti o dvou kontejnerech přítomných v lusku:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

K hlasovací aplikaci se nemůžete připojit, dokud nevytvoříte [bránu][istio-reference-gateway] Istio a [virtuální službu][istio-reference-virtualservice]. Tyto prostředky Istio směrovat provoz z výchozí brány příchozího přenosu dat Istio do naší aplikace.

> [!NOTE]
> **Brána** je součást na okraji sítě služby, která přijímá příchozí nebo odchozí přenosy HTTP a TCP.
> 
> **Virtuální služba** definuje sadu pravidel směrování pro jednu nebo více cílových služeb.

Pomocí `kubectl apply` příkazu nasadit bránu a virtuální službu yaml. Nezapomeňte zadat obor názvů, do kterého jsou tyto prostředky nasazeny.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Následující ukázkový výstup ukazuje, že se vytváří nová brána a virtuální služba:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Pomocí následujícího příkazu získáte IP adresu brány istio ingress gateway:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Následující ukázkový výstup ukazuje IP adresu vstupní brány:

```output
20.188.211.19
```

Otevřete prohlížeč a vložte do IP adresy. Zobrazí se ukázková hlasovací aplikace AKS.

![Hlasovací aplikace AKS spuštěná v našem clusteru AKS s povoleným istio.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Informace v dolní části obrazovky ukazují, že `1.0` `voting-app` aplikace `1.0` používá `voting-storage` verzi a verzi (Redis).

## <a name="update-the-application"></a>Aktualizace aplikace

Pojďme nasadit novou verzi komponenty analytics. Tato nová `1.1` verze zobrazuje součty a procenta kromě počtu pro každou kategorii.

Následující diagram znázorňuje, co bude spuštěno na `1.1` konci `voting-analytics` této části - `voting-app` pouze verze naší součásti má provoz směrovaný z komponenty. I když `1.0` verze `voting-analytics` naší součásti nadále běží `voting-analytics` a je odkazována službou, istio proxy zakázáno provoz do a z ní.

![Součásti a směrování aplikace AKS Hlasování.](media/servicemesh/istio/scenario-routing-components-02.png)

Pojďme nasadit `1.1` verzi `voting-analytics` komponenty. Vytvořte tuto `voting` komponentu v oboru názvů:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Následující příklad výstupu ukazuje vytvářené prostředky:

```output
deployment.apps/voting-analytics-1-1 created
```

Otevřete ukázkovou hlasovací aplikaci AKS znovu v prohlížeči pomocí IP adresy istio ingress gateway získané v předchozím kroku.

Váš prohlížeč se střídá mezi dvěma zobrazeními uvedenými níže. Vzhledem k tomu, že používáte `voting-analytics` [službu][kubernetes-service] Kubernetes`app: voting-analytics`pro komponentu pouze s jedním voličem popisků ( ), Kubernetes používá výchozí chování kruhového dotazování mezi pody, které odpovídají tomuto voliči. V tomto případě je `1.0` to `1.1` jak `voting-analytics` verze, tak vaše pody.

![Verze 1.0 analytické komponenty spuštěné v naší hlasovací aplikaci AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Verze 1.1 analytické komponenty spuštěné v naší hlasovací aplikaci AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Přepínání mezi dvěma verzemi `voting-analytics` komponenty můžete vizualizovat následujícím způsobem. Nezapomeňte použít IP adresu vlastní brány istio ingress.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Následující příklad výstupu ukazuje příslušnou část vráceného webu jako web přepíná mezi verzemi:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Uzamknout provoz na verzi 1.1 aplikace

Nyní uzamkneme provoz pouze `1.1` na `voting-analytics` verzi komponenty a na verzi `1.0` komponenty. `voting-storage` Potom definujete pravidla směrování pro všechny ostatní součásti.

> * **Virtuální služba** definuje sadu pravidel směrování pro jednu nebo více cílových služeb.
> * **Cílové pravidlo** definuje zásady provozu a zásady specifické pro verzi.
> * **Zásada** definuje, jaké metody ověřování lze přijmout na úlohy.

Pomocí `kubectl apply` příkazu můžete nahradit definici `voting-app` virtuální služby ve vašem a přidat [pravidla určení][istio-reference-destinationrule] a [virtuální služby][istio-reference-virtualservice] pro ostatní součásti. Do oboru `voting` názvů přidáte [zásadu,][istio-reference-policy] která zajistí, že veškerá komunikace mezi službami bude zabezpečena pomocí vzájemných tls a klientských certifikátů.

* Zásady `peers.mtls.mode` nastavila `STRICT` tak, aby zajistila, že vzájemné TLS je vynuceno mezi vašimi službami v rámci oboru `voting` názvů.
* Také jsme `trafficPolicy.tls.mode` nastavili, aby `ISTIO_MUTUAL` ve všech našich cílových pravidel. Istio poskytuje služby se silnými identitami a zabezpečuje komunikaci mezi službami pomocí vzájemných TLS a klientských certifikátů, které Istio transparentně spravuje.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Následující ukázkový výstup ukazuje, že nové zásady, pravidla určení a virtuální služby jsou aktualizovány/vytvořeny:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Pokud znovu otevřete hlasovací aplikaci AKS v prohlížeči, `voting-analytics` komponenta použije `voting-app` pouze novou verzi `1.1` komponenty.

![Verze 1.1 analytické komponenty spuštěné v naší hlasovací aplikaci AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Můžete si představit, že jste nyní směrováni pouze na verzi `1.1` `voting-analytics` komponenty následujícím způsobem. Nezapomeňte použít IP adresu vlastní brány Istio Ingress:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Následující ukázkový výstup ukazuje příslušnou část vráceného webu:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Nyní si potvrdíme, že Istio používá vzájemné TLS k zabezpečení komunikace mezi jednotlivými našimi službami. K tomu budeme používat [příkaz authn tls-check][istioctl-authn-tls-check] na binárním `istioctl` klientovi, který má následující podobu.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Tato sada příkazů poskytuje informace o přístupu k zadaným službám ze všech podů, které jsou v oboru názvů a odpovídají sadě popisků:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Následující příklad výstup ukazuje, že vzájemné TLS je vynuceno pro každý z našich dotazů výše. Výstup také zobrazuje pravidla zásad a určení, která vynucuje vzájemné TLS:

```output
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

## <a name="roll-out-a-canary-release-of-the-application"></a>Zavedení kanárského uvolnění aplikace

Nyní nasadíme novou `2.0` verzi `voting-app` `voting-analytics`aplikace `voting-storage` , a komponent. Nová `voting-storage` komponenta používá MySQL místo Redis a komponenty `voting-app` a `voting-analytics` jsou aktualizovány, aby mohly používat tuto novou `voting-storage` komponentu.

Komponenta `voting-app` nyní podporuje funkci příznaku prvku. Tento příznak funkce umožňuje otestovat schopnost kanárského vydání Istio pro podmnožinu uživatelů.

Následující diagram ukazuje, co budete mít spuštěna na konci této části.

* Verze `1.0` `voting-app` komponenty, `1.1` verze `voting-analytics` komponenty `1.0` a `voting-storage` verze komponenty jsou schopny vzájemně komunikovat.
* Verze `2.0` `voting-app` komponenty, `2.0` verze `voting-analytics` komponenty `2.0` a `voting-storage` verze komponenty jsou schopny vzájemně komunikovat.
* Verze `2.0` komponenty `voting-app` je přístupná pouze uživatelům, kteří mají nastaven příznak určité funkce. Tato změna je spravována pomocí příznaku funkce prostřednictvím souboru cookie.

![Součásti a směrování aplikace AKS Hlasování.](media/servicemesh/istio/scenario-routing-components-03.png)

Nejprve aktualizujte pravidla cíle Istio a virtuální služby, abyste se postarali o tyto nové součásti. Tyto aktualizace zajišťují, že nesměrujete provoz nesprávně na nové součásti a uživatelé nezískají neočekávaný přístup:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Následující ukázkový výstup ukazuje, že jsou aktualizována pravidla určení a virtuální služby:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Dále přidáme objekty Kubernetes pro nové `2.0` součásti verze. Službu také `voting-storage` aktualizujete `3306` tak, aby zahrnovala port pro MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Následující ukázkový výstup ukazuje, že objekty Kubernetes jsou úspěšně aktualizovány nebo vytvořeny:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Počkejte, až `2.0` budou spuštěny všechny verze podů. Pomocí příkazu [kubectl get pods][kubectl-get] s přepínačem `-w` hodinek sledujte změny na všech podech v `voting` oboru názvů:

```console
kubectl get pods --namespace voting -w
```

Nyní byste měli mít možnost `1.0` přepínat mezi verzí a verzí `2.0` (kanárkovou) hlasovací aplikace. Přepínač příznaku funkce v dolní části obrazovky nastaví soubor cookie. Tento soubor cookie `voting-app` používá virtuální služba k směrování `2.0`uživatelů na novou verzi .

![Verze 1.0 hlasovací aplikace AKS - příznak funkce není nastaven.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Verze 2.0 hlasovací aplikace AKS – nastaven příznak funkce.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Počty hlasů se liší mezi verzemi aplikace. Tento rozdíl zdůrazňuje, že používáte dva různé back-endy úložiště.

## <a name="finalize-the-rollout"></a>Dokončení zavedení

Po úspěšném otestování kanárské verze `voting-app` aktualizujte virtuální službu `2.0` tak, aby směrovala veškerý provoz do verze komponenty. `voting-app` Všichni uživatelé `2.0` pak zobrazit verzi aplikace, bez ohledu na to, zda je nastaven příznak funkce nebo ne:

![Součásti a směrování aplikace AKS Hlasování.](media/servicemesh/istio/scenario-routing-components-04.png)

Aktualizujte všechna pravidla určení a odeberte verze součástí, které již nechcete aktivní. Potom aktualizujte všechny virtuální služby a přestaňte odkazovat na tyto verze.

Vzhledem k tomu, že již neexistuje žádný provoz na některé ze starších verzí součástí, můžete nyní bezpečně odstranit všechna nasazení pro tyto součásti.

![Součásti a směrování aplikace AKS Hlasování.](media/servicemesh/istio/scenario-routing-components-05.png)

Nyní jste úspěšně zavedli novou verzi hlasovací aplikace AKS.

## <a name="clean-up"></a>Vyčištění 

Můžete odebrat hlasovací aplikaci AKS, kterou jsme použili v tomto `voting` scénáři, z clusteru AKS takto:

```console
kubectl delete namespace voting
```

Následující příklad výstupu ukazuje, že všechny součásti aks hlasování aplikace byly odebrány z clusteru AKS.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Další kroky

Můžete prozkoumat další scénáře pomocí [příkladu aplikace Istio Bookinfo][istio-bookinfo-example].

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
