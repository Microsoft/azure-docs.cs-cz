---
title: Osvědčené postupy pro správu prostředků
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro vývojáře aplikací pro správu prostředků ve službě Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 693cabac616dca8e108a2029c173a5e1b71c2695
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516737"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro vývojáře aplikací pro správu prostředků ve službě Azure Kubernetes Service (AKS)

Při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS) je potřeba zvážit několik klíčových oblastí. Způsob správy nasazení aplikací může mít negativní dopad na činnost služeb, které poskytujete, pro koncové uživatele. Abychom vám pomohli uspět, mějte na paměti některé osvědčené postupy, které můžete při vývoji a spouštění aplikací v AKS sledovat.

Tento článek o osvědčených postupech se zaměřuje na spuštění clusteru a úloh z perspektivy pro vývojáře aplikací. Informace o osvědčených postupech pro správu najdete [v tématu osvědčené postupy pro izolaci a správu prostředků ve službě Azure Kubernetes (AKS)][operator-best-practices-isolation]. V tomto článku se naučíte:

> [!div class="checklist"]
> * Co jsou požadavky a omezení prostředků
> * Způsoby vývoje a nasazení aplikací s využitím mostu do Kubernetes a Visual Studio Code
> * Jak používat `kube-advisor` Nástroj ke kontrole problémů s nasazeními

## <a name="define-pod-resource-requests-and-limits"></a>Definování požadavků a omezení prostředků pod

**Doprovodné materiály k osvědčeným postupům** – nastavte v YAML manifestech pod požadavky a omezení na všechny lusky. Pokud cluster AKS používá *kvóty prostředků*, může být nasazení odmítnuto, pokud tyto hodnoty nedefinujete.

Hlavním způsobem správy výpočetních prostředků v rámci clusteru AKS je použití požadavků a omezení pod. Tyto požadavky a omezení umožňují plánovači Kubernetes zjistit, jaké výpočetní prostředky by měl být pod ním přiřazen.

* V případě **požadavků na procesor a paměť** definujte v pravidelných intervalech velikost procesoru a paměti, které musí.
    * Když se Plánovač Kubernetes pokusí umístit uzel pod na uzel, používají se k určení, který uzel má dostatek dostupných zdrojů pro plánování, použít žádosti pod.
    * Požadavek na hodnotu pod se nastaví jako výchozí pro definovaný limit.
    * Pro úpravu těchto požadavků je velmi důležité monitorovat výkon vaší aplikace. Pokud jsou požadavky na prostředky nedostatečné, může vaše aplikace obdržet snížený výkon, protože by bylo možné naplánovat uzel. Pokud jsou požadavky přehodnoceny, může být vaše aplikace zvýšena o naplánovaných potížích.
* **Omezení počtu procesorů a paměti** jsou maximální množství procesorů a paměti, které může použít pod. Omezení paměti usnadňují definování, které lusky by měly být ukončeny v případě nestability uzlu kvůli nedostatečným prostředkům. Bez správných limitů se bude ukončit až do chvíle, kdy je zatížení prostředku zrušeno. Část pod může nebo nemusí být schopná překročit limit procesoru po určitou dobu, ale v seznamu nebude ukončený za překročení limitu procesoru. 
    * Omezení pod vám pomůžou definovat, kdy došlo ke ztrátě řízení spotřeby prostředků pod. Při překročení limitu je pole pod prioritou pro usmrcování, aby udržoval stav uzlu a minimalizoval dopad na lusky sdílející uzel.
    * Při nastavení limitu na hodnotu pod se výchozí hodnota nastaví na nejvyšší dostupnou hodnotu na daném uzlu.
    * Nenastavujte limit na hodnotu pod vyšší než vaše uzly můžou podporovat. Každý uzel AKS rezervuje pro základní komponenty Kubernetes nastavenou velikost procesoru a paměti. Vaše aplikace se může pokusit spotřebovat příliš mnoho prostředků v uzlu, aby bylo možné ostatní lusky úspěšně spustit.
    * Je velmi důležité monitorovat výkon aplikace v různou dobu během dne nebo týdne. Určete, kdy je poptávka ve špičce, a zarovnejte omezení na základě prostředků vyžadovaných pro splnění maximálního počtu požadavků aplikace.

