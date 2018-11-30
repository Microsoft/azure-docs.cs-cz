---
title: Osvědčené postupy – operátor - pokročilé funkce plánovače ve službě Azure Kubernetes služby (AKS)
description: Přečtěte si operátor clusteru osvědčené postupy při používání pokročilých Plánovač funkce, jako je poskvrnění a tolerations, uzel selektory a vztahů, nebo vztahů mezi pod a proti spřažení ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 3fa2183a5bb3239059c349e8417aeb52553829cf
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428981"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro Plánovač pokročilé funkce ve službě Azure Kubernetes Service (AKS)

Jak budete spravovat clustery ve službě Azure Kubernetes Service (AKS), často potřebujete oddělit týmy a úlohy. Plánovač Kubernetes poskytuje pokročilé funkce, které umožňují řídit podů, které je možné naplánovat na určitých uzlech, nebo jak více podů, které aplikace mají správný distribuovat napříč clusterem. 

Tento článek o osvědčených postupech se zaměřuje na pokročilé funkce plánování pro operátory clusteru Kubernetes. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití poskvrnění a tolerations omezit jaké podů naplánovaných na uzlech
> * Dát přednost podů pro spuštění na některé uzly s selektory uzlu nebo spřažení uzlu
> * Této doby změny nepublikujete dělené tunelové propojení nebo společně podů skupiny vztahů mezi pod nebo proti spřažení

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Zadejte vyhrazené uzly pomocí poskvrnění a tolerations

**Osvědčené postupy pro moduly** – omezení přístupu pro aplikace náročné na prostředek, jako jsou řadiče příchozího přenosu dat, ke konkrétním uzlům. Zachovat uzel prostředky dostupné pro úlohy, které požadují a Nepovolovat plánování dalších úloh v uzlech.

Při vytváření clusteru AKS je možné nasadit uzly s podporou GPU nebo velký počet procesory. Tyto uzly jsou často používá pro úlohy zpracování velkých dat, jako jsou machine learning (ML) nebo umělé inteligence (AI). Tento typ hardwaru je obvykle prostředek nákladné uzel k nasazení, omezte zátěží, které je možné naplánovat na těchto uzlech. Místo toho můžete chtít vyhradit některé uzly v klastru, aby spouštění služeb příchozího přenosu dat a zabránilo jiné úlohy.

Plánovač Kubernetes můžete použít poskvrnění a tolerations omezit, co můžete spouštět úlohy na uzlech.

* A **taint** se použije na uzel, který označuje pouze konkrétní podů naplánovaných na ně.
* A **toleration** se následně použije na pod, které jim umožní *tolerovat* barvu uzlu.

Když nasadíte podu na AKS cluster, Kubernetes pouze naplánuje podů na uzlech, kde je v souladu toleration s barvu. Jako příklad předpokládejme, že máte nodepool ve vašem clusteru AKS pro uzly s GPU podporovat. Definovat název, jako například *gpu*, pak hodnota pro plánování. Pokud nastavíte tuto hodnotu na *NoSchedule*, Plánovač Kubernetes nelze naplánovat podů na uzlu, pokud chcete pod příslušnou toleration nedefinuje.

```console
kubectl taint node aks-nodepool1 gpu:NoSchedule
```

Pomocí barvu na uzlech pak definovat toleration ve specifikaci pod, která umožňuje plánování na uzlech. Následující příklad definuje `key: gpu` a `effect: NoSchedule` tolerovat barvu použitý pro uzel v předchozím kroku:

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
  - key: "gpu"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

Při nasazení tohoto podu, jako je třeba použití `kubectl apply -f gpu-toleration.yaml`, Kubernetes můžete úspěšně naplánovat podu na uzly s barvu použít. Tuto logickou izolaci umožňuje řídit přístup k prostředkům v rámci clusteru.

Při použití poskvrnění pracujete s vaší aplikací vývojáři a vlastníci a povolení jejich definování požadované tolerations v jejich nasazeních.

Další informace o poskvrnění a tolerations najdete v tématu [použití poskvrnění a tolerations][k8s-taints-tolerations].

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Plánování s použitím selektory uzlu a vztahů pod ovládací prvek

**Osvědčené postupy pro moduly** – řízení plánování podů na uzlech, pomocí uzlu selektory, spřažení uzlu, nebo mezi pod spřažení. Tato nastavení umožňují Plánovač Kubernetes logicky izolaci úloh, jako například hardware v uzlu.

