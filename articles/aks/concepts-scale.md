---
title: Koncepty – škálování aplikací ve službách Azure Kubernetes Services (AKS)
description: Další informace o škálování ve službě Azure Kubernetes Service (AKS), včetně horizontální pod autoscaler, automatické škálování clusteru a konektoru Azure Container Instances.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595854"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Možnosti škálování pro aplikace ve službě Azure Kubernetes Service (AKS)

Při spouštění aplikací ve službě Azure Kubernetes Service (AKS) budete muset zvýšit nebo snížit množství výpočetních prostředků. Vzhledem k tomu, že počet instancí aplikace, které potřebujete změnit, může být nutné změnit také počet základních uzlů Kubernetes. Také může být nutné rychle zřídit velký počet dalších instancí aplikace.

Tento článek představuje základní koncepty, které vám pomohou škálovat aplikace v AKS:

- [Ruční škálování](#manually-scale-pods-or-nodes)
- [Horizontální pod autoscaler (HPA)](#horizontal-pod-autoscaler)
- [Automatický škálovač clusteru](#cluster-autoscaler)
- [Integrace instance kontejneru Azure (ACI) s AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Ruční škálování podů nebo uzlů

Můžete ručně škálovat repliky (pody) a uzly a otestovat, jak aplikace reaguje na změnu dostupných prostředků a stavu. Ruční škálování prostředků také umožňuje definovat nastavené množství prostředků, které chcete použít k udržení pevných nákladů, jako je například počet uzlů. Chcete-li ručně škálovat, definujte počet replik nebo uzlů. Rozhraní API Kubernetes pak naplánuje vytvoření dalších podů nebo vypouštěcích uzlů na základě této repliky nebo počtu uzlů.

Při škálování dolů uzly, rozhraní API Kubernetes volání příslušné horozhraní Azure Compute API vázané na typ výpočetní ch od počítače používané vašeho clusteru. Například pro clustery postavené na škálování virtuálních her nastaví logiku pro výběr uzlů, které chcete odebrat, je určena rozhraníapi škálování virtuálních her. Další informace o tom, jak jsou uzly vybrány pro odebrání při škálování, najdete v [nejčastějších dotazech k VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Chcete-li začít s ručníškálování podů a uzlů viz [Škálování aplikací v AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Horizontální pod autoscaler

Kubernetes používá horizontální pod autoscaler (HPA) ke sledování poptávky po prostředcích a automaticky škálovat počet replik. Ve výchozím nastavení horizontální pod autoscaler kontroluje rozhraní API metriky každých 30 sekund pro všechny požadované změny v počtu replik. Pokud jsou požadovány změny, počet replik se odpovídajícím způsobem zvýší nebo sníží. Horizontální pod autoscaler pracuje s clustery AKS, které nasadily server metrik pro Kubernetes 1.8+.

![Kubernetes horizontální pod automatické škálování](media/concepts-scale/horizontal-pod-autoscaling.png)

Při konfiguraci automatického škálování horizontálnípod pro dané nasazení, definujete minimální a maximální počet replik, které lze spustit. Můžete také definovat metriku pro monitorování a založit na všech rozhodnutích škálování, jako je například využití procesoru.

Chcete-li začít s horizontálním pod autoscaler v AKS, viz [automatické škálování pody v AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Přebíjecí akce škálování

Jako horizontální pod automatického škálování kontroluje rozhraní API metriky každých 30 sekund, předchozí události škálování nemusí mít úspěšně dokončena před provedením další kontroly. Toto chování může způsobit horizontální pod autoscaler změnit počet replik před předchozí škálování události může přijímat zatížení aplikace a požadavky na prostředky odpovídajícím způsobem upravit.

Chcete-li minimalizovat tyto závodní události, jsou nastaveny hodnoty přebíjení nebo zpoždění. Tyto hodnoty definují, jak dlouho musí automatický škálovací modul vodorovného podu čekat po události škálování, než může být spuštěna jiná událost škálování. Toto chování umožňuje nový počet replik se projeví a rozhraní API metriky tak, aby odrážely distribuované úlohy. Ve výchozím nastavení je zpoždění při škálování událostí 3 minuty a zpoždění událostí škálování dolů je 5 minut

V současné době nelze vyladit tyto hodnoty cooldown z výchozího nastavení.

## <a name="cluster-autoscaler"></a>Automatický škálovač clusteru

Chcete-li reagovat na měnící se požadavky pod, Kubernetes má automatické škálování clusteru, který upravuje počet uzlů na základě požadovaných výpočetních prostředků ve fondu uzlů. Ve výchozím nastavení automaticky škálátor clusteru kontroluje server rozhraní API metrikkaždých 10 sekund, zda nenajdete požadované změny v počtu uzlů. Pokud automatické škálování clusteru určuje, že je požadována změna, počet uzlů v clusteru AKS se odpovídajícím způsobem zvýší nebo sníží. Automatický škálovací systém clusteru pracuje s clustery AKS s podporou RBAC, které běží na kubernetes 1.10.x nebo vyšší.

![Autoscaler clusteru Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Autoscaler clusteru se obvykle používá vedle horizontálního pod autoscaler. V kombinaci horizontální pod autoscaler zvyšuje nebo snižuje počet podů na základě požadavku aplikace a automatického škálování clusteru upraví počet uzlů podle potřeby spustit tyto další pody odpovídajícím způsobem.

Chcete-li začít s automatickým škálování clusteru v AKS, najdete v [tématu Automatické škálování clusteru na AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Škálování událostí

Pokud uzel nemá dostatek výpočetních prostředků pro spuštění požadovaného podu, tento pod nemůže projít procesem plánování. Pod nelze spustit, pokud další výpočetní prostředky jsou k dispozici v rámci fondu uzlů.

Když autoškálování clusteru oznámení pody, které nelze naplánovat z důvodu omezení prostředků fondu uzlů, počet uzlů v rámci fondu uzlů se zvýší poskytnout další výpočetní prostředky. Pokud jsou tyto další uzly úspěšně nasazeny a jsou k dispozici pro použití v rámci fondu uzlů, pody jsou pak naplánováno ke spuštění na nich.

Pokud vaše aplikace potřebuje rychle škálovat, některé pody mohou zůstat ve stavu čekání na naplánování, dokud další uzly nasazené automatickým škálovačem clusteru nepřijmují naplánované pody. Pro aplikace, které mají vysoké požadavky na shluk, můžete škálovat s virtuálními uzly a instancemi kontejnerů Azure.

### <a name="scale-down-events"></a>Škálování událostí

Automatický škálovač clusteru také monitoruje stav plánování podu pro uzly, které v poslední době neobdržely nové požadavky na plánování. Tento scénář označuje, že fond uzlů má více výpočetních prostředků, než je požadováno, a počet uzlů lze snížit.

Uzel, který prochází prahovou hodnotou pro již není potřeba po dobu 10 minut ve výchozím nastavení je naplánováno odstranění. Když nastane tato situace pods jsou naplánovány ke spuštění na jiných uzlech v rámci fondu uzlů a automatického škálování clusteru snižuje počet uzlů.

Vaše aplikace může dojít k narušení jako pody jsou naplánovány na různých uzlech při automatického škálování clusteru snižuje počet uzlů. Chcete-li minimalizovat narušení, vyhněte se aplikacím, které používají jednu instanci pod.

## <a name="burst-to-azure-container-instances"></a>Burst na instance kontejneru Azure

Chcete-li rychle škálovat cluster AKS, můžete integrovat s instancemi kontejnerů Azure (ACI). Kubernetes má vestavěné součásti pro škálování počtu replik a uzlů. Pokud však vaše aplikace potřebuje rychle škálovat, může automatické škálování podů naplánovat více podů, než může poskytnout existující výpočetní prostředky ve fondu uzlů. Pokud je tento scénář nakonfigurován, by pak aktivovat automatického škálování clusteru nasadit další uzly ve fondu uzlů, ale může trvat několik minut pro tyto uzly úspěšně zřídit a povolit Plánovač Kubernetes ke spuštění podů na nich.

![Kubernetes praskla škálování na ACI](media/concepts-scale/burst-scaling.png)

ACI umožňuje rychle nasadit instance kontejneru bez další chod infrastruktury. Při připojení s AKS, ACI se stane zabezpečené, logické rozšíření clusteru AKS. Komponenta [virtuálních uzlů,][virtual-nodes-cli] která je založena na [virtuálníkubelet][virtual-kubelet], je nainstalována v clusteru AKS, který představuje ACI jako virtuální uzel Kubernetes. Kubernetes pak můžete naplánovat pods, které běží jako instance ACI prostřednictvím virtuálních uzlů, nikoli jako pody na uzlech virtuálních realit přímo v clusteru AKS. Virtuální uzly jsou aktuálně ve verzi preview v AKS.

Aplikace nevyžaduje žádné úpravy používat virtuální uzly. Nasazení lze škálovat napříč AKS a ACI a bez zpoždění jako clusteru autoscaler nasazuje nové uzly ve vašem clusteru AKS.

Virtuální uzly se nasazují do další podsítě ve stejné virtuální síti jako váš cluster AKS. Tato konfigurace virtuální sítě umožňuje zabezpečit provoz mezi ACI a AKS. Stejně jako cluster AKS je instance ACI zabezpečený logický výpočetní prostředek, který je izolován od ostatních uživatelů.

## <a name="next-steps"></a>Další kroky

Chcete-li začít s škálováním aplikací, nejprve postupujte [podle rychlého startu a vytvořte cluster AKS pomocí příkazového příkazu k řešení Azure .][aks-quickstart] Potom můžete začít ručně nebo automaticky škálovat aplikace v clusteru AKS:

- Ruční škálování [podů][aks-manually-scale-pods] nebo [uzlů][aks-manually-scale-nodes]
- Použití [automatického škálování vodorovného podu][aks-hpa]
- Použití [automatického škálování clusteru][aks-cluster-autoscaler]

Další informace o základních konceptech Kubernetes a AKS naleznete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / AKS přístup a identita][aks-concepts-identity]
- [Kubernetes / AKS zabezpečení][aks-concepts-security]
- [Kubernetes / AKS virtuální sítě][aks-concepts-network]
- [Kubernetes / AKS úložiště][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md