Ve specifikacích pod je **osvědčeným postupem a velmi důležité** definovat tyto požadavky a omezení na základě výše uvedených informací. Pokud tyto hodnoty nezahrnete, Plánovač Kubernetes se nemůže přihlédnout k prostředkům, které vaše aplikace potřebuje k podpoře při plánování rozhodnutí.

Pokud Plánovač umístí uzel pod na uzlu s nedostatečnými prostředky, bude výkon aplikace snížený. Pro správce clusteru se důrazně doporučuje nastavit *kvóty prostředků* v oboru názvů, který vyžaduje, abyste nastavili požadavky a omezení prostředků. Další informace najdete v tématu [kvóty prostředků v clusterech AKS][resource-quotas].

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

**Doprovodné materiály k osvědčeným postupům** – vývojové týmy by měly nasadit a LADIT cluster AKS pomocí mostu na Kubernetes.

S mostem na Kubernetes můžete vyvíjet, ladit a testovat aplikace přímo s clusterem AKS. Vývojáři v týmu spolupracují na sestavování a testování v celém životním cyklu aplikace. Můžete dál používat existující nástroje, jako je například Visual Studio nebo Visual Studio Code. Pro most do Kubernetes je nainstalováno rozšíření, které umožňuje vývoj přímo v clusteru AKS.

Tento integrovaný proces vývoje a testování s mostem na Kubernetes snižuje potřebu místních testovacích prostředí, jako je [minikube][minikube]. Místo toho vyvíjíte a otestujete cluster AKS. Tento cluster může být zabezpečený a izolovaný, jak je uvedeno v předchozí části týkající se použití oborů názvů k logické izolaci clusteru.

Most do Kubernetes je určený pro použití s aplikacemi, které běží na systémech Linux a uzlech.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Použít rozšíření Visual Studio Code pro Kubernetes

**Pokyny k osvědčeným postupům** – při psaní manifestů YAML nainstalujte a použijte rozšíření vs Code pro Kubernetes. Můžete také použít rozšíření pro integrované řešení nasazení, které může pomáhat vlastníkům aplikací, kteří zřídka pracují s clusterem AKS.

[Rozšíření Visual Studio Code pro Kubernetes][vscode-kubernetes] pomáhá vyvíjet a nasazovat aplikace do AKS. Rozšíření poskytuje IntelliSense pro prostředky Kubernetes a grafy a šablony Helm. Prostředky Kubernetes můžete procházet, nasazovat a upravovat i v rámci VS Code. Rozšíření také poskytuje kontrolu nad požadavky nebo omezení prostředků v technologii IntelliSense v rámci specifikací pod:

![VS Code rozšíření pro upozornění na chybějící limity paměti pro Kubernetes](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Pravidelně kontrolovat problémy s aplikací pomocí Kube-Advisoru

**Doprovodné materiály k osvědčeným postupům** – pravidelně spouštějte nejnovější verzi `kube-advisor` nástroje open source a zjistěte problémy v clusteru. Pokud použijete kvóty prostředků v existujícím clusteru AKS, spusťte `kube-advisor` nejprve rutiny, které nemají požadavky na prostředky a definované limity.

Nástroj [Kube-Advisor][kube-advisor] je přidružený AKS open source projekt, který vyhledává cluster Kubernetes a oznamuje nalezené problémy. Jednou z užitečných kontrol je identifikovat lusky, které nemají požadavky na prostředky a omezení.

Nástroj Kube-Advisor může vykazovat požadavky na prostředky a omezení chybějící v PodSpecs pro aplikace Windows i pro aplikace pro Linux, ale samotný nástroj Kube-Advisor musí být naplánován na Linux pod. Můžete naplánovat spuštění pod v rámci fondu uzlů s konkrétním operačním systémem pomocí [voliče uzlů][k8s-node-selector] v konfiguraci pod.

V clusteru AKS, který je hostitelem mnoha vývojových týmů a aplikací, může být obtížné sledovat lusky, aniž by bylo nutné tyto požadavky na prostředky a omezení nastavit. Osvědčeným postupem je pravidelně spouštět `kube-advisor` v clusterech AKS.

## <a name="next-steps"></a>Další kroky

Tento článek s osvědčenými postupy se zaměřuje na spuštění clusteru a úloh z perspektivy operátora clusteru. Informace o osvědčených postupech pro správu najdete [v tématu osvědčené postupy pro izolaci a správu prostředků ve službě Azure Kubernetes (AKS)][operator-best-practices-isolation].

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
