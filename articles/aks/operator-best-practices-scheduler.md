---
title: Doporučené postupy pro obsluhu – základní funkce plánovače ve službě Azure Kubernetes Services (AKS)
description: Seznamte se s osvědčenými postupy pro použití základních funkcí plánovače, jako jsou kvóty prostředků a rozpočty přerušení v Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 8c0f1d0cda61638abe03b92c627a5ea0455c31cb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104894"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro základní funkce plánovače ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes (AKS) je často potřeba izolovat týmy a úlohy. Plánovač Kubernetes vám umožňuje řídit distribuci výpočetních prostředků nebo omezit dopad událostí údržby.

Tento článek o osvědčených postupech se zaměřuje na základní funkce plánování Kubernetes pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití kvót prostředků k poskytnutí pevného množství prostředků týmům nebo úlohám
> * Omezte dopad plánované údržby pomocí rozpočtů přerušení pod.
> * Vyhledání chybějících požadavků a omezení prostředků pod pomocí `kube-advisor` nástroje

## <a name="enforce-resource-quotas"></a>Vynutilit kvóty prostředků

> **Osvědčené postupy** 
> 
> Plánování a použití kvót prostředků na úrovni oboru názvů. Pokud lusky nedefinují požadavky a omezení prostředků, zamítnout nasazení. Monitorujte využití prostředků a podle potřeby upravte kvóty.

Požadavky na prostředky a omezení jsou umístěné ve specifikaci pod. Omezení používá Plánovač Kubernetes v době nasazování k nalezení dostupného uzlu v clusteru. Omezení a požadavky fungují na úrovni jednotlivých pod. Další informace o tom, jak definovat tyto hodnoty, najdete v tématu [definice pod a požadavky na prostředky a omezení][resource-limits] .

Chcete-li zajistit způsob, jak rezervovat a omezit prostředky v rámci vývojového týmu nebo projektu, měli byste použít *kvóty prostředků*. Tyto kvóty jsou definované v oboru názvů a dají se použít k nastavení kvót na následujícím základě:

* **Výpočetní prostředky**, jako je například procesor a paměť nebo GPU.
* **Prostředky úložiště**, včetně celkového počtu svazků nebo množství místa na disku pro danou třídu úložiště.
* Je možné vytvořit **počet objektů**, jako je například maximální počet tajných klíčů, služeb nebo úloh.

Kubernetes neprovádí přepisování prostředků. Jakmile celková velikost žádosti o prostředky projde přiřazenou kvótou, všechna další nasazení budou neúspěšná.

Při definování kvót prostředků musí všechny lusky vytvořené v oboru názvů poskytovat omezení nebo požadavky ve svých specifikacích pod. Pokud tyto hodnoty nezadávají, můžete nasazení odmítnout. Místo toho můžete [nakonfigurovat výchozí požadavky a omezení pro obor názvů][configure-default-quotas].

Následující příklad manifestu YAML s názvem *dev-App-Team-kvóty. yaml* nastaví pevný limit celkového počtu *10* procesorů, *20Gi* paměti a *10* lusků:

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

Tuto kvótu prostředků můžete použít zadáním oboru názvů, jako je například *vývoj aplikací*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Spolupracujte s vývojáři vaší aplikace a vlastníky, abyste porozuměli jejich potřebám a použili příslušné kvóty prostředků.

