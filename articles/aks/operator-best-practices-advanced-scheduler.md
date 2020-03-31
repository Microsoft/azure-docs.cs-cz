---
title: Osvědčené postupy pro operátory – pokročilé funkce plánovače ve službách Azure Kubernetes Services (AKS)
description: Seznamte se s doporučenými postupy operátora clusteru pro používání pokročilých funkcí plánovače, jako jsou například počina a tolerance, voliče uzlů a spřažení, nebo spřažení a protispřaženosti v Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 546c1d6ae25a33c6df93469ccf8c230b4b1c474b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252893"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro pokročilé funkce plánovače ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) často potřebujete izolovat týmy a úlohy. Plánovač Kubernetes poskytuje pokročilé funkce, které umožňují řídit, které pody lze naplánovat na určitých uzlech nebo jak mohou být aplikace s více pody vhodně distribuovány v rámci clusteru. 

Tento článek osvědčených postupů se zaměřuje na pokročilé funkce plánování Kubernetes pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití počitadla a tolerance k omezení toho, jaké lusky lze naplánovat na uzlech
> * Upřednostnit pody pro spuštění na určitých uzlech s voliči uzlů nebo spřažení uzlů
> * Rozdělit nebo seskupit lusky se spřažením mezi lusky nebo proti afinitě

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Poskytněte vyhrazené uzly pomocí potu a tolerance

**Pokyny pro osvědčené postupy** – omezte přístup pro aplikace náročné na prostředky, jako jsou řadiče příchozích dat, na konkrétní uzly. Udržujte prostředky uzlů k dispozici pro úlohy, které je vyžadují, a nepovolujte plánování jiných úloh na uzlech.

Při vytváření clusteru AKS můžete nasadit uzly s podporou GPU nebo velkým počtem výkonných procesorů. Tyto uzly se často používají pro velké úlohy zpracování dat, jako je strojové učení (ML) nebo umělá inteligence (AI). Vzhledem k tomu, že tento typ hardwaru je obvykle nákladné uzel prostředek k nasazení, omezit úlohy, které lze naplánovat na těchto uzlech. Místo toho můžete chtít vyhradit některé uzly v clusteru ke spuštění služby příchozího přenosu dat a zabránit další úlohy.

Tato podpora pro různé uzly je k dispozici pomocí více fondů uzlů. Cluster AKS poskytuje jeden nebo více fondů uzlů.

Plánovač Kubernetes můžete použít potu a tolerace omezit úlohy lze spustit na uzlech.

* Na uzel se použije **počin,** který označuje, že na nich mohou být naplánovány pouze určité pody.
* **Tolerace** se pak aplikuje na lusk, který jim umožňuje *tolerovat* počin uzlu.

Při nasazení pod u clusteru AKS Kubernetes pouze naplánuje pody na uzlech, kde je tolerance zarovnána s potu. Jako příklad předpokládejme, že máte fond uzlů v clusteru AKS pro uzly s podporou GPU. Definujete název, například *GPU*, pak hodnotu pro plánování. Pokud nastavíte tuto hodnotu *NoSchedule*, plánovač Kubernetes nelze naplánovat pody na uzlu, pokud pod nedefinuje odpovídající tolerance.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

S potu aplikované na uzly pak definujete tolerance ve specifikaci podu, která umožňuje plánování na uzlech. Následující příklad definuje `sku: gpu` a `effect: NoSchedule` tolerovat počin použitý na uzel v předchozím kroku:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Při nasazení tohoto podu, `kubectl apply -f gpu-toleration.yaml`například pomocí , Kubernetes můžete úspěšně naplánovat pod na uzly s potu použít. Tato logická izolace umožňuje řídit přístup k prostředkům v rámci clusteru.

Když použijete počitadla, spolupracujte s vývojáři a vlastníky aplikací, abyste jim umožnili definovat požadované tolerance v jejich nasazeních.

Další informace o pochvy a tolerance viz [použití potu a tolerance][k8s-taints-tolerations].

