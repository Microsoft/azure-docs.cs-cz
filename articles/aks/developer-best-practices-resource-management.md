---
title: Osvědčené postupy pro správu prostředků
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro vývojáře aplikací pro správu prostředků ve službě Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105268"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro vývojáře aplikací pro správu prostředků ve službě Azure Kubernetes Service (AKS)

Při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS) je potřeba zvážit několik klíčových oblastí. Způsob správy nasazení aplikací může mít negativní dopad na činnost služeb, které poskytujete, pro koncové uživatele. Aby bylo možné postupovat úspěšně, mějte na paměti některé osvědčené postupy, které můžete při vývoji a spouštění aplikací v AKS sledovat.

Tento článek se zaměřuje na spuštění clusteru a úloh z perspektivy pro vývojáře aplikací. Informace o osvědčených postupech pro správu najdete [v tématu osvědčené postupy pro izolaci a správu prostředků ve službě Azure Kubernetes (AKS)][operator-best-practices-isolation]. V tomto článku se naučíte:

> [!div class="checklist"]
> * Požadavky a omezení prostředků pod.
> * Způsoby vývoje a nasazení aplikací s využitím mostu do Kubernetes a Visual Studio Code.
> * Jak používat `kube-advisor` Nástroj ke kontrole problémů s nasazeními.

## <a name="define-pod-resource-requests-and-limits"></a>Definování požadavků a omezení prostředků pod

> **Osvědčené postupy**
> 
> Nastavte v YAML manifestech pod požadavky a omezení na všechny lusky. Pokud cluster AKS používá *kvóty prostředků* a tyto hodnoty nedefinujete, může být nasazení odmítnuté.

Pro správu výpočetních prostředků v rámci clusteru AKS použijte požadavky a omezení pod. Požadavky a omezení na hodnotu a informují Plánovač Kubernetes, který výpočetní prostředky přiřadí k poli pod.

### <a name="pod-cpumemory-requests"></a>Požadavky na procesor/paměť na pod
*Žádosti pod* definují sadu množství CPU a paměti, které je potřeba v pravidelných intervalech.

Ve specifikacích pod je **osvědčeným postupem a velmi důležité** definovat tyto požadavky a omezení na základě výše uvedených informací. Pokud tyto hodnoty nezahrnete, Plánovač Kubernetes se nemůže přihlédnout k prostředkům, které vaše aplikace potřebuje k podpoře při plánování rozhodnutí.

Monitorujte výkon aplikace a upravte žádosti pod. 
* Pokud podceňují skutečnou žádosti pod, může vaše aplikace obdržet snížený výkon z důvodu naplánování uzlu. 
* Pokud jsou požadavky přehodnoceny, může být vaše aplikace zvýšena o naplánovaných potížích.

### <a name="pod-cpumemory-limits"></a>Omezení počtu PROCESORů a paměti * * 
*Omezení* v poli nastaví maximální množství procesoru a paměti, které může použít. 

* *Omezení paměti* definují, které lusky by měly být ukončeny, když jsou uzly nestabilní kvůli nedostatečným prostředkům. Bez správných limitů bude ukončeno lusky až do chvíle, kdy je zatížení prostředku zrušeno. 
* I když v poli pod může být překročen *limit procesoru* pravidelně, nebude ukončeno, aby se překročil limit procesoru. 

Omezení pod definují, kdy došlo ke ztrátě řízení spotřeby prostředků pod. Pokud překročí limit, je pole pod označeno k usmrcování. Toto chování udržuje stav uzlu a minimalizuje dopad na lusky, které sdílí uzel. Při nastavení limitu na hodnotu pod se výchozí hodnota nastaví na nejvyšší dostupnou hodnotu na daném uzlu.

Vyhněte se nastavení limitu pod vyšším, než můžou vaše uzly podporovat. Každý uzel AKS rezervuje pro základní komponenty Kubernetes nastavenou velikost procesoru a paměti. Vaše aplikace se může pokusit spotřebovat příliš mnoho prostředků v uzlu, aby bylo možné ostatní lusky úspěšně spustit.

Monitorujte výkon aplikace v různou dobu během dne nebo týdne. Určete dobu potřeb špičky a zarovnejte limity na prostředky, které jsou potřeba pro splnění maximálního počtu požadavků.

> [!IMPORTANT]
>
> Ve specifikacích pod definujte tyto požadavky a omezení na základě výše uvedených informací. Selhání při zahrnutí těchto hodnot brání plánovači Kubernetes z monitorování účtů pro prostředky, které vaše aplikace vyžadují při plánování rozhodnutí.

