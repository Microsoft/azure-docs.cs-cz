---
title: Operátor osvědčené postupy – základní Plánovač funkcí ve službě Azure Kubernetes služby (AKS)
description: Přečtěte si operátor clusteru doporučené postupy pro používání scheduleru základní funkce, jako jsou kvóty prostředků a pod rozpočty přerušení ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: abdce8b63a035fe55f4bd37acc5012237bd499da
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428967"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro Plánovač základní funkce ve službě Azure Kubernetes Service (AKS)

Jak budete spravovat clustery ve službě Azure Kubernetes Service (AKS), často potřebujete oddělit týmy a úlohy. Plánovač Kubernetes poskytuje funkce, které vám umožní řídit distribuci výpočetní prostředky, nebo omezení dopadu událostí údržby.

Tento článek o osvědčených postupech se zaměřuje na základní funkce plánování pro operátory clusteru Kubernetes. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Poskytuje pevné množství prostředků pro týmy nebo úlohy pomocí kvóty prostředků
> * Omezit dopad těchto plánované údržby pomocí rozpočet pod přerušení
> * Vyhledat chybějící pod prostředků požadavky a omezení použití `kube-advisor` nástroj

## <a name="enforce-resource-quotas"></a>Vynucovat kvóty prostředků

**Osvědčené postupy pro moduly** – plánování a kvóty prostředků na úrovni oboru názvů. Pokud podů nebudete definovat omezení a požadavky na zdroje, odmítnout nasazení. Monitorovat využití prostředků a kvót podle potřeby upravte.

Omezení a požadavky na zdroje jsou umístěny ve specifikaci pod. Tato omezení jsou používány Plánovač Kubernetes v době nasazení se najít dostupný uzel v clusteru. Tato omezení a požadavky fungují na úrovni jednotlivých pod. Další informace o tom, jak definovat tyto hodnoty, najdete v části [definovat pod prostředků požadavky a omezení][resource-limits]

Chcete-li poskytnout způsob, jak rezervovat a omezit prostředky napříč vývojový tým nebo projektu, měli byste použít *kvóty prostředků*. Tyto kvóty jsou definovány v oboru názvů a slouží k nastavení kvót na základě následující:

* **Výpočetní prostředky**, jako je například procesor a paměť nebo GPU.
* **Prostředky úložiště**, obsahuje celkový počet svazků nebo množství místa na disku pro třídu daného úložiště.
* **Počet objektů**, jako je například maximální počet tajné kódy, je vytvořit služeb nebo úloh.

Kubernetes není přetížit prostředky. Jakmile kumulativní součet prostředků požadavky nebo omezení předá přidělenou kvótu, žádné další nasazení jsou úspěšné.

Při definování kvóty prostředků, musíte zadat všechny podů vytvořené v oboru názvů omezení nebo požadavků v jejich pod specifikace. Pokud neobsahují tyto hodnoty, může odmítnout nasazení. Místo toho můžete [nakonfigurovat výchozí požadavky a omezení pro obor názvů][configure-default-quotas].

Následující příklad YAML manifestu s názvem *vývoj aplikace týmu quotas.yaml* nastaví vynucené omezení celkem *10* procesory, *20Gi* paměti, a *10*pody:

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

Tato kvóta zdrojů lze použít zadáním obor názvů, jako například *vývoj aplikací*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Práce s vývojáři a vlastníci pochopit jejich potřeby a kvóty odpovídající prostředek.