Další informace o použití více fondů uzlů v AKS naleznete v [tématu Vytvoření a správa více fondů uzlů pro cluster v AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Chování potu a tolerance v AKS

Při upgradu fondu uzlů v AKS se změny a tolerance řídí nastaveným vzorem při jejich použití na nové uzly:

- **Výchozí clustery, které používají škálovací sady virtuálních strojů**
  - Předpokládejme, že máte cluster se dvěma uzny - *node1* a *node2*. Upgradovat fond uzlů.
  - Jsou vytvořeny dva další uzly, *node3* a *node4*a počiny jsou předány.
  - Původní *uzel1* a *node2* jsou odstraněny.

- **Clustery bez podpory škálovací sady virtuálních strojů**
  - Opět předpokládejme, že máte cluster se dvěma uzlinami - *node1* a *node2*. Při upgradu je vytvořen další uzel *(node3).*
  - Počiny z *node1* jsou použity na *node3*, pak *node1* je pak odstraněn.
  - Je vytvořen jiný nový uzel (s názvem *node1*, protože předchozí *uzel1* byl odstraněn) a *node2* potu jsou použity na nový *uzel1*. Potom *node2* je odstraněn.
  - V podstatě *node1* se stane *node3*a *node2* se stane *node1*.

Při škálování fondu uzlů v AKS, počiny a tolerance nepřenášejí záměrné.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Řízení plánování podů pomocí selektorů uzlů a spřažení

**Pokyny pro osvědčené postupy** – řídí plánování podů na uzlech pomocí selektorů uzlů, spřažení uzlů nebo spřažení mezi pody. Tato nastavení umožňují Plánovač Kubernetes logicky izolovat úlohy, například podle hardwaru v uzlu.

Poskvrnění a tolerance se používají k logickému izolátu zdrojů s pevným odříznutím - pokud pod netoleruje poskvrnění uzlu, není naplánováno na uzlu. Alternativní přístup je použití selektorů uzlů. Označujete uzly, například označujete místně připojené úložiště SSD nebo velké množství paměti a pak definujete ve specifikaci podu volič uzlu. Kubernetes pak naplánuje tyto pody na odpovídající uzel. Na rozdíl od tolerance pody bez odpovídající hod nodů lze naplánovat na označené uzly. Toto chování umožňuje nepoužívané prostředky na uzlech využívat, ale dává přednost pody, které definují odpovídající volič uzlu.

Podívejme se na příklad uzlů s vysokým množstvím paměti. Tyto uzly mohou upřednostňovat pody, které požadují vysoké množství paměti. Chcete-li se ujistit, že prostředky nejsou nečinné, umožňují také spuštění jiných podů.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Specifikace podu `nodeSelector` pak přidá vlastnost k definování voliče uzlu, který odpovídá značce nastavené na uzlu:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

Při použití těchto možností plánovače, práce s vývojáři aplikací a vlastníky, aby jim umožnila správně definovat jejich pod specifikace.

Další informace o používání selektorů uzlů naleznete v tématu [Přiřazení podů uzlům][k8s-node-selector].

### <a name="node-affinity"></a>Spřažení uzlů

Volič uzlů je základní způsob, jak přiřadit pody k danému uzlu. Větší flexibilita je k dispozici pomocí *spřažení uzlů*. S afinitou uzlů definujete, co se stane, když pod nelze spárovat s uzlem. Můžete *požadovat, aby* plánovač Kubernetes odpovídá pod s označeným hostitelem. Nebo můžete *dát přednost* shodě, ale povolit pod, které mají být naplánovány na jiném hostiteli, pokud není shoda je k dispozici.

Následující příklad nastaví spřažení uzlu na *requiredDuringSchedulingIgnoredDuringExecution*. Tato spřažení vyžaduje Kubernetes plán použít uzel s odpovídající popisek. Pokud není k dispozici žádný uzel, pod musí čekat na plánování pokračovat. Chcete-li povolit pod naplánovat na jiném uzlu, můžete místo toho nastavit hodnotu *preferredDuringScheduledIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

*IgnorovanáduringExecution* část nastavení označuje, že pokud se změní popisky uzlu, pod by neměl být vyřazen z uzlu. Plánovač Kubernetes používá pouze aktualizované popisky uzlů pro nové pody, které jsou naplánovány, nikoli pody, které jsou již naplánovány na uzlech.

Další informace naleznete [v tématu Affinity and anti-afinity][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Afinita mezi lusky a anti-afinita

Jeden konečný přístup pro plánovač Kubernetes logicky izolovat úlohy je pomocí inter-pod spřažení nebo anti-afinity. Nastavení definují, že pody *by neměly* být naplánovány na uzlu, který má existující odpovídající pod, nebo že *by měly* být naplánovány. Ve výchozím nastavení plánovač Kubernetes pokusí naplánovat více podů v replice sady přes uzly. Můžete definovat konkrétnější pravidla kolem tohoto chování.

Dobrým příkladem je webová aplikace, která také používá Azure Cache pro Redis. Můžete použít pod pravidla proti spřažení požadovat, aby Plánovač Kubernetes distribuuje repliky mezi uzly. Potom můžete použít pravidla spřažení a ujistěte se, že každá komponenta webové aplikace je naplánována na stejném hostiteli jako odpovídající mezipaměť. Distribuce podů mezi uzly vypadá jako v následujícím příkladu:

| **Uzel 1** | **Uzel 2** | **Uzel 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | mezipaměť-2    | cache-3    |

Tento příklad je složitější nasazení než použití selektorů uzlů nebo spřažení uzlů. Nasazení umožňuje kontrolu nad tím, jak Kubernetes plánuje pody na uzlech a může logicky izolovat prostředky. Úplný příklad této webové aplikace s Azure Cache for Redis najdete v [tématu Co-locate pods na stejném uzlu][k8s-pod-affinity].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na pokročilé funkce plánovače Kubernetes. Další informace o operacích clusteru v AKS naleznete v následujících doporučených postupech:

* [Víceklientská architektura a izolace clusteru][aks-best-practices-scheduler]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]
* [Ověřování a autorizace][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
