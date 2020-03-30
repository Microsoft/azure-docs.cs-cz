---
title: Osvědčené postupy operátora – základní funkce plánovače ve službách Azure Kubernetes Services (AKS)
description: Seznamte se s doporučenými postupy operátora clusteru pro používání základních funkcí plánovače, jako jsou kvóty prostředků a rozpočty přerušení podu ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126574"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro základní funkce plánovače ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) často potřebujete izolovat týmy a úlohy. Plánovač Kubernetes poskytuje funkce, které umožňují řídit distribuci výpočetních prostředků nebo omezit dopad událostí údržby.

Tento článek osvědčených postupů se zaměřuje na základní funkce plánování Kubernetes pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití kvót zdrojů k poskytnutí pevného množství prostředků týmům nebo úlohám
> * Omezení dopadu plánované údržby pomocí rozpočtů přerušení provozu podu
> * Kontrola chybějících požadavků a omezení `kube-advisor` prostředků podu pomocí nástroje

## <a name="enforce-resource-quotas"></a>Vynutit kvóty prostředků

**Pokyny pro osvědčené postupy** – naplánujte a použijte kvóty prostředků na úrovni oboru názvů. Pokud pody nedefinují požadavky na prostředky a omezení, odmítnout nasazení. Sledujte využití prostředků a podle potřeby upravte kvóty.

Požadavky na prostředky a limity jsou umístěny ve specifikaci pod. Tato omezení používají plánovač Kubernetes v době nasazení k nalezení dostupného uzlu v clusteru. Tyto limity a požadavky pracují na úrovni jednotlivých podů. Další informace o definování těchto hodnot naleznete v [tématu Definování požadavků a limitů prostředků podu][resource-limits]

Chcete-li poskytnout způsob, jak rezervovat a omezit zdroje v rámci vývojového týmu nebo projektu, měli byste použít *kvóty zdrojů*. Tyto kvóty jsou definovány v oboru názvů a lze je použít k nastavení kvót na následujícím základě:

* **Výpočetní prostředky**, například procesor a paměť nebo gpu.
* **Prostředky úložiště**, zahrnuje celkový počet svazků nebo velikost místa na disku pro danou třídu úložiště.
* **Počet objektů**, například maximální počet tajných kódů, služby nebo úlohy mohou být vytvořeny.

Kubernetes nepřeceňuje prostředky. Jakmile kumulativní součet požadavků na prostředky nebo omezení překročí přiřazenou kvótu, nebudou úspěšná žádná další nasazení.

Při definování kvót prostředků musí všechny pody vytvořené v oboru názvů poskytovat omezení nebo požadavky ve specifikacích podu. Pokud tyto hodnoty neposkytují, můžete nasazení odmítnout. Místo toho můžete [nakonfigurovat výchozí požadavky a omezení pro obor názvů][configure-default-quotas].

Následující příklad manifestu YAML s názvem *dev-app-team-quotas.yaml* nastavuje pevný limit celkem *10* procesorů, *20 Gi* paměti a *10* podů:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Tuto kvótu prostředků lze použít zadáním oboru názvů, například *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Spolupracujte s vývojáři a vlastníky aplikací, abyste porozuměli jejich potřebám a použili příslušné kvóty prostředků.