Pokud Plánovač umístí uzel pod na uzlu s nedostatečnými prostředky, bude výkon aplikace snížený. Správci clusteru **musí** nastavit *kvóty prostředků* v oboru názvů, který vyžaduje, abyste nastavili požadavky a omezení prostředků. Další informace najdete v tématu [kvóty prostředků v clusterech AKS][resource-quotas].

Pokud definujete požadavek na procesor nebo limit, hodnota se měří v jednotkách procesoru. 
* *1,0* procesor se rovná jednomu základnímu virtuálnímu jádru na uzlu. 
    * Pro GPU se používá stejná míra.
* Můžete definovat zlomky měřené v millicores. Například *100 milionů* je *0,1* základního vCPU jádra.

V následujícím základním příkladu pro jedno NGINX pod vyžádá pod *100 milionů* čas procesoru a *128Mi* paměti. Omezení prostředků v poli pod jsou nastavená na *250m* CPU a *256Mi* paměť:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Další informace o měřeních a přiřazeních prostředků najdete v tématu [Správa výpočetních prostředků pro kontejnery][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Vývoj a ladění aplikací v clusteru AKS

> **Osvědčené postupy** 
>
> Vývojové týmy by měly nasadit a ladit cluster AKS pomocí mostu na Kubernetes.

S mostem na Kubernetes můžete vyvíjet, ladit a testovat aplikace přímo s clusterem AKS. Vývojáři v týmu spolupracují na sestavování a testování v průběhu životního cyklu aplikace. Můžete nadále používat stávající nástroje, jako je například Visual Studio, nebo Visual Studio Code s přemostěním na rozšíření Kubernetes. 

Použití integrovaného vývojového a testovacího procesu s mostem na Kubernetes snižuje potřebu místních testovacích prostředí, jako je [minikube][minikube]. Místo toho vyvíjíte a otestujete cluster AKS, a to i zabezpečené a izolované clustery. 

> [!NOTE]
> Most do Kubernetes je určený pro použití s aplikacemi, které běží na systémech Linux a uzlech.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Použití rozšíření Visual Studio Code (VS Code) pro Kubernetes

> **Osvědčené postupy** 
>
> Při psaní manifestů YAML nainstalujte a použijte rozšíření VS Code pro Kubernetes. Můžete také použít rozšíření pro integrované řešení nasazení, které může pomáhat vlastníkům aplikací, kteří zřídka pracují s clusterem AKS.

[Rozšíření Visual Studio Code pro Kubernetes][vscode-kubernetes] pomáhá vyvíjet a nasazovat aplikace do AKS. Rozšíření poskytuje:
* IntelliSense pro prostředky Kubernetes, Helm grafy a šablony. 
* Umožňuje procházet, nasazovat a upravovat možnosti prostředků Kubernetes v rámci VS Code. 
* Technologie IntelliSense kontroluje požadavky prostředků nebo omezení nastavená ve specifikacích pod:

    ![VS Code rozšíření pro upozornění na chybějící limity paměti pro Kubernetes](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Pravidelně kontrolovat problémy s aplikací pomocí Kube-Advisoru

> **Osvědčené postupy** 
> 
> Pravidelně spouštějte nejnovější verzi `kube-advisor` Open Source nástroje pro detekci problémů v clusteru. Spusťte `kube-advisor` před použitím kvót prostředků v existujícím clusteru AKS a najděte lusky, které nemají požadavky na prostředky a definované limity.

Nástroj [Kube-Advisor][kube-advisor] je přidružený AKS open source projekt, který prohledává cluster Kubernetes a oznamuje zjištěné problémy. Jednou z užitečných kontrol je identifikovat lusky bez požadavků na prostředky a omezení.

I když `kube-advisor` nástroj může ohlásit požadavky na prostředky a omezení chybějící v PodSpecs pro aplikace pro Windows a Linux, `kube-advisor` musí být sám naplánovaná na Linux pod. Použijte [selektor uzlů][k8s-node-selector] v konfiguraci pod a naplánujte tak, aby se spouštěla ve fondu uzlů s určitým operačním systémem.

V clusteru AKS, který je hostitelem mnoha vývojových týmů a aplikací, zjistíte, že je snazší sledovat lusky pomocí požadavků a omezení prostředků. Osvědčeným postupem je pravidelně spouštět `kube-advisor` v clusterech AKS.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na spuštění clusteru a úloh z perspektivy operátora clusteru. Informace o osvědčených postupech pro správu najdete [v tématu osvědčené postupy pro izolaci a správu prostředků ve službě Azure Kubernetes (AKS)][operator-best-practices-isolation].

Chcete-li implementovat některé z těchto doporučených postupů, přečtěte si následující články:

* [Vývoj s využitím mostu na Kubernetes][btk]
* [Vyhledat problémy s Kube-Advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
