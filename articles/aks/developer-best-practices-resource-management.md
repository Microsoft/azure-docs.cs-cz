---
title: Doporučené postupy pro vývojáře – správa prostředků ve službách Azure Kubernetes Services (AKS)
description: Seznamte se s doporučenými postupy pro vývojáře aplikací pro správu prostředků ve službě Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 66676c90e73d1886c86d8afda8cbbecce239a005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259523"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro vývojáře aplikací pro správu prostředků ve službě Azure Kubernetes Service (AKS)

Při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS) je několik klíčových oblastí, které je třeba zvážit. Způsob správy nasazení aplikací může negativně ovlivnit prostředí koncových uživatelů služeb, které poskytujete. Abyste uspěli, mějte na paměti některé osvědčené postupy, které můžete sledovat při vývoji a spouštění aplikací v AKS.

Tento článek osvědčených postupů se zaměřuje na to, jak spustit cluster a úlohy z hlediska vývojáře aplikací. Informace o osvědčených postupech pro správu naleznete v [doporučených postupech operátora clusteru pro izolaci a správu prostředků ve službě Azure Kubernetes Service (AKS).][operator-best-practices-isolation] V tomto článku se dozvíte:

> [!div class="checklist"]
> * Co jsou požadavky a limity prostředků pod
> * Způsoby vývoje a nasazování aplikací pomocí dev spaces a kódu Visual Studio
> * Jak pomocí `kube-advisor` nástroje zkontrolovat problémy s nasazením

## <a name="define-pod-resource-requests-and-limits"></a>Definování požadavků a limitů prostředků podu

**Osvědčené postupy pokyny** – Nastavte pod požadavky a omezení na všechny pody v manifestech YAML. Pokud cluster AKS používá *kvóty prostředků*, může být vaše nasazení odmítnuto, pokud tyto hodnoty nedefinujete.

Primární způsob správy výpočetních prostředků v rámci clusteru AKS je použití požadavků a omezení podu. Tyto požadavky a omezení umožňují plánovači Kubernetes vědět, jaké výpočetní prostředky pod by měl být přiřazen.

* **Požadavky na procesor/paměť pod definovat** nastavené množství procesoru a paměti, které pod potřebuje v pravidelných intervalech.
    * Když plánovač Kubernetes pokusí umístit pod na uzel, pod požadavky se používají k určení uzlu má dostatek prostředků k dispozici pro plánování.
    * Nenastaví pod požadavek bude výchozí na limit definovaný.
    * Je velmi důležité sledovat výkon aplikace upravit tyto požadavky. Pokud jsou provedeny nedostatečné požadavky, aplikace může získat snížený výkon z důvodu přes plánování uzlu. Pokud jsou požadavky nadhodnoceny, může mít vaše aplikace zvýšené potíže s plánováním.
* **Limity procesoru/paměti podu** jsou maximální množství procesoru a paměti, které může pod používat. Tato omezení pomáhají definovat pody by měly být usmrceny v případě nestability uzlu z důvodu nedostatku prostředků. Bez správných limitů budou pody zabity, dokud nebude tlak zdrojů zrušen.
    * Limity podu pomáhají definovat, kdy pod ztratil kontrolu nad spotřebou prostředků. Při překročení limitu pod je prioritou pro usmrcování zachovat stav uzlu a minimalizovat dopad podů sdílení uzlu.
    * Nenastaví pod limit výchozí ho na nejvyšší dostupnou hodnotu na daném uzlu.
    * Nenastavujte limit podu vyšší, než může podporovat vaše uzly. Každý uzel AKS si vyhrazuje nastavené množství procesoru a paměti pro základní komponenty Kubernetes. Aplikace se může pokusit spotřebovat příliš mnoho prostředků v uzlu pro ostatní pody úspěšně spustit.
    * Opět je velmi důležité sledovat výkon vaší aplikace v různých časech během dne nebo týdne. Určete, kdy je poptávka ve špičce, a zarovnejte limity podu s prostředky potřebnými ke splnění maximálních potřeb aplikace.

Ve specifikacích podu je **osvědčeným postupem a velmi důležité** definovat tyto požadavky a omezení na základě výše uvedených informací. Pokud tyto hodnoty nezahrnete, plánovač Kubernetes nemůže vzít v úvahu prostředky, které vaše aplikace potřebují k podpoře při rozhodování o plánování.

Pokud plánovač umístí pod na uzel s nedostatečnými prostředky, výkon aplikace bude snížena. Důrazně doporučujeme správcům clusteru nastavit *kvóty prostředků* v oboru názvů, který vyžaduje nastavení požadavků na prostředky a omezení. Další informace naleznete v [tématu kvóty prostředků v clusterech AKS][resource-quotas].

Když definujete požadavek nebo limit procesoru, hodnota se měří v jednotkách procesoru. 
* *1.0* CPU se rovná jednomu podkladovému jádru virtuálního procesoru v uzlu. 
* Stejné měření se používá pro GPU.
* Můžete definovat zlomky měřené v milijádrích. Například *100 m* je *0,1* základního jádra virtuálního procesoru.

