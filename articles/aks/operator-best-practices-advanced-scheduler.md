---
title: Osvědčené postupy pro funkce Scheduleru
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro použití pokročilých funkcí Scheduleru, jako jsou například chuti a tolerovánosti, selektory uzlů a spřažení nebo spřažení a spřažení ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 27b32d7d10b691ed806e4d7aa31a095630d2bfc9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103619"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro pokročilé funkce plánování ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes (AKS) je často potřeba izolovat týmy a úlohy. Pokročilé funkce, které poskytuje Plánovač Kubernetes, umožňují řídit:
* Které lusky je možné naplánovat na určitých uzlech.
* Způsob, jakým lze aplikace pro multi-pod clusterem vhodně distribuovat napříč clusterem. 

Tento článek o osvědčených postupech se zaměřuje na pokročilé funkce plánování Kubernetes pro operátory clusterů. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Pomocí chuti a omezení můžete omezit, které lusky je možné plánovat na uzlech.
> * Poskytněte přednost rozchodu do lusků na určitých uzlech s selektory uzlů nebo spřažením uzlů.
> * Rozdělte nebo Seskupujte v kombinaci s použitím spřažení nebo proti spřažení.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Poskytování vyhrazených uzlů pomocí chuti a tolerovánosti

> **Doprovodné materiály k osvědčeným postupům:** 
>
> Omezte přístup k aplikacím náročným na prostředky, jako jsou například řadiče příchozího přenosu dat, na konkrétní uzly. Udržujte prostředky uzlů k dispozici pro úlohy, které je vyžadují, a nepovolujte plánování dalších úloh v uzlech.

Při vytváření clusteru AKS můžete nasadit uzly s podporou GPU nebo velkým množstvím výkonných procesorů. Tyto uzly můžete použít pro zpracování velkých objemů dat, jako je Machine Learning (ML) nebo umělá Intelligence (AI). 

Vzhledem k tomu, že hardware tohoto prostředku uzlu je obvykle nákladný k nasazení, omezte zatížení, která lze na těchto uzlech naplánovat. Místo toho byste vyhradi některé uzly v clusteru, aby se spouštěly služby příchozího přenosu dat, a Zabraňte ostatním úlohám.

Tato podpora pro různé uzly je poskytována pomocí více fondů uzlů. Cluster AKS poskytuje jeden nebo více fondů uzlů.

Plánovač Kubernetes pomocí chuti a tolerování omezuje, jaké úlohy je možné spouštět na uzlech.

* Použití **chuti** na uzel k označení pouze určitých lusků, které je možné naplánovat.
* Pak můžete použít **tolerování** na uzel pod, což jim umožní *tolerovat* jeho chuti.

Když nasadíte uzel pod do clusteru AKS, Kubernetes pouze plánuje na uzly, jejichž funkce chuti je zarovnána s tolerovánou. Předpokládejme například, že máte ve svém clusteru AKS fond uzlů pro uzly s podporou GPU. Definujte název, jako je například *GPU*, a pak hodnotu pro plánování. Nastavení této hodnoty na hodnotu *neplánuje* Plánovač Kubernetes z plánování lusků pomocí nedefinovaného omezení na uzlu.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Při použití chuti v uzlech můžete definovat tolerování ve specifikaci pod, která umožňuje plánování na uzlech. Následující příklad definuje `sku: gpu` a `effect: NoSchedule` k tolerování chuti aplikovaný na uzel v předchozím kroku:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
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

Pokud je tento uzel pod nasazen pomocí `kubectl apply -f gpu-toleration.yaml` , může Kubernetes úspěšně naplánovat uzel pod v uzlech s aplikovanou příchuti. Tato logická izolace umožňuje řídit přístup k prostředkům v rámci clusteru.

Když použijete chuti, pracujte s vývojáři vaší aplikace a vlastníky, abyste jim umožnili definovat požadovaná tolerovánost v jejich nasazeních.