Další informace o dostupných objektech prostředků, oborech a prioritách naleznete [v tématu Kvóty prostředků v Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Plánování dostupnosti pomocí rozpočtů přerušení podu

**Pokyny pro osvědčené postupy** – chcete-li zachovat dostupnost aplikací, definujte rozpočty přerušení podu (PDBs) a ujistěte se, že v clusteru je k dispozici minimální počet podů.

Existují dvě rušivé události, které způsobují, že pody mají být odebrány:

* *Nedobrovolná narušení* jsou události mimo typické řízení operátora clusteru nebo vlastníka aplikace.
  * Tyto nedobrovolné poruchy zahrnují selhání hardwaru ve fyzickém počítači, paniku jádra nebo odstranění virtuálního počítače uzlu.
* *Dobrovolné přerušení* jsou události požadované operátorem clusteru nebo vlastníkem aplikace.
  * Tato dobrovolná narušení zahrnují upgrady clusteru, aktualizovanou šablonu nasazení nebo náhodné odstranění podu.

Nedobrovolné narušení lze zmírnit pomocí více replik podů v nasazení. Spuštění více uzlů v clusteru AKS také pomáhá s těmito nedobrovolnými narušení. Pro dobrovolné přerušení Kubernetes poskytuje *pod přerušení rozpočty,* které umožňují operátorclusteru definovat minimální dostupné nebo maximální počet nedostupných prostředků. Tyto rozpočty přerušení pod umožňují naplánovat, jak nasazení nebo sady replik reagovat při dobrovolné přerušení události.

Pokud má být cluster upgradován nebo aktualizována šablona nasazení, plánovač Kubernetes zajistí, že další pody jsou naplánovány na jiných uzlech, než mohou pokračovat události dobrovolného přerušení. Plánovač čeká před restartováním uzlu, dokud definovaný počet podů jsou úspěšně naplánovány na jiných uzlech v clusteru.

Podívejme se na příklad sady replik s pěti pody, které běží NGINX. Podům v sadě replik je `app: nginx-frontend`přiřazen popisek . Během události dobrovolné přerušení, jako je například upgrade clusteru, chcete zajistit, aby alespoň tři pody nadále běžet. Následující manifest YAML pro objekt *PodDisruptionBudget* definuje tyto požadavky:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Můžete také definovat procento, například *60 %*, které umožňuje automaticky kompenzovat změnu velikosti sady replik, která nastavuje počet podů.

V sadě replik můžete definovat maximální počet nedostupných instancí. Opět lze definovat procento pro maximální nedostupné pody. Následující pod přerušení rozpočtu YAML manifest definuje, že ne více než dva pody v sadě replik není k dispozici:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Jakmile je rozpočet narušení podu definován, vytvoříte jej v clusteru AKS jako u jiných objektů Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Spolupracujte s vývojáři a vlastníky aplikací, abyste porozuměli jejich potřebám a použili příslušné rozpočty přerušení podu.

Další informace o použití rozpočtů přerušení podu naleznete [v tématu Určení rozpočtu přerušení pro vaši aplikaci][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Pravidelně kontrolujte problémy clusteru s kube-advisor

**Pokyny pro osvědčené postupy** – `kube-advisor` pravidelně spouštějte nejnovější verzi nástroje s otevřeným zdrojovým kódem ke zjišťování problémů v clusteru. Pokud použijete kvóty prostředků na existující cluster `kube-advisor` AKS, spusťte nejprve najít pody, které nemají požadavky na prostředky a omezení definovány.

Nástroj [kube-advisor][kube-advisor] je přidružený projekt aks open source, který skenuje cluster Kubernetes a informuje o problémech, které najde. Jednou z užitečných kontrol je identifikace podů, které nemají požadavky na prostředky a omezení na místě.

Nástroj kube-advisor může podávat zprávy o žádosti o prostředky a omezuje chybějící v PodSpecs pro aplikace systému Windows, stejně jako linuxové aplikace, ale samotný nástroj kube-advisor musí být naplánován na pod linuxu. Pod můžete naplánovat tak, aby se spouštěl ve fondu uzlů s konkrétním osem pomocí [voliče uzlů][k8s-node-selector] v konfiguraci podu.

V clusteru AKS, který hostuje více vývojových týmů a aplikací, může být obtížné sledovat pody bez těchto požadavků na prostředky a nastavených limitů. Jako osvědčený postup pravidelně `kube-advisor` spouštět na clustery AKS, zejména pokud nechcete přiřadit kvóty prostředků obory názvů.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na základní funkce plánovače Kubernetes. Další informace o operacích clusteru v AKS naleznete v následujících doporučených postupech:

* [Víceklientská architektura a izolace clusteru][aks-best-practices-cluster-isolation]
* [Pokročilé funkce plánovače Kubernetes][aks-best-practices-advanced-scheduler]
* [Ověřování a autorizace][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