V následujícím základním příkladu pro jeden pod NGINX pod pod pod požaduje *100 m* času procesoru a *128Mi* paměti. Omezení prostředků pro pod jsou nastaveny na *250 m* procesoru a *256Mi* paměti:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Další informace o měření zdrojů a přiřazení, najdete [v tématu Správa výpočetních prostředků pro kontejnery][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Vývoj a ladění aplikací v clusteru AKS

**Pokyny pro osvědčené postupy** – vývojové týmy by měly nasadit a ladit proti clusteru AKS pomocí dev spaces. Tento model vývoje zajišťuje, že jsou implementovány ovládací prvky přístupu založené na rolích, potřeby sítě nebo úložiště před nasazením aplikace do produkčního prostředí.

S Azure Dev Spaces vyvíjíte, ladíte a testujete aplikace přímo proti clusteru AKS. Vývojáři v rámci týmu spolupracují na vytváření a testování v průběhu celého životního cyklu aplikace. Můžete pokračovat v používání existujících nástrojů, jako je Visual Studio nebo Visual Studio Code. Pro dev spaces je nainstalováno rozšíření, které poskytuje možnost spuštění a ladění aplikace v clusteru AKS:

![Ladění aplikací v clusteru AKS pomocí dev spaces](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Tento integrovaný proces vývoje a testování s dev spaces snižuje potřebu místních testovacích prostředí, jako je [minikube][minikube]. Místo toho můžete vyvíjet a testovat proti clusteru AKS. Tento cluster lze zabezpečit a izolovat, jak je uvedeno v předchozí části o použití oborů názvů logicky izolovat clusteru. Když jsou vaše aplikace připravené k nasazení do produkčního prostředí, můžete s jistotou nasadit, protože váš vývoj byl proveden proti skutečnému clusteru AKS.

Azure Dev Spaces je určen pro použití s aplikacemi, které běží na linuxových podech a uzlech.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Použití rozšíření kódu visual studia pro Kubernetes

**Pokyny pro osvědčené postupy** – instalace a použití rozšíření v kódu VS pro Kubernetes při psaní manifestů YAML. Můžete také použít rozšíření pro integrované řešení nasazení, které může pomoci vlastníkům aplikací, které zřídka komunikovat s clusterem AKS.

[Rozšíření kódu Visual Studio pro Kubernetes][vscode-kubernetes] vám pomůže vyvíjet a nasazovat aplikace do AKS. Rozšíření poskytuje intellisense pro kubernetes prostředky a Helm grafy a šablony. Můžete také procházet, nasazovat a upravovat prostředky Kubernetes z v rámci VS Code. Rozšíření také poskytuje intellisense kontrolu žádostí o prostředky nebo omezení jsou nastaveny ve specifikacích pod:

![Rozšíření kódu VS pro Kubernetes varování před chybějícími limity paměti](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Pravidelně kontrolujte problémy s aplikací u kube-advisor

**Pokyny pro osvědčené postupy** – `kube-advisor` pravidelně spouštějte nejnovější verzi nástroje s otevřeným zdrojovým kódem ke zjišťování problémů v clusteru. Pokud použijete kvóty prostředků na existující cluster `kube-advisor` AKS, spusťte nejprve najít pody, které nemají požadavky na prostředky a omezení definovány.

Nástroj [kube-advisor][kube-advisor] je přidružený projekt aks open source, který skenuje cluster Kubernetes a informuje o problémech, které najde. Jednou z užitečných kontrol je identifikace podů, které nemají požadavky na prostředky a omezení na místě.

Nástroj kube-advisor může podávat zprávy o žádosti o prostředky a omezuje chybějící v PodSpecs pro aplikace systému Windows, stejně jako linuxové aplikace, ale samotný nástroj kube-advisor musí být naplánován na pod linuxu. Pod můžete naplánovat tak, aby se spouštěl ve fondu uzlů s konkrétním osem pomocí [voliče uzlů][k8s-node-selector] v konfiguraci podu.

V clusteru AKS, který hostuje mnoho vývojových týmů a aplikací, může být obtížné sledovat pody bez těchto požadavků na prostředky a limity nastaveny. Jako osvědčený postup pravidelně `kube-advisor` spouštět na clustery AKS.

## <a name="next-steps"></a>Další kroky

Tento článek osvědčených postupů zaměřený na způsob spuštění clusteru a úloh y z hlediska operátora clusteru. Informace o osvědčených postupech pro správu naleznete v [doporučených postupech operátora clusteru pro izolaci a správu prostředků ve službě Azure Kubernetes Service (AKS).][operator-best-practices-isolation]

Chcete-li implementovat některé z těchto doporučených postupů, naleznete v následujících článcích:

* [Vývoj s využitím Dev Spaces][dev-spaces]
* [Zkontrolujte problémy s kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