Další informace o dostupných prostředků objekty, obory a priority, naleznete v tématu [kvóty prostředků v Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Plánování dostupnosti s využitím rozpočet pod přerušení

**Osvědčené postupy pro moduly** – Chcete-li zachovat dostupnost aplikace, definujte Pod přerušení rozpočty (PDB) abyste měli jistotu, že minimální počet podů jsou k dispozici v clusteru.

Existují dva rušivé události, které způsobují podů odebrat:

* *Involuntary přerušení* jsou události mimo typické kontrolu operátor clusteru nebo vlastník aplikace.
  * Selhání hardwaru na fyzický počítač, poplachu jádra nebo odstranění uzlu virtuálního počítače zahrnují tyto involuntary přerušení služeb
* *Dobrovolná přerušení* jsou události požadoval operátor clusteru nebo vlastník aplikace.
  * Tyto dobrovolné přerušení zahrnovat upgrady clusteru, šablonu nasazení aktualizace nebo nechtěného odstranění pod.

Involuntary přerušení lze zmírnit použitím víc replik vašich podů v nasazení. Spuštění více uzlů v clusteru AKS také pomáhá s těmito involuntary přerušení služeb. Dobrovolná přerušení služeb, Kubernetes poskytuje *pod přerušení rozpočty* , která umožní operátor clusteru definovat minimální není k dispozici prostředek k dispozici nebo maximální počet. Tyto pod přerušení rozpočty umožňují naplánovat způsob nasazení ani pro sady replik reagovat při výskytu události přerušení dobrovolná.

Pokud cluster, je možné upgradovat nebo šablonu nasazení aktualizace, že Kubernetes scheduleru zkontrolujte další pody jsou naplánovány na jiných uzlech, před pokračováním v události dobrovolné přerušení. Plánovač čeká předtím, než se uzel restartuje, dokud definovaný počet podů se úspěšně naplánovala na jiných uzlech v clusteru.

Pojďme se podívat na příklad repliky sadu s pěti podů, na kterých běží server NGINX. Podů v replice nastavte, jak přiřadit popisek `app: nginx-frontend`. Během události dobrovolné přerušení, jako je například upgradu clusteru budete chtít Ujistěte se, že aspoň tři podů dál běžet. Následující kód YAML manifestu pro *PodDisruptionBudget* objekt definuje tyto požadavky:

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

Můžete také definovat procento, jako například *60 %*, která umožňuje automaticky jako kompenzaci za repliky nastavit škálování počtu podů.

Můžete definovat maximální počet instancí nedostupný replik. Znovu můžete také definovat procento pro maximální není k dispozici tyto pody. Následující pod narušení rozpočtu YAML manifest definuje, že více než dva podů v replice sadu nebudou k dispozici:

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

Po definování narušení rozpočtu pod jejím vytvoření v clusteru AKS stejně jako jakýkoli jiný objekt Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Pracovat s vývojáři aplikace a vlastníci pochopit jejich potřeby a aplikovat rozpočet pod příslušnou přerušení.

Další informace o používání pod přerušení rozpočty, naleznete v tématu [zadejte narušení rozpočtu pro vaši aplikaci][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Pravidelně zjišťovat problémy clusteru kube Advisor

**Osvědčené postupy pro moduly** – pravidelně používat nejnovější verzi `kube-advisor` a detekujte problémy ve vašem clusteru. Pokud použijete kvóty prostředků v existujícím clusteru AKS, spusťte `kube-advisor` nejprve k vyhledání podů, které nemají definovaná omezení a požadavky prostředků.

[Kube advisor] [ kube-advisor] nástroj vyhledá Kubernetes cluster, informuje o problémech, které najde. Jeden užitečné se identifikovat podů, které nemají omezení a požadavky na zdroje v místě.

V clusteru AKS, který je hostitelem více vývojových týmů a aplikace může být obtížné sledovat podů bez těchto prostředků požadavky a omezení sady. Jako osvědčený postup, pravidelně spouštět `kube-advisor` v clusterech služby AKS, zejména v případě, že kvóty prostředků nepřiřazovat k obory názvů.

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na základní funkce plánovače Kubernetes. Další informace o operacích clusteru ve službě AKS najdete v následující osvědčené postupy:

* [Izolace více tenantů a clusteru][aks-best-practices-cluster-isolation]
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
