---
title: Koncepty – škálování aplikací ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o škálování ve službě Azure Kubernetes Service (AKS), včetně horizontálního automatického škálování pod automatickým škálováním, automatického škálování clusteru a konektoru Azure Container Instances.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b72ed7cefc6a16eb484e1337dbd64e5f069a2201
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94686034"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Možnosti škálování pro aplikace ve službě Azure Kubernetes Service (AKS)

Při spouštění aplikací ve službě Azure Kubernetes Service (AKS) možná budete muset zvýšit nebo snížit množství výpočetních prostředků. Vzhledem k počtu instancí aplikace, které potřebujete změnit, může být potřeba změnit také počet podkladových uzlů Kubernetes. Také možná budete muset rychle zřídit velký počet dalších instancí aplikace.

V tomto článku se seznámíte se základními koncepty, které vám pomůžou škálovat aplikace v AKS:

- [Ruční škálování](#manually-scale-pods-or-nodes)
- [Horizontální automatické škálování pod (HPA)](#horizontal-pod-autoscaler)
- [Automatické škálování clusteru](#cluster-autoscaler)
- [Integrace Azure Container instance (ACI) s AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Ruční škálování v luskech nebo uzlech

Můžete ručně škálovat repliky (lusky) a uzly, abyste otestovali, jak vaše aplikace reaguje na změnu dostupných prostředků a stavu. Ruční škálování prostředků také umožňuje definovat sadu množství prostředků, které se mají použít k údržbě pevných nákladů, jako je třeba počet uzlů. Chcete-li ručně škálovat, definujte počet replik nebo uzlů. Rozhraní Kubernetes API pak plánuje vytváření dalších lusků nebo vyprazdňování uzlů na základě této repliky nebo počtu uzlů.

Při horizontálním škálování uzlů volá rozhraní API Kubernetes příslušné rozhraní API služby Azure COMPUTE vázané na výpočetní typ používaný clusterem. Například pro clustery založené na VM Scale Sets logiku pro výběr uzlů, které mají být odebrány, je určena rozhraním API VM Scale Sets. Další informace o tom, jak jsou uzly vybrané k odstranění při horizontálním navýšení kapacity, najdete v tématu [VMSS – Nejčastější dotazy](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Pokud chcete začít s ručním škálováním v luskech a uzlech, přečtěte si téma [škálování aplikací v AKS][aks-scale]

## <a name="horizontal-pod-autoscaler"></a>Horizontální automatické škálování podů

Kubernetes používá k monitorování požadavků na prostředky horizontální horizontální navýšení (HPA) a automaticky škáluje počet replik. Ve výchozím nastavení automatické škálování pod automaticky kontroluje metriky rozhraní API každých 30 sekund pro všechny požadované změny v počtu replik. Když se vyžadují změny, počet replik se odpovídajícím způsobem zvýší nebo sníží. Funkce automatického škálování pod možností horizontálního navýšení funguje s clustery AKS, které nasadily Server metrik pro Kubernetes 1.8 +.

![Automatické škálování Kubernetes vodorovně pod](media/concepts-scale/horizontal-pod-autoscaling.png)

Když pro dané nasazení nakonfigurujete horizontální automatické škálování pod, definujete minimální a maximální počet replik, které se dají spustit. Také definujete metriku, která bude monitorovat a založit všechna rozhodnutí o škálování na, jako je například využití procesoru.

Informace o tom, jak začít s autohorizontálně pod autoškálou v AKS, najdete v tématu [Automatické škálování v AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Cooldown událostí škálování

Když automatické škálování pod vodorovnou rovinou kontroluje metriky rozhraní API každých 30 sekund, předchozí události škálování se nemusely úspěšně dokončit před provedením další kontroly. Toto chování by mohlo způsobit, že horizontální dopředný automatické škálování změní počet replik předtím, než by předchozí událost škálování mohla přijmout aplikační úlohu, a prostředek vyžaduje, aby se odpovídajícím způsobem upravil.

Pro minimalizaci událostí časování je nastavená hodnota zpoždění. Tato hodnota určuje, jak dlouho musí automatické škálování vodorovně pod po událostech škálování počkat, než se může aktivovat jiná událost škálování. Toto chování umožňuje uplatnění nového počtu replik a rozhraní API metrik pro odrážet distribuovanou úlohu. Neexistují [žádné zpoždění pro události škálování Kubernetes 1,12](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay), ale zpoždění událostí horizontálního navýšení kapacity je nastavené na 5 minut.

V současné době nemůžete tyto hodnoty cooldown z výchozího nastavení ladit.

## <a name="cluster-autoscaler"></a>Automatické škálování clusteru

Pro reakci na změnu požadavků pod Kubernetes má modul automatického škálování clusteru, který přizpůsobí počet uzlů na základě požadovaných výpočetních prostředků ve fondu uzlů. Ve výchozím nastavení provádí automatické škálování clusteru metriky serveru rozhraní API metrik každých 10 sekund pro všechny požadované změny v počtu uzlů. Pokud automatické škálování clusteru určí, že se vyžaduje změna, počet uzlů v clusteru AKS se odpovídajícím způsobem zvýší nebo sníží. Automatické škálování clusteru funguje s clustery AKS Kubernetes s povolenou RBAC, které spouští Kubernetes 1.10. x nebo vyšší.

![Automatické škálování clusteru Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Automatické škálování clusteru se obvykle používá společně s autohorizontálním škálováním pod. V kombinaci se horizontální automatické škálování pod ním zvyšuje nebo snižuje počet lusků v závislosti na požadavcích aplikace a automatické škálování clusteru podle potřeby upraví počet uzlů podle potřeby, aby tyto další lusky běžely odpovídajícím způsobem.

Pokud chcete začít s nástrojem pro automatické škálování clusteru v AKS, přečtěte si téma [Automatické škálování clusteru na AKS][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Horizontální navýšení kapacity událostí

Pokud uzel nemá dostatek výpočetních prostředků ke spuštění požadovaného uzlu, může to v průběhu procesu plánování probíhat. Uzel pod nemůže začít, pokud není v rámci fondu uzlů k dispozici další výpočetní prostředky.

Když se pro automatické škálování clusteru vydává oznámení, že se nedá naplánovat kvůli omezením prostředků fondu uzlů, zvýší se počet uzlů v rámci fondu uzlů, aby se poskytly další výpočetní prostředky. Pokud jsou tyto další uzly úspěšně nasazeny a k dispozici pro použití v rámci fondu uzlů, potom je naplánováno jejich spuštění.

Pokud vaše aplikace potřebuje rychle škálovat, některé lusky můžou zůstat ve stavu, který čeká na naplánování, dokud další uzly nasazené nástrojem pro automatické škálování clusteru nemůžou akceptovat naplánované lusky. U aplikací, které mají vysoké požadavky na nárůst zatížení, můžete škálovat pomocí virtuálních uzlů a Azure Container Instances.

### <a name="scale-in-events"></a>Škálování v událostech

Automatické škálování clusteru také monitoruje stav plánování pod uzlem, které nedávno nepřijaly nové požadavky na plánování. Tento scénář znamená, že fond uzlů má víc výpočetních prostředků, než kolik jich je potřeba, a počet uzlů může být snížený.

Uzel, který předává prahovou hodnotu pro již nepotřebnou dobu 10 minut, je ve výchozím nastavení naplánován pro odstranění. V případě, že dojde k této situaci, je naplánováno spuštění lusků na jiných uzlech v rámci fondu uzlů a automatické škálování clusteru sníží počet uzlů.

V případě, že se při automatickém škálování clusteru sníží počet uzlů, může dojít k výpadkům v případě, že se aplikace naplánovala v různých uzlech. Aby se minimalizovalo přerušení, vyhněte se aplikacím, které používají jednu instanci pod.

## <a name="burst-to-azure-container-instances"></a>Nárůst na Azure Container Instances

Pokud chcete rychle škálovat cluster AKS, můžete ho integrovat s Azure Container Instances (ACI). Kubernetes má integrované součásti pro škálování počtu replik a uzlů. Pokud ale vaše aplikace potřebuje rychle škálovat, může horizontální horizontální navýšení kapacity naplánovat více lusků, než je možné v rámci fondu uzlů poskytnout stávající výpočetní prostředky. V případě konfigurace by tento scénář mohl aktivovat automatické škálování clusteru pro nasazení dalších uzlů ve fondu uzlů, ale může trvat několik minut, než se tyto uzly úspěšně zřídí a povolí plánovači Kubernetes, aby na nich běžela lusky.

![Škálování rozKubernetesho shluku na ACI](media/concepts-scale/burst-scaling.png)

ACI umožňuje rychle nasadit instance kontejnerů bez dalších režijních nákladů na infrastrukturu. Když se připojíte pomocí AKS, ACI se bude zabezpečeným logickým rozšířením vašeho clusteru AKS. Součást [virtuálních uzlů][virtual-nodes-cli] , která je založená na [Virtual Kubelet][virtual-kubelet], je nainstalovaná v clusteru AKS, který prezentuje ACI jako virtuální uzel Kubernetes. Kubernetes může naplánovat lusky spouštěné jako instance ACI prostřednictvím virtuálních uzlů, ne jako lusky na uzlech virtuálních počítačů přímo v clusteru AKS.

Vaše aplikace nevyžaduje žádné úpravy k použití virtuálních uzlů. Nasazení se můžou škálovat napříč AKS a ACI a bez prodlevy, protože automatické škálování clusteru nasazuje nové uzly v clusteru AKS.

Virtuální uzly se nasazují do další podsítě ve stejné virtuální síti jako cluster AKS. Tato konfigurace virtuální sítě umožňuje zabezpečení provozu mezi ACI a AKS. Podobně jako cluster AKS je instance ACI zabezpečeným logickým výpočetním prostředkem, který je izolovaný od ostatních uživatelů.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s škálováním aplikací, nejdřív postupujte podle pokynů v [rychlém startu a vytvořte cluster AKS pomocí Azure CLI][aks-quickstart]. Pak můžete začít ručně nebo automaticky škálovat aplikace v clusteru AKS:

- Ruční škálování v [luskech][aks-manually-scale-pods] nebo [uzlech][aks-manually-scale-nodes]
- Použití [automatického škálování vodorovně pod][aks-hpa]
- Použití [automatického škálování clusteru][aks-cluster-autoscaler]

Další informace o základních konceptech Kubernetes a AKS najdete v následujících článcích:

- [Clustery a úlohy Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Přístup a identita Kubernetes/AKS][aks-concepts-identity]
- [Zabezpečení Kubernetes/AKS][aks-concepts-security]
- [Virtuální sítě Kubernetes/AKS][aks-concepts-network]
- [Úložiště Kubernetes/AKS][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