Další informace o použití více fondů uzlů v AKS najdete v tématu [Vytvoření a Správa fondů více uzlů pro cluster v AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Chování chuti a jejich tolerovánosti v AKS

Když upgradujete fond uzlů v AKS, příchuti a tolerovánosti se řídí vzorem, který se použije pro nové uzly:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Výchozí clustery, které používají službu VM Scale Sets
Z rozhraní API pro AKS můžete změnit velikost [fondu uzlů][taint-node-pool] , aby se nově škálované uzly dostaly do rozhraní API.

Pojďme předpokládat:
1. Začínáte s clusterem se dvěma uzly: *Uzel1* a *Uzel2*. 
1. Upgradujete fond uzlů.
1. Vytvoří se dva další uzly: *Uzel3* a *Uzel4*. 
1. Hodnoty chuti jsou předány v uvedeném pořadí.
1. Původní *Uzel1* a *Uzel2* se odstraní.

#### <a name="clusters-without-vm-scale-set-support"></a>Clustery bez podpory sady škálování virtuálních počítačů

Znovu, pojďme předpokládat:
1. Máte cluster se dvěma uzly: *Uzel1* a *Uzel2*. 
1. Provedete upgrade, potom fond uzlů.
1. Vytvoří se další uzel: *Uzel3*.
1. V *Uzel3* se aplikují chuti z *Uzel1* .
1. *Uzel1* se odstraní.
1. Vytvoří se nový *Uzel1* , který se nahradí na původní *Uzel1*.
1. *Uzel2* chuti se aplikují na nový *Uzel1*. 
1. *Uzel2* se odstraní.

V podstatě *Uzel1* se stala *Uzel3* a *Uzel2* se stal novým *Uzel1*.

Při horizontálním navýšení kapacity fondu uzlů v AKS se neprovádí návrh.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Řízení pod plánováním pomocí selektorů uzlů a spřažení

> **Osvědčené postupy** 
> 
> Řízení plánování lusků na uzlech pomocí selektorů uzlů, spřažení uzlů nebo spřažení mezi uzly. Tato nastavení umožňují, aby Plánovač Kubernetes logicky izoluje úlohy, jako je například hardware v uzlu.

Příchuti a tolerování logicky izolují prostředky pomocí pevného oříznutí. Pokud v poli není tolerována příchuti uzlu, není naplánována na uzlu. 

Alternativně můžete použít selektory uzlů. Například označíte uzly tak, aby označovaly místně připojené úložiště SSD nebo velké množství paměti a pak definovali ve specifikaci pod modulem pro výběr uzlu. Kubernetes plánuje tyto lusky na vyhovujícím uzlu. 

Na rozdíl od tolerování je stále možné naplánovat lusky bez odpovídajícího voliče uzlů v uzlech s popisky. Toto chování umožňuje nevyužité prostředky na uzlech využívat, ale určuje prioritu lusků, které definují odpovídající selektor uzlů.

Pojďme se podívat na příklad uzlů s velkým množstvím paměti. Tyto uzly stanovují prioritu lusků, které vyžadují vysoké množství paměti. Pokud chcete zajistit, aby se prostředky nečinné, mohly by také běžet jiné lusky.

```console
kubectl label node aks-nodepool1 hardware=highmem
```

Specifikace pod pak přidá `nodeSelector` vlastnost k definování voliče uzlu, který odpovídá sadě popisku na uzlu:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
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

Když použijete tyto možnosti plánovače, spolupracujte s vývojáři vaší aplikace a vlastníky a umožněte jejich správnému definování specifikace pod.

Další informace o používání selektorů uzlů najdete v tématu [přiřazení lusků k uzlům][k8s-node-selector].

### <a name="node-affinity"></a>Spřažení uzlů

Selektor uzlů je základní řešení pro přiřazení lusků k danému uzlu. *Spřažení uzlů* poskytuje větší flexibilitu, což vám umožní definovat, co se stane, pokud se uzel pod nedá spárovat s uzlem. Další možnosti: 
* *Vyžaduje* , aby Plánovač Kubernetes odpovídal znaku pod pod názvem hostitele. Nebo:
* *Preferovat* shodu, ale pokud není k dispozici žádná shoda, nechejte možnost naplánovaná na jiném hostiteli.

Následující příklad nastaví spřažení uzlu na *requiredDuringSchedulingIgnoredDuringExecution*. Tento spřažení vyžaduje, aby plán Kubernetes používal uzel se shodným popiskem. Pokud není k dispozici žádný uzel, musí čekat na pokračování plánování. Chcete-li nechat naplánovaná hodnota v poli pod jiným uzlem, můžete místo toho nastavit hodnotu ***preferované** DuringSchedulingIgnoreDuringExecution *:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
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

*IgnoredDuringExecution* část nastavení indikuje, že by neměl být vyřazen z uzlu, pokud se změní jmenovky uzlu. Plánovač Kubernetes používá pouze aktualizované popisky uzlů pro naplánování nových lusků, nikoli již v uzlech.

Další informace najdete v tématu [spřažení a anti-spřažení][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Spřažení a proti spřažení

Jeden z konečných přístupů pro Kubernetes Scheduler pro logickou izolaci úloh používá spřažení nebo proti spřažení. Tato nastavení definují, že by *neměl* nebo *měl* být naplánován na uzel, který má existující odpovídající uzel pod. Ve výchozím nastavení se Plánovač Kubernetes pokusí naplánovat více lusků v sadě replik napříč uzly. Kolem tohoto chování můžete definovat více specifických pravidel.

Máte například webovou aplikaci, která používá také službu Azure cache pro Redis. 
1. Pomocí pravidel ochrany proti spřažení si vyžádáte, aby Plánovač Kubernetes distribuuje repliky napříč uzly. 
1. Pomocí pravidel spřažení zajistíte, aby se každá součást webové aplikace naplánovala na stejném hostiteli jako příslušná mezipaměť. 

Rozdělení lusků mezi uzly vypadá jako v následujícím příkladu:

| **Uzel 1** | **Uzel 2** | **Uzel 3** |
|------------|------------|------------|
| WebApp-1   | WebApp – 2   | WebApp-3   |
| mezipaměť – 1    | mezipaměť – 2    | mezipaměť – 3    |

Spřažení a proti spřažení poskytují složitější nasazení než selektory uzlů nebo spřažení uzlů. V případě nasazení logicky izolujete prostředky a řídíte, jak se Kubernetes plány na uzlech. 

Úplný příklad této webové aplikace s mezipamětí Azure cache pro Redis najdete v tématu [společné umístění jednotlivých uzlů na stejný uzel][k8s-pod-affinity].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na pokročilé funkce plánovače Kubernetes. Další informace o operacích clusteru v AKS najdete v následujících osvědčených postupech:

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
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
