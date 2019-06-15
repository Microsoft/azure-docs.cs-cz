---
title: Osvědčené postupy pro vývojáře – Správa prostředků ve službě Azure Kubernetes služby (AKS)
description: Přečtěte si osvědčené postupy pro vývojáře aplikací pro správu prostředků ve službě Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: zarhoads
ms.openlocfilehash: 69f60036bd718264174bf1befe832305e250e77c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073953"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro vývojáře aplikací ke správě prostředků ve službě Azure Kubernetes Service (AKS)

Vývoj a spouštění aplikací ve službě Azure Kubernetes Service (AKS), existuje několik klíčových oblastí, které byste měli zvážit. Jak spravovat vaše nasazení aplikace může mít negativní vliv na činnost koncového uživatele služeb, které zadáte. Které vám pomůžou uspět, mějte na paměti některé osvědčené postupy můžete použít jako vývoj a spouštění aplikací ve službě AKS.

Tento článek o osvědčených postupech se zaměřuje na tom, jak spustit cluster a úlohy z pohledu vývojáře aplikace. Informace o osvědčených postupech pro správu najdete v tématu [clusteru operátor osvědčené postupy pro izolaci a správu prostředků ve službě Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. V tomto článku se dozvíte:

> [!div class="checklist"]
> * Jaké jsou omezení a požadavky na prostředku pod
> * Způsoby, jak vyvíjet a nasazovat aplikace s prostory Dev a Visual Studio Code
> * Jak používat `kube-advisor` nástroj ke kontrole problémů s nasazením

## <a name="define-pod-resource-requests-and-limits"></a>Definovat omezení a požadavky na prostředku pod

**Osvědčené postupy pro moduly** – nastavení pod požadavky a omezení na všechny podů v vaše YAML manifesty. Pokud AKS cluster používá *kvóty prostředků*, pokud nebudete definovat tyto hodnoty se můžou odmítnout vašeho nasazení.

Primární způsob, jak spravovat výpočetní prostředky v rámci clusteru AKS je použití pod požadavky a omezení. Tyto požadavky a omezení dejte vědět, co výpočetní prostředky, které by mělo být přiřazeno podu Kubernetes plánovače.

* **Pod požadavky** stanovený počet procesoru a paměti, která chcete pod musí definovat. Tyto požadavky by měla být množství výpočetních prostředků pod potřebuje k poskytování přijatelnou úroveň výkonu.
    * Když Plánovač Kubernetes se pokusí umístit pod na uzlu, pod žádosti slouží k určení, který uzel má dostatek prostředků, které jsou k dispozici.
    * Monitorování výkonu aplikace upravit tyto požadavky, abyste měli jistotu, že nebudete definovat méně prostředků, které jsou nutné k údržbě přijatelnou úroveň výkonu.
* **Pod omezení** jsou maximální dobu, procesoru a paměti, které můžete použít pod. Tato omezení pomáhají zabránit jednu nebo dvě vyčerpává dlouho běžící podů tak moc procesoru a paměti z uzlu. Tento scénář by snížit výkon uzlu a dalších podů, které běží na ní.
    * Nenastavujte vyšší, než může zajistit uzly pod limit. Každý uzel AKS rezervuje sadu množství CPU a paměti pro součásti jádra Kubernetes. Vaše aplikace může pokusit o využívání příliš mnoho prostředků v uzlu pro další pody k úspěšnému spuštění.
    * Znovu monitorování výkonu aplikace v různou dobu během dne nebo týdne. Určení, kdy je poptávku ve špičce a zarovnat pod omezení pro prostředky nutné ke splnění potřeb vaší aplikace.

Ve vašich požadavků pod je osvědčeným postupem je definovat tyto požadavky a omezení. Pokud nechcete zahrnout tyto hodnoty, Plánovač Kubernetes nerozumí, které prostředky jsou potřeba. Plánovač může naplánovat podu na uzel bez dostatek prostředků k zajištění výkonu přijatelné aplikace. Správce clusteru může nastavit *kvóty prostředků* oboru názvů, který je potřeba nastavit omezení a požadavky prostředků. Další informace najdete v tématu [kvóty prostředků v AKS clusterů][resource-quotas].

Při definování procesoru požadavku nebo omezení, hodnota se měří v jednotkách procesoru. *1.0* procesoru odpovídá jedné základní virtuální jádro procesoru na uzlu. Stejné měření se používá pro GPU. Můžete také definovat desetinné části žádosti nebo omezení, obvykle v millicpu. Například *100 mil.* je *0,1* základní virtuální jádra procesoru.

V následující základní příklad pro jeden server NGINX pod pod požadavků *100 mil.* času procesoru, a *128Mi* paměti. Omezení prostředků pro pod jsou nastaveny na *250m* procesoru a *256Mi* paměti:

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

Další informace o měření prostředků a přiřazení najdete v tématu [Správa výpočetních prostředků pro kontejnery][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Vývoj a ladění aplikací proti clusteru AKS

**Osvědčené postupy pro moduly** -vývojové týmy by měly nasazovat a ladit proti clusteru AKS pomocí prostorů vývoj. Tento model vývoje zajišťuje, implementovat řízení přístupu na základě rolí, sítě nebo se úložiště udržet pod před nasazením aplikace do produkčního prostředí.

S Azure Dev mezery vývoj, ladění a testování aplikací přímo na clusteru AKS. Vývojáři v rámci týmu společně pro vytváření a testování v průběhu životního cyklu aplikací. Můžete nadále používat stávající nástroje, jako je Visual Studio nebo Visual Studio Code. Rozšíření je nainstalované v prostorech Dev, která poskytuje možnost spustit a ladit aplikace v clusteru AKS:

![Ladění aplikací v clusteru AKS Dev mezer](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Tento integrované vývojové a testovací proces s mezerami Dev snižuje potřebu místní testovací prostředí, jako například [minikube][minikube]. Místo toho vyvíjejte a testujte proti clusteru AKS. Tento cluster budou zabezpečené a izolované, jak je uvedeno v předchozí části týkající se použití oborů názvů logicky izolovat clusteru. Pokud vaše aplikace jsou připraveny k nasazení do produkčního prostředí, můžete bez obav nasadit jako vývoj bylo provedeno proti skutečným clusterem AKS.

Azure Dev prostory je určena pro použití s aplikací, které běží na systému Linux podů a uzly.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Použití rozšíření Visual Studio Code pro Kubernetes

**Osvědčené postupy pro moduly** – instalace a používání rozšíření VS Codu pro Kubernetes při psaní YAML manifesty. Rozšíření můžete také použít pro nasazení integrovaných řešení, které mohou pomoci počet vlastníků aplikace, které nevyužívají interakci s clusterem AKS.

[Rozšíření Visual Studio Code pro Kubernetes] [ vscode-kubernetes] vám pomůže vyvíjet a nasazovat aplikace pro AKS. Toto rozšíření poskytuje intellisense pro prostředky Kubernetesu a grafy Helm a šablony. Můžete také procházet, nasadit a upravit prostředky Kubernetesu z editoru VS Code. Rozšíření také poskytuje kontrolu technologie intellisense pro požadavky na prostředek nebo omezuje nastavena v podu specifikace:

![Rozšíření VS Codu pro Kubernetes varování o chybějících omezení paměti](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Pravidelně zjišťovat problémy s aplikací s využitím kube advisoru

**Osvědčené postupy pro moduly** – pravidelně používat nejnovější verzi `kube-advisor` open source nástroj a detekujte problémy ve vašem clusteru. Pokud použijete kvóty prostředků v existujícím clusteru AKS, spusťte `kube-advisor` nejprve k vyhledání podů, které nemají definovaná omezení a požadavky prostředků.

[Kube advisor] [ kube-advisor] nástroj je přidružené AKS opensourcový projekt, který prohledá Kubernetes cluster a zprávy o problémech, které nalezne. Jeden užitečné se identifikovat podů, které nemají omezení a požadavky na zdroje v místě.

Nástroj kube advisor může podávat požadavkem na prostředky a omezení v aplikacích PodSpecs pro Windows, jakož i Linuxové aplikace chybí, ale vlastního nástroje kube advisor musí být naplánováno na Linuxu pod. Můžete naplánovat podu spustit na fond uzlů s konkrétním použití operačního systému [uzlu selektoru] [ k8s-node-selector] v konfiguraci pod.

V clusteru AKS, který je hostitelem mnoho vývojových týmů a aplikace může být obtížné sledovat podů bez těchto prostředků požadavky a omezení sady. Jako osvědčený postup, pravidelně spouštět `kube-advisor` v clusterech služby AKS.

## <a name="next-steps"></a>Další postup

Tento článek o osvědčených postupech, zaměřuje na spuštění clusteru a úloh z hlediska operátor clusteru. Informace o osvědčených postupech pro správu najdete v tématu [clusteru operátor osvědčené postupy pro izolaci a správu prostředků ve službě Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

K provedení některých těchto osvědčených postupů, naleznete v následujících článcích:

* [Vývoj s využitím vývoje prostorů][dev-spaces]
* [Zkontrolovat problémy s využitím kube advisoru][aks-kubeadvisor]

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