Další informace o dostupných objektech prostředků, oborech a prioritách najdete v tématu [kvóty prostředků v Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Plánování dostupnosti pomocí rozpočtů přerušení pod

> **Osvědčené postupy** 
>
> Chcete-li zachovat dostupnost aplikací, definujte v podsystému soubory PDB, aby bylo zajištěno, že v clusteru bude k dispozici minimální počet lusků.

Existují dvě rušivé události, které způsobují, že se odeberou lusky.

### <a name="involuntary-disruptions"></a>Nedobrovolné výpadky

*Nedobrovolnými výpadky* jsou události nad rámec obvyklé kontroly nad operátorem clusteru nebo vlastníkem aplikace. Připojit
* Selhání hardwaru na fyzickém počítači
* Nouzové jádro
* Odstranění virtuálního počítače uzlu

Nedobrovolným výpadkům můžete zmírnit:
* Použití několika replik v rámci nasazení. 
* Spuštění více uzlů v clusteru AKS. 

### <a name="voluntary-disruptions"></a>Dobrovolné výpadky

*Dobrovolné přerušení* jsou události vyžádané operátorem clusteru nebo vlastníkem aplikace. Připojit
* Upgrady clusteru
* Aktualizovaná šablona nasazení
* Náhodné odstranění pod

Kubernetes poskytuje *rozpočty přerušení* v případě dobrovolných výpadků a umožňuje vám naplánovat, jak nasazení nebo sady replik reagují na odpověď, když dojde k dobrovolnému přerušení. Pomocí rozpočtů přerušení v rámci mohou operátoři clusteru definovat minimální dostupný nebo maximální počet nedostupných zdrojů. 

Pokud upgradujete cluster nebo aktualizujete šablonu nasazení, Plánovač Kubernetes naplánuje další lusky na jiných uzlech, než umožní pokračovat v akcích dobrovolného přerušení. Plánovač počká, až se uzel restartuje, dokud se neúspěšně naplánuje definovaný počet lusků na jiných uzlech v clusteru.

Pojďme se podívat na příklad sady replik s pěti lusky, které používají NGINX. V luskech sady replik se přiřadí popisek `app: nginx-frontend` . Během dobrovolné události přerušení, jako je například upgrade clusteru, chcete zajistit, aby běžely alespoň tři lusky. Následující manifest YAML pro objekt *PodDisruptionBudget* definuje tyto požadavky:

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

Můžete také definovat procento, například *60%*, které vám umožní automaticky kompenzovat sadu replik a škálovat tak počet lusků.

V sadě replik můžete definovat maximální počet nedostupných instancí. Znovu je možné definovat i procento pro maximální nedostupné lusky. Následující manifest YAML s dopadem na narušení rozpočtu definuje, že není k dispozici více než dvě lusky v sadě repliky:

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

Po definování rozpočtu přerušení v prostředí je vytvoříte v clusteru AKS jako s jakýmkoli jiným objektem Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Spolupracujte se s vývojáři vaší aplikace a vlastníky, abyste porozuměli jejich potřebám a použili příslušné rozpočty přerušení na základě.

Další informace o použití rozpočtů přerušení v systému najdete v tématu [určení rozpočtu přerušení pro vaši aplikaci][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Pravidelně zjišťovat problémy s clustery pomocí Kube-Advisoru

> **Osvědčené postupy** 
>
> Pravidelně spouštějte nejnovější verzi `kube-advisor` nástroje open source, aby se zjistily problémy v clusteru. Pokud použijete kvóty prostředků v existujícím clusteru AKS, spusťte `kube-advisor` nejprve rutiny, které nemají požadavky na prostředky a definované limity.

Nástroj [Kube-Advisor][kube-advisor] je přidružený AKS open source projekt, který prohledává cluster Kubernetes a oznamuje zjištěné problémy. `kube-advisor` ukáže se jako užitečné při identifikaci lusků bez požadavků na prostředky a omezení na místě.

I když `kube-advisor` nástroj může ohlásit požadavky prostředku a omezení chybějící v PodSpecs pro aplikace pro Windows a Linux, musí být samotný nástroj naplánován na Linux pod. Naplánujte, aby se spouštěl pod fondem uzlů s konkrétním operačním systémem pomocí [voliče uzlů][k8s-node-selector] v konfiguraci pod.

Sledování lusků bez nastavování požadavků na prostředky a omezení v clusteru AKS hostujícím více vývojových týmů a aplikací může být obtížné. Osvědčeným postupem je pravidelně spouštět `kube-advisor` v clusterech AKS, zejména pokud nepřiřazujete kvóty prostředků oborům názvů.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na základní funkce plánovače Kubernetes. Další informace o operacích clusteru v AKS najdete v následujících osvědčených postupech:

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