Poskvrnění a tolerations se používají k logicky izolaci prostředků pomocí pevné oříznutím – Pokud chcete pod nebude tolerovat barvu uzlu, není naplánované na uzlu. Alternativním přístupem je použití uzlu selektorů. Uzly, označíte jako místně připojené úložiště SSD nebo velké množství paměti, a pak definovat ve specifikaci pod uzel selektor. Kubernetes potom naplánuje tyto pody na odpovídající uzlu. Na rozdíl od tolerations můžete naplánovat podů bez odpovídající selektor uzlu na uzly s popiskem. Toto chování umožňuje nevyužité prostředky na uzlech využívat, ale dává priority podů, které definují odpovídající selektor uzlu.

Pojďme se podívat na příklad z uzlů s vysoké množství paměti. Tyto uzly můžete dát přednost podů, které požadují vysoké množství paměti. Pokud chcete mít jistotu, že prostředky nejsou umístěni nečinné, umožňují taky další pody ke spuštění.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Specifikace pod pak přidá `nodeSelector` k definování selektor uzel, který odpovídá popisku nastavenou na uzlu:

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

Při použití těchto možností Plánovač pracujete s vaší aplikací vývojáři a vlastníci a povolení jejich správně definovat jejich pod specifikace.

Další informace o používání selektorů. uzel, naleznete v tématu [přiřazení Podů uzly][k8s-node-selector].

### <a name="node-affinity"></a>Uzel vztahů

Výběr uzlu je základní způsob, jak přiřadit podů daný uzel. Větší flexibilitu, je k dispozici pomocí *spřažení uzlu*. Přidružení uzlu můžete definovat, co se stane, když chcete pod nejde spárovat s uzlem. Je možné *vyžadují* , Plánovač Kubernetes odpovídá podu s hostitelem, s popiskem. Nebo můžete *raději* shodu a povolit pod naplánovaná na jiného hostitele, pokud není shoda je k dispozici.

Následující příklad nastaví spřažení uzlu *requiredDuringSchedulingIgnoredDuringExecution*. Toto přidružení vyžaduje Kubernetes plánu pro uzel s odpovídající popisku. Pokud je k dispozici žádný uzel, pod musí čekat plánování pokračujte. Povolit pod naplánovaná na jiný uzel, můžete místo toho nastavte hodnotu na *preferredDuringScheduledIgnoreDuringExecution*:

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

*IgnoredDuringExecution* součástí nastavení znamená, že pokud uzel označuje změnu, by neměla pod vyřazovány z uzlu. Plánovač Kubernetes pouze pomocí popisků aktualizovaný uzel pro nových podů naplánované, ne podů naplánována na uzlech.

Další informace najdete v tématu [vztahů a proti spřažení][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Spřažení mezi pod a proti spřažení

Poslední jedním z přístupů pro Kubernetes scheduler k logické izolaci úloh používá spřažení mezi pod nebo proti spřažení. Nastavení definují této podů *by neměl* naplánovat na uzel, který má existující odpovídající pod nebo že *by měl* naplánovat. Ve výchozím nastavení Kubernetes plánovače pokusí naplánovat několik podů se v sadě uzlech replik. Můžete definovat konkrétnější pravidla kolem tohoto chování.

Dobrým příkladem je webová aplikace využívající Redis cache. Pod proti spřažení pravidla můžete požadovat, že Kubernetes Plánovač distribuuje replik napříč uzly. Pak můžete ise spřažení pravidla, abyste měli jistotu, že každá součást webové aplikace je naplánováno na stejném hostiteli jako odpovídající mezipaměti. Distribuce podů napříč uzly bude vypadat jako v následujícím příkladu:

| **Uzel 1** | **Uzel 2** | **Uzel 3** |
|------------|------------|------------|
| webové aplikace – 1   | 2 webové aplikace   | webové aplikace – 3   |
| mezipaměť-1    | mezipaměť-2    | Cache – 3    |

V tomto příkladu je složitější nasazení než používání selektorů. uzel nebo spřažení uzlu. Nasazení umožňuje ovládat způsob Kubernetes naplánuje podů na uzlech a logicky můžete izolovat prostředky. Kompletní příklad této webové aplikace s Redis cache příklad, naleznete v tématu [společné umístění podů na stejném uzlu][k8s-pod-affinity].

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na pokročilé funkce plánovače Kubernetes. Další informace o operacích clusteru ve službě AKS najdete v následující osvědčené postupy:

* [Izolace více tenantů a clusteru][aks-best-practices-scheduler]
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